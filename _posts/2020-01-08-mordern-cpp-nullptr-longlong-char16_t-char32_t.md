---
layout: single
title: "#8. [모던 C++] (C++11~) nullptr, nullptr_t, long long, char16_t, char32_t"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# nullptr

기존에는 널 포인터를 표현하기 위해 `0`이나 `NULL`(`#define NULL 0`)을 사용했으나, C++11 부터는 `nullptr` 리터럴이 제공됩니다.

```cpp
int* ptr1 = 0;
int* ptr2 = NULL;
int* ptr3 = nullptr;
```

`0`이나 `NULL`은 사실 포인터가 아니라 정수이기 때문에 만약 `auto`를 사용한다면 다음처럼 초기값에 따라 `int`나 `long long`으로 추론되는 문제가 있습니다. 그러니 앞으로는 `nullptr`
을 사용하시기 바랍니다.


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

# nullptr_t

`nullptr_t`는 `nullptr`을 저장할 수 있는 타입이며, 어떠한 포인터로도 암시적으로 변환될 수 있는 타입입니다. 크기는 `sizeof(void*)`와 동일합니다.

```cpp
using nullptr_t = decltype(nullptr);
```

# long long

기존 `long`은 시스템 비트수에 따라 변하지만 `long long`은 최소 8byte를 보장합니다.

|항목|내용|용량|
|--|--|--|
|`long`|`int`보다 크거나 같은 정수|16bit : 4byte,<br/>32bit : 4byte,<br/>64bit : 8byte|
|`long long`|`long`보다 크거나 같은 정수|최소 8byte|

# char16_t 와 char32_t

기존 `wchar_t`는 시스템 비트수에 따라 가변적이어서 2byte와 4byte 크기로 다루기 위한 `char16_t`와 `char32_t`가 추가되었습니다.

|항목|내용|용량|
|--|--|--|
|`char`|1byte 문자|1byte|
|`wchar_t`|와이드 문자|시스템의 비트수에 따라 다르며, 대부분 2byte 또는 4byte.<br/>단, Windows는 2byte|
|`char16_t`| UTF-16 문자|`16bit`(2byte) 보다 크거나 같음|
|`char32_t`| UTF-32 문자|`32bit`(4byte) 보다 크거나 같음|


