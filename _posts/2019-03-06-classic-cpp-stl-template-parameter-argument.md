---
layout: single
title: "#6. [고전 C++ STL] 템플릿 인자(Parameter)와 인수(Argument)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


# 템플릿 인자

* 클래스(구조체), 정수 계열 타입, 포인터 타입, 
* 기본값이 있는 클래스(구조체) 타입

# 기본 템플릿 인수

template<typename T1, typename T2 = int> class A;
template<typename T1 = int, typename T2> class A;
 
// the above is the same as the following:
template<typename T1 = int, typename T2 = int> class A;

  
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

# 배열 및 함수 형식

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

# 종속 타입
    template<typename T>
    void f(Other<T>, typename Another<T>::Type) {} // typename : 템플릿 정의 내에서 종속된 타입임을 컴파일러에게 Hint 전달 안그러면 static 변수 참조와 헷갈림.


```cpp
template<typename T>
struct X : B<T> // "B<T>" is dependent on T
{
    typename T::A* pa; // "T::A" is dependent on T
                       // (see below for the meaning of this use of "typename")
 
    void f(B<T>* pb)
    {
        static int i = B<T>::i; // "B<T>::i" is dependent on T
        pb->j++; // "pb->j" is dependent on T
    }
};
```

늦은 바인딩 수행.

```cpp
struct A // A has a nested variable X and a nested type struct X
{
    struct X {};
    int X;
};
 
struct B
{
    struct X {}; // B has a nested type struct X
};
 
template<class T>
void f(T t)
{
    typename T::X x;
}
 
void foo()
{
    A a;
    B b;
    f(b); // OK: instantiates f<B>, T::X refers to B::X
    f(a); // error: cannot instantiate f<A>:
          // because qualified name lookup for A::X finds the data member
}
```

```cpp
#include <iostream>
#include <vector>
 
int p = 1;
 
template<typename T>
void foo(const std::vector<T> &v)
{
    // std::vector<T>::const_iterator is a dependent name,
    typename std::vector<T>::const_iterator it = v.begin();
 
    // without 'typename', the following is parsed as multiplication
    // of the type-dependent member variable 'const_iterator'
    // and some variable 'p'. Since there is a global 'p' visible
    // at this point, this template definition compiles.
    std::vector<T>::const_iterator* p;
 
    typedef typename std::vector<T>::const_iterator iter_t;
    iter_t * p2; // iter_t is a dependent name, but it's known to be a type name
}
 
template<typename T>
struct S
{
    typedef int value_t; // member of current instantiation
 
    void f()
    {
        S<T>::value_t n{}; // S<T> is dependent, but 'typename' not needed
        std::cout << n << '\n';
    }
};
 
int main()
{
    std::vector<int> v;
    foo(v); // template instantiation fails: there is no member variable
            // called 'const_iterator' in the type std::vector<int>
    S<int>().f();
}

```

# 종속 이름의 템플릿 명확성

마찬가지로, 템플릿 정의에서 현재 인스턴스화의 멤버가 아닌 종속 이름은 명확성 키워드가 아닌 한 템플릿 이름으로 간주되지 않습니다 템플렛 가 사용되거나 템플릿 이름으로 이미 설정되지 않은 경우:

```cpp
template<typename T>
struct S
{
    template<typename U>
    void foo() {}
};
 
template<typename T>
void bar()
{
    S<T> s;
    s.foo<T>();          // error: < parsed as less than operator
    s.template foo<T>(); // OK
}
```



