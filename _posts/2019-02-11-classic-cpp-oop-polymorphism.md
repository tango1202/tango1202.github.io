---
layout: single
title: "#11. [고전 C++ 가이드] 다형성"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

추상화 : 공통성과 본질을 모아 추출
상속 : 기존의 클래스를 재활용하여 새로운 클래스를 작성
다형성 : 어떤 개체의 속성이나 기능이 상황에 따라 여러가지 형태로 가질 수 있는 성질
캡슐화 : 서로 연관있는 데이터와 기능을 하나의 캡슐로 만들어 데이터를 외부로 부터 보호하는것 



# 다형성

1. 함수 오버로딩(함수명이 동일하고 인자가 다름)
2. 상속
3. 가상함수 오버라이딩(함수명과 인자는 동일하며 자식 개체에서 재구현)
4. 가상함수 테이블
5. 다형 소멸, 가상 소멸
6. 가상 생성자
8. 순가상 함수
9. 일반화
10. dynamic_cast
11. 런타임 타입 정보
12. typeinfo

# 다형성(Polymorphism)

```cpp
class Base
{
    public: virtual void Print() { // Base의 내용을 출력한다. 
    }
};
class Driven : public Base
{
    public: virtual void Print() { Base::Print(); // Base 내용 출
력
// Driven의 내용을 출력한다. 

}
};
Base b; 
Driven d;
 Base* b1 = &b; 
 Base* b2 = &d; b1->Print(); // Base출력
b2->Print(); // Driven출력
b2->Base::Print(); // Base출력
```

virtual : 파생 클래스에서 재정의 할 수 있
다.


기본 클래스의 가상함수 오버라이딩. 파생클래스의 반환타입은 바뀔 수 있다.(단
리턴되는 타입이 상속관계에 있어야 한다.)
인자는 반드시 같아야 한다.

Base 포인터로부터 실행되었지만 Driven 함수가 호출된다. virtual이 없었다면 Base::Print 가 실행되
었을 것이다. b2->Base::Print(); 로 하여 강제로 Base 의 Print 함수를 부를 수도 있다.

다형성
사용하는 객체의 종류를 모른채 동작할 수
있다.

# 가상 생성자

기본 클래스의 가상함수 오버라이딩. 파생클래스의 반환타입은 바뀔 수 있다.(단
리턴되는 타입이 상속관계에 있어야 한다.)
인자는 반드시 같아야 한다.

```cpp
class Base { 
    virtual Base* Clone() const = 0; 
};
class Driven :
 public Base { 
    virtual Driven* Clone() const { 
        return new Driven(*this); // 복사 생성자 호출로 새로운 복제본 만듬
}
};
Driven d; Base* b = d.Clone(); // (O) b는 d의 복제본을 가리키는 포인터
Base* b2 = b->Clone();// (O) b2는 d의 복제본을 가리키는 포인터
```
# 다형 소멸, public Virtual 소멸자

다형성을 가진 기본 클래스는 반드시 소멸자를 virtual 로 만들어라.

```cpp
class Base
{
    public: ~Base(); 
};
class Driven : public Base
{
    public: ~Driven(); 
};
Base* b1 = new Base; 
Base* b2 = new Driven; 
delete b1; // (O). Base 구성요소 소멸
delete b2; // (X). Base 구성요소만 소멸
```

Driven 구성요소가 소멸되게 하려면 소멸자를
virtual로 만들어야 한다. TPL 564 page
```cpp
class Base
{public: virtual ~Base(); };
```

# 런타임 타입 정보

# type_info




