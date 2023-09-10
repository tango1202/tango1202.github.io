---
layout: single
title: "#19. [모던 C++] (C++11~) 가변 템플릿, 파라메터(인자) 팩"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 가변 인자를 활용한 가변 템플릿이 추가되어 코딩 자유도가 높아졌습니다.

# 개요

[인자(매개변수, Parameter)](
https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 에서 `...`을 이용한 가변 인자를 소개해 드렸는데요, C++11에서는 템플릿에서도 가변 인자를 사용할 수 있고, 이를 가변 템플릿 이라 합니다. 가변 템플릿은 `...`으로 표기되는 파라메터 팩을 가져야 합니다.

가변 템플릿을 이용해서 `Sum` 을 구현하면 다음처럼 할 수 있습니다.

```cpp
template<typename Type>
Type Sum(Type param) {
    return param; // 재귀 호출하다가 마지막에 도달하면 호출됩니다.
}
template<typename Type, typename... Types>
Type Sum(Type param, Types... params) {
    return param + Sum(params...); // 재귀 호출시 params의 첫번째 인자는 param으로 전달되고, 나머지는 params에 전달됩니다.
}

int val = Sum(1, 3, 5);
EXPECT_TRUE(val == 1 + 3 + 5); 
```

# sizeof...() 연산자

`sizeof()`연산자는 개체의 크기를 리턴하는데요, `sizedof...()`은 파라메터 팩의 인자수를 리턴합니다.

```cpp
template<typename... Types>
int Func(Types... params) {
    return sizeof...(params);
}

EXPECT_TRUE(Func(1, 2, 3) == 3);
```