---
layout: single
title: "#5. [고전 C++ STL] 함수 템플릿 인수 추론과 오버로딩 결정 규칙"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)시에는 `T`보다는 `T*` 보다는 `const T*`가 선택된다.
> * [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)은 비멤버 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)으로 작성하라.
> * [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)시 `T&`는 `T`로 추론된다.

> **모던 C++**
> * (C++17~) [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)처럼 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 타입을 생략할 수 있습니다.
> * (C++17~) [클래스 템플릿 인수 추론 사용자 정의 가이드](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EA%B0%80%EC%9D%B4%EB%93%9C)가 추가되어 [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)시 컴파일러에게 가이드를 줄 수 있습니다.
> * (C++20~) [컨셉(concept)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항(requires)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 추가되어 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 줄 수 있습니다.

# 개요

[함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)의 [템플릿 인스턴스화](??)는 다음 2가지 과정을 거칩니다.

1. [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) : 전달된 타입을 어떤 타입으로 사용할지 추론
2. [함수 템플릿 오버로딩 결정](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) : [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 후 일반 함수와 여러개의 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)중 적합한 것을 결정

# 함수 템플릿 인수 추론

`<>`내의 인수가 생략됐거나 `<>`가 아예없는 경우 경우 누락된 인수를 추론합니다.

```cpp
template<typename T, typename U>
T f(T, U) {return 2;}

f<int>(0, (double)0); // int f(int, double)
f<double>(0, (int)0); // double f(double, int)
f(0, 0); // int f(int, int)
```

값 타입과 포인터 타입은 타입 그대로 추론하지만 다른 타입들은 약간의 변형을 합니다.


1. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 제거
    
    [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거하고 추론합니다.(*[참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD) 참고*) 단 명시적으로 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 작성하면 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 사용할 수 있습니다.

    ```cpp
    template<typename T, typename U, typename V>
    void f(T, U, V) {}

    int val = 0;
    int* ptr = NULL;
    int& ref = val;

    f(val, ptr, ref); // f<int, int*, int>(int, int*, int). 참조자가 제거됩니다.

    f<int, int*, int&>(val, ptr, ref); // f<int, int*, int&>(int, int*, int&). 명시적으로 지정하면 참조자로 사용할 수 있습니다.
    ````

2. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 포인터 붕괴

    [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 포인터로 추론합니다.

    ```cpp
    template<typename T>
    void f(T) {}

    int arr[3];
    // Argument : int[3] -> int*로 조정 
    // T : int* 
    // Parameter : int*
    f(arr); // f<int*>(int*)
    ```
    단, [배열의 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%B0%B8%EC%A1%B0%EC%9E%90-%EC%82%AC%EC%9A%A9%EB%B2%95)는 변환하지 않고 그대로 사용됩니다. 

    따라서 다음처럼 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)을 이용하여 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)를 구할 수 있습니다.

    ```cpp
    template<typename T, size_t N>
    std::size_t f(T(&arr)[N]) { // 배열에 대한 참조
        return N;
    }
    int arr[3];
    EXPECT_TRUE(sizeof(arr) == 3 * sizeof(int)); // 배열의 전체 용량
    
    // Argument : int[3] 
    // T : int(&)[3] 
    // Parameter : int(&)[3]    
    EXPECT_TRUE(f(arr) == 3); // 배열 요소 갯수
    ```

3. [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 변경

    함수는 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)로 추론합니다.

    ```cpp
    template<typename T>
    void f(T) {}

    void Func(int) {}

    // Argument : void (*)(int) 
    // T : void (*)(int) 
    // Parameter : void (*)(int)
    f(Func); // // f<void (*)(int)>(void (*)(int))
    ```

4. [최상위 const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0) 무시

    [최상위 const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)는 무시됩니다.(*[오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고*)

    ```cpp
    template<typename T>
    void f(T) {}

    const int a = 0;
    // Argument : const int -> int로 조정 
    // T : int 
    // Parameter : int
    f(a); // f<int>(int)
    ```
5. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 축약

    [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 될 수 있도록 변환합니다.

    ```cpp
    template<typename T>
    void f(const T&) {}

    int a = 0;
    int& b = a;

    // Argument : int 
    // T : int 
    // Parameter : const int&
    f(a); // f<int>(const int&)

    // Argument : int& 
    // T : int 
    // Parameter : const int&
    f(b); // f<int>(const int&)
    ```

6. 포인터 축약

    포인터의 포인터가 중첩되어 만들어지지 않도록(*[다차원 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)가 안되도록*) 변환합니다.

    ```cpp
    template<typename T>
    void f(const T*) {}

    int a = 0;
    int* b = &a;

    // Argument : int* 
    // T : int 
    // Parameter : const int*
    f(&a); // f<int>(const int*)
    f(b); // f<int>(const int*)
    ```

7. Up casting

    자식 개체는 부모 개체로 조정될 수 있습니다.

    ```cpp
    template<typename T>
    class Base {};

    template<typename T>
    class Derived : public Base<T> {};

    template<typename T>
    void f(Base<T>*) {}

    Derived<int> d;
    // Argument : Derived<int>* -> Base<int>* 로 조정 
    // T : int 
    // Parameter : Base<int>*
    f(&d); // f<int>(Base<int>*)
    ```

8. 다른 [인수의 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)

    다른 [인수의 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)으로 부터 추론될 수 있습니다.

    ```cpp
    template<typename T>
    class Other {};

    template<typename T>
    class Another {
    public:
        typedef T Type;
    };

    template<typename T>
    void f(Other<T>, typename Another<T>::Type) {} // typename : 템플릿 정의 내에서 종속된 타입임

    Other<int> other;
    // Argument1 : Other<int> 
    // T : int 
    // Parameter1 : Other<int>
    // Argument2 : int -> Argument1에서 T가 int로 추론되어 Another<int>::Type 확인 
    // Parameter2 : int
    f(other, 10); // f<int>(Other<int>, int)
    ```

[템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 타입이 정확히 일치하지 않으면 추론할 수 없습니다. 따라서 하기에서 `A<10>`에서 `10`은 `int` 타입이기 때문에 `short`를 사용하는 `f()` 함수로 전달할 수 없습니다. 

다만, `f<10>(a)`와 같이 명시적으로 호출하면, `short`에 `10`을 전달하고,`short` 타입 `10`으로 만들어진 `A<10>`을 사용하기 때문에,  `int` 타입 `10`으로 만들어진 `A<10>`을 전달할 수 있습니다.

```cpp
template<int i> 
class A {};

template<short s>
void f(A<s> a) {}

A<10> a;
// Argument :A<10>에서 10은 int 타입임
// Parameter : 템플릿 인자가 A<short>이어서 A<10>을 전달받을 수 없음.
f(a); // (X) 컴파일 오류. A<int>와 A<short>는 서로 다른 타입이기에 암시적 변환할 수 없음

// Argument :int 타입 10으로 만들어진 A<10>
// T : short 타입의 10
// Parameter : short 타입 10으로 만들어진 A<10>
f<10>(a); // 명시적으로 10을 전달하여 A<10> 타입이 됨. 같은 타입이어서 함수 호출됨   
```
> *(C++17~) [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)처럼 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 타입을 생략할 수 있습니다.*<br/>
> *(C++17~) [클래스 템플릿 인수 추론 사용자 정의 가이드](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EA%B0%80%EC%9D%B4%EB%93%9C)가 추가되어 [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)시 컴파일러에게 가이드를 줄 수 있습니다.*

# 함수 템플릿 오버로딩 결정 규칙

서로 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 다른 함수들은 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보군에서 가장 적합한 것으로 결정됩니다.(*[오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고*)

하지만 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)의 경우는 정의시에는 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 다르지만, [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 과정에서 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 같아질 수 있습니다. 이런 경우 좀더 특수화된 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전(*좀 더 특수화된 버전은 좀 더 적은 타입을 허용한다고 생각하시면 됩니다.*)을 선택하게 됩니다.

예를 들면,

```cpp
template<typename T> 
int f(T) {return 1;} // #1.

template<typename T> 
int f(T*) {return 2;} // #2. 함수 템플릿 오버로딩. 

int a;
// #1 T == int* 이면 int f(int*)
// #2 T == int 이면 int f(int*)
EXPECT_TRUE(f(&a) == 2); 
```

상기 코드는 다음 단계에 따라 특수화된 버전을 선택합니다.

1. [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전들로 부터 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 생성

    ```cpp
    int f(int*) // #1. 로부터 생성. T == int* 인 경우
    int f(int*) // #2. 로부터 생성. T == int 인 경우
    ```

2. 더 특수화된 버전 선택 - **Partial Ordering**
    
    `f(T)`와 `f(T*)`에서 포인터만 처리하는(*좀 더 적은 타입을 허용하는*) #2 선택

3. [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)이 아닌 일반 함수와 [오버로딩 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 적용. 이때 일반 함수가 우선 순위가 높음

# 함수 템플릿 오버로딩 사례

1. 함수 템플릿보다는 일반 함수

    [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF) 보다는 일반 함수를 선호합니다.

    ```cpp
    int f(int) {return 1;} // #1.

    template<typename T>
    T f(T) {return 2;}  // #2.

    // #1 : f(int)
    // #2 : T == int 이면 f(int)
    EXPECT_TRUE(f(1) == 1); // 일반 버전을 선택합니다.
    ```

2. `T` 보다는 `T*` 보다는 `const T*

    `T` 보다는 `T*` 가 특수화된 버전이므로 선택됩니다.

    ```cpp
    template<typename T>
    int f(T a) {return 1;} // #1.

    template<typename T>
    int f(T* a) {return 2;} // #2.

    int* p;
    // #1 : T == int* 이면 f(int*) 
    // #2 : T == int 이면 f(int*)
    EXPECT_TRUE(f(p) == 2); // 특수화된 버전으로 선택합니다.
    ```

    `T*` 보다는 `const T*` 가 특수화된 버전이므로 선택됩니다.

    ```cpp
    template<typename T>
    int f(T a) {return 1;} // #1.

    template<typename T>
    int f(T* a) {return 2;} // #2.

    template<typename T>
    int f(const T* a) {return 3;} // #3.

    const int* p;
    // #1 : T == const int* 이면 f(const int*) 
    // #2 : T == const int 이면 f(const int*) 
    // #3 : T == int 이면 f(const int*) 
    EXPECT_TRUE(f(p) == 3); // 더 특수화된 버전으로 선택합니다.
    ```

3. [기본값 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90) 무시

    특수화된 버전 선택시 [기본값 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90)는 무시하고 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전을 결정합니다.

    ```cpp
    template<typename T>
    int f(T) {return 1;} // #1.

    template<typename T>
    int f(T*, int = 1) {return 2;} // #2.

    int* p;
    // #1 : T == int* 이면 f(int*) 인자 1개 - 인자가 1개라고 무조건 선택되지 않습니다. 인자 2개인 #2에서 기본값 인자를 뺀 버전과 경합합니다.
    // #2 : T == int 이면 f(int*, int = 1) 인자 2개 -> 기본값이 있는 인자를 빼면 f(int*)
    EXPECT_TRUE(f(p) == 2); // 더 특수화된 버전으로 선택합니다.
    ```

4. 특수화된 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)

    [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/) 타입을 이용하여 좀 더 적합하게 특수화(*좀 더 적은 타입을 허용*) 되었다면, 해당 [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전을 선택합니다.

    ```cpp
    template<typename T>
    class A {};

    template<typename T>
    int f(T&) {return 1;} // #1.

    template<typename T>
    int f(A<T>&) {return 2;} // #2.   

    A<int> a;
    // #1 : T == A<int> 이면 f(A<int>&) 
    // #2 : T == int 이면 f(A<int>&)
    EXPECT_TRUE(f(a) == 2); // 더 특수화된 버전으로 선택합니다.
    ```

    다음 경우에는 `A<T, U>` 보다는 `A<T, T>` 가 더 특수화 되어, 선택됩니다.

    ```cpp
    template<typename T, typename U>
    class A {};

    template<typename T, typename U>
    int f(T, A<T, U>* p = 0) {return 1;} // #1.

    template<typename T>
    int f(T, A<T, T>* p = 0) {return 2;} // #2. 

    // #1 : T == int, U == char 이면 f(int, A<int, char>*) 
    // #2 : T == int 이면 f(int, A<int, int>*)
    EXPECT_TRUE(f(0, (A<int, char>*)0) == 1); // 인자가 더 잘 맞는 버전으로 선택합니다.

    // #1 : T == int, U == int 이면 f(int, A<int, int>*) 
    // #2 : T == int 이면 f(int, A<int, int>*) 
    EXPECT_TRUE(f(0, (A<int, int>*)0) == 2); // 더 특수화된 버전으로 선택합니다.    
    EXPECT_TRUE(f(0) == 2); 
    ```     

5. [연산자를 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)한 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)

    일반적으로 [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 시에는 멤버 버전과 비멤버 버전중 멤버 버전이 선택됩니다.

    ```cpp
    template<typename T>
    class A {
    public:
        int operator +(int) const {return 1;} // #1.
    };

    template<typename T>
    int operator +(const T&, int) {return 2;} // #2.    

    A<int> a;
    // #1 : T == A<int> 이면 operator +(int)
    // #2 : T == A<int> 이면 operator +(A<int>&, int) 
    EXPECT_TRUE( a + 10 == 1); // 멤버 함수 버전이 호출됩니다.
    ```

    하지만 멤버 버전이 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이라면, 비멤버 버전으로 임시 생성하고 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 평가하기 때문에, 모호성 문제가 발생하며, 그냥 대충 비멤버 버전을 호출합니다.(*경고가 나오긴 합니다.*) 

    ```cpp
    template<typename T>
    class A {
    public:
        template<typename U>
        int operator +(U) const {return 1;} // #1. 멤버 버전도 함수 템플릿입니다.
    };

    template<typename T, typename U>
    int operator +(const T&, U) {return 2;} // #2.   

    A<int> a;

    // #1 : T == A<int>, U == int 이면 operator +(int)
    //      비멤버 버전으로 변환하면     operator +(const A<int>&, int)             
    // #2 : T == A<int>, U == int 이면 operator +(const A<int>&, int) 
    EXPECT_TRUE( a + 10 == 2); // (X) 컴파일 경고. #1 과 #2 가 모호하고, 대충 비멤버 버전을 호출합니다.
    ```

    따라서 [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)으로 구현해야 한다면, 아예 비멤버 버전으로 작성하는게 좋습니다.

    ```cpp
    template<typename T>
    class A {
    };

    template<typename T, typename U>
    int operator +(const A<T>&, U) {return 1;} // #1. 비멤버 버전

    template<typename T, typename U>
    int operator +(const T&, U) {return 2;} // #2.   

    A<int> a;

    // #1 : T == int, U == int 이면 operator +(A<int>&, int)
    // #2 : T == A<int>, U == int 이면 operator +(A<int>&, int) 
    EXPECT_TRUE( a + 10 == 1); // (O) 더 특수화된 버전으로 선택합니다.
    ```

# 함수 템플릿 오버로딩 실패 사례

1. 동등한 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)

    동등한 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)이면 모호하여 컴파일 오류가 발생합니다. 다음의 경우는 `T`와 `const T`로 다릅니다만, [최상위 const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)는 제거되어 결국 동등해집니다.

    ```cpp
    template<typename T>
    int f(T a) {return 1;} // #1

    template<typename T>
    int f(const T a) {return 2;} // #2. (X) 컴파일 오류. 재정의됨. 최상위 const는 제거되어 #1인 f(T a)와 동일합니다.
    ```

2. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 인한 모호

    포인터의 경우 `T`보다는 `T*`가 특수화된 버전이므로, 선택됩니다만, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 붙이거나 뗄 수 있습니다. 다음 코드에서는 #1, #2 모두 `f(int&)` 버전을 제공할 수 있기 때문에 모호성 오류가 발생합니다.

    ```cpp
    template<typename T>
    int f(T) {return 1;}  // #1.

    template<typename T>
    int f(T&) {return 2;} // #2.

    int a = 0;
    int& b = a;

    // #1 : T == int 이면 f(int), 
    //      T == int& 이면 f(int&) 
    // #2 : T == int 이면 f(int&)
    EXPECT_TRUE(f(a) == 1); // (X) 컴파일 오류. 어느것을 호출할지 모호합니다.
    EXPECT_TRUE(f(b) == 2); // (X) 컴파일 오류. 어느것을 호출할지 모호합니다.
    ```

3. 특수화 판단 모호

    어느것이 더 특수화 되었는지 판단하기 어려울때 컴파일 오류가 발생합니다. 다음 코드에서는 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전인 #1, #2 모두 `f(int, int*)` 버전을 제공할 수 있고, 어느것이 더 특수화 되었는지 판단하기 어려워 모호성 오류가 발생합니다.

    ```cpp
    template<typename T>
    int f(T, T*) {return 1;}   // #1.

    template<typename T>
    int f(T, int*) {return 2;} // #2.

    char ch;
    int i;
    int* p;

    // #1 : T == char 이면 f(char, char*)
    // #2 : T == char 이면 f(char, int*) 
     EXPECT_TRUE(f(ch, p) == 2);  // (O) 인자 타입이 일치하는 #2를 선택합니다.

    // #1 : T == int 이면 f(int, int*)
    // #2 : T == int 이면 f(int, int*) 
    EXPECT_TRUE(f(i, p) == 2); // (X) 컴파일 오류. 어느것을 호출할지 모호합니다.
    ```

