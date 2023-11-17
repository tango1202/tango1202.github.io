---
layout: single
title: "#5. [모던 C++] (C++11~) 개선된 클래스(함수 default, 함수 delete, override, final, 생성자 위임, 생성자 상속)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#11] 정의되지 않은 비공개 함수보다 삭제된 함수를 선호하라([delete를 이용한 암시적 형변환과 템플릿 인스턴스화 차단](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#delete%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98%EA%B3%BC-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94-%EC%B0%A8%EB%8B%A8) 참고)
> * [MEC++#12] 재정의 함수들을 override로 선언하라(오버라이드가 안되는 사용자 실수를 감지해준다.)
>   * 멤버 함수 참조 한정자로 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value), 좌측값을 구분해서 함수 호출 가능

> * (C++11~) [default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete)가 추가되어 암시적으로 생성되는 멤버 함수의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.
> * (C++11~)  [override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override)가 추가되어 가상 함수 상속의 코딩 규약이 좀더 단단해졌습니다.
> * (C++11~) [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final)이 추가되어 가상 함수를 더이상 오버라이딩 못하게 할 수 있습니다.
> * (C++11~) [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final)이 추가되어 강제적으로 상속을 제한할 수 있습니다.
> * (C++11~) [생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84)이 추가되어 생성자의 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 코드가 좀더 간결해 졌습니다.
> * (C++11~) [생성자 상속](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%83%81%EC%86%8D)이 추가되어 부모 개체의 생성자도 상속받아 사용할 수 있어 자식 개체의 생성자 재정의 코드가 좀더 간결해 졌습니다.
> * (C++20~) [삼중 비교 연산자를 default로 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90-default-%EC%A0%95%EC%9D%98)할 수 있습니다.

# default와 delete

기존에는 암시적으로 정의되는 멤버 함수들은 `private`를 이용하여 억지로 가시성을 조정하여 사용 여부를 제어했는데요(*[클래스의 암시적 정의](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-implicit-definition/) 참고*), 

C++11 부터는  [default나 delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete)로 사용 여부를 결정합니다.

```cpp
class T_11 {
public:
    T_11() = default; // 암시적 버전의 기본 생성자 사용
    T_11(const T_11&) = delete; // 암시적 버전의 복사 생성자 막음    
};
```
> *(C++20~) [삼중 비교 연산자를 default로 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90-default-%EC%A0%95%EC%9D%98)할 수 있습니다.*

# delete를 이용한 암시적 형변환과 템플릿 인스턴스화 차단

[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 그 어떤 함수도 삭제할 수 있습니다. "함수를 안만들면 되지, 뭐하러 만들고 삭제하냐" 할 수 있을텐데요, 이 기능을 활용하면, 재밌게도 함수 호출시의 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단할 수 있습니다.

다음의 `Func_11(int)`함수는 `Func_11('a')`와 같이 `char`를 전달해도 실행됩니다. `char`가 `int`로 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)되기 때문이죠. 

그런데, `void Func_11(char) = delete;`와 같이 `char`를 사용하는 함수를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)한다면, `Func_11('a')`와 같이 호출했을때 컴파일 오류가 발생하여 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단할 수 있습니다.

```cpp
class T_11 {
public:
    void Func_11(int) {}
    void Func_11(char) = delete; // char를 인자로 받는 함수를 삭제합니다.
};

T_11 t;
t.Func_11(10);
t.Func_11('a'); // (X) 컴파일 오류. delete된 함수입니다.
```

또한 특정 타입의 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)를 차단할 수 있습니다. 

다음 예에서는 `char`타입의 함수 템플릿 특수화를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 하여 `char`타입의 인스턴스화를 차단합니다.

```cpp
template<typename T> 
void Func_11(T) {}

template<> 
void Func_11<char>(char) = delete; // char 버전 함수 삭제

Func_11(10);
Func_11('a'); // (X) 컴파일 오류. delete된 함수입니다.
```

# override

