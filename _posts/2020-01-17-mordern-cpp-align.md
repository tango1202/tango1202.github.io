---
layout: single
title: "#17. [모던 C++] (C++11~) alignas(), alignof()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `alignas()` 와 `alignof()` 를 이용하여 메모리 정렬 방식을 표준화 했습니다.

# 개요

기존에는 `#pragma pack`을 이용하여 비표준 방식으로 **메모리 정렬(Memory Alignment)** 을 했습니다.([개체 크기와 메모리 정렬](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC) 참고) 

C++11부터는 `alignas()` 와 `alignof()` 로 이를 표준화하였습니다.

|항목|내용|
|--|--|
|`alignof()`|주어진 타입의 메모리 정렬 크기를 구합니다.|
|`alignas()`|주어진 값으로 메모리 정렬합니다. 단, 2, 4, 8, 16... 단위로 정렬하며, 내부 멤버중 제일 큰 값보다 작으면 무시됩니다.|

```cpp
// 4byte 단위로 정렬합니다.
class alignas(alignof(int)) A {
    char m_A;
    int m_B;
};

// 2, 4, 8, 16... 단위로 정렬하며, 내부 멤버중 제일 큰값보다 커야 합니다.
class alignas(8) B {
    char m_A[13];
    int m_B; 
};

// int 보다 적은값이므로 무시되고 alignof(int) 크기로 정렬됩니다.
class alignas(2) C {
    char m_A[13];
    int m_B; 
};


// 4byte 단위로 멤버 변수가 할당 되므로 char(1) + int(4) = 5 이므로 4 * 2 개 영역에 할당됨
EXPECT_TRUE(alignof(A) == 4 && sizeof(A) == 4 * 2);

// 8byte 단위로 멤버 변수가 할당 되므로 13 + int(4) = 17 이므로 8 * 3 개 영역에 할당됨
EXPECT_TRUE(alignof(B) == 8 && sizeof(B) == 8 * 3); 

// 4byte 단위로 멤버 변수가 할당 되므로 13 + int(4) = 17 이므로 4 * 5 개 영역에 할당됨
EXPECT_TRUE(alignof(C) == 4 && sizeof(C) == 4 * 5); 
```
