---
layout: single
title: "#29. [모던 C++] (C++11~) 템플릿 오른쪽 꺽쇠 괄호"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 파싱을 개선하여 템플릿 인스턴스화시 `>`가 중첩되어 `>>`와 같이 되더라도 공백을 추가할 필요가 없습니다.

# 개요

템플릿 인스턴스화시 템플릿 개체로 인스턴스화 하면 `>`이 중첩됩니다.

기존에는 이게 비트 Right Shift 연산자 `>>` 로 파싱되어 컴파일 되지 않았습니다. 따라서, `std::vector<A<int> >`와 같이 억지로 띄어쓰기를 했는데요([템플릿 파싱 오류](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98) 참고), 

C++11 부터는 파싱을 개선하여 `std::vector<A<int>>`와 같은 작성법도 지원합니다.

```cpp
template <typename T>
class A {};

std::vector<A<int> > a; // C++03. 공백을 추가해야 했습니다.
std::vector<A<int>> b; // C++11
```