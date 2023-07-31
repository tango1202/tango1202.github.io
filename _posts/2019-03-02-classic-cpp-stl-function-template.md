---
layout: single
title: "#2. [고전 C++ STL] 함수 템플릿"
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

컴파일 시에 구체화(인스턴스화) 된다. 구체화되지 않은 템플릿 클래스는 컴파일되지 조차 않는다. 컴파일 오류는 구체화된 지점에서 발생한다.

String<char> v1; String<unsigned char> v2; String<unsigned int> v3;


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

