---
layout: single
title: "#4. [고전 C++ 가이드] BOOL과 bool"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

|항목|BOOL|bool|
|--|--|--|
|정의|Visual C++ `define`|C++ 표준 자료형|
|값|`#define TRUE 1`, `#define FALSE 0`| true, false|
|용량|sizeof(BOOL) == sizeof(int)|1 <= sizeof(bool) <= sizeof(long)(Visual C++ 은 1byte)|


