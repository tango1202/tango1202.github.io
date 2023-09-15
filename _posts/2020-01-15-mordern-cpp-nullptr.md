---
layout: single
title: "#15. [모던 C++] (C++11~) nullptr, std::nullptr_t"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

* `nullptr` 리터럴이 추가되어 좀더 타입 안정적인 코딩 계약이 가능해 졌습니다.

# nullptr

기존에는 [널 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 표현하기 위해 `0`이나 `NULL`(`#define NULL 0`)을 사용했는데요, 

C++11 부터는 `nullptr` 리터럴이 제공됩니다.

```cpp
int* ptr1 = 0;
int* ptr2 = NULL;
int* ptr3 = nullptr; // C++11
```

`0`이나 `NULL`은 사실 포인터가 아니라 정수이기 때문에 만약 `auto`를 사용한다면 다음처럼 초기값에 따라 `int`나 `long long`으로 추론되는 문제가 있습니다. 그러니 앞으로는 `nullptr`을 사용하시기 바랍니다.


```cpp
int* ptr1 = 0;
int* ptr2 = NULL;
int* ptr3 = nullptr;

EXPECT_TRUE(f(ptr1) == 2); // int* 이므로 f(int*) 호출
EXPECT_TRUE(f(ptr2) == 2); // int* 이므로 f(int*) 호출
EXPECT_TRUE(f(ptr3) == 2); // int* 이므로 f(int*) 호출

// auto를 사용하면
auto ptr1 = 0; // int
auto ptr2 = NULL; // long long
auto ptr3 = nullptr; // nullptr_t

EXPECT_TRUE(f(ptr1) == 1); // f(int) 호출  
EXPECT_TRUE(f(ptr2) == 1); // f(int) 호출
EXPECT_TRUE(f(ptr3) == 2); // f(int*) 호출
```

# std::nullptr_t

`std::nullptr_t`는 `nullptr`을 저장할 수 있는 타입이며, 어떠한 포인터로도 암시적으로 변환될 수 있는 타입입니다. 크기는 `sizeof(void*)`와 동일합니다.

```cpp
using nullptr_t = decltype(nullptr);
```
