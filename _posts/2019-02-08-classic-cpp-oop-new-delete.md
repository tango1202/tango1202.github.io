---
layout: single
title: "#8. [고전 C++ 가이드] 개체 생성과 소멸"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


https://en.cppreference.com/w/cpp/language/data_members
멤버 초기화

`operator new`와 `operator delete`

delete p; // p가 NULL이어도 안전

위치 지정 생성((Placement New))
T* p = new(buf) T; // 명시적 delete 호출

지역 객체 : { } 안에서 유효

void f() {
{
int a; // (O) a = 10; // (O) }a


heap 객체 : new 시 생성, delete 시 소멸

T* p = new T; delete p; // (O) delete p; // (X) 두번 죽일 순 없다.

new - delete 쌍
new[] - delete [] 쌍(delete를 하면 첫번째 것만 소멸됨)

new 는 bad_alloc 예외를 던짐
T* p = new T;
if (!p) {} // 예외를 던진다는데 왜 널검사를 하니? 널검사 하고 싶으면 다음처럼... T* p = new (std::nothrow) T; // 실패하면 0 리턴


클래스 멤버 : 초기화 리스트 시 생성, 소멸자에서 소멸



class Date {
int d, m, y; public: Date(int dd, int mm, int yy) : d(dd), m(mm), y(yy) {
}~Date() {}
}

지역 정적 객체 : 최초 함수 호출시 생성. 소멸시점은 정의되지 않음

void f() { static int s = 1; // 최초 함수 호출시 생성
s++; }

비지역 객체(전역개체, 클래스의 정적 개체) : main 호출시 생성, main 종료시 소멸

class Date { static int today; };

// cpp 어딘가에 정의되야 한다. int Date::today = 20; 

dll 의 비지역 객체는 dll이 링크될
때 까지 생성되지 않는다. 복잡하므로 지역 정적 개체를 사용
하라.

위치 지정 new, 명시적 소멸자 호출(재정의 방법은 EC++ 8장 참고)

void * operator new(size_t, void* p) {return p}; void* buf = malloc(sizeof(X)); // X 클래스만큼 할당
X* p = new(buf)X; // buf에 X 객체를 만든다. p->~X(); // 명시적 소멸자 호출
free(buf); // 할당된 

임시 객체 : 명시적으로 작성되지 않은 객체. 이름없는 객체. 표현식이 종료되면 소멸

인자 전달시 임시객체가 생성되지 않도록 하는 것이 좋다. 암시적인 형변환이 되지는 않는지 검토할것. 명시적인 것 보다는 임시 객체를 사용하는 편이 좋다. 언젠간 컴파일러가 최적화 해줄수도 있다.
int i = f(); // 명시적으로 객체 i에 저장
g(i); 보다는
g(f()); // 언젠간 컴파일러가 최적화 할꺼다.

RVO(Return Value Optimization, MEC++ 20항목, 1996년 ISO 제정)

T f() { T t; return t;} void g() {T t = f();} 에서 T 객체는 f()에서 1회, 리턴하면서 임시객
체 1회 g() 함수에서 1회, 3회 생성되고 대입이
1회 이루어진다.

T f() {return T();} // 임시객체로 생성
void g() {T t = f();} 에서 f() 에서 생성한 T 객체를 g() 함수에서
생성한 T객체에 만들어줌
