---
layout: single
title: "9. [고전 C++ 가이드] 상속"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 부모 개체의 멤버 함수를 오버로딩 하지 마라. [오버로딩 함수 탐색 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9-%ED%95%A8%EC%88%98-%ED%83%90%EC%83%89-%EA%B7%9C%EC%B9%99)에서 제외된다.
> * 자식 개체를 부모 개체에 대입하지 마라. 아무런 오류 없이 복사 손실 된다.
> * 구현 코드가 없는 [단위 전략 인터페이스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/)인 경우에만 다중 상속하라.
> * 소멸자에서 가이드한 것과 같이,
> > * 다형 소멸이 필요하면 부모 개체에 `virtual` 소멸자를 사용하라.(`virtual` 소멸자가 아니면 메모리 릭이 발생한다.)
> > * `public` Non-Virtual 소멸자인 개체는 상속하지 마라.
> > * `is-a`관계에서는 `public` Virtual 소멸자를 사용하라.(`virtual` 소멸자가 아니면 메모리 릭이 발생한다.)
> > * `has-a`관계에서는 `protected` Non-Virtual 소멸자를 사용하라.

# 개요

상속이란, 기존의 개체를 재활용하여, 새로운 클래스를 만드는 프로그래밍 기법입니다.

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

상속받을때 접근 지정자를 명시하여, 부모 개체의 멤버를 외부에 노출할때 어떻게 노출할지 가시성을 조정할 수 있습니다.

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

# 부모 개체 멤버 접근

일반적으로 부모 개체의 멤버는 그냥 접근할 수 있으나, 이름이 동일한 경우 `::`(범위 확인 연산자)를 이용하여 명시적으로 부모 개체의 멤버에 접근할 수 있습니다.

```cpp
class Base {
public:
    int f() {return 10;} 
};

class Derived : public Base {
public:
    int f() {return 20;} // (△) 비권장. 부모 개체의 이름을 가립니다.
};

Derived d;
Base* b = &d;

EXPECT_TRUE(b->f() == 10);  // (△) 비권장. Base 개체를 이용하면 Base::f()가 호출됨 
EXPECT_TRUE(d.f() == 20);

EXPECT_TRUE(b->Base::f() == 10); // 부모 개체에 명시적으로 접근합니다.
EXPECT_TRUE(d.Base::f() == 10); // 부모 개체에 명시적으로 접근합니다.
```

# 부모 개체의 멤버 함수 오버로딩

부모 개체의 멤버 함수를 오버로딩(함수명이 동일하고 인자가 다름)하면 부모 개체의 것을 가려 오버로딩 함수의 후보군에 포함되지 않습니다.([오버로딩 함수 탐색 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9-%ED%95%A8%EC%88%98-%ED%83%90%EC%83%89-%EA%B7%9C%EC%B9%99) 참고) 

```cpp
class Base {
public:
    int f() {return 10;} 
};

class Derived : public Base {
public:
    int f(int) {return 20;} // (△) 비권장. 부모 개체의 이름을 가립니다.
};

Derived d;
Base* b = &d;

EXPECT_TRUE(b->f() == 10);  // (△) 비권장. Base 개체를 이용하면 Base::f()가 호출됨  
EXPECT_TRUE(d.f() == 10); // (X) 컴파일 오류. 오버로딩 함수 탐색 규칙에서 제외됨
EXPECT_TRUE(d.f(1) == 20);

EXPECT_TRUE(b->Base::f() == 10);
EXPECT_TRUE(d.Base::f() == 10); 
```

# 부모 개체의 가상 함수 오버라이딩

