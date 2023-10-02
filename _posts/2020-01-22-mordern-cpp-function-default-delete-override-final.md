---
layout: single
title: "#22. [모던 C++] (C++11~) 함수 default, 함수 delete, override, final"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `default`와 `delete`가 추가되어 암시적으로 생성되는 멤버 함수의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.
> * `override`가 추가되어 가상 함수 상속의 코딩 규약이 좀더 단단해졌습니다.
> * `final` 이 추가되어 가상 함수를 더이상 오버라이딩 못하게 할 수 있습니다.
> * `final`이 추가되어 강제적으로 상속을 제한할 수 있습니다.

# default와 delete

기존에는 암시적으로 정의되는 멤버 함수들은 `private`를 이용하여 억지로 가시성을 조정하여 사용 여부를 제어했는데요([클래스의 암시적 정의](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-implicit-definition/) 참고), 

C++11 부터는 `default`나 `delete` 키워드를 사용하여 제어할 수 있습니다.

```cpp
class T {
public:
    T() = default; // 암시적 버전의 기본 생성자 사용
    T(const T&) = delete; // 암시적 버전의 복사 생성자 막음    
};
```

# override

기존에는 부모 개체의 가상 함수를 상속할 때 아무런 조치 없이 상속했는데요, 가끔 오타인지, 새로운 가상 함수 정의인지 헷갈릴 때가 있습니다. 

```cpp
class Base {
public:
    virtual void Func1() {};
    virtual void Func2() {};
};
class Derived : public Base {
    virtual void Func1() {}; // (O)
    virtual void Func2() {}; // (O)
    virtual void Func_2() {}; // (△) 비권장. 오타일까요? 새로운 가상 함수를 정의한 것일까요?
};
```

C++11 부터는 명시적으로 `override`를 지정함으로서 컴파일러단에서 잘못된 오버라이드인지 검사할 수 있습니다.

```cpp
class Base {
public:
    virtual void Func1() {};
    virtual void Func2() {};
};
class Derived : public Base {
    virtual void Func1() override {}; // (O)
    virtual void Func2() override {}; // (O)
    virtual void Func_2() override {}; // (X) 컴파일 오류. 부모 개체에 해당 멤버 없음
};
```

# final

기존에는 상속을 막기 위해 생성자를 `private`로 만들어 가시성을 제한 하거나, 소멸자를 `public` Non-Virtual로 만들고 상속하지 않도록 서로간에 정하자 라고 했었는데요([상속 제한](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C) 참고),

C++11 부터는 다음처럼 `final`로 상속을 제한할 수 있습니다.

```cpp
class Base final {
};
// (X) 컴파일 오류. Base는 상속할 수 없습니다.
class Derived : public Base {
};
```

또한, 멤버 함수의 `override`도 막을 수 있습니다.

```cpp
class Base {
public:
    virtual void Func1() {};
    virtual void Func2() final {};
};
class Derived1 : public Base {
    virtual void Func1() override final {}; // (O) Base를 오버라이드하고, 자식 개체에서는 오버라이드 못하게 합니다.
    virtual void Func2() override {}; // (X) 컴파일 오류. Func2는 오버라이드 할 수 없습니다.
}; 
class Derived2 : public Derived1 {
    virtual void Func1() override final {}; // (X) 컴파일 오류. Func1은 오버라이드 할 수 없습니다.
}; 
```