기존에는 부모 개체의 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 상속할 때 아무런 조치 없이 상속했는데요(*[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 참고*), 가끔 오타인지, 새로운 가상 함수 정의인지 헷갈릴 때가 있습니다. 하기와 같은 실수를 찾아내는게 상당히 어려웠죠.

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

C++11 부터는 명시적으로 [override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override)를 지정해주면 컴파일러단에서 잘못된 오버라이딩인지 검사해 줍니다. 무조건 사용하세요. 삶이 쾌적해 집니다.

```cpp
class Base {
public:
    virtual void Func1() {};
    virtual void Func2() {};
};
class Derived_11 : public Base {
    virtual void Func1() override {}; // (O)
    virtual void Func2() override {}; // (O)
    virtual void Func_2() override {}; // (X) 컴파일 오류. 부모 개체에 해당 멤버 없음
};       
```

# final

기존에는 상속을 막기 위해 생성자를 `private`로 만들어 가시성을 제한 하거나, [public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 만들고 상속하지 않도록 서로간에 정하자 라고 했었는데요(*[상속 제한](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C) 참고*),

C++11 부터는 다음처럼 [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final)로 상속을 제한할 수 있습니다.

```cpp
class Base_11 final {
};
// (X) 컴파일 오류. Base_11은 상속할 수 없습니다.
class Derived_11 : public Base_11 {
};
```

또한, 멤버 함수의 [override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override)도 막을 수 있습니다.

```cpp
class Base_11 {
public:
    virtual void Func1() {};
    virtual void Func2() final {};
};
class Derived1_11 : public Base_11 {
    virtual void Func1() override final {}; // (O) Base를 오버라이드하고, 자식 개체에서는 오버라이드 못하게 합니다.
    virtual void Func2() override {}; // (X) 컴파일 오류. Func2는 오버라이드 할 수 없습니다.
}; 
class Derived2_11 : public Derived1_11 {
    virtual void Func1() override final {}; // (X) 컴파일 오류. Func1은 오버라이드 할 수 없습니다.
}; 
```

# 생성자 위임

기존에는 다양한 버전의 생성자를 만들고자 할때 각각의 생성자에서 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)를 사용하여 멤버 변수를 초기화 했는데요(*[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 참고*),

```cpp
class T {
    int m_X;
    int m_Y;
    int m_Z;
public:
    T() : m_X{0}, m_Y{0}, m_Z{0} {}
    explicit T(int x) : m_X{x}, m_Y{0}, m_Z{0} {}
    T(int x, int y) : m_X{x}, m_Y{y}, m_Z{0} {}
    T(int x, int y, int z) : m_X{x}, m_Y{y}, m_Z{z} {}  
};
```

C++11 부터는 다른 [생성자에 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84)할 수 있어 좀더 간결하게 생성자 코드를 만들 수 있습니다.

```cpp
class T_11 {
    int m_X;
    int m_Y;
    int m_Z;
public:
    T_11() : T_11{0, 0, 0} {} // T_11(int x, int y, int z)에 위임
    explicit T_11(int x) : T_11{x, 0, 0} {} // T_11(int x, int y, int z)에 위임
    T_11(int x, int y) : T_11{x, y, 0} {} // T_11(int x, int y, int z)에 위임
    T_11(int x, int y, int z) : m_X{x}, m_Y{y}, m_Z{z} {} 
};
```

# 생성자 상속

기존에는 자식 개체 생성자에서 부모 개체의 생성자를 명시적으로 호출해야 했는데요(*[자식 개체의 생성자 재정의](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%9E%90%EC%8B%9D-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9E%AC%EC%A0%95%EC%9D%98) 참고*),

```cpp
class Base {
    int m_X;
    int m_Y;
public:
    Base(int x, int y) : m_X{x}, m_Y{y} {}
};

class Derived : public Base {
public:
    // Derived 생성자에서 Base(x, y) 생성자를 명시적으로 호출
    Derived(int x, int y) : Base{x, y} {}
};

Derived d(10, 20);
```

C++11 부터는 [using 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/#using-%EC%84%A0%EC%96%B8)의 형태로 부모 개체의 생성자를 그대로 상속받아 사용할 수 있습니다.

```cpp
class Base {
    int m_X;
    int m_Y;
public:
    Base(int x, int y) : m_X{x}, m_Y{y} {}
};

class Derived_11 : public Base {
public:
    // 생성자 상속
    using Base::Base; 
};

Derived_11 d{10, 20};    
```

