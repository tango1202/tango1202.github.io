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

템플릿 인스턴스화시 템플릿 개체로 인스턴스화 하면 `>`이 중첩되는데요, C++03은 이게 비트 Right Shift 연산자 `>>` 로 파싱되어 컴파일 되지 않았습니다. 따라서, ``std::vector<A<int> >`와 같이 억지로 띄어쓰기를 했는데요, C++11에서는 파싱을 개선하여 `std::vector<A<int>>`와 같은 작성법도 지원합니다.

```cpp
template <typename T>
class A {};

std::vector<A<int> > a; // C++03. 공백을 추가해야 했습니다.
std::vector<A<int>> b; // C++11
```