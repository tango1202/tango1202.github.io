---
layout: single
title: "#18. [모던 C++] (C++11~) long long"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) 최소 8byte 크기를 보장하는 [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/) 타입이 추가되었습니다.
> * (C++11~) [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)용 정수형 상수인 `ll`, `ull`, `LL`, `ULL` 리터럴이 추가되었습니다.

# 개요

기존 `long`은 시스템 비트수에 따라 변하는데요([기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 참고), 

C++11 부터는 [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)을 제공하며 최소 8byte를 보장합니다.

|항목|내용|용량|
|--|--|--|
|`long`|`int`보다 크거나 같은 정수|16bit : 4byte,<br/>32bit : 4byte,<br/>64bit : 8byte|
|[long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/) (C++11~)|`long`보다 크거나 같은 정수|최소 8byte|

```cpp
unsigned long long val_11{18446744073709550592ull}; 
```