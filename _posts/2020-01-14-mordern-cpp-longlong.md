---
layout: single
title: "#14. [모던 C++] (C++11~) long long"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 최소 8byte 크기를 보장하는 `long long` 타입이 추가되었습니다.

# long long

기존 `long`은 시스템 비트수에 따라 변하지만 `long long`은 최소 8byte를 보장합니다.

|항목|내용|용량|
|--|--|--|
|`long`|`int`보다 크거나 같은 정수|16bit : 4byte,<br/>32bit : 4byte,<br/>64bit : 8byte|
|`long long`|`long`보다 크거나 같은 정수|최소 8byte|


