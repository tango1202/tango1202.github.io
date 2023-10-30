---
layout: single
title: "#9. [모던 C++] (C++11~) nullptr, nullptr_t"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#8] 0과 NULL 보단 nullptr를 선호하라.(오버로딩 함수 호출, auto 추론)

* (C++11~) [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) 리터럴이 추가되어 좀더 타입에 안전한 코딩 계약이 가능해 졌습니다.

# nullptr

기존에는 [널 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 표현하기 위해 `0`이나 `NULL`(`#define NULL 0`)을 사용했는데요, 

C++11 부터는 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) 리터럴이 제공됩니다.

```cpp
int* ptr1{0};
int* ptr2{NULL};
int* ptr3_11{nullptr}; // C++11
```

`0`이나 `NULL`은 사실 포인터가 아니라 정수이기 때문에 오버로딩 함수 호출시 `int` 타입이 호출됩니다.

```cpp
int f(int) {return 1;} // #1
int f(int*) {return 2;} // #2

int* ptr1{0};
int* ptr2{NULL};
int* ptr3_11{nullptr};

EXPECT_TRUE(f(ptr1) == 2); // int* 이므로 f(int*) 호출
EXPECT_TRUE(f(ptr2) == 2); // int* 이므로 f(int*) 호출
EXPECT_TRUE(f(ptr3_11) == 2); // int* 이므로 f(int*) 호출
```

또한 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용한다면 다음처럼 초기값에 따라 `int`와 같은 정수형으로 추론되는 문제가 있습니다. 그러니 앞으로는 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/)을 사용하시기 바랍니다.

```cpp
// auto를 사용하면
auto ptr1{0}; // (△) 비권장. int
auto ptr2{NULL}; // (△) 비권장. long long
auto ptr3_11{nullptr}; // nullptr_t

EXPECT_TRUE(f(ptr1) == 1); // f(int) 호출  
EXPECT_TRUE(f(ptr2) == 1); // f(int) 호출
EXPECT_TRUE(f(ptr3_11) == 2); // f(int*) 호출
```

또한 다음과 같은 구문을 만났을때 좀더 명확한 분석이 가능합니다.

```cpp
auto result = Func();
if (result == 0) {} // (△) 비권장. result가 정수인지 포인터인지 불명확 합니다.
if (result == nullptr) {} // result는 포인터라는 것이 좀더 명확합니다.
```

# nullptr_t

[nullptr_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/#nullptr_t)는 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/)을 저장할 수 있는 타입이며, 어떠한 포인터로도 암시적으로 변환될 수 있는 타입입니다. 크기는 `sizeof(void*)`와 동일합니다.

```cpp
using nullptr_t = decltype(nullptr);
```