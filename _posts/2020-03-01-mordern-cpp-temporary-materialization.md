---
layout: single
title: "#34. [모던 C++] (C++17~) 임시 구체화"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

기존에는 표현식의 다양한 상황에서 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 생성되며, 임시 개체의 불필요한 복사나 대입을 최소화 하기 위해 컴파일러가 자체적인 최적화를 해줬습니다.

1. 생성자 호출 최적화 및 함수 인수 전달 최적화

2. RVO

```cpp
class A {};
A a{A{}}; // A{}를 기본 생성자로 생성하고 a에 이동 생성 합니다. 하지만 컴파일러에 따라 a를 기본 생성자로 생성합니다.

void Func1(A param) {}
Func1(A{}); // A{}를 기본 생성자로 생성하고, param에 이동 생성 합니다. 하지만 컴파일러에 따라 param을 기본 생성자로 생성합니다.

A Func2() {return A{};}
A result = Func2(); // A{} 기본 생성자로 생성하고, result에 이동 생성합니다. 하지만 컴파일러에 따라 result를 기본 생성자로 생성합니다.
```


RVO
중괄호 복사 초기화 T t = {};, t = {};, f({}), return {} : 컴파일러 최적화에 의해 1회만 생성

T obj3 = T(2); // T obj3(2); 와 동일


임시 구체화. prvalue-to-xvalue 변환

glvalue가 있어야 할 자리라면 언제라도 prvalue가 유효하게 사용될 수 있으며, 임시 객체가 생성되어 prvalue로 초기화됩니다.

int f(int const&);
int r = f(3);