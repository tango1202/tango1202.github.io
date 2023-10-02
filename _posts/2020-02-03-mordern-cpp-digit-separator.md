---
layout: single
title: "#33. [모던 C++] (C++14~) 숫자 구분자"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.

큰 숫자인 경우 가독성이 좋아집니다.

```cpp
int val1 = 1000000;
int val2 = 1'000'000;

EXPECT_TRUE(val1 == val2);
```