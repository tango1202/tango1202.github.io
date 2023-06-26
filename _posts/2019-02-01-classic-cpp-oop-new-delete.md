---
layout: single
title: "#6. [고전 C++ 가이드] 생성과 소멸"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

`operator new`와 `operator delete`


위치 지정 생성((Placement New))
T* p = new(buf) T; // 명시적 delete 호출