# SFINAE(Substitution failure is not an error)

[SFINAE](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)는 ***[템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 과정에서 발생할 수 있는 대체 실패는 컴파일 오류가 아니다***라는 뜻입니다. 즉, 대체 실패가 발생하면 그냥 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보군에서 제외할 뿐 오류를 발생시키지 않습니다.

다음 `A`클래스를 보면, `Int`와 `Char`라는 [종속 타입](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)을 가지고 있고, `f()` 함수는 이를 사용하고 있습니다.

`f<A>()`를 사용한다면, 

```cpp
class A {
public:
    typedef int Int; // 종속 타입
    typedef char Char; // 종속 타입                
};

template<typename T>
typename T::Int f(typename T::Int param) {
    return 1;
};
template<typename T>
typename T::Char f(typename T::Char param) {
    return 2;
};

EXPECT_TRUE(f<A>(10) == 1); // (O) T::Int f(typename T::Int param) 버전을 호출합니다.
EXPECT_TRUE(f<A>('a') == 2); // (O) T::Char f(typename T::Char param) 버전을 호출합니다.
```

전달한 인수에 부합하는 함수를 호출하기 위해, [함수 템플릿 오버로딩 결정 규칙](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)에 따라 다음 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보군이 만들어지고, 가장 적합한 함수가 호출됩니다.

```cpp
A::Int f(A::Int param); // typename T::Int f(typename T::Int param)
A::Char f(A::Char param); // typename T::Char f(typename T::Char param)
```

다음 `B`클래스는, [종속 타입](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85) 이 `Int`만 있고, `Char`는 없는데요, `f<B>('a')`는 `char`를 전달했는데, `int`버전이 호출됩니다. 아무런 컴파일 오류도 발생시키지 않고요.

```cpp
class B {
public:
    typedef int Int; // 종속 타입
};

EXPECT_TRUE(f<B>(10) == 1);
EXPECT_TRUE(f<B>('a') == 1); // char를 전달했는데 int 버전이 호출됩니다. 
```

[SFINAE](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error) 때문에 컴파일 오류가 발생하지 않고, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 때문에 `char`가 `int`로 변환되어 호출된 겁니다.

우선, 다음과 같은 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 목록이 생깁니다.

```cpp
B::Int f(B::Int param); // typename T::Int f(typename T::Int param)
B::Char f(B::Char param); // typename T::Char f(typename T::Char param) -> B에는 Char 종속 타입이 없으니 대체 실패합니다.
```

하지만, `B`에는 `Char` [종속 타입](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)이 없으니 `B::Char f(B::Char param);` 는 대체 실패를 합니다. 이러한 실패는 컴파일 오류가 아니며, 그냥 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 목록에서 제외합니다. 이걸 [SFINAE](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error) 라고 합니다. 

따라서 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 목록은 다음 하나밖에 없으며,

```cpp
B::Int f(B::Int param); // typename T::Int f(typename T::Int param)
```

`f<B>('a') == 1`는 `int` 타입으로 [암시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)되어 합법적으로 실행됩니다.

**SFINAE 평가 범위** 

또한, [SFINAE](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)는 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)와 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)에서만 평가됩니다. 

