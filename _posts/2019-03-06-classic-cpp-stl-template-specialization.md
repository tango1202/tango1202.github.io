---
layout: single
title: "#6. [고전 C++ STL] 템플릿 특수화, 부분 특수화"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---



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

# 부분 탬플릿 특수화

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
