---
layout: single
title: "#10. [모던 C++] (C++11~) 전달 참조와 완벽한 전달(forward())"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#23] std::move와 std::forward를 숙지하라.
>   * move 구현 원리, const를 move할때 주의하라.
>   * forward는 우측값 참조일때만 우측값 참조로 변환한다.

> * [MEC++#24] 전달 참조와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 구별하라.([전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0))

# 개요

값 타입이나 포인터는 함수를 통해 전달할 때 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)는 변하지 않습니다. [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 될 뿐이죠.

```cpp
int a;
int b = a; // 복사 대입 됩니다.
int* p1 = &a; 
int* p2 = p1; // 포인터가 가리키는 값 말고 포인터 값은 복사 대입됩니다.
```

하지만 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 경우는 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)가 변할 수 있습니다. 
다음 예에서 `f()`함수는 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `ref`를 전달해 봤자 값 타입으로 전달받습니다.(*[템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 참고*)

```cpp
template<typename T, typename U, typename V>
void f(T, U, V) {}

int val = 0;
int* ptr = NULL;
int& ref = val;

f(val, ptr, ref); // f<int, int*, int>(int, int*, int). 참조자가 제거됩니다.
```

또한, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 이름이 있는 변수로 전달 받는 순간 이름이 부여되서 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 됩니다.(*[이름이 부여된 우측값](??) 참고*)

```cpp
class A {};

int f_11(A&) {return 1;} // 인자가 좌측값 참조이면 호출됩니다.
int f_11(A&&) {return 2;} // 인자가 우측값 참조이면 호출됩니다.

A lvalue;
A&& rvalue_11 = std::move(lvalue); // rvalue는 이름이 부여됐으므로 좌측값입니다.
EXPECT_TRUE(f_11(rvalue_11) == 1); // f_11(A&)를 호출합니다.
```

이렇게 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 다른 변수로 전달할때 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)가 변경되는 문제가 있습니다. 

이 문제는 함수내에서 포워딩을 할때 특히 문제입니다.

```cpp
void Forwarding_11(A&& param) { // 함수 인자는 이름이 부여됐으므로 좌측값입니다.
    f(param); // 좌측값으로만 호출합니다.
}
```

따라서 [이름이 부여된 우측값](??)에서 억지로 `move()`를 사용하는 방법을 소개해 드렸는데요,

```cpp
void Forwarding_11(A&& param) { // 함수 인자는 이름이 부여됐으므로 좌측값입니다.
    f(std::move(param)); // 우측값으로 형변환하여 호출합니다.
}
```

[참조 축약](??)을 활용한 [forward()](??) 함수를 이용하여 [완벽하게 전달](??)하는 방법이 좀더 효율적이고 직관적입니다. 

# 참조 축약

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 공부하기 전에 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)의 개념을 알아두는게 좋습니다.

원칙적으로 [포인터의 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)는 합법이지만, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 참조는 불법입니다. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 메모리상에 어떻게 구성되는지 표준에 정의되지 않았고, 그저 개체에 대한 별칭이니까요. 그저 또다른 별칭을 만들뿐 포인터처럼 [다차원](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)적으로 구성되지는 않습니다.

```cpp
int val;
int* p;
int** pp = &p; // 포인터의 포인터는 합법입니다.

int& r = val;
int& & rr = r; // (X) 컴파일 오류. 참조자의 참조는 불법입니다.
int& r2 = r; // 또다른 별칭일 뿐입니다.
```

하지만, 템플릿을 사용하면 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)에 참조를 더할 수 있습니다. 마치 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 참조 처럼요.

[템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)시에는 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)은 제거되지만, 명시적으로 지정하면 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용할 수 있는데요,

```cpp
template<typename T>
void f(T) {}

int val = 0;
int& ref = val;
f(ref); // f<int>(int). 기본적으로 참조자가 제거됩니다.
f<int&>(ref); // f<int&>(int&). 명시적으로 지정하면 참조자로 사용됨
```

이 특징을 이용하면, 템플릿 클래스에서도 명시적으로 지정하여 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용할 수 있습니다.  

```cpp
template<typename T>
class Convert_11 {
public:
    using Type = T;
};

Convert_11<int>::Type a; // int
Convert_11<int&>::Type b = a; // int&입니다. 참조자 입니다.
```

