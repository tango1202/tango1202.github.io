---
layout: single
title: "#4. [고전 C++ STL] 멤버 템플릿
"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

멤버 함수, 중첩 클래스로 템플릿 사용 가능

```cpp

#include <algorithm>
#include <iostream>
#include <string>
#include <vector>
 
struct Printer
{
    // generic functor
    std::ostream& os;
    Printer(std::ostream& os) : os(os) {}
    template<typename T>
    void operator()(const T& obj) { os << obj << ' '; } // member template
};
 
int main()
{
    std::vector<int> v{1,2,3};
    std::for_each(v.begin(), v.end(), Printer(std::cout));
    std::string s{"abc"};
    std::ranges::for_each(s, Printer(std::cout));
}

```

# 특수화 지원

```cpp
struct A
{
    template<class T> struct B;        // primary member template
    template<class T> struct B<T*> {}; // OK: partial specialization
//  template<> struct B<int*> {};      // OK via CWG 727: full specialization
};
template<> struct A::B<int*> {};       // OK
template<class T> struct A::B<T&> {};  // OK
```

# 선언과 정의 분리

바깥쪽 클래스 선언이 클래스 템플릿인 경우 멤버 템플릿이 클래스 본문 외부에 정의되면 두 개의 템플릿 매개 변수 집합이 필요한데, 하나는 바깥쪽 클래스에 대한 것이고 다른 하나는 바깥쪽 클래스에 대한 것입니다.

```cpp
template<typename T1>
struct string
{
    // member template function
    template<typename T2>
    int compare(const T2&);
    // constructors can be templates too
    template<typename T2>
    string(const std::basic_string<T2>& s) { /*...*/ }
};
// out of class definition of string<T1>::compare<T2> 
template<typename T1> // for the enclosing class template
template<typename T2> // for the member template
int string<T1>::compare(const T2& s) { /* ... */ }
```

# 소멸자, 복사생성자는 템플릿이 안됨

# 템플릿은 virtual 일 수 없음

# 동일 함수 오버로딩

```cpp
template<typename T>
struct A
{
    void f(int); // non-template member
 
    template<typename T2>
    void f(T2); // member template
};
 
//template member definition
template<typename T>
template<typename T2>
void A<T>::f(T2)
{
    // some code
}
 
int main()
{
    A<char> ac;
    ac.f('c'); // calls template function A<char>::f<char>(char)
    ac.f(1);   // calls non-template function A<char>::f(int)
    ac.f<>(1); // calls template function A<char>::f<int>(int)
}
```