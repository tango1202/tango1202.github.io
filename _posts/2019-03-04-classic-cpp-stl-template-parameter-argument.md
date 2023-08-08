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

템플릿의 종속된 타입명은 표기 방법이 `static`개체 접근과 동일하여 컴파일러가 타입으로 인식하지 못합니다. 이러한 경우 `typename` 을 사용하여 종속된 이름임을 명시해 줍니다.

```cpp
template<typename T>
class A {};

template<typename T>
class B {
public:
    typedef T Type; // 종속 타입
    static int m_Static; // 정적 멤버 변수
};

// 템플릿 클래스
template<typename T>
class C : B<T> {
    B<T>::Type m_Val; // (X) 컴파일 오류. B<T>::Type이 static 변수인지, B<T>에 종속된 타입인지 모릅니다.
    typename B<T>::Type m_Val; // (O)

    void f() {
        B<T>::m_Static; // 정적 멤버 변수 접근
    } 
};

// 템플릿 템플릿 인자
template<template<typename> typename T> 
class D {
    T<int>::Type m_Val; // (X) 컴파일 오류. B<T>::Type이 static 변수인지, B<T>에 종속된 타입인지 모릅니다.
    typename T<int>::Type m_Val; // (O) 
    
};

// 템플릿 함수
template<typename T>
void f(B<T>::Type val) {} // (X) 컴파일 오류. B<T>::Type이 static 변수인지, B<T>에 종속된 타입인지 모릅니다. 
void f(typename B<T>::Type val) {} // (O)
```

유사하게, 템플릿 정의시 아직 인스턴스화 되지 않는 멤버에 접근할때 `template` 을 명시해야 합니다.

```cpp
template<typename T>
class A {
    template<typename U>
    void f() {}
};

template<typename T>
void g() {
    A<T> a;
    a.f<T>(); // (X) 아직 인스턴스화 되지 않아 함수 이름에 접근할 수 없습니다.
    a.template f<T>(); // (O) 템플릿임을 명시합니다.
}
```

# 템플릿 동등성

템플릿을 정의하면, 약간의 타입이 
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