즉, `T`가 `int&`라면, 다음의 `LRef`는 `int&` + `&`이며, `RRef`는 `int&` + `&&`이 됩니다. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)에 참조를 더하게 된거죠. 마치 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 참조한 것처럼요. 

```cpp
template<typename T>
class Convert_11 {
public:
    using LRef = T&; // T 타입에 좌측값 참조를 더합니다.
    using RRef = T&&; // T 타입에 우측값 참조를 더합니다.
};
```

[참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)에 참조를 더한 결과는 [포인터처럼 다차원적으로 동작](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)하지는 않고, [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)나 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 중 하나로 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)됩니다.

1. 기본 형태

    ```cpp
    class T {};
    T obj;
    Convert_11<T>::LRef a = obj; // T&
    Convert_11<T>::RRef b = std::move(obj); // T&& 
    ```

2. & + & 과 & + &&

    ```cpp
    class T {};
    T obj;
    Convert_11<T&>::LRef a = obj; // T&, & + &
    Convert_11<T&>::RRef b = obj; // T&, & + &&
    ```

3. && + & 과 && + &&

    ```cpp
    class T {};
    T obj;
    Convert_11<T&&>::LRef a = obj; // T&, && + &
    Convert_11<T&&>::RRef b = std::move(obj);; // T&&, && + &&  
    ```

즉 다음과 같이 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)을 합니다. [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)끼리를 더한 경우를 제외하고는 모두 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 됩니다.

|항목|결과|
|--|--|
|`&` + `&`|`&`|
|`&` + `&&`|`&`|
|`&&` + `&`|`&`|
|`&&` + `&&`|`&&`|

[참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)은 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto), [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)와 [using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/), [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype), [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92), [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/)에서 발생합니다.

# 전달 참조

보통 `A&&`의 형태로 타입에 `&&`을 붙이면, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 모두 전달 받을 수 있는데요,

```cpp
class A {};
A val;
A&& ref_11 = std::move(val); // A&&는 우측값만 받을 수 있습니다.
```

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)는 특별히 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)나 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 전달받을 수 있습니다.

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 만드려면 다음 조건을 만족해야 합니다.

1. 정확히 `auto&&`과 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) `T`의 `T&&` 이어야 합니다. `const auto&&`나 `const T&&`는 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아닙니다.

    ```cpp
    class A {};
    A val;

    auto&& a_11 = val; // A&. 전달 참조입니다. val 타입으로 추론합니다.
    // const auto&& b_11 = val; // (X) 컴파일 오류. const를 붙여서 전달 참조가 아닙니다. &&은 우측값만 받을 수 있습니다.
    // A&& c_11 = val; // (X) 컴파일 오류. 타입 추론을 안하므로 전달 참조가 아닙니다. &&은 우측값만 받을 수 있습니다.

    template<typename T>
    void f(T&& val) {} // 전달 참조입니다. val 타입으로 추론합니다.
    ```

2. 반드시 타입 추론이 되야 합니다.
 
    다음 예에서 템플릿 `A`의 `f()`함수는 인스턴스화 된 뒤에는 `f(int&& val)`로 구체화될 수 있기 때문에 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 라고 확신하기는 어렵습니다.

    하지만 템플릿 `A`의 `g()`함수는 함수 호출 시점에 전달한 타입으로 추론하기 때문에 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)입니다.

    ```cpp
    template<typename T> 
    class A {
    public:
        void f(T&& val) {} // 전달 참조가 아닙니다. A<int> val; 와 같이 인스턴스화 된 뒤에는 f(int&& val)로 구체화될 수 있기 때문에 val 타입으로 추론된다고 확신할 수 없습니다. 

        template<typename U> 
        void g(U&& val) {} // 전달 참조 입니다. val 타입으로 추론합니다.
    }; 
    ```
    또한 다음과 같이 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들이 여러개인 경우 `T&&`로 표현하면 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아닙니다. `param1`, `param2`중 어느 하나가 인스턴스화 된 뒤에는 나머지는 인스턴스화된 것으로 부터 구체화 될 수 있기 때문입니다. 따라서, `typename T, typename U`와 같이 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 독립적으로 만들어야 합니다.

    ```cpp
    template<typename T>
    void f(T&& param1, T&& param2) {} // 전달 참조가 아닙니다. param1, param2중 어느 하나가 인스턴스화 된 뒤에는 나머지는 인스턴스화된 것으로 부터 구체화 될 수 있기 때문입니다. 

    template<typename T, typename U>
    void f(T&& param1, U&& param2) {} // param1, param2 모두 전달 참조입니다.
    ```


