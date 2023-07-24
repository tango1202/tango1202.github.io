---
layout: single
title: "#10. [고전 C++ 가이드] 상속"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

상속을 이용하면, [접근 지정자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90)에 따라 부모 개체에 정의된 멤버 변수, 멤버 함수등을 물려받아(상속받아) 자식 개체에서 사용할 수 있습니다. 

|항목|내용|
|--|--|
|`private`(클래스 기본값)|자기 자신만 사용|
|`protected`|상속받은 자식 개체에만 허용| 
|`public`(구조체 기본값)|외부 접근 허용|

```cpp
class Base {
private: 
    int m_Private;
protected:
    int m_Protected;
public:
    int m_Public;    
};
class Derived : public Base {
    void f() {
        m_Private; // (X) 컴파일 오류. private 접근 불가
        m_Protected; // (O) 상속한 개체에서는 사용 가능
        m_Public; // (O)
    }
};
Derived d;
d.m_Private; // (X) 컴파일 오류. private 접근 불가
d.m_Protected; // (X) 컴파일 오류. 외부 노출 불가. 상속한 개체내에서는 가능
d.m_Public; // (O)
```

부모 - 자식 관계는 다음처럼도 불립니다.

|항목|내용|
|--|--|
|부모|기반, 슈퍼, 상위, Base|
|자식|파생, 서브, 하위, Derived|

# public 상속, protected 상속, private 상속

상속받을때 접근 지정자를 명시하여, 부모 개체의 멤버를 노출할때 어떻게 노출할지 가시성을 조정할 수 있습니다.

|항목|부모의 `private`|부모의 `protected`|부모의 `public`|
|--|--|--|--|
|`private` 상속(기본값)|접근 불가|`private`|`private`|
|`protected` 상속|접근 불가|`protected`|`protected`|
|`public` 상속|접근 불가|`protected`|`public`|

```cpp
class Base {
private:
    int m_Private;
protected:
    int m_Protected;
public:
    int m_Public;
};

// Base의 protected, public 접근
// 외부 노출이 안되도록 Base protected->private, Base public->private
class PrivateDerived : private Base { 
    void f() {
        m_Private; // (X) 컴파일 오류. private 접근 불가
        m_Protected; // (O)
        m_Public; // (O)
    }
};
PrivateDeirved obj1;
obj1.m_Private; // (X) 컴파일 오류. private 접근 불가
obj1.m_Protected; // (X) 컴파일 오류. private 접근 불가
obj1.m_Public; // (X) 컴파일 오류. private 접근 불가

// Base의 protected, public 접근
// 외부 노출이 안되지만 상속은 되도록 Base protected->protected, Base public->protected
class ProtectedDerived : protected Base { 
    void f() {
        // m_Private; // (X) 컴파일 오류. private 접근 불가
        m_Protected; // (O)
        m_Public; // (O)
    }
};
class ProtectedDerived2 : public ProtectedDerived { 
    void g() {
        m_Private; // (X) 컴파일 오류. private 접근 불가
        m_Protected; // (O)
        m_Public; // (O)
    }
};

ProtectedDerived obj2;
obj2.m_Private; // (X) 컴파일 오류. private 접근 불가
obj2.m_Protected; // (X) 컴파일 오류. protected 접근 불가
obj2.m_Public; // (X) 컴파일 오류. protected 접근 불가

// Base의 protected, public 접근
// 외부 노출과 상속은 되도록 Base protected->protected, Base public->public
class PublicDerived : public Base { 
    void f() {
        // m_Private; // (X) 컴파일 오류. private 접근 불가
        m_Protected; // (O)
        m_Public; // (O)
    }
};

PublicDerived obj3;
obj3.m_Private; // (X) 컴파일 오류. private 접근 불가
obj3.m_Protected; // (X) 컴파일 오류. protected 접근 불가
obj3.m_Public; // (O) 
```

# has-a 관계

# is-a 관계 

상속에 있어서 `has-a`관계는 자식 개체가 부모 개체의 멤버를 가지고 있다(포함한다)고 생각 할 수 있습니다. 

쉽게 말하자면, 부모 개체의 기능을 상속받아 가지고 있다 로 

상속하는데 있어서 `is-a` 관계를 형성하는지, `has-a` 관계를 형성하는지에 따라 부모 클래스의 정의 방법이 다르며, 주의 사항도 다릅니다.

```cpp
class Base // 기본 클래스, 슈퍼 클래스
{
};class Driven : public Base //  파생 클래스, 서브 클래스 기본 클래스로 부터 속성을 물려받는다. 다중 상속을 받을 수도 있다.
{
}; 
```

# 빈 부모 최적화

**빈 클래스와 자식 개체의 크기** 에서 언급

텅빈 클래스는 1byte



# 파생 클래스에서 생성자 재정의

class Base
{public: Base(int x, int y); };class Driven : public Base
{public: Driven(int x, int y, int z) : Base(x, y), m_z(z) {
}
};

1. 기본 클래스의 생성자 호출
2. 자신의 멤버 초기화 리스트

# virtual 상속, 다이아몬드 상속

```cpp
struct B { int n; };
class X : public virtual B {};
class Y : virtual public B {};
class Z : public B {};
 
// every object of type AA has one X, one Y, one Z, and two B's:
// one that is the base of Z and one that is shared by X and Y
struct AA : X, Y, Z
{
    AA()
    {
        X::n = 1; // modifies the virtual B subobject's member
        Y::n = 2; // modifies the same virtual B subobject's member
        Z::n = 3; // modifies the non-virtual B subobject's member
 
        std::cout << X::n << Y::n << Z::n << '\n'; // prints 223
    }
};
```

class W
{
public:
    void f();
};
 
class A : private virtual W {};
 
class B : public virtual W {};
 
class C : public A, public B
{
    void f()
    {
        W::f(); // OK: W is accessible to C through B
    }
};



# 복사 손실

Driven d; Base e = d; // (x) 복사 손실. d에서 e에 해당하는 멤버만 복사된다.

# 오버로딩과 오버라이딩

오버로딩 : 인자가 다른 함수로 재정의
오버라이딩 : 동일 함수를 재정의

**부모 개체의 멤버 함수 호출**

`::`(범위 확인 연산자)를 이용하여 부모 개체의 함수를 호출할 수 있습니다.

```cpp
class Base {
public:
    int f() {return 10;} 
};

class Derived : public Base {
public:
    int f() {return 20;} 
};

Derived d;
Base* b = &d;

EXPECT_TRUE(b->f() == 10); 
EXPECT_TRUE(d.f() == 20);

EXPECT_TRUE(b->Base::f() == 10); // 부모 개체의 함수를 호출할 수 있습니다.
EXPECT_TRUE(d.Base::f() == 10); // 부모 개체의 함수를 호출할 수 있습니다.
```


# 부모 개체의 멤버 함수 오버로딩

함수 이름이 같으면 가림

struct B
{
    void f();
};
 
struct D : B
{
    void f(int); // D::f hides B::f (wrong parameter list)
};