부모 개체의 가상 함수를 오버라이딩(함수명과 인자는 동일하며 자식 개체에서 재구현)하여 다형적으로 동작하게 할 수 있습니다. 단, 인자는 동일해야 하며, 리턴값은 부모 개체의 것과 같거나 상속 관계(공변, covariant)이면 됩니다.([가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 참고)

```cpp
class Base {
public:
    virtual int f() {return 10;} 
};

class Derived : public Base {
public:
    virtual int f() {return 20;} // (O) 다형적으로 동작합니다.
};
Derived d;
Base* b = &d;

EXPECT_TRUE(b->f() == 20); // (O) 자식 개체의 함수가 호출됩니다.
EXPECT_TRUE(b->Base::f() == 10); // (△) 비권장. 부모 개체에 명시적으로 접근하면, 부모 개체의 함수가 호출됩니다.
EXPECT_TRUE(d.f() == 20);     
```

# 자식 개체의 생성자 재정의

자식 개체에서 생성자를 재정의하면, 명시적으로 부모 클래스의 생성자를 호출해야 합니다.

```cpp
class Base { 
    int m_X;
    int m_Y;
public: 
    Base(int x, int y) :
        m_X(x),
        m_Y(y) {}  
};
class Derived : public Base {
    int m_Z;
public: 
    Derived(int x, int y, int z) : 
        Base(x, y), // 부모 클래스 생성자 호출 
        m_Z(z) {}
};
```

# 복사 손실

안타깝게도, 자식 개체를 부모 개체에 대입하면, 아무런 컴파일 경고 없이 실행됩니다. 다음 코드에서는 `Base`의 멤버 변수인 `m_X`, `m_Y`가 대입되고, `m_Z`는 손실됩니다.(반대의 경우는 컴파일 오류가 발생합니다.)

```cpp
class Base { 
    int m_X;
    int m_Y;
public: 
    Base(int x, int y) :
        m_X(x),
        m_Y(y) {}  
};
class Derived : public Base {
    int m_Z;
public: 
    Derived(int x, int y, int z) : 
        Base(x, y), 
        m_Z(z) {}
};

Derived d(1, 2, 3);
Base b = d; // (X) 오동작. 아무런 경고없이 실행됩니다. 
d = b; // (X) 컴파일 오류
```

# 다중 상속

자식 개체는 다양한 부모로부터 상속받을 수 있습니다. 하지만, 구체 구현된 클래스를 다중 상속하면, 이름 충돌의 우려가 있기 때문에 잘 사용하지 않습니다. [단위 전략 인터페이스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/)인 경우에만 다중 상속을 사용하시기 바랍니다.

다음 코드를 보면, `Idol`은 `Singer`와 `Dancer`를 모두 상속받아 내부적으로 `m_Age`를 2벌 가지고 있습니다.

```cpp
class Singer {
public:
    int m_Age; // 동일한 멤버 변수
};
class Dancer {
public:
    int m_Age; // 동일한 멤버 변수
};
class Idol : 
    public Singer,
    public Dancer {
};

Idol obj;
obj.m_Age = 10; // (X) 컴파일 오류. Singer::m_Age와 Dancer::m_Age가 모호합니다.
obj.Singer::m_Age = 20; // (△) 비권장. 이름이 동일한 멤버는 ::(범위 확인 연산자)를 이용해 접근 가능합니다.
obj.Dancer::m_Age = 30;

EXPECT_TRUE(obj.Singer::m_Age == 20);
EXPECT_TRUE(obj.Dancer::m_Age == 30);       
```

# 다이아몬드 상속과 virtual 상속

다중 상속은 다이아몬드 상속이 만들어질 우려가 있습니다.

```cpp
class Person {
public:
    int m_Age; 
};
class Singer : public Person {};
class Dancer : public Person {};
class Idol : 
    public Singer, // (△) 비권장. Singer::Person::m_Age 존재
    public Dancer { // (△) 비권장. Dancer::Person::m_Age 존재
};

Idol obj;
obj.Singer::m_Age = 20; 
obj.Dancer::m_Age = 30;

EXPECT_TRUE(obj.Singer::m_Age == 20);
EXPECT_TRUE(obj.Dancer::m_Age == 30);   
```

상기 상속 구조는 다음 그림과 같습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0a2fb9e6-aa46-4e8a-84ed-f95ce473ef31)

