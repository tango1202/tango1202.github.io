---
layout: single
title: "#2. [고전 C++ STL] 함수 템플릿"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

템플릿 함수는  template 적용이 가능하다.(가상 함수 불가)

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



# 명시적 인스턴스화

컴파일 시에 구체화(인스턴스화) 된다. 구체화되지 않은 템플릿 클래스는 컴파일되지 조차 않는다. 컴파일 오류는 구체화된 지점에서 발생한다.

String<char> v1; String<unsigned char> v2; String<unsigned int> v3;

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