다음 코드에서 `ResultType`을 `f()` 함수 본문에서 사용했는데요, [SFINAE](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)는 함수 본문까지는 평가하지 않습니다.

```cpp
class A {
public:
    typedef int Int; // 종속 타입
    typedef char Char; // 종속 타입
};

template<typename T>
typename T::Int f(typename T::Int param) {
    return 1;
};
template<typename T>
typename T::Char f(typename T::Char param) {
    typename T::ResultType result = 2; // A에는 ResultType이 없습니다.
    return result;
};
```

다음과 같이 사용한다면,

```cpp
EXPECT_TRUE(f<A>(10) == 1);
```

 다음처럼 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 목록을 만드는데요,

```cpp
A::Int f(A::Int param); // typename T::Int f(typename T::Int param)
A::Char f(A::Char param); // typename T::Char f(typename T::Char param)
```

`f<A>(10)`를 호출하면, 전달한 인수 `10`은 `int` 타입이므로 `A::Int f(A::Int param);`이 정상적으로 호출됩니다.

하지만, 다음과 같이 사용한다면,

```cpp
EXPECT_TRUE(f<A>('a') == 2); // (X) 컴파일 오류. ResultType이 없습니다.
```

함수 본문에 있는 것은 [SFINAE](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)가 평가하지 않으니, [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 목록은 이전과 동일하게 다음 2가지 입니다.

```cpp
A::Int f(A::Int param); // typename T::Int f(typename T::Int param)
A::Char f(A::Char param); // typename T::Char f(typename T::Char param)
```

`f<A>('a')`에 적합한 것은 `A::Char f(A::Char param)`이어서 이를 채택하고 호출하지만, 함수 본문에 있는 `ResultType`이 아무데도 정의되지 않았으니, 컴파일 오류를 발생시킵니다.

> *(C++20~) [컨셉(concept)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항(requires)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 추가되어 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 줄 수 있습니다.*

# 함수 템플릿 인수 추론 확인

실제 [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 결과를 보는 유틸리티 사이트가 있습니다.

[https://cppinsights.io/](https://cppinsights.io/)에서 코드를 입력하고 "Run C++ Insights" 버튼을 클릭하면 됩니다. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/daa75bf8-c261-498b-818f-8004d4e3fe91)