`Idol`의 부모인 `Singer`와 `Dancer`가 모두 `Person`을 상속받아, `m_Age`가 2벌이나 되는군요. 이러한 문제를 해결하기 위해 `virtual` 상속을 하면, 상속 트리에서 이름이 같은 조상 개체를 1개로 통합시켜 줍니다.

```cpp
class Person {
public:
    int m_Age; 
};
class Singer : public virtual Person {}; // 상속 트리에서 이름이 같은 조상 개체는 1개로 통합시킵니다.
class Dancer : public virtual Person {};
class Idol : 
    public Singer, 
    public Dancer { 
};

Idol obj;
obj.m_Age = 10;
obj.Singer::m_Age = 20; // obj.m_Age와 동일
obj.Dancer::m_Age = 30; // obj.m_Age와 동일

EXPECT_TRUE(obj.m_Age == 30);
EXPECT_TRUE(obj.Singer::m_Age == 30);
EXPECT_TRUE(obj.Dancer::m_Age == 30);  
```

# 상속 제한

부모 개체로 사용하지 않을 것이라면, [public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 정의합니다. 다만, 아무런 **코딩 계약**이 되어 있지 않아, 상속할 수도 있습니다.([public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고)

강제로 상속을 제한하려면, 생성자를 `private`로 만들면 되는데요, 이 경우 해당 클래스를 생성하려면 `Create()` 함수를 별도로 만들어야 합니다.([생성자 접근 차단 - private 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A0%91%EA%B7%BC-%EC%B0%A8%EB%8B%A8---private-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)

```cpp
class T {
private:
    T() {} // 상속 및 외부에서는 접근 불가
public:
    static T Create() {return T();} // 내부 static 함수로 생성
    static T* CreatePtr() {return new T;}
};
class U : public T {};
// U u; // (X) 컴파일 오류. 상속해서 생성할 수 없음
// U* p = new u; // (X) 컴파일 오류  

// T t; // (X) 컴파일 오류 
T t(T::Create()); // (O) T를 복사 생성    
T* p = T::CreatePtr(); // (O) T의 포인터 생성
delete p;  
```

프로그램 내에서 대부분의 개체는 상속하지 않고 사용하므로, 조금은 심한 **코딩 계약**이 될 수도 있고, 나중에 상속할 수 있는 개체로 리팩토링 했을때 기존에 사용한 `Create()`함수들을 모두 수정해야 하기 때문에 리팩토링 부담이 있어 권장하지는 않습니다. 

그냥 [public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)라면, 상속하지 말라는 뜻이니, 절대 상속하지 마세요. 혹시나 상속이 필요하다면, 그때 [public Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)나, [protected Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 리팩토링 하시기 바랍니다.


# is-a 관계

`is-a` 관계는 자식 개체를 부모 개체처럼 사용하려고 할때의 상속 관계입니다. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b5052bc8-51a8-4c40-90e0-80766f1c75dc)

상기와 같은 상속 구조를 가지고 부모 개체인 `Shape` 의 포인터로 `Rectangle`, `Ellipse`, `Triangle`을 관리하고 소멸시키는 관계 입니다.

```cpp
class Shape {
public:
    virtual ~Shape() {} // 다형 소멸을 하려면 public virtual 소멸자를 사용합니다.
    virtual void Draw() const {}
};
class Rectangle : public Shape {
public:
    virtual void Draw() const {}
};
class Ellipse : public Shape {
public:
    virtual void Draw() const {}
};
class Triangle : public Shape {
public:
    virtual void Draw() const {}
};

Shape* shapes[3] = { // 도형들을 Shape* 로 관리합니다.
    new Rectangle(), 
    new Ellipse(), 
    new Triangle()
};

for(int i = 0; i < 3; ++i) {
    shapes[i]->Draw(); // 다형적으로 그립니다.
}
for(int i = 0; i < 3; ++i) {
    delete shapes[i]; // 다형 소멸 합니다. Shape*로 Rectangle, Ellipse, Triangle을 소멸합니다.
}   
```

물려받은 기능은 주로 외부에 그대로 노출되므로, `public` 상속을 받습니다. 

부모 개체의 소멸자는 [public Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 만듭니다.

1. 외부에서 부모 개체 포인터로 소멸시키기 때문에 `public` 소멸자로 정의하고,
2. [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 해야 하므로 `virtual` 소멸자로 만듭니다. 만약 `virtual`로 하지 않으면, 자식 개체의 소멸자가 호출되지 않아 메모리 릭이 발생할 수 있습니다.

# has-a 관계

`has-a` 관계는 [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)은 하지 않고, 자식 개체가 부모 개체의 멤버 변수, 멤버 함수등을 물려받아 포함하고 있는 상속 관계입니다. 주로 코드 구현을 공통화 하여 상속받을때나 단위 전략 인터페이스를 상속받을때 사용합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7652b59f-0344-4183-9d31-4087fe9c3c1c)

물려받은 기능을 

1. 외부에 노출할 필요가 없다면 `private` 상속,
2. 다시 상속할 수 있게 한다면 `protected` 상속,
3. 외부에서 사용하게 하고 싶다면, `public` 상속

을 합니다.

부모 개체의 소멸자는 [protected Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 만듭니다.

1. 부모 개체를 상속받아서만 사용하도록 함과 동시에 부모 개체 포인터로부터 [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 시도하지 못하도록 `protected` 소멸자로 정의하고,
2. [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 안하므로 Non-Virtual 소멸자로 만듭니다.

**공통 코드 구현**

여러 개체의 공통 구현을 부모 개체로 만들어 `has-a` 관계를 맺을때 사용할 수 있습니다.

다음 코드는 `Rectangle`과 `Ellipse`에서 공통으로 사용하는 너비/높이 처리를 `ResizeableImpl`에 공통 구현하고, `Rectangle`과 `Ellipse`에서 `has-a`관계로 상속한 예입니다.

```cpp
// 너비/높이에 대한 공통 구현
class ResizeableImpl {
private:
    int m_Width;
    int m_Height;    
protected:
    ResizeableImpl(int w, int h) :
        m_Width(w), 
        m_Height(h) {}
    ~ResizeableImpl() {} // has-a 관계로 사용되기 때문에, 단독으로 생성되지 않도록 protected입니다.
public:    
    // Get/Set 함수
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetWidth(int val) {m_Width = val;}
    void SetHeight(int val) {m_Height = val;}
};
class Rectangle : public ResizeableImpl {
    int m_Left;
    int m_Top;
public:
    Rectangle(int l, int t, int w, int h) :
        ResizeableImpl(w, h),
        m_Left(l),
        m_Top(t) {}
};
class Ellipse : public ResizeableImpl {
    int m_CenterX;
    int m_CenterY;
public:
    Ellipse(int centerX, int centerY, int w, int h) :
        ResizeableImpl(w, h),
        m_CenterX(centerX),
        m_CenterY(centerY) {}
};
Rectangle r(0, 0, 10, 20);
Ellipse e(5, 10, 10, 20);
```

**단위 전략 인터페이스**

단위 전략 인터페이스는 개체의 기능 스펙이라 할 수 있습니다. 작은 단위의 기능 스펙을 순가상 함수로 제공하며, [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 지원하지 않습니다.

```cpp
class IEatable {
protected:
    ~IEatable() {} //  상속할 수 있지만, 다형적으로 사용하지 않아 non-virtual 입니다.

public:
    virtual void Eat() = 0;
};
class IWalkable {
protected:
    ~IWalkable() {} // 상속할 수 있지만, 다형적으로 사용하지 않아 non-virtual 입니다.

public:
    virtual void Walk() = 0;
};

class Dog :
    public IEatable,
    public IWalkable {
public:        
    virtual void Eat() {}
    virtual void Walk() {}
};
IEatable eatable; // (X) 컴파일 오류. 소멸자가 protected
IWalkable walkable; // (X) 컴파일 오류. 소멸자가 protected
Dog dog; // (O)

IEatable* p = &dog:
delete* p; // (X) 컴파일 오류. IEatable의 소멸자가 protected
```