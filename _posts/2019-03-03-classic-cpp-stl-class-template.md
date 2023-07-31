---
layout: single
title: "#3. [고전 C++ STL] 클래스 템플릿"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

클래스 템플릿 인스턴스화
클래스 템플릿 자체는 형식, 개체 또는 다른 엔터티가 아닙니다. 템플릿 정의만 포함된 소스 파일에서 코드가 생성되지 않습니다. 어떤 코드가 나타나기 위해서는, 템플릿이 인스턴스화되어야 합니다: 컴파일러가 실제 클래스(또는 함수 템플릿에서 함수)를 생성할 수 있도록 템플릿 인수가 제공되어야 합니다.

```cpp
namespace N 
{
    template<class T> 
    class Y // template definition
    { 
        void mf() {} 
    }; 
}
 
// template class Y<int>; // error: class template Y not visible in the global namespace
using N::Y;
// template class Y<int>; // error: explicit instantiation outside 
                          // of the namespace of the template
template class N::Y<char*>;       // OK: explicit instantiation
template void N::Y<double>::mf(); // OK: explicit instantiation
```

**암시적 인스턴스화**

코드가 완전히 정의된 형식이 필요한 컨텍스트의 템플릿을 참조하거나 형식의 완전성이 코드에 영향을 주고 이 특정 형식이 명시적으로 인스턴스화되지 않은 경우 암시적 인스턴스화가 발생합니다. 예를 들어, 이 형식의 개체가 생성되지만 이 형식에 대한 포인터가 생성되는 경우는 그렇지 않습니다.

이것은 클래스 템플릿의 멤버에 적용됩니다: 멤버가 프로그램에서 사용되지 않는 한, 멤버는 인스턴스화되지 않으며 정의가 필요하지 않습니다.

```cpp
template<class T> 
struct Z // template definition
{
    void f() {}
    void g(); // never defined
}; 
 
template struct Z<double>; // explicit instantiation of Z<double>
Z<int> a;                  // implicit instantiation of Z<int>
Z<char>* p;                // nothing is instantiated here
 
p->f(); // implicit instantiation of Z<char> and Z<char>::f() occurs here.
        // Z<char>::g() is never needed and never instantiated:
        // it does not have to be defined
```

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




