---
layout: single
title: "#4. [고전 C++ STL] 템플릿 인자(Parameter)와 인수(Argument)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 템플릿 인자

템플릿 클래스 정의시 `<>` 사이에 인자(Parameter) 집합을 정의하고, 템플릿 인스턴스화시 전달한 인수(Argument) 집합으로 대체되어 클래스가 생성됩니다.

```cpp
// T, U : 인자(Parameter) 집합
template<typename T, typename U> 
class A {};

// int, char : 인자에 대응되는 인수(Argument) 집합
A<int, char> a; 
```

템플릿 인자는 다음처럼 타입, 템플릿 개체, 비 템플릿 개체, 템플릿 템플릿 인자로 작성할 수 있습니다.

1. 타입
   
    ```cpp
    template<typename T, typename U>
    class A {
    public:
        T f(U param) {return param;}
    };

    A<int, char> a;
    EXPECT_TRUE(a.f('a') == static_cast<int>('a'));
    ```

2. 템플릿 개체

    ```cpp
    template<typename T, T val>
    class A {
    public:
        T f() {return val;} // val을 사용할 수 있습니다.
    };

    A<int, 10> a;
    EXPECT_TRUE(a.f() == 10);
    ```

3. 비 템플릿 개체

    ```cpp
    template<typename T, int val>
    class A {
    public:
        T f() {return val;}
    };

    A<int, 10> a;
    EXPECT_TRUE(a.f() == 10);

    A<int, 11> b;
    a = b; // (X) 컴파일 오류. A<int, 10> 은 A<int, 11> 과 타입이 다릅니다.
    ```

4. 템플릿 템플릿 인자

    ```cpp
    template<typename T> 
    class A {
    public:
        int m_X;
    };

    template<typename T> 
    class A<T*> { // A의 템플릿 특수화 버전
    public: 
        long m_Y;
    };

    // U : 템플릿 템플릿 인자. 인스턴스화된 타입이 아닌 템플릿을 전달함
    template<template<typename> typename U>
    class B {
    public:
        U<int> m_U1; // 템플릿인 U로 부터 개체 생성
        U<int*> m_U2;
    };

    B<A> b; 
    b.m_U1.m_X = 10;
    b.m_U2.m_Y = 20;

    EXPECT_TRUE(b.m_U1.m_X == 10);
    EXPECT_TRUE(b.m_U1.m_X == 20);
    ```

# 불완전한 형식의 인스턴스화 

전방 선언등 불완전한 형식이라도 타입이라면 인수로 지정할 수 있습니다.

```cpp
class A; // 전방 선언
typedef class {} B; // 이름없이 클래스를 typedef

template<typename T>
class C {}; 

C<A> x1;  // (O) A는 전방선언만 한 클래스 개체
C<A*> x2; // (O)
C<B> x3;  // (O) B는 이름 없는 클래스의 typedef   
```

**템플릿 인자 끝 `>` 과 대소비교 `>`**  

`>`는 템플릿 인자 구문의 끝이나, 인자내에 대소 비교로 작성된다면, 다음처럼 괄호를 사용해야 합니다.

```cpp
template<bool b = 3 > 4> // (X) 컴파일 오류. 
class A {};

template<bool b = (3 > 4)> // (O)
class B {};
```

# 기본 템플릿 인자

함수의 [기본값 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90)와 마찬가지로 인자에 기본값을 줄 수 있으며, 기본값 인자를 사용하면, 그 뒤로는 다 기본값을 사용해야 합니다.

```cpp
template<typename T = char, typename U = int>
class A {};

A<> a; // T == char, U == int
A<char> b; // U == int
A<char, char> c;
```


# 종속 타입




```cpp
template<typename T>
class A {};

template<typename T>
class B {
public:
    typedef T Type;
};

    template<typename T>
    void f(Other<T>, typename Another<T>::Type) {} // typename : 템플릿 정의 내에서 종속된 타입임을 컴파일러에게 Hint 전달 안그러면 static 변수 참조와 헷갈림.
```

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



# 템플릿 동등성

정수 계열 또는 열거형 형식이며 해당 값은 동일합니다

또는 포인터 유형이며 동일한 포인터 값을 갖습니다.

또는 포인터-멤버 형식이고 동일한 클래스 멤버를 참조하거나 둘 다 null 멤버 포인터 값입니다

또는 lvalue 참조 유형이며 동일한 객체 또는 함수를 참조합니다


템플릿 인수 동등성
템플릿 인수 동등성은 두 템플릿 ID가 동일한지 여부를 판별하는 데 사용됩니다.

두 값은 동일한 유형이고

정수 계열 또는 열거형 형식이며 해당 값은 동일합니다
또는 포인터 유형이며 동일한 포인터 값을 갖습니다.
또는 포인터-멤버 형식이고 동일한 클래스 멤버를 참조하거나 둘 다 null 멤버 포인터 값입니다
또는 lvalue 참조 유형이며 동일한 객체 또는 함수를 참조합니다

```cpp
// equivalent
template<int I>
void f(A<I>, A<I+10>); // overload #1
template<int I>
void f(A<I>, A<I+10>); // redeclaration of overload #1
 
// not equivalent
template<int I>
void f(A<I>, A<I+10>); // overload #1
template<int I>
void f(A<I>, A<I+11>); // overload #2
 
// functionally-equivalent but not equivalent
// This program is ill-formed, no diagnostic required
template<int I>
void f(A<I>, A<I+10>);      // overload #1
template<int I>
void f(A<I>, A<I+1+2+3+4>); // functionally equivalent

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