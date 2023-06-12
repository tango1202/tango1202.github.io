---
layout: single
title: "#3. [고전 C++ 가이드] 기본 자료형"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

**자료형 종류**

C++에는 하기의 기본 자료형들이 있습니다. 

|항목|내용|용량|
|--|--|--|
|`bool`|`true`, `false`|`1 <= sizeof (bool) <= sizeof(long)`|
|`char`|1byte 문자| 1byte|
|`short`|2byte 정수| 2byte|
|`int`|기본 연산 단위 크기의 정수|16bit : 2byte, 32bit : 4byte, 64bit : 4byte|
|`unsigned`|부호 없는 `int`|`int` 와 동일|
|`long`|`int`보다 크거나 같은 정수|16bit : 4byte, 32bit : 4byte, 64bit : 8byte|
|`float`|단정밀도 부동 소수점 실수|4byte|
|`double`|배정밀도 부동 소수점 실수|8byte|
|`long double`|확장정밀도 부동소수점 실수|`double` 과 동일|
|`*`|포인터형|32bit : 4byte, 64bit : 8byte|
|`&`|참조자|참조하는 개체의 별칭으로서 해당 용량은 스펙에 정의되지 않음. 다만, `sizeof()`시 참조하는 개체와 동일 크기를 리턴하도록 스펙에 정의됨.(`sizeof(T&) == sizeof(T)`)|
|`enum`|열거형 상수|모든 열거자 값을 나타낼 수 있는 정수 형식의 크기. (보통 4byte)|

그외 `signed`, `unsigned` 와 결합하여 다양한 조합이 나올 수 있습니다.

**자료형 재정의**

typedef

**자료형 크기**

sizeof

**자료형 최대/최소값**

