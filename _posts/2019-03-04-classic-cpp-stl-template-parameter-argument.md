---
layout: single
title: "#4. [고전 C++ STL] 템플릿 인자(Parameter)와 인수(Argument)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> 종속 타입인 경우 `typename`, 파싱 오류시 `template`을 작성하라.

> **모던 C++**
> * (C++11~) 파라메터 팩을 이용한 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)이 추가되어 코딩 자유도가 높아졌습니다.
> * (C++11~) [템플릿 오른쪽 꺽쇠 괄호](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%A5%B8%EC%AA%BD-%EA%BA%BD%EC%87%A0-%EA%B4%84%ED%98%B8) 파싱을 개선하여 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 `>`가 중첩되어 `>>`와 같이 되더라도 공백을 추가할 필요가 없습니다.
> * (C++17~) [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)처럼 타입을 생략할 수 있습니다.
> * (C++17~) 타입이 아닌 개체를 [템플릿 인자로 사용할때 auto를 사용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-auto-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)할 수 있습니다.
> * (C++20~) [템플릿 인자에 타입이 아닌 개체가 확장](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90%EC%97%90-%ED%83%80%EC%9E%85%EC%9D%B4-%EC%95%84%EB%8B%8C-%EA%B0%9C%EC%B2%B4-%ED%99%95%EC%9E%A5)되어 실수 타입과 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)을 사용할 수 있습니다.

# 템플릿 인자

