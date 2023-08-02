---
layout: single
title: "#2. [고전 C++ STL] 함수 템플릿"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

템플릿 함수는 다음처럼 정의하고 인스턴스화 합니다.

```cpp
template <typename T>
T Plus(T left, T right) {
    return left + right;
}

EXPECT_TRUE(Plus<int>(10, 10) == 20);
EXPECT_TRUE(Plus<char>('a', 1) == 'b');
```

명시적인 타입을 생략하고 인수의 타입에 따라 추론이 가능합니다. 

```cpp
EXPECT_TRUE(Plus(10, 10) == 20); // (O) 인수로부터 int가 추론됨
EXPECT_TRUE(Plus<>(10, 10) == 20); // (O) <>를 기재하여 템플릿 함수를 명시하고, 인수로부터 추론

EXPECT_TRUE(Plus('a', 1) == 'b'); // (X) 인수가 int, char로 각각 다르므로 추론이 어려움 
EXPECT_TRUE(Plus('a', static_cast<char>(1)) == 'b'); // (O) 인수로부터 char 가 추론됨
```

템플릿 인수 추론의 좀더 상세한 내용은 [템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)을 참고하시기 바랍니다.


