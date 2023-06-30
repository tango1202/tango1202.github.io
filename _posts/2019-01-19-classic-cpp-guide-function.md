---
layout: single
title: "#19. [고전 C++ 가이드] 함수"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
동일한 함수명인 경우 인자 타입에 따라 함수 호출. Type 정확도가 중요. 
• 형변환 규칙은 일반화 프로그래밍의 해법이 될 수도 있다
다형적인 가상함수에서 기본값을 다르게 하면 안된다
예외명세를 사용하지 마라.

함수는 다음 목적을 위해 만듭니다.

1. 함수 코드 재활용(코드 중복 제거) 
2. 함수 인자와의 **코딩 계약**
3. 디버깅 용이

함수 정의의 일반적인 형태는 하기와 같습니다.(`[]`인 부분은 옵션입니다.)

```cpp
return-type function-name(parameter-list) [const] [throw(exception-list)] {}
```

|항목|내용|
|--|--|
|`return-type`|자료형과 타입. 단, 배열은 안됨|
|`[const]`|멤버 함수인 경우 개체를 수정하지 않음([상수(const), 변경 가능 지정자(mutable), 최적화 제한 지정자(volatile)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable/) 참고)
|`[throw(exception-list)]`|함수가 발생하는 예외 명세.|

**ㅇㅖ외 명세 실체**
사실 throw 한다

**인자(매개변수, Parameter) 작성법**

**명명된 인자 선언**

**기본값을 사용한 인자**

1. 디폴트 인자가 주어지면, 그 뒤로는 다 디폴트 값이 있어야 함.

    // 단 줄임표는 가능
   int g(int n = 0, ...); // OK


2. 함수 결과를 기본값으로 사용하면, 런타임  호출 시점의 값을 반영함
```cpp
   int a = 1;
 
int f(int);
 
int g(int x = f(a)); // lookup for f finds ::f, lookup for a finds ::a
                     // the value of ::a, which is 1 at this point, is not used
 
void h()
{
    a = 2; // changes the value of ::a
    {
        int a = 3;
        g(); // calls f(2), then calls g() with the result
    }
}
```

3. 선언과 정의시 기본값 재정의 안됨.또는 변경

 컴파일 오류 확인 필요

4. 상속관계에서 기본값 재정의

    b.f(); // OK: calls Derived::f(7) 
    d.f(); // Error: no default 
    void f(int v1, int v2 = 10); // 기본값
class Base { virtual void f(int v1 = 20); };class Driven : public Base { virtual void f(int v1 = 30); };Driven d; Base* b = &d; b->f(); // 20일까 30일까? 20이다. // (vtable 때문) d.f(); // 20일까 30일까? 30이다.




5. 지역변수로 기본값 사용 안됨.


6. this 안됨
   void f(A* p = this) {} // error: this is not allowed

**이름이 없는 인자**

**인자 없음**

int f(void, int); and int f(const void); 은 오류

**가변 인자(...)**

Defined in header <cstdarg>
va_start 가변 인자 액세스 시작 매크로 함수
 
enables access to variadic function arguments
(function macro)
va_arg 가변인자 추출 매크로 함수
 
accesses the next variadic function argument
(function macro)

va_end 가변 인자 사용 종료 매크로 함수
 
ends traversal of the variadic function arguments
(function macro)
va_list 가변 인자에 대한 typedef
 
holds the information needed by va_start, va_arg, va_end, and va_copy
(typedef)
```cpp
#include <cstdarg>
 
int add_nums(int count, ...) 
{
    int result = 0;
    std::va_list args;
    va_start(args, count);
    for (int i = 0; i < count; ++i) {
        result += va_arg(args, int);
    }
    va_end(args);
    return result;
}
 
int main() 
{
    std::cout << add_nums(4, 25, 25, 50, 50) << '\n';
}
```
**함수 포인터**

void error(int code); void (*p)(int); // 함수 포인터
p = &error; p(10); // 함수 포인터를 이용하여 error 호출

*

**멤버함수 포인터**

클래스 멤버함수 호출
class Date { public: void Print(); };void f(Date* d) { d->Print(); } 

**멤버함수 포인터 사용하기**

class Date { public: void Print(); };
typedef void (Data::*Func)(); void f(Date* d) { Func* func = &Date::Print(); (d->*func)(); // 멤버함수 포인터를 통한 호출. 미친짓 같지만 라이브러리 구성하다보면 쓸때도 있다. }


**인자의 모호성은 피할것**

void f(double); void f(long);
f(1); // f(double(1)) 일까 f(long(1)) 일까?

**유효범위 우선**

void f(int); 
void g()
{ 
   void f(double); 
   f(1); // 유효범위 우선에 의해 // f(double) 이 호출됨
}
**오버로딩 매칭 규칙**

하기는 동일함수선언임.
T의 배열은 T의 포인터로 변경됨.

최상위 const는 매개변수 유형에서 생략됨

int f(char s[3]);
int f(char[]);
int f(char* s);
int f(char* const);
int f(char* volatile s);

** 인수 종속성**
ADL 또는 Koenig 조회 [1] 라고도 하는 인수 종속 조회

네임스페이스 **이름 탐색 규칙** 참고

https://neurowhai.tistory.com/196 참고


1. 타입 완전 일치
2. 경미한 형변환
3. 승격 일치(bool을 int로, char를 int로) 
4. 표준 변환(파생 클래스 포인터를 베이스 포인터로, T*를 void*로, int를 double로, double을 int로) 
5. 사용자 정의 형변환(형변환 연산
자 오버로딩)
C로부터 상속된 잔재. Type 정확도를 해친다. 의도하지 않는 형변환 발생.

1. 후보함수 세트 구축
2. 실행가능 함수 세트 구축
3. 암시적 변환을 포함하여 실행가능 함수 결정
   1. 완전일치
   2. 정수 변환, 부동 소수점 변환, 부동 정수 변환, 포인터 변환, 포인터-멤버 변환, 부울 변환, 파생 클래스를 기본 클래스로 사용자 정의 변환

**스트림 오버로딩**
Stream extraction and insertion
std::ostream& operator<<(std::ostream& os, const T& obj)
{
    // write obj to stream
    return os;
}
 
std::istream& operator>>(std::istream& is, T& obj)
{
    // read obj from stream
    if (/* T could not be constructed */)
        is.setstate(std::ios::failbit);
    return is;
}