클래스 템플릿 정의시 `<>` 사이에 [템플릿 인자(Parameter)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 집합을 정의하고, [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 전달한 인수(Argument) 집합으로 대체되어 클래스가 생성됩니다.

```cpp
// T, U : 인자(Parameter) 집합
template<typename T, typename U> 
class A {};

// int, char : 인자에 대응되는 인수(Argument) 집합
A<int, char> a; 
```

만약 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 `A`에서 사용하지 않는다면, 인자명을 생략할 수 있습니다.

```cpp
// 인자를 사용하지 않는다면, 인자명을 생략할 수 있습니다.
template<typename, typename> 
class A {};

// int, char : 인자에 대응되는 인수(Argument) 집합
A<int, char> a;   
```

[템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)는 다음처럼 타입, 타입이 아닌 템플릿 개체, 타입이 아닌 비 템플릿 개체, 템플릿 템플릿 인자로 작성할 수 있습니다.

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

2. 타입이 아닌 개체 : 템플릿 개체

    `T`에 종속적인 `T val`을 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 사용할 수 있습니다.

    ```cpp
    template<typename T, T val>
    class A {
    public:
        T f() {return val;} // val을 사용할 수 있습니다.
    };

    A<int, 10> a;
    EXPECT_TRUE(a.f() == 10);
    ```

3. 타입이 아닌 개체 : 비 템플릿 개체

    `int val`과 같이 `T`와 무관한 타입을 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 사용할 수 있습니다.

    `int`등의 정수 타입, [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 열거자, 포인터나 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)등을 사용할 수 있습니다.

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

    > *(C++17~) 타입이 아닌 개체를 [템플릿 인자로 사용할때 auto를 사용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-auto-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)할 수 있습니다.*<br/>
    > *(C++20~) [템플릿 인자에 타입이 아닌 개체가 확장](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90%EC%97%90-%ED%83%80%EC%9E%85%EC%9D%B4-%EC%95%84%EB%8B%8C-%EA%B0%9C%EC%B2%B4-%ED%99%95%EC%9E%A5)되어 실수 타입과 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)을 사용할 수 있습니다.*

4. 템플릿 템플릿 인자

    인스턴스화 되지 않은 템플릿을 인자로 사용할 수 있습니다.

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
    b.m_U2.m_Y = 20; // A<T*> 버전으로 생성됩니다.

    EXPECT_TRUE(b.m_U1.m_X == 10);
    EXPECT_TRUE(b.m_U2.m_Y == 20);
    ```

**불완전한 형식의 인스턴스화**

[전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)등 불완전한 형식이라도 인수로 지정할 수 있습니다.

```cpp
class A; // 전방 선언
typedef class {} B; // 이름없이 클래스를 typedef

template<typename T>
class C {}; 

C<A> x1;  // (O) A는 전방 선언만 한 클래스 개체
C<A*> x2; // (O)
C<B> x3;  // (O) B는 이름 없는 클래스의 typedef   
```

# 기본 템플릿 인자

함수의 [기본값 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90)와 마찬가지로 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)에 기본값을 줄 수 있으며, 기본값 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 사용하면, 그 뒤로는 다 기본값을 사용해야 합니다.

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

// 클래스 템플릿
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

// 함수 템플릿
template<typename T>
void f(B<T>::Type val) {} // (X) 컴파일 오류. B<T>::Type이 static 변수인지, B<T>에 종속된 타입인지 모릅니다. 
void f(typename B<T>::Type val) {} // (O)
```

# 템플릿 파싱 오류

**템플릿 인자 끝 `>` 과 대소 비교 `>`**  

[템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 집합 내에 대소 비교 `>`가 있다면, 컴파일러가 헷갈리므로 다음처럼 괄호를 사용해야 합니다.

```cpp
template<bool b = 3 > 4> // (X) 컴파일 오류. 
class A {};

template<bool b = (3 > 4)> // (O)
class B {};
```

**템플릿 인자 끝 `>` 중첩**

[템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 끝 `>`가 중첩되어 `>>`가 되면 비트 Right Shift 연산자로 파싱되어 컴파일 오류가 납니다. 따라서 `> >`와 같이 공백을 추가해야 합니다.

```cpp
template<typename T>
class A {};

std::vector<A<int>> a; // (X) 컴파일 오류. >> 는 비트 Right Shift 연산자로 파싱됩니다.
std::vector<A<int> > b; // (O) 공백을 추가해야 합니다.
```

> *(C++11~) [템플릿 오른쪽 꺽쇠 괄호](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%A5%B8%EC%AA%BD-%EA%BA%BD%EC%87%A0-%EA%B4%84%ED%98%B8) 파싱을 개선하여 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 `>`가 중첩되어 `>>`와 같이 되더라도 공백을 추가할 필요가 없습니다.*

**템플릿 명시**

템플릿 정의시 개체의 멤버 함수에 접근할 때 `template` 을 명시하는 경우가 있습니다.

다음 코드에서 `Run()` 함수는 `T`타입의 개체 `m_Obj`의 `f()`함수를 호출합니다. 공교롭게도 `f()` 함수는 템플릿 멤버 함수이고, 잘 동작합니다.

```cpp
template<typename T> 
class Runner {
    T m_Obj;
public:
    int Run() {
        return m_Obj.f(10); // 템플릿 정의에서는 함수 정의가 없어도 컴파일 됩니다.  
    }
};
    
class A {
public:
    template<typename U>
    int f(U val) {return 1;} // 멤버 함수 정의 입니다.
};
class B {
public:
    template<typename U>
    int f(U val) {return 2;} // 멤버 함수 정의 입니다.
};

Runner<A> a;
Runner<B> b;

EXPECT_TRUE(a.Run() == 1);
EXPECT_TRUE(b.Run() == 2); 
```

하지만, `f()`함수를 명시적으로 하기 위해 다음과 같이 작성한다면, 

```cpp
return m_Obj.f<int>(10);
```

`<`를 대소 비교 연산자로 파싱하여 컴파일 오류가 나옵니다. 이런 경우에는 다음처럼 `template` 을 강제로 작성해야 합니다.

```cpp
template<typename T> 
class Runner {
    T m_Obj;
public:
    int Run() {
        // (X) 컴파일 오류. 템플릿 멤버 함수를 명시적으로 호출하기 위해 <>을 사용하면, 
        // < 을 비교 연산으로 파싱해서 컴파일 오류가 납니다.
        // return m_Obj.f<int>(10);   

        // (O) < 가 템플릿으로 파싱되도록 template 키워드를 작성했습니다.
        return m_Obj.template f<int>(10);
    }
};
    
class A {
public:
    template<typename U>
    int f(U val) {return 1;} 
};
class B {
public:
    template<typename U>
    int f(U val) {return 2;}
};
```

# 템플릿 동등성

[템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)의 이름이 다르더라도 의미상으로 동일하면 동등한 템플릿입니다.

```cpp
template<typename T> // #1.
class A {};

template<typename U> // (X) 컴파일 오류. #1과 동등
class A {};
```
