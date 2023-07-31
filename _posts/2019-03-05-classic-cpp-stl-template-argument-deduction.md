---
layout: single
title: "#5. [고전 C++ STL] 템플릿 인수 추론"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


# 템플릿 인자

* 클래스(구조체), 정수 계열 타입, 포인터 타입, 
* 기본값이 있는 클래스(구조체) 타입
  


**배열 및 함수 형식**

개체 포인터와 함수 포인터로 대체됨

# 인자 이름은 선택사항임

```cpp
// Declarations of the templates shown above:
template<class>
class My_vector;
template<class = void>
struct My_op_functor;
template<typename...>
class My_tuple;
```

# 템플릿 템플릿 인자

```cpp
template<typename T>
class my_array {};
 
// two type template parameters and one template template parameter:
template<typename K, typename V, template<typename> typename C = my_array>
class Map
{
    C<K> key;
    C<V> value;
};
```

# 템플릿 인수 추론

템플릿을 인스턴스화하려면 모든 템플릿 매개 변수(형식, 비형식 또는 템플릿)를 해당 템플릿 인수로 바꿔야 합니다. 

클래스 템플릿의 경우 인수는 명시적으로 제공되거나, 이니셜라이저에서 추론 되거나(C++17부터) 기본값으로 제공됩니다. 

함수 템플릿의 경우 인수는 명시적으로 제공되거나, 컨텍스트에서 추론되거나, 기본값으로 설정됩니다.

```cpp
template<typename T>
void f(T s)
{
    std::cout << s << '\n';
}
 
template void f<double>(double); // instantiates f<double>(double)
template void f<>(char);         // instantiates f<char>(char), template argument deduced
template void f(int);            // instantiates f<int>(int), template argument deduced
```
# 인수가 type-id와 표현식 둘 다로 해석될 수 있는 경우, 해당 템플리트 매개변수가 type이 아닌 경우에도 항상 type-id로 해석됩니다.

```cpp
template<class T>
void f(); // #1
 
template<int I>
void f(); // #2
 
void g()
{
    f<int()>(); // "int()" is both a type and an expression,
                // calls #1 because it is interpreted as a type
}
```

# 템플릿 인수 명시적 인스턴스화 

불완전한 형식 지정

```cpp
template<typename T>
class X {}; // class template
 
struct A;            // incomplete type
typedef struct {} B; // type alias to an unnamed type
 
int main()
{
    X<A> x1;  // OK: 'A' names a type
    X<A*> x2; // OK: 'A*' names a type
    X<B> x3;  // OK: 'B' names a type
}
```

# 암시적 인스턴스화


```cpp
#include <iostream>
 
template<typename T>
void f(T s)
{
    std::cout << s << '\n';
}
 
int main()
{
    f<double>(1); // instantiates and calls f<double>(double)
    f<>('a');     // instantiates and calls f<char>(char)
    f(7);         // instantiates and calls f<int>(int)
    void (*pf)(std::string) = f; // instantiates f<string>(string)
    pf("∇");                     // calls f<string>(string)
}
```




# 템플릿 템플릿 인수

```cpp
template<typename T> // primary template
class A { int x; };
 
template<typename T> // partial specialization
class A<T*> { long x; };
 
// class template with a template template parameter V
template<template<typename> class V>
class C
{
    V<int> y;  // uses the primary template
    V<int*> z; // uses the partial specialization
};
 
C<A> c; // c.y.x has type int, c.z.x has type long
```

# 기본 템플릿 인수

template<typename T1, typename T2 = int> class A;
template<typename T1 = int, typename T2> class A;
 
// the above is the same as the following:
template<typename T1 = int, typename T2 = int> class A;

# > 

형식이 아닌 템플릿 매개 변수에 대한 기본 템플릿 인수를 구문 분석할 때 첫 번째 중첩되지 않은 인수는 보다 큼 연산자가 아닌 템플릿 매개 변수 목록의 끝으로 사용됩니다. >

```cpp
template<int i = 3 > 4>   // syntax error
class X { /* ... */ };
 
template<int i = (3 > 4)> // OK
class Y { /* ... */ };
```

# 멤버 엑세스 지정은 컴파일 타임에 확인됨

```cpp
class B {};
 
template<typename T>
class C
{
protected:
    typedef T TT;
};
 
template<typename U, typename V = typename U::TT>
class D: public U {};
 
D<C<B>>* d; // error: C::TT is protected
```

#템플릿 동등성

정수 계열 또는 열거형 형식이며 해당 값은 동일합니다

또는 포인터 유형이며 동일한 포인터 값을 갖습니다.

또는 포인터-멤버 형식이고 동일한 클래스 멤버를 참조하거나 둘 다 null 멤버 포인터 값입니다

또는 lvalue 참조 유형이며 동일한 객체 또는 함수를 참조합니다


# 함수 template 오버로딩

```cpp
template <typename T>
T sqrt(T);
tempate <tylename T>
complex<T> sqrt(complex<T>); double sqrt(double); 
void g(complex<double> z) { 
    sqrt(2); // sqrt<int>(2); 
    sqrt(2.0); // sqrt(double); 
    sqrt(z); // sqrt<double>(complex<T>); 
}
```

**오버로딩 스탭**
1. 먼저 다음의 2개의 특수화된 템플릿 함수 후
보 생성
sqrt<complex<double>>(complex<double>) sqrt<double>(complex<double>) 2. 더 특수화된 버전 선택
T sqrt(T) 보다 complex<T> >
sqrt(complex<T>) 가 더 특수화 되었다. 3. 보통 함수 sqrt와 함수 오버로딩 규칙 적용. 단, 특수화된 템플릿 함수는 승격/표준변환/사용
자정의 변환을 적용하지 않음
4. 보통함수와 템플릿 특수화 버전만 남으면 보
통함수 선택

```cpp
template <typename T> 
T max(T, T);
 max(1, 2); // max<int>(1, 2) 
 max('a', 'b'); // max<char>('a', 'b')
  max(2.7, 4.9); // max<double>(2.7, 4.9) 
  
  const int s = 0;
   max(s, 10); // (X) max<int>((int)s, 10) 
   max('a', 10); // (X) 모호성 에러
```

**해법**

명시적으로 쓰거나
max<int>('a', 10); 오버로딩
inline int max(int i, int j) { return max<int>(i, j); }

