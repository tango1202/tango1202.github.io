---
layout: single
title: "#1. [고전 C++ 가이드] 기본 자료형"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

**자료형 종류**

C++에는 하기의 기본 자료형들이 있습니다. 

|항목|내용|크기|
|--|--|--|
|`bool`|`true`, `false`|`1 <= sizeof (bool) <= sizeof(long)`|
|`char`|1byte 문자| 1byte|
|`short`|2byte 정수| 2byte|
|`int`|기본 연산 단위 크기의 정수|16bit : 2byte, 32bit : 4byte, 64bit : 4byte|
|`unsigned`|부호 없는 int|int 와 동일|
|`long`|int보다 크거나 같은 정수|16bit : 4byte, 32bit : 4byte, 64bit : 8byte|
|`float`|부동 소수점 실수|3.4E +/- 38 (7 digits)|
|`double`|부동 소수점 실수|1.7E +/- 308 (15 digits)|
|`long double`|`double`과 동일|double 과 동일|
|`*`|포인터형||
|`&`|참조형||
|`enum`|열거형 상수||

그외 `signed`, `unsigned` 와 결합하여 다양한 조합이 나올 수 있습니다.

**자료형 재정의**


