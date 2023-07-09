---
layout: single
title: "#12. [고전 C++ 가이드] 가상 함수, 다형성"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
https://en.cppreference.com/w/cpp/language/virtual
가상함수

# 가상함수. 오버라이딩. 다형성(Polymorphism)

class Base
{public: virtual void Print() { // Base의 내용을 출력한다. }
};class Driven : public Base
{public: virtual void Print() { Base::Print(); // Base 내용 출
력
// Driven의 내용을 출력한다. }
};Base b; Driven d; Base* b1 = &b; Base* b2 = &d; b1->Print(); // Base출력
b2->Print(); // Driven출력
b2->Base::Print(); // Base출력

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

# 다형 소멸

class Base
{public: ~Base(); };class Driven : public Base
{public: ~Driven(); };Base* b1 = new Base; Base* b2 = new Driven; delete b1; // (O). Base 구성요소 삭제
delete b2; // (X). Base 구성요소만 삭제

Driven 구성요소가 삭제되게 하려면 소멸자를
virtual로 만들어야 한다. TPL 564 page
class Base
{public: virtual ~Base(); };

# 가상 함수 메카니즘

vtbl 포인터가 메모리에 추가됨(객체마다 추가) vtbl이 추가됨(클래스 레벨 추가) -> > 가상 함수 없는 상속이라면 추가되지 않음

# 런타임 타입 정보




