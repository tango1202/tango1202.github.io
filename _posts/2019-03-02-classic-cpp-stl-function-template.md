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

템플릿 함수는 다음처럼 정의하고 인스턴스화 합니다.

```cpp
template <typename T>
T Plus(T left, T right) {
    return left + right;
}

EXPECT_TRUE(Plus<int>(10, 10) == 20);
EXPECT_TRUE(Plus<char>('a', 1) == 'b');
```

명시적인 타입을 생략하고 인수의 타입에 따라 추론이 가능합니다.

```cpp
EXPECT_TRUE(Plus(10, 10) == 20); // (O) 인수로부터 int가 추론됨
EXPECT_TRUE(Plus('a', 1) == 'b'); // (X) 인수가 int, char로 각각 다르므로 추론이 어려움 
EXPECT_TRUE(Plus('a', static_cast<char>(1)) == 'b'); // (O) 인수로부터 char 가 추론됨
```










# 명시적 인스턴스화

컴파일 시에 구체화(인스턴스화) 된다. 구체화되지 않은 템플릿 클래스는 컴파일되지 조차 않는다. 컴파일 오류는 구체화된 지점에서 발생한다.

String<char> v1; 
String<unsigned char> v2; 
String<unsigned int> v3;

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
