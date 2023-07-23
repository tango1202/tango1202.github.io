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

```cpp
class Base // 기본 클래스, 슈퍼 클래스
{
};class Driven : public Base //  파생 클래스, 서브 클래스 기본 클래스로 부터 속성을 물려받는다. 다중 상속을 받을 수도 있다.
{
}; 
```


https://en.cppreference.com/w/cpp/language/derived_class
Derived 참고


https://en.cppreference.com/w/cpp/language/ebo
빈 부모 초기화


https://en.cppreference.com/w/cpp/language/access
접근 지정자 참고

# 기본 클래스에서의 범위 한정자

class Base
{private: 파생 클래스(X), 외부 클래스(X)
protected: 파생 클래스(O), 외부 클래스(X)
public: }; 파생 클래스(O), 외부 클래스(O)

# 파생클래스에서 접근 지정자

Driven Is a Base 관계
Base의 private -> Driven에서 사용 불가
Base의 protected -> Driven의 protected Base의 public -> Driven의 public

class Driven : public Base
{
};

Driven Use a Base 관계
Base의 private -> Driven에서 사용 불가
Base의 protected -> Driven의 protected Base의 public -> Driven의 protected

class Driven : protected Base
{
};

Driven Use a Base 관계
Base의 private -> Driven에서 사용 불가
Base의 protected -> Driven의 private Base의 public -> Driven의 private

class Driven : private Base
{
};

# 파생 클래스에서 생성자 재정의

class Base
{public: Base(int x, int y); };class Driven : public Base
{public: Driven(int x, int y, int z) : Base(x, y), m_z(z) {
}
};

1. 기본 클래스의 생성자 호출
2. 자신의 멤버 초기화 리스트

# virtual 상속

# 다이아몬드 상속

# 복사 손실

Driven d; Base e = d; // (x) 복사 손실. d에서 e에 해당하는 멤버만 복사된다.

# 가상 생성자

기본 클래스의 가상함수 오버라이딩. 파생클래스의 반환타입은 바뀔 수 있다.(단
리턴되는 타입이 상속관계에 있어야 한다.)
인자는 반드시 같아야 한다.

class Base { virtual Base* Clone() const = 0; };class Driven : public Base { virtual Driven* Clone() const { return new Driven(*this); // 복사 생성자 호출로 새로운 복제본 만듬
}
};Driven d; Base* b = d.Clone(); // (O) b는 d의 복제본을 가리키는 포인터
Base* b2 = b->Clone();// (O) b2는 d의 복제본을 가리키는 포인터

**부모 개체의 멤버 함수 호출**

`::`(범위 확인 연산자)를 이용하여 부모 개체의 가상 함수를 호출할 수 있습니다.

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
