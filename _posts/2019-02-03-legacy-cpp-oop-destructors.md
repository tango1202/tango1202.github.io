---
layout: single
title: "#3. [레거시 C++ 개체 지향] 소멸자"
categories: "legacy-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [획득된 자원은 꼭 소멸](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)시켜라.
> * [암시적 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)가 정상 작동하도록 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/) 정의시 스마트 포인터(*[auto_ptr](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 사용하라.
> * [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)이 필요하면 부모 개체에 [가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하라.(*[가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)가 아니면 메모리 릭이 발생한다.*)
> * [public Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)인 개체는 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)하지 마라.
> * [is-a 관계](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#is-a-%EA%B4%80%EA%B3%84)에서는 [public Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하라.(*[가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)가 아니면 메모리 릭이 발생한다.*)
> * [has-a 관계](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#has-a-%EA%B4%80%EA%B3%84)에서는 [protected Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하라.
> * [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)처럼 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서도 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 마라.
> * [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 예외를 방출하지 마라.(*필요하다면 `Release()`함수를 구현하라.*)

> **모던 C++**
> * (C++11~) [default, delete](https://tango1202.github.io/cpp/modern-cpp-class/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.
> * (C++11~) [final](https://tango1202.github.io/cpp/modern-cpp-class/#final)이 추가되어 강제적으로 [상속을 제한](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C)할 수 있습니다.
> * (C++11~) [noexcept](https://tango1202.github.io/cpp/modern-cpp-noexcept/)가 추가되어 함수의 예외 방출 여부를 보증하며, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)는 기본적으로 [noexcept](https://tango1202.github.io/cpp/modern-cpp-noexcept/)로 동작합니다.

# 소멸자

[소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)는 개체의 수명이 다해 소멸될때 호출되는 특수 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)입니다. 개체가 활동하면서 생성했던 메모리나 리소스를 해제하는 역할을 합니다.

|항목|내용|
|--|--|
|`~T() {}`|[소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)|
|`virtual ~T() {}`|[가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)|
|`virtual ~T() = 0;`|[순가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%88%9C%EA%B0%80%EC%83%81-%EC%86%8C%EB%A9%B8%EC%9E%90)|

예를 들어 [new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 로 생성한 [포인터 멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 사용후 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸시키지 않으면, 메모리에 그대로 남아 있어(*메모리 릭*), 메모리 부족으로 프로그램이 중단됩니다. 

따라서 획득된 자원은 꼭 소멸(*[RAII(Resource Acquisition Is Initialization)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)*)시켜야 하는데요, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)는 개체가 소멸될때 호출되므로, 보통 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 이러한 자원을 소멸 시킵니다.

```cpp
class T {
    int* m_Ptr;
public:
    T() : m_Ptr(new int(10)) {} // new로 생성한 것은
    ~T() {delete m_Ptr;} // (O) delete로 소멸
};
```

# 소멸자 호출 시점

[소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)는 하기 상황에서 자동으로 호출됩니다.

1. [전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), 정적 변수(*[정적 전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98), [함수내 정적 지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)*)인 경우 프로그램 종료시
2. [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 생성한 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)인 경우 블록 [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)의 끝
3. [new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 생성한 [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체인 경우 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 시
4. [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)인 경우 표현식의 끝
5. [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)에 따른 [스택 풀기](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)시 

# 개체 소멸 순서

개체가 소멸되면, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 본문이 실행되고, 본문내의 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 소멸시키고, [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 소멸시킵니다. 그뒤 부모 클래스의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)를 호출합니다. 모든 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 호출이 끝나면, 메모리를 해제합니다.

1. [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 본문 실행 
2. 본문 내의 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 소멸(*선언의 역순*)
3. [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/) 소멸(*선언의 역순*)
4. 부모 클래스 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 본문 호출
5. 부모 클래스 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 본문내의 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 소멸(*선언의 역순*)
6. 부모 클래스 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/) 소멸(*선언의 역순*)
7. 메모리 해제

```cpp
class BaseMemberObj {
public:
    BaseMemberObj() {std::cout << "1. BaseMemberObj::BaseMemberObj()" << std::endl;}
    ~BaseMemberObj() {std::cout << "10. BaseMemberObj::~BaseMemberObj()" << std::endl;}
};
class BaseLocalObj {
public:
    ~BaseLocalObj() {std::cout << "9. BaseLocalObj::~BaseLocalObj()" << std::endl;}
};
class Base {
    BaseMemberObj m_BaseMemberObj;
public:
    Base() {std::cout << "2. Base::Base()" << std::endl;}
    ~Base() {
        BaseLocalObj baseLocalObj;
        std::cout << "8. Base::~Base()" << std::endl;
    }
};
class DerivedMemberObj {
public:
    DerivedMemberObj() {std::cout << "3. DerivedMemberObj::DerivedMemberObj()" << std::endl;}
    ~DerivedMemberObj() {std::cout << "7. DerivedMemberObj::~DerivedMemberObj()" << std::endl;}
};
class DerivedLocalObj {
public:
    ~DerivedLocalObj() {std::cout << "6. DerivedLocalObj::~DerivedLocalObj()" << std::endl;}
};        
class Derived : public Base {
    DerivedMemberObj m_DerivedMemberObj;
public:
    Derived() {std::cout << "4. Derived::Derived()" << std::endl;}
    ~Derived() {
        DerivedLocalObj derivedLocalObj;
        std::cout << "5. Derived::~Derived()" << std::endl;
    }
};

Derived d;
```

실행 결과는 다음과 같습니다.

```cpp
1. BaseMemberObj::BaseMemberObj()
2. Base::Base()
3. DerivedMemberObj::DerivedMemberObj()
4. Derived::Derived()
5. Derived::~Derived() // 소멸자 호출
6. DerivedLocalObj::~DerivedLocalObj() // 소멸자 지역 변수 소멸(선언의 역순)
7. DerivedMemberObj::~DerivedMemberObj() // 멤버 변수 소멸(선언의 역순)
8. Base::~Base() // 부모 클래스 소멸자 호출
9. BaseLocalObj::~BaseLocalObj()
10. BaseMemberObj::~BaseMemberObj()
```

# 암시적 소멸자

[복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)와 마찬가지로, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)를 정의하지 않으면, 컴파일러는 [암시적 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)를 정의해 줍니다. [암시적 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)는 아무 작업을 하지 않으며 예외를 방출하지 않습니다.

[소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)를 명시적으로 구현해서 사용한 리소스를 직접 소멸시키기 보다는 [암시적 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)를 그대로 사용할 수 있도록 스마트 포인터(*[auto_ptr](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 이용하는게 실수를 줄일 수 있고 [예외 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/)에 더 좋습니다.(*[Holder의 필요성](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/#holder%EC%9D%98-%ED%95%84%EC%9A%94%EC%84%B1) 참고*)

스마트 포인터(*[auto_ptr](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 이용해서 [암시적 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)와 호환하는 방법은 [복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)에서 언급한 것처럼, 

1. 스마트 포인터를 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)로 정의하고,
2. [new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)한 개체를 스마트 포인터 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 해주시면 됩니다.

> *(C++11~) [default, delete](https://tango1202.github.io/cpp/modern-cpp-class/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.*

# 다형 소멸

개체 지향 프로그래밍을 하다보면, 부모 개체 포인터로 자식 개체를 사용/소멸하는 작업을 빈번히 하게 됩니다.

```cpp
class Base {};
class Derived1 : public Base {};
class Derived2 : public Base {};

Base* ptr1 = new Derived1();
Base* ptr2 = new Derived2();
... // ptr1, ptr2를 사용하고,

delete ptr1; // Derived1을 소멸시킵니다.
delete ptr2; // Derived2를 소멸시킵니다.
```

다음 예제의 `Derived`를 생성/소멸시키면, `Derived`와 `Base` [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 잘 호출되어 1, 2가 모두 호출됩니다.

```cpp
class Base {
public:
    ~Base() {std::cout << "2. Base::~Base()" << std::endl;} // (△) 비권장. 그냥 public non-virtual 이어서 다형 소멸을 지원하지 않습니다.
};

class Derived : public Base {
public:
    ~Derived() {std::cout << "1. Derived::~Derived()" << std::endl;}
};
Derived* d = new Derived;
delete d; // 1, 2 호출됨
```

하지만 `Base`의 포인터로 소멸시키면, 

```cpp
Derived* d = new Derived;
Base* b = d;
delete b; // (X) 오동작. 2만 호출됨. Derived 소멸자가 호출되지 않음. 메모리 릭.
```

`Base` [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)만 호출됩니다. 따라서, `Derived` [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 호출되지 않아 `Derived` 에서 사용한 자원의 메모리 릭이 발생할 수 있습니다. 

이렇게 부모 개체 포인터로부터 [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 해야 하는 경우에는 자식 개체의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 호출되도록 꼭 `virtual`[소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)로 작성하여야 합니다.

```cpp
class Base {
public:
    virtual ~Base() {std::cout << "2. Base::~Base()" << std::endl;} // (O) 다형 소멸을 지원함
};

class Derived : public Base {
public:
    ~Derived() {std::cout << "1. Derived::~Derived()" << std::endl;}
};

Derived* d = new Derived;
Base* b = d;
delete b; // (O) 1, 2 호출됨. 다형 소멸 지원.
```

# public Non-Virtual 소멸자

부모 개체로 사용하지 않을 것이라면, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)는 [public Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 정의합니다.

1. 외부에서 생성/소멸해야 하니 `public`이어야 하겠고,
2. [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)받지 않으니 [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)이 필요없어 Non-Virtual로 정의합니다.

다만, 아무런 제약이 없기 때문에 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)한다면, [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)이 안되는 우려가 있습니다. 개체가 [public Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)라면, [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)하지 말라는 뜻이니, 절대 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)하지 마세요. 혹시나 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)이 필요하다면, 다음에 설명할 [public Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)나, [protected Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 리팩토링 하시기 바랍니다.(*[상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)을 강제로 제한하는 방법은 [상속 제한](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C) 참고*)

```cpp
class T {
public:
    ~T() {
        std::cout << "2. T::~T()" << std::endl;        
    }
};
// (△) 비권장. 다형 소멸이 되지 않습니다.
class U : public T { 
public:
    ~U() {
        std::cout << "1. U::~U()" << std::endl;        
    }
};
T t; // ~T() 호출
U u; // ~U() 호출 및 ~U() 호출

// (△) 비권장. 누군가가 다음처럼 부모 클래스 포인터로 사용해 버리면 메모리 릭입니다.
T* ptr = new U;
delete ptr; // (△) 비권장. ~T() 호출. ~U()가 호출되지 않습니다.
```

> *(C++11~) [final](https://tango1202.github.io/cpp/modern-cpp-class/#final)이 추가되어 강제적으로 [상속을 제한](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C)할 수 있습니다.*

# public Virtual 소멸자

[is-a 관계](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#is-a-%EA%B4%80%EA%B3%84)에서는 부모 개체 포인터로 자식 개체를 제어하고, [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 합니다. (*[is-a 관계](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#is-a-%EA%B4%80%EA%B3%84) 참고*)
 
1. 외부에서 생성/소멸해야 하니 `public`이어야 하겠고,
2. [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 해야 하니 `virtual`로 정의합니다.

# protected Non-Virtual 소멸자

[has-a 관계](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#has-a-%EA%B4%80%EA%B3%84)는 [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 하지 않는 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/) 관계입니다. 자식 개체가 부모 개체의 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)나 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)의 기능을 포함하고 제공하는 관계라고 할 수 있습니다.(*[has-a 관계](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#has-a-%EA%B4%80%EA%B3%84) 참고*)

1. 외부에서 부모 클래스로 직접 생성/소멸하지 않고, 자식 개체를 통해 생성/소멸하므로 `protected`이어야 하겠고,
2. [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 하지 않으니 Non-Virtual로 정의합니다.

`protected` 이기 때문에 부모 개체를 직접 인스턴스화 할 수 없고, 오로지 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)해서만 사용할 수 있습니다. 또한 실수로 부모 개체 포인터로 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하려고 하면, `protected`여서 컴파일 오류가 발생합니다. 아주 단단한 **코딩 계약**이죠.

```cpp
class Base {
protected:
    ~Base() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.
public:
    virtual void Func() = 0;
};
class Derived : public Base {
public:        
    virtual void Func() {}
};
Base base; // (X) 컴파일 오류. 소멸자가 protected

Derived derived; // (O)

Base* p = new Derived();
delete p; // (X) 컴파일 오류. Base의 소멸자가 protected
```

# 가상 함수가 없는 경우의 다형성
    
보통 다형적 동작을 하는 개체들은 부모 클래스에 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있습니다. 하지만, [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 아예 없는 경우도 있을 수 있는데요, 이럴때 억지로 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 추가하고 싶으면 [가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 로 만들면 됩니다.

다음은 부모 개체를 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)받아 자식 개체에서 타입명만 다르게 하여 사용한 한 예입니다. `Error`에서는 자식 개체에서 필요로하는 모든 함수를 제공하고 있어 별도의 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 정의가 필요없고, [다형 소멸](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 위해 [public Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)만 제공하고 있습니다.

```cpp
class Error {
private:
    std::string m_Message;
public:
    virtual ~Error() {} // (O) 다형 소멸. 소멸자 외에는 별다른 가상 함수가 없습니다.
    // 모든 자식 개체들에서 사용합니다. 가상 함수는 없습니다.
    void SetMessage(const char* msg) {m_Message = msg;}
    const std::string& GetMessage() const {return m_Message;}
};

// 모든 자식 개체들이 타입명만 다릅니다.
class FileError : public Error {};
class NetworkError : public Error {};
class PrintError : public Error {};

std::vector<Error*> errors;
errors.push_back(new FileError());
errors.push_back(new NetworkError());
errors.push_back(new PrintError());

// 다형 소멸
for(std::vector<Error*>::iterator itr = errors.begin(); itr != errors.end(); ++itr) {
    delete *itr;
}
errors.clear();
```

# 순가상 소멸자

C++에서는 [순가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%88%9C%EA%B0%80%EC%83%81-%EC%86%8C%EB%A9%B8%EC%9E%90)를 허용합니다. 다만 실제 구현 정의가 반드시 있어야 합니다.(*없으면 컴파일 오류가 납니다.*)

```cpp
class T {
public:
    virtual ~T() = 0;
};
T::~T() {} // 실제 구현 정의가 있어야 함
```

[순가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%88%9C%EA%B0%80%EC%83%81-%EC%86%8C%EB%A9%B8%EC%9E%90)는 다음의 경우에 사용할 수 있습니다.

1. [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/) 전용 기반 클래스
   
    [protected 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%83%81%EC%86%8D-%EC%A0%84%EC%9A%A9-%EA%B8%B0%EB%B0%98-%ED%81%B4%EB%9E%98%EC%8A%A4---protected-%EC%83%9D%EC%84%B1%EC%9E%90)나 [protected Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)처럼 개체 인스턴스화를 못하게 하고, [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)해서만 사용할 수 있게 만듭니다. 이런 경우라면 [protected 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%83%81%EC%86%8D-%EC%A0%84%EC%9A%A9-%EA%B8%B0%EB%B0%98-%ED%81%B4%EB%9E%98%EC%8A%A4---protected-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용하는게 더 좋습니다. 소멸시에 컴파일 오류가 발생하는 것보다 생성시에 컴파일 오류가 발생하는게 좀더 직관적이거든요.(*[상속 전용 기반 클래스 - protected 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%83%81%EC%86%8D-%EC%A0%84%EC%9A%A9-%EA%B8%B0%EB%B0%98-%ED%81%B4%EB%9E%98%EC%8A%A4---protected-%EC%83%9D%EC%84%B1%EC%9E%90) 참고, [상속 강제](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C) 참고*)

2. [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)
   
    [protected Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)처럼 [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4) 구현시 사용합니다. 

    이 경우는 [protected Non-Virtual 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하는게 더 좋습니다. [순가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%88%9C%EA%B0%80%EC%83%81-%EC%86%8C%EB%A9%B8%EC%9E%90)는 정의를 따로 만들어야 하기 때문에 번거롭거든요.(*특히 [함수 내부의 로컬 클래스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%ED%95%A8%EC%88%98-%EB%82%B4%EB%B6%80%EC%9D%98-%EB%A1%9C%EC%BB%AC-%ED%81%B4%EB%9E%98%EC%8A%A4)로 정의한 경우, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 구현 정의를 할 방법이 없습니다.*)

3. 마땅한 [순가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 없는 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)
   
   이경우에는 [순가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%88%9C%EA%B0%80%EC%83%81-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용해야 합니다. [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)를 참고하세요. 

# 소멸자에서 가상 함수 호출 금지

부모 개체의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하면, 자식 개체는 이미 소멸된 상태이기에 자식 개체의 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 호출되는게 아니라 부모 클래스의 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 호출됩니다. 

일반적인 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 호출시에는 자식 개체의 함수가 호출되는데, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서는 달라집니다. 이는 코드 분석에 어려움을 주고 잠재적인 코드 결함을 유발할 수 있으니, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서는 아예 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 않는게 좋습니다.(*[생성자에서 가상 함수 호출 금지](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%83%9D%EC%84%B1%EC%9E%90%EC%97%90%EC%84%9C-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EA%B8%88%EC%A7%80) 참고*)

```cpp
class BaseMemberObj {
public:
    ~BaseMemberObj() {std::cout << "4. BaseMemberObj::~BaseMemberObj()" << std::endl;}
};
class Base {
    BaseMemberObj m_BaseMemberObj;
public:
    virtual ~Base() {
        std::cout << "3. Base::~Base()" << std::endl;

        f(); // (X) 오동작. 가상 함수를 소멸자에서 호출합니다. Derived는 이미 소멸되었고, Base::f()가 호출됩니다.
    }
    virtual void f() {std::cout << "*** Base::f() ***" << std::endl;}
};
class DerivedMemberObj {
public:
    ~DerivedMemberObj() {std::cout << "2. DerivedMemberObj::~DerivedMemberObj()" << std::endl;}
};

class Derived : public Base {
    DerivedMemberObj m_DerivedMemberObj;
public:
    ~Derived() { std::cout << "1. Derived::~Derived()" << std::endl;}
    virtual void f() {std::cout << "*** Derived::f() ***" << std::endl;}
};

Derived d; // (X) 오동작. 소멸자에서 가상 함수 호출
```

실행 결과는 다음과 같습니다.

```cpp
1. Derived::~Derived() // 자식 개체의 소멸자가 먼저 호출됩니다. 
2. DerivedMemberObj::~DerivedMemberObj()
3. Base::~Base()
*** Base::f() *** // (X) 오동작. Base의 가상 함수가 호출됩니다.
4. BaseMemberObj::~BaseMemberObj()
```

# 소멸자에서 예외 방출 금지

[예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)에 따른 [스택 풀기](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)시에도 개체가 소멸되면서 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 호출됩니다. 이러한 상황에서 또다시 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, 정상적인 [스택 풀기](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)를 방해하므로 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서는 예외를 방출하지 않도록 해야 합니다. 
[소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 것 같으면 `Release()`와 같은 별도의 정리 함수를 만들어 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 호출전에 명시적으로 호출하는게 좋습니다.

```cpp
class T {
public:
    void Release() {} // 예외 발생할 수 있는 경우, 메모리나 리소스 해제
};     

T t;
t.Release(); // 소멸자 호출전에 명시적으로 메모리나 리소스 해제
```

> *(C++11~) [noexcept](https://tango1202.github.io/cpp/modern-cpp-noexcept/)가 추가되어 함수의 예외 방출 여부를 보증하며, [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)는 기본적으로 [noexcept](https://tango1202.github.io/cpp/modern-cpp-noexcept/)로 동작합니다.*


