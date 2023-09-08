---
layout: single
title: "#5. [모던 C++] (C++11~) non-trivial 공용체와 varient"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

**개요**

[공용체](
https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 에서의 언급처럼, 기존에는 생성자/소멸자/가상 함수가 없는 trivial 타입만 공용체의 멤버가 될 수 있었으나, C++11 부터는 소멸자와 Placement New(위치 지정 생성)로 활성 멤버를 전환하여 사용할 수 있습니다.


