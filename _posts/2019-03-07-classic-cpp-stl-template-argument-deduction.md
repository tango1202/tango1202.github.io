---
layout: single
title: "#6. [고전 C++ STL] 템플릿 오버로딩 결정과 인수 추론"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

템플릿을 인스턴스화하려면 모든 템플릿 매개 변수(형식, 비형식 또는 템플릿)를 해당 템플릿 인수로 바꿔야 합니다. 

클래스 템플릿의 경우 인수는 명시적으로 제공되거나, 이니셜라이저에서 추론 되거나(C++17부터) 기본값으로 제공됩니다. 


더 특수화된 버전을 사용함. 적은 타입을 허용함


# 템플릿 동등성

정수 계열 또는 열거형 형식이며 해당 값은 동일합니다

또는 포인터 유형이며 동일한 포인터 값을 갖습니다.

또는 포인터-멤버 형식이고 동일한 클래스 멤버를 참조하거나 둘 다 null 멤버 포인터 값입니다

또는 lvalue 참조 유형이며 동일한 객체 또는 함수를 참조합니다

# 최상위 const는 제거됨

```cpp
template<class T>
void f(T t);
 
template<class X>
void g(const X x);
 
template<class Z>
void h(Z z, Z* zp);
 
// two different functions with the same type, but 
// within the function, t has different cv qualifications
f<int>(1);       // function type is void(int), t is int
f<const int>(1); // function type is void(int), t is const int
 
// two different functions with the same type and the same x
// (pointers to these two functions are not equal,
//  and function-local statics would have different addresses)
g<int>(1);       // function type is void(int), x is const int
g<const int>(1); // function type is void(int), x is const int
 
// only top-level cv-qualifiers are dropped:
h<const int>(1, NULL); // function type is void(int, const int*) 
                       // z is const int, zp is const int*
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




# 함수 template 오버로딩

```cpp
template <typename T>
T sqrt(T);

template <tylename T>
complex<T> sqrt(complex<T>);

double sqrt(double); 

void g(complex<double> z) { 
    sqrt(2); // sqrt<int>(2); 
    sqrt(2.0); // sqrt(double); 
    sqrt(z); // sqrt<double>(complex<T>); (?????)
}
```

**오버로딩 스탭**
1. 먼저 다음의 2개의 특수화된 템플릿 함수 후보 생성
    ```cpp   
    sqrt<complex<double>>(complex<double>) 
    sqrt<double>(complex<double>) 
    ```
2. 더 특수화된 버전 선택
    T sqrt(T) 보다 sqrt<complex<T>> sqrt(complex<T>) 가 더 특수화 되었다. 
3. 보통 함수 sqrt와 함수 오버로딩 규칙 적용. 단, 특수화된 템플릿 함수는 승격/표준변환/사용
사용자 정의 변환을 적용하지 않음
4. 보통함수와 템플릿 특수화 버전만 남으면 보통함수 선택

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

# partial ordering of overloaded function templates를 통해 가장 일치하는 항목 선택

```cpp
template<class X>
void f(X a);
template<class X>
void f(X* a);
 
int* p;
f(p);
```

# 더 특수화 된 버전 선택

