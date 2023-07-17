---
layout: single
title: "#12. [C++ 코딩 패턴] 스마트 포인터"
categories: "cpp-coding-pattern"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


자원 획득은 초기화이다.

RAII는 C++에서 자주 쓰이는 idiom으로 자원의 안전한 사용을 위해 객체가 쓰이는 스코프를 벗어나면 자원을 해제해주는 기법이다.