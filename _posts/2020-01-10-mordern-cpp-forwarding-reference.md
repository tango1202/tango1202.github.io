---
layout: single
title: "#10. [모던 C++] (C++11~) 전달 참조와 완벽한 전달"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#23] std::move와 std::forward를 숙지하라.

> * [MEC++#24] 전달 참조와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 구별하라.([전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0))

# 개요

값 타입이나 포인터는 함수를 통해 전달할 때 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)나 타입이 변하지 않습니다. [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 될 뿐이죠.

```cpp
int a;
int b = a; // 복사 대입 됩니다.
int* p1 = a; 
int* p2 = p1; // 포인터가 가리키는 값 말고 포인터 값은 복사 대입됩니다.
```

하지만 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 경우는 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)나 타입이 변할 수 있습니다. 
다음 예에서 `f()`함수는 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `ref`를 전달해 봤자 값 타입으로 전달받습니다.(*[템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 참고*)

```cpp
template<typename T, typename U, typename V>
void f(T, U, V) {}

int val = 0;
int* ptr = NULL;
int& ref = val;

f(val, ptr, ref); // f<int, int*, int>(int, int*, int). 참조자가 제거됩니다.
```

또한, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 인자로 받는 순간 이름이 부여되서 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 됩니다.(*[이름이 부여된 우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%A6%84%EC%9D%B4-%EB%B6%80%EC%97%AC%EB%90%9C-%EC%9A%B0%EC%B8%A1%EA%B0%92) 참고*)

```cpp
class A {
public:
    int Func() & {return 1;} 
    int Func() && {return 2;}    
};

void f(A&& a) {
    EXPECT_TRUE(a.Func() == 1);
}
```

이렇게[좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 전달에 따라 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)나 타입이 변경되는 문제가 있습니다. 

이에 따라 [전달 참조라는 특수한 참조자](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 이용하여 [완벽한 전달](??)을 합니다.

# 참조 축약

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 공부하기 전에 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)의 개념을 알아두는게 좋습니다.

원칙적으로 [포인터의 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)는 합법이지만, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 참조는 불법입니다. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 메모리상에 어떻게 구성되는지 표준에 정의되지 않았고, 그저 개체에 대한 별칭이니까요. 그저 또다른 별칭을 만들뿐 포인터처럼 [다차원](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)적으로 구성되지는 않습니다.

```cpp
int* p;
int** pp = &p; // 포인터의 포인터는 합법입니다.

int& r = val;
int& & rr = r; // (X) 컴파일 오류. 참조자의 참조는 불법입니다.
int& r2 = r; // 또다른 별칭일 뿐입니다.
```

하지만, 템플릿을 사용하면 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)에 참조를 더할 수 있습니다. 마치 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 참조 처럼요.

[템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)에서 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)은 제거되지만, 명시적으로 지정하면 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용할 수 있는데요,

```cpp
template<typename T>
void f(T) {}

int val = 0;
int& ref = val;
f(ref); // f<int>(int). 참조자가 제거됩니다.
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

[참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)은 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto), [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)와 [using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/), [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype) 에서 발생합니다.

# 전달 참조

보통 `A&&`의 형태로 타입에 `&&`을 붙이면, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 전달 받을 수 있는데요,

```cpp
A val;
A&& ref_11 = std::move(val);
```

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)는 특별히 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)나 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 전달받을 수 있습니다.

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 만드려면 다음 조건을 만족해야 합니다.

1. 정확히 `auto&&`과 템플릿 인자 `T`의 `T&&` 이어야 합니다. `const auto&&`나 `const T&&`는 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아닙니다.

2. 반드시 타입 추론이 되야 합니다.
 
다음 예에서 템플릿 `A`의 `f()`함수는 인스턴스화 된 뒤에는 `f(int&& val)`로 구체화될 수 있기 때문에 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 라고 확신하기는 어렵습니다.

하지만 템플릿 `A`의 `g()`함수는 함수 호출 시점에 전달한 타입으로 추론하기 때문에 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)입니다.

```cpp
auto&& a_11 = val; // 전달 참조입니다. val 타입으로 추론합니다.
const auto&& b_11; // cosnt를 붙여서 전달 참조가 아닙니다.

template<typename T>
void f(T&& val) {} // 전달 참조입니다. val 타입으로 추론합니다.

template<typename T> 
class A {
public:
    void f(T&& val) {} // 전달 참조가 아닙니다. A<int> val; 와 같이 인스턴스화 된 뒤에는 f(int&& val)로 구체화될 수 있기 때문에 val 타입으로 추론된다고 확신할 수 없습니다. 

    template<typename U> 
    void g(U&& val) {} // 전달 참조 입니다. val 타입으로 추론합니다.
}; 
```

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)는 

1. [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 모두 전달받을 수 있습니다.


    `rref_11`은 `A&&`로 선언했지만, 이름이 있기 때문에 좌측값입니다.(*[이름이 부여된 우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%A6%84%EC%9D%B4-%EB%B6%80%EC%97%AC%EB%90%9C-%EC%9A%B0%EC%B8%A1%EA%B0%92) 참고*) 따라서, `c_11`은 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `A&`입니다.

    ```cpp
    class A {};
    A val;
    A& ref = val;
    A&& rref_11 = static_cast<A&&>(val);   

    auto&& a_11 = val; // T&. val는 좌측값. 이를 참조로 만들면 T&
    auto&& b_11 = ref; // T&. ref는 좌측값 참조
    auto&& c_11 = rref_11; // T&. rref_11는 이름이 있으니 좌측값
    auto&& d_11 = std::move(val); // T&& 
    ```

2. [상수 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), 비상수 개체를 모두 전달받을 수 있습니다.

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

`move()`함수는 STL에 다음처럼 구현됩니다.

1. `remove_reference`는 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)던, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)던 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거한 타입입니다.
2. 참조성을 제거한 것에 `&&`을 붙여 캐스팅하여 리턴합니다.

먼저 참조성을 제거한 것 말고는 `static_cast<T&&>`와 같죠.

```cpp
template<typename T>
typename remove_reference<T>::&& move(T&& param) {
    using ReturnType = typname remove_reference<T>::type&&;

    return static_cast<ReturnType>(param);
}
```

```cpp
template<typename T>
T&& MyMove(T& val) {
    using ReturnType = T&;
    auto T
    return static_cast<ReturnType&&>
}

class T{
public:
    int Func(T&) {return 1;} // #1
    int Func(T&&) {return 1;} // #2
};
T a;

T& b = a;
EXPECT_TRUE(a.Func(MyMove(b)));
```

# const 타입 move()

`move()`함수는 `T`와 `T&`를 단순히 `&&`로 변환해 주는 함수이므로 `const T` 를 전달하면 `const T&&`로 변경합니다. 그런데, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 `T&&`를 사용하므로 `const T&&`로 변환하면 호출되지 않습니다. 따라서 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 제거하고 사용해야 합니다.(*[이동 연산을 지원하는 래퍼](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%84-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EB%9E%98%ED%8D%BC) 참고*)

# forward() 원리

MEC++29 참조 축약을 숙지하라.

# forward()를 이용한 완벽한 전달

MEC++#25 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에는 std::move()를, 전달 참조에는 std::forward()를 사용하라.
MEC++#30 완벽한 전달이 실패하는 경우들을 잘 알아둬라

# 전달 참조의 오버로딩

MEC++#26 전달 참조에 대한 오버로딩을 피하라.
MEC++#27 전달 참조에 대한 오버로딩 대신 사용할 수 있는 기법들을 알아 두라