---
layout: single
title: "#2. [고전 C++ STL] template"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 일반화

타입과 무관하게 클래스를 일반화한다.

```cpp
template<typename T> // T는 대충 타입명이다. 
class String {
    String(const T& arg); 
};
```
# 인스턴스화

컴파일 시에 구체화(인
스턴스화) 된다. 구체화되지 않은 템플
릿 클래스는 컴파일되
지 조차 않는다. 컴파일 오류는 구체화
된 지점에서 발생한다.

String<char> v1; String<unsigned char> v2; String<unsigned int> v3;

# template 인자 - 타입

```cpp
template <typename T, typename G>
class String {};

String<char, int> s;
```

# template 인자 - 인수

```cpp
template <typename T, T val>
class String {};
String<char, 'a'> s;
```

# 비 template 인자

```cpp
template <typename T, int val>
class String {};

String<char, 100> s;

String<char, 100> s1; String<char, 101> s2; s2 = s1; // (X) 타입이 다르다.
```

# 함수 탬플릿

함수에도 template 적용이 가능하다.(가상 함수 불가)

```cpp
template <typename T>
T plus(T left, T right);

int a = plus<int>(10, 10);
```

인자의 타입에 따라 추론이 가능하다.
int a = plus(10, 10);


추론이 불가능하면 명시적으로 써라.

```cpp
template <typename T, typename U>
T cast(U u) {return u;} 
void g(int i) { double j = cast(i); // (X) T를 추론못함
double j = cast<double>(i); // (O) U는 인자로 부터 추론할 수 있다. 
char j = cast<char, double>(i); // (O) 아주 명시적이다. 
char* j = cast<char*, int>(i); // (X) T는 char*, U 는 int 로 추론할 수 있지만
// int를 char*로 캐스팅 할 수 없다.
```

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

**tamplate 특수화**

특정 타입에 대한 버전 재정의(클래스, 함수 모두 가능)

```cpp
template<typename T>
class MyClass
{
};
template<>
class MyClass<int> // int 형에 대해 특수
화
{
};
```

```cpp
template<typename T>
void MyFunc(T arg) {}
template<>
void MyFunc<int>(int arg) {}
혹은, 추론 가능한 int를 아예 빼서
template<>
void MyFunc(int arg) {}
```

#부분 탬플릿 특수화

```cpp
template<typename T>
class MyClass
{
};
template<typename T>
class MyClass<T*> // 모든 포인터에 대해 특수화
{
};
```

순서 : 더 특수화된 템플릿에 우선 순위가 있다. 
1. template<> class MyClass<int>
2. template<typename T> class MyClass<T*>
3. template<typename T> class MyClass