[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)는 

1. 값 타입을 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 전달 받고,

2. [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 모두 전달받을 수 있습니다.


    `rref_11`은 `A&&`로 선언했지만, 이름이 있기 때문에 좌측값입니다.(*[이름이 부여된 우측값](??) 참고*) 따라서, `c_11`은 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `A&`입니다.

    ```cpp
    class A {};
    A val;
    A& ref = val;
    A&& rref_11 = static_cast<A&&>(val);   

    auto&& a_11 = val; // A&. val는 좌측값. 이를 참조로 만들면 A&인 좌측값 참조
    auto&& b_11 = ref; // A&. ref는 좌측값 참조
    auto&& c_11 = rref_11; // A&. rref_11는 이름이 있으니 좌측값
    auto&& d_11 = std::move(val); // A&& 
    ```

3. [상수 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), 비상수 개체를 모두 전달받을 수 있습니다.

    ```cpp
    class A {};
    const A val;
    const A& ref = val;
    const A&& rref_11 = static_cast<const A&&>(val);   

    auto&& a_11 = val; // const T&. val는 좌측값. 이를 참조로 만들면 const T&
    auto&& b_11 = ref; // const T&. ref는 좌측값 참조
    auto&& c_11 = rref_11; // const T&. rref_11는 이름이 있으니 좌측값
    auto&& d_11 = std::move(val); // const T&& 
    ```

# move() 원리

`move()`함수는 STL에 대략 다음처럼 구현됩니다.

```cpp
template<typename T>
typename remove_reference<T>::type&& move(T&& param) {
    using ReturnType = typename remove_reference<T>::type&&; // 참조성을 뗀 뒤 &&을 더합니다.

    return static_cast<ReturnType>(param);
}
```

코드를 보시면 아시겠지만, `move()`함수의 실체는 무작정 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 캐스팅 하는게 아니라, ***전달된 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 뗐다가 `&&`을 붙여주는 함수*** 입니다. 

1. `move()`함수의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 `T&& param`로 [전달 참조](??)입니다. 즉, 값 타입,  [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 모두 전달 받을 수 있고, [상수 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), 비상수 개체를 모두 전달 받을 수 있습니다.

1. `remove_reference`는 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)던, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)던 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거한 타입을 구합니다.

    다음처럼 [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%83%AC%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)를 이용하여 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거할 수 있습니다.

    ```cpp
    template<typename T>
    struct remove_reference { 
        using type = T; 
    };
    // 좌측값 참조인 경우 템플릿 부분 특수화. 좌측값 참조를 제거한 T를 type으로 사용합니다.
    template<typename T>
    struct remove_reference<T&> { 
        using type = T; 
    };
    // 우측값 참조인 경우 탬플릿 부분 특수화. 우측값 참조를 제거한 T를 type으로 사용합니다.
    template<typename T>
    struct remove_reference<T&&> { 
        using type = T; 
    };
    ```

