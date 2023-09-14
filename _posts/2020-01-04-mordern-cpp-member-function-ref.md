---
layout: single
title: "#4. [모던 C++] (C++11~) 멤버 함수 참조 지정자"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 멤버 함수에 `&`, `&&` 참조 지정자가 추가되어 좌측값으로 호출될때와 우측값으로 호출될때를 오버로딩 할 수 있습니다.


# 개요

멤버 함수에 `&`, `&&` 참조 지정자가 추가되어 좌측값으로 호출될때와 우측값으로 호출될때를 오버로딩 할 수 있습니다.

```cpp
class T {
public:
    int Func() & {return 1;} // #1. 좌측값에서 호출
    int Func() && {return 2;} // #2. 우측값에서 호출
};

T t;
EXPECT_TRUE(t.Func() == 1);
EXPECT_TRUE(std::move(t).Func() == 2);
EXPECT_TRUE(T{}.Func() == 2);
```
