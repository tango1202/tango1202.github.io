---
layout: single
title: "#14. [모던 C++] (C++11~) 범위 있는 열거형, (C++17~) 열거형의 중괄호 직접 초기화 허용"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#10] 범위 없는 enum보다 범위 있는 enum을 선호하라.([열거형의 암시적 형변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#%EC%97%B4%EA%B1%B0%ED%98%95%EC%9D%98-%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98), [전방 선언](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8), [기반 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#%EA%B8%B0%EB%B0%98-%ED%83%80%EC%9E%85))

> * (C++11~) 이름 범위를 한정하는 [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)이 추가되어 이름 충돌 회피가 쉬워졌고, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하며, [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)도 지원합니다.
> * (C++17~) [열거형의 중괄호 직접 초기화를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#c17-%EC%97%B4%EA%B1%B0%ED%98%95%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%97%88%EC%9A%A9)하여 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하는 사용자 정의 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 사용이 좀더 쉬워졌습니다.


# 개요

기존에는 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 이름이 한정되지 않아 이름 충돌의 우려가 있어 클래스 내에 정의하는 방식을 사용했었는데요,

```cpp
// 기존
enum Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week{Sunday}; // (△) 비권장. 이름 충돌이 쉬움

// 혹은
class Week {
public:
    // 클래스내에 정의. 사용시 클래스명을 기재해야 함
    enum Val {
        Sunday, Monday, Tuesday, Wednesday, 
        Thursday, Friday, Saturday
    };
};

Week::Val val{Week::Sunday}; // 범위 확인 연산자와 클래스명 사용
```

C++11 부터는 [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)을 지원하여 이름 충돌 회피가 훨씬 쉬워졌습니다.

```cpp
// 범위 있는 열거형
enum class Week_11 {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week_11 week{Week_11::Sunday}; // 범위명을 지정하여 이름 충돌 회피
```

# 열거형의 암시적 형변환

기존 [열거형은 암시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)이 되지만, [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)은 형변환되지 않습니다.

```cpp
enum Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week = Sunday;
int val = week; // int형으로 형변환 됩니다.

enum class Week_11 {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday    
};

Week_11 week_11 = Week_11::Sunday;
// int val_11 = week_11; // (X) 컴파일 오류. 형변환되지 않습니다.
```
# 전방 선언

[범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)은 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)을 지원합니다. 지난날 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 항목이 추가될 때마다 엄청나게 많은 파일들이 쓸데없이 재빌드 되던 걸 생각하면, 참 사소하지만 감사한 기능입니다.

```cpp
enum MyEnum; // (X) 컴파일 오류.
enum class MyEnum_11;
```

# 기반 타입

기존에는 [열거형의 크기](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/#%EC%97%B4%EA%B1%B0%ED%98%95%EC%9D%98-%ED%81%AC%EA%B8%B0)를 지정하기 위하여 열거자에 강제적으로 `dummy` 값을 입력했는데요, 

C++11 부터는 [기반 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#%EA%B8%B0%EB%B0%98-%ED%83%80%EC%9E%85)을 지정할 수 있습니다. 기본적으로는 `int`를 사용하며, 다음과 같이 명시적으로 변경할 수 있습니다.

```cpp
enum MyEnum1_11 : int {a, b, c}; // int 형을 기반 타입으로 사용합니다.
enum class MyEnum2_11 : char {i, j, k}; // char 형을 기반 타입으로 사용합니다.
```

# 열거형 초기화와 암시적 형변환 차단

열거자에 [기반 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#%EA%B8%B0%EB%B0%98-%ED%83%80%EC%9E%85)을 지정하고 열거자를 생략할 수 있습니다.

```cpp
    enum MyInt_11 : int {}; // 열거자가 빠졌습니다!!
```

보통 나열된 열거자를 사용하기 위해 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)을 정의하는데, 이를 생략하다니 좀 의아한데요, 재밌게도 다음과 같이 값을 대입할 수 있습니다. 반드시 같은 타입의 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)만 대입할 수 있어요.

```cpp
enum MyInt_11 : int {};

// 특정 값을 대입합니다. 반드시 MyInt 타입만 대입받을 수 있습니다.
MyInt_11 val1(MyInt_11(10));
MyInt_11 val2 = MyInt_11(10); // MyInt_11 val1(MyInt_11(10)); 와 동일
MyInt_11 val3{val1}; 

// MyInt_11 val4 = 10; // (X) 컴파일 오류. 정수는 대입 받을 수 없습니다.
// MyInt_11 val5(10); // (X) 컴파일 오류. 정수는 대입 받을 수 없습니다.
// MyInt_11 val6{10}; // (X) 컴파일 오류. 중괄호 직접 초기화는 지원하지 않습니다.
// MyInt_11 val7 = {10}; // (X) 컴파일 오류. 중괄호 복사 초기화는 지원하지 않습니다.        
```

이 기능을 이용하면, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하는 정수 타입을 만들 수 있습니다. 다음 예에서 `Func()`은 암시적으로 `int`로 변환될 수 있는 모든 타입이 전달될 수 있지만, `Func_11()`은 오로지 `MyInt_11`만 전달 받을 수 있습니다. 

```cpp

enum MyInt_11 : int {};
void Func(int val) {}
void Func_11(MyInt_11 val) {}    

Func(10);
Func('c'); // (△) 비권장. 암시적 형변환 됩니다.

// Func_11(10); // (X) 컴파일 오류. 암시적 형변환을 차단합니다.
// Func_11('c'); // (X) 컴파일 오류. 암시적 형변환을 차단합니다.
Func_11(MyInt_11(10)); // MyInt_11 타입만 가능합니다.    
```

# (C++17~) 열거형의 중괄호 직접 초기화 허용

C++17 부터는 [열거형의 중괄호 직접 초기화를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#c17-%EC%97%B4%EA%B1%B0%ED%98%95%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%97%88%EC%9A%A9)하여 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하는 사용자 정의 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 사용이 좀더 쉬워졌습니다.

```cpp
enum MyInt_11 : int {};

MyInt_11 val1(MyInt_11(10));
MyInt_11 val2 = MyInt_11(10); // MyInt val1(MyInt(10)); 와 동일
MyInt_11 val3{val1}; 

// MyInt_11 val4 = 10; // (X) 컴파일 오류. 정수는 대입 받을 수 없습니다.
// MyInt_11 val5(10); // (X) 컴파일 오류. 정수는 대입 받을 수 없습니다.
MyInt_11 val6_17{10}; // (O) C++17~ 중괄호 직접 초기화를 지원합니다.
// MyInt_11 val7 = {10}; // (X) 컴파일 오류. 중괄호 복사 초기화는 지원하지 않습니다.    
```