2. [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거한 것에 `&&`을 붙여 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 캐스팅하여 리턴합니다.

전달되는 타입에 따라 다음과 같은 원리로 `&&`로 변환합니다.

|항목|형태|내부 연산|리턴값|
|--|--|--|--|
|값 타입|`move(T)`|`T + &&`|`T&&`|
|[좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`move(T&)`|`T&`-> `T` 후 + `&&`| `T&&`|
|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|`move(T&&)`|`T&&`->`T` 후 + `&&`|`T&&`|

[const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)인 개체라면 다음과 같습니다.

|항목|형태|내부 연산|리턴값|
|--|--|--|--|
|값 타입|`move(const T)`|`const T + &&`|`const T&&`|
|[좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`move(const T&)`|`const T&`-> `const T` 후 + `&&`| `const T&&`|
|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|`move(const T&&)`|`const T&&`->`const T` 후 + `&&`|`const T&&`|

만약 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거하지 않고, `&&`을 더한다면, `T`가 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `&` 였을때  [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)에 의해 `& + &&`은 `&`가 됩니다. 따라서, [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거하고 `&&`을 더해준 거죠.

# 상수 개체의 move(), 이동 연산을 지원하는 래퍼

`move()`함수는 전달된 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 뗐다가 `&&`을 붙여주는 함수이므로 `const T` 를 전달하면 `const T&&`로 변경합니다. 

그런데, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 `T&&`를 사용하므로 `const T&&`로는 호출되지 않습니다. 이 덕에 [상수 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)를 `move()`하면 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 호출할 수 없으니 대입 연산을 해줍니다.(*[이동 연산 변환의 안전성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0-%EB%B3%80%ED%99%98%EC%9D%98-%EC%95%88%EC%A0%84%EC%84%B1) 참고*)

[상수 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)는 [상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)에 의해 수정되면 안되므로, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 하면 안되죠. 

그런데, [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(*[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)*)라면 어짜피 버려질 개체이니 그냥 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 해버리는게 좋은 경우가 생길 수도 있습니다.

[이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 지원하기 위해 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 구현한 `Big_11` 개체가 있다고 합시다.

```cpp
class Big_11 {
public:    
    Big_11() {}
    ~Big_11() {}

    // 복사 생성자
    Big_11(const Big_11& other) {
        std::cout << "Big_11 : Copy Constructor" << std::endl;
    }
    // 이동 생성자
    Big_11(Big_11&& other) {
        std::cout << "Big_11 : Move Constructor" << std::endl;
    }  
};    
```

이를 관리하는 래퍼는 다음과 같이 만들 수 있습니다.

```cpp
class Wrapper_11 {
    Big_11 m_Data;
public:
    Wrapper_11() : m_Data{} {}
    const Big_11& GetData() const {
        return m_Data;
    }
};
```

`GetData()`를 호출하는 곳에서 쓸데없이 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하지 않도록 `const Big_11&`를 리턴했습니다.

```cpp
Wrapper_11 wrapper;
const Big_11& big{wrapper.GetData()}; // const Big_11&을 리턴하므로 참조만 합니다.
```

이를 복사해서 사용하고 싶다면 다음과 같이 하면 됩니다.

```cpp
Wrapper_11 wrapper;
Big_11 big{wrapper.GetData()}; // const Big_11& 를 리턴한 것을 Big_11로 받으므로 복사 생성 합니다.
```

만약 `wrapper`가 이제 버려질 값이라면, 복사 부하를 줄이기 위해 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하는게 좋습니다. 하지만 다음과 같이 `wrapper` 개체를 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 만들어 봤자, `GetData()`는 `const Big_11&`인 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴하므로, `Big_11`의 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)는 호출되지 않습니다. 

```cpp
// std::move(wrapper)로 우측값으로 만들어 봤자, std::move(a).GetData()는 const Big_11&(좌측값 참조)를 리턴
Wrapper_11 wrapper;
Big_11 big{std::move(wrapper).GetData()}; // 복사 생성자를 호출합니다.

// Wrapper_11{}은 임시 개체인 우측값 이지만, 여전히 GetData()는 const Big_11&(좌측값 참조)를 리턴
Big_11 big{Wrapper_11{}.GetData()}; // 복사 생성자를 호출합니다.
```

그러면 다음은 될까요? [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 `move()`했지만, `const Big_11&`를 `const Big_11&&`로 바꾸기 때문에, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)와 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입이 달라 (*[이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)는 `Big_11(Big_11&& other)`입니다. [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)가 다르죠.*) 그냥 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.

```cpp
// Wrapper_11{}.GetData()는 const Big_11&를 리턴하고, move() 는 const Big_11&& 을 리턴. 이동 생성자와 인자 타입과 다르므로, 그냥 복사 생성자 호출 
Big_11 big{std::move(Wrapper_11{}.GetData())}; 
```

다음처럼 [const_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)로 [상수성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)을 버려야 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)을 할 수 있습니다.

```cpp
// Big_11의 이동 생성자 호출
Big_11 big{std::move(const_cast<Big_11&>(Wrapper_11{}.GetData()))}; 
```

되기는 합니다만, 참 실수하기 쉽고, 타이핑도 번거롭고 [const_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)가 보기 싫습니다.

[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 에 언급되었듯, **잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게** 구현할 필요가 있는데요, [멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 이럴때 딱 좋습니다.

```cpp
class Wrapper_11 {
    Big_11 m_Data;
public:
    Wrapper_11() : m_Data{} {}
    const Big_11& GetData() const & { // #1. Wrapper_11가 좌측값일때 호출됩니다.
        return m_Data;
    }

    Big_11&& GetData() && {
        return std::move(m_Data); // #2. Wrapper_11가 우측값일때 호출됩니다.
    }
};
```

기존 `GetData()` 함수에 `&`을 붙였고, `GetData() &&` 오버로딩 버전을 추가하였습니다. 이제 `Wrapper_11`이 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(*[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)*) 일때는 알아서 `GetData() &&` 버전을 호출하므로, 사용하기 훨씬 수월합니다.

```cpp
// wrapper은 좌측값. #1이 호출되어 const Big_11&을 리턴하고, Big_11의 복사 생성자 호출
Wrapper_11 wrapper;
Big_11 big{wrapper.GetData()}; 

// wrapper은 좌측값. std::move(wrapper)는 우측값. #2가 호출되어 Big_11&&을 리턴하고, Big_11의 이동 생성자 호출
Wrapper_11 wrapper;
Big_11 big{std::move(wrapper).GetData()};            

// Wrapper_11{}은 임시 개체인 우측값. #2가 호출되어 Big_11&&을 리턴하고, Big_11의 이동 생성자 호출
Big_11 big{Wrapper_11{}.GetData()}; 
```

# forward() 와 완벽한 전달

함수가 전달받은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수에 전달하는 `Forwarding_11()` 함수를 구현한다고 합시다.

다음처럼 값 타입인 경우에는 불필요한 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 발생하고, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)인 경우엔 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 바껴버려서 컴파일 오류가 납니다.

```cpp
class A {};
void f_11(A param1, A& param2, A&& param3) {}

void Forwarding_11(A param1, A& param2, A&& param3) {
    f_11(
        param1, // (△) 비권장. A로 전달합니다. 복사 대입합니다.
        param2, // A&로 전달합니다.
        param3 // (X) 컴파일 오류. param3은 우측값을 참조하는 좌측값입니다. A&로 전달합니다.
    );
}
```

이런 문제를 해결하고자 C++11 에서는 함수가 전달받은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수로 [완벽하게 전달](??)하는 [forward()](??) 함수를 제공합니다.

```cpp
class A {};
void f_11(A param1, A& param2, A&& param3) {}

void Forwarding_11(A param1, A& param2, A&& param3) {
    f_11(
        std::forward<A>(param1), // A&&로 전달합니다.
        std::forward<A&>(param2), // A&로 전달합니다.
        std::forward<A&&>(param3) // A&&로 전달합니다. std::forward<A>(param3) 도 동일합니다.
    );
} 

A a;
A b;
A& ref = a;

Forwarding_11(a, ref, std::move(b));
```

`std::forward<A&&>(param3)`의 경우는 특별히 `std::forward<A>(param3)`와 같이 `&&`없이 사용할 수 있습니다.(*[forward() 원리](??) 참고*)

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 라면 다음과 같이 `forward<T>()`, `forward<U>()`, `forward<V>()`와 같이 `&`나 `&&` 명시 없이 사용할 수 있습니다.(*[forward() 원리](??) 참고*)

```cpp
class A {};
void f_11(A param1, A& param2, A&& param3) {}

template<typename T, typename U, typename V>
void Forwarding_11(T&& param1, U&& param2, V&& param3) {
    f_11(
        std::forward<T>(param1), // A&&로 전달합니다. param1은 Forwarding_11 함수에서 임시로 복사 대입 받은 것이므로, 임시 개체로 취급하고, f_11에 이동 대입한 후 버립니다.
        std::forward<U>(param2), // A&로 전달합니다.
        std::forward<V>(param3) // A&&로 전달합니다.
    );
}

A a;
A b;
A& ref = a;

Forwarding_11(a, ref, std::move(b));   
```

# forward() 원리

사실 [forward() 원리](??)를 굳이 이해해야 모던 C++을 사용할 수 있는건 아닙니다. "그냥 함수끼리 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 포워딩 할때는 [forward()](??) 함수를 써야 하는구나" 외우고 사용하셔도 크게 문제될 일은 많이 없을 겁니다. 하지만, 이를 이해한다면, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)와 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)에 대한 통찰력이 생겨 좀더 효율적인 코드를 만들 수 있습니다. 무지하게 난해하지만, 도전할 가치가 있다고 봅니다.
 
[forward()](??) 함수는 STL에 대략 다음처럼 구현됩니다.

```cpp
// #1
template<typename T> 
T&& forward(typename remove_reference<T>::type& param) {
    return static_cast<T&&>(param);
}

// #2. 우측값 참조일 경우 오버로딩
template<typename T>
T&& forward(typename remove_reference<T>::type&& param) {
    return static_cast<T&&>(param);
}
```

언뜻 보면 `static_cast<T&&>(param);` 을 했으니, 무조건 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 형변환 하는 것처럼 보이는데요, [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)이 반영되어 절묘하게 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 전달에 최적화된 적절한 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)로 형변환이 됩니다.

각 케이스별로 살펴 보겠습니다.

**값 타입이 전달된 경우**

```cpp
class A {};
void f(A val) {}
void Forwarding_11(A param) {
    f(std::forward<A>(param));
}

A a;
Forwarding_11(a);
```
와 같이 사용한 경우 입니다. `#1` 오버로딩이 사용되며, `T == A`, `param == A` 입니다.

`forward()` 함수는 다음처럼 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 됩니다.

```cpp
// #1. 템플릿 전개
A + && forward(typename A + & param) {
    return static_cast<A + &&>(param);
}

// [참조 축약]을 반영하면 다음과 같습니다. 즉 `A&` 를 전달받아 `A&&`로 리턴합니다.
A&& forward(typename A& param) {
    return static_cast<A&&>(param);
}
``` 

즉, 다음처럼 값 타입으로 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달 받았다면, 전달 과정에서 또다시 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하지 않도록 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 전달합니다.

```cpp
void f(T val) {}
void Forwarding_11(T param) {
    f(std::forward<T>(param)); // 우측값 참조로 전달합니다.
}   
```

다만 다음과 같이 [forward()](??)로 이동된 `param`을 다시 사용한다면 낭패이니 주의하세요.

```cpp
void f(T val) {}
void Forwarding_11(T param) {
    f(std::forward<T>(param)); // 우측값 참조로 전달합니다.

    param.GetData(); // (X) 오동작. param은 이동되었습니다. 사용하시면 안됩니다. 
}   
```

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)로 사용한 경우에 값 타입을 전달하면, 

```cpp
class A {};
void f(A val) {}

template<typename T>
void Forwarding_11(T&& param) { // 전달 참조입니다. 값 타입을 전달하면, T == A, param은 A&입니다.
    f(std::forward<T>(param));
}
```

값 타입이 전달되었으므로 `param`은 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 됩니다. 즉,  `T == A`, `param == A&` 입니다. 

따라서, [템플릿 인스턴스화](??)와 [참조 축약](??)을 반영하면, `A&& forward(typename A& param)` 형태로 동일합니다.

```cpp
// #1. 템플릿 전개
A + && forward(typename A + & param) {
    return static_cast<A + &&>(param);
}

// 참조 축약 버전
A&& forward(typename A& param) {
    return static_cast<A&&>(param);
}
``` 

**[좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 타입이 전달된 경우**

```cpp
class A {};
void f(A& val) {}
void Forwarding_11(A& param) {
    f(std::forward<A&>(param));
}

A a;
A& ref = a;
Forwarding_11(ref);
```

와 같이 사용한 경우 입니다. `#1` 오버로딩이 사용되며, `T == A&`, `param == A&` 입니다.

`forward()` 함수는 다음처럼 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 됩니다.

```cpp
// #1. 템플릿 전개
A& + && forward(typename A + & param) {
    return static_cast<A& + &&>(param);
}

// [참조 축약]을 반영하면 다음과 같습니다. 즉 `A&` 를 전달받아 `A&`로 리턴합니다.
A& forward(typename A& param) {
    return static_cast<A&>(param);
}
```

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)로 사용한 경우에 [좌측값 참조](??)를 전달하면, 

```cpp
class A {};
void f(A& val) {}

template<typename T>
void Forwarding_11(T&& param) { // 전달 참조입니다. 좌측값 참조을 전달하면, T == A&, param은 A&입니다. 
    // forward 함수에는 템플릿 인자로 T를 전달(즉, A&)합니다.
    f(std::forward<T>(param));
}
```

[좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 전달되었으므로 `param`은 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 되어  즉, `T == A&`, `param == A&` 입니다. 

따라서, [템플릿 인스턴스화](??)와 [참조 축약](??)을 반영하면, `A& forward(typename A& param)` 형태로 동일합니다.

```cpp
// #1. 템플릿 전개
A& + && forward(typename A + & param) {
    return static_cast<A& + &&>(param);
}

// 참조 축약 버전
A& forward(typename A& param) {
    return static_cast<A&>(param);
}
``` 

**[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 타입이지만, 이름이 부여된 좌측값이 전달된 경우**

```cpp
class A {};
void f(A&& val) {}
void Forwarding_11(A&& param) {
    f(std::forward<A&&>(param));
}

A a;
Forwarding_11(std::move(a));
```
와 같이 사용한 경우 입니다. `#1` 오버로딩이 사용되며, `T == A&&`, `Forwarding_11()`이 전달받은 `param`은 `A&&` 이지만, `forward()`에 전달할때는 `A&` 입니다.



`forward()` 함수는 다음처럼 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 됩니다.

```cpp
// #1
A&& + && forward(typename A + & param) {
    return static_cast<A&& + &&>(param);
}

이므로, 참조 축약을 반영하면 다음과 같습니다. 즉 `A&` 를 전달받아 `A&&`로 리턴합니다.

A&& forward(typename A + & param) {
    return static_cast<A&&>(param);
}
```

    결국 `forward<T>`와 `forward<T&&>`는 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 결과가 동일합니다.


4. [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 타입이 전달된 경우(T == A&&, param == A&&)

    `forward()` 함수는 다음처럼 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 됩니다.

    ```cpp
    // #2
    A&& + && forward(typename A + && param) {
        return static_cast<A&& + &&>(param);
    }

    이므로 [참조 축약]을 반영하면 다음과 같습니다. 즉 `A&&` 를 전달받아 `A&&`로 리턴합니다.

    ```cpp
    A&& forward(typename A&& param) {
        return static_cast<A&&>(param);
    }
    ```

    `forword<A>`와 같이 `A`를 전달하더라도 동일하게 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 됩니다.

    ```cpp
     A + && forward(typename A + && param) {
        return static_cast<A + &&>(param);
    }  
    
    이므로,

    A&& forward(typename A&& param) {
        return static_cast<A&&>(param);
    }     
    ```

결론적으로 전달되는 타입에 따라 다음과 같이 변환합니다. [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 경우만 빼고는 모두 forward<T>의 형태로 호출할 수 있습니다.

|항목|형태|리턴값|
|--|--|--|
|값 타입|`forward<T>(값타입)`|`T&&`|
|[좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`forward<T&>(좌측값 참조)`| `T&`|
|[함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)처럼 [우측값을 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하는 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`forward<T&&>(좌측값 참조)`<br/>`forward<T>(좌측값 참조)`| `T&&`|
|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|`forward<T&&>(우측값 참조)`<br/>`forward<T>(우측값 참조)`|`T&&`|

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)인 경우는 모두 `forward<T>`의 형태로 호출할 수 있습니다.



MEC++29 참조 축약을 숙지하라.




# forward()를 이용한 완벽한 전달

MEC++#25 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에는 std::move()를, 전달 참조에는 std::forward()를 사용하라.
MEC++#30 완벽한 전달이 실패하는 경우들을 잘 알아둬라

# 전달 참조의 오버로딩

MEC++#26 전달 참조에 대한 오버로딩을 피하라.
MEC++#27 전달 참조에 대한 오버로딩 대신 사용할 수 있는 기법들을 알아 두라



