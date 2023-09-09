---
layout: single
title: "#9. [모던 C++] (C++11~) 유니코드 문자열(char16_t, char32_t)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# char16_t 와 char32_t

기존 `wchar_t`는 시스템 비트수에 따라 가변적이어서 2byte와 4byte 크기로 다루기 위한 `char16_t`와 `char32_t`가 추가되었습니다.

|항목|내용|용량|
|--|--|--|
|`char`|1byte 문자|1byte|
|`wchar_t`|와이드 문자|시스템의 비트수에 따라 다르며, 대부분 2byte 또는 4byte.<br/>단, Windows는 2byte|
|`char16_t`| UTF-16 문자|`16bit`(2byte) 보다 크거나 같음|
|`char32_t`| UTF-32 문자|`32bit`(4byte) 보다 크거나 같음|


