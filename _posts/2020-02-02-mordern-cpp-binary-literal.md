---
layout: single
title: "#32. [모던 C++] (C++14~) 이진 리터럴"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++14~) `0b`, `0B` 접두어를 이용하여 이진수 상수를 표현할 수 있습니다.

# 개요

`0b`, `0B` 접두어를 이용하여 이진수 상수를 표현할 수 있습니다.


```cpp
int val1{0b11};
int val2{0b1111};
int val3{0B11111111};

EXPECT_TRUE(val1 == 0x03);
EXPECT_TRUE(val2 == 0x0F);
EXPECT_TRUE(val3 == 0xFF);
``````