---
layout: single
title: "#28. [모던 C++] (C++11~) 멤버 sizeof()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [sizeof() 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 개체를 인스턴스화 하지 않더라도 멤버의 크기를 구할 수 있도록 개선되었습니다.

# 개요

```cpp
class T {
public:
    int m_X;
};

// C++03 에서는 컴파일 오류
// C++11 부터 허용
EXPECT_TRUE(sizeof(T::m_X) == sizeof(int));
```