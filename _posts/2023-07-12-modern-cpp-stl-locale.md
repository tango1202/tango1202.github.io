---
layout: single
title: "#12. [모던 C++ STL] 현지화"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [isblank()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)가 추가되었습니다.
> * (C++11~C++17) [문자열 현지화 변환](https://tango1202.github.io/cpp-stl/modern-cpp-stl-locale/#c11-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%98%84%EC%A7%80%ED%99%94-%EB%B3%80%ED%99%98)(`wstring_convert`, `wbuffer_convert`, `codecvt_utf8`, `codecvt_utf16`, `codecvt_utf8_utf16`, `codecvt_mode`)이 기존 구현이 정상적으로 되었는지 확인되지 않아 deprecate 되었습니다.

# C스타일 Locale

|항목|내용|
|--|--|
|[setlocale()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|로케일 혹은 인코딩 정보를 설정합니다.|
|`LC_ALL`<br>`LC_COLLATE`<br>`LC_CTYPE`<br>`LC_MONETARY`<br>`LC_NUMERIC`<br>`LC_TIME`|(작성중)|
|`localeconv()`|(작성중)|
|`lconv()`|(작성중)|

# Locale

|항목|내용|
|--|--|
|`locale`|(작성중)|
|`use_facet()`|(작성중)|
|`has_facet()`|(작성중)|

# Facet 카테고리

|항목|내용|
|--|--|
|`ctype_base`|(작성중)|
|`codecvt_base`|(작성중)|
|`messages_base`|(작성중)|
|`time_base`|(작성중)|
|`money_base`|(작성중)|

# Facet

각 `locale`에 따른 문자, 숫자, 문자 비교, 화폐, 시간의 처리 방법을 정의합니다.

|항목|내용|
|--|--|
|`ctype`|(작성중)|
|`ctype_byname`|(작성중)|
|`codecvt`|(작성중)|
|`codecvt_byname`|(작성중)|
|`num_get`|(작성중)|
|`num_put`|(작성중)|
|`numpunct`|(작성중)|
|`numpunct_byname`|(작성중)|
|`collate`|(작성중)|
|`collate_byname`|(작성중)|
|`time_get`|(작성중)|
|`time_get_byname`|(작성중)|
|`time_put`|(작성중)|
|`time_put_byname`|(작성중)|
|`money_get`|(작성중)|
|`money_put`|(작성중)|
|`moneypunct`|(작성중)|
|`messages`|(작성중)|
|`messages_byname`|(작성중)|

# 문자 분류(Locale)

|항목|내용|
|--|--|
|`isspace()`|(작성중)|
|`isblank()` (C++11~)|(작성중)|
|`iscntrl()`|(작성중)|
|`isupper()`|(작성중)|
|`islower()`|(작성중)|
|`isalpha()`|(작성중)|
|`isdigit()`|(작성중)|
|`ispunct()`|(작성중)|
|`isxdigit()`|(작성중)|
|`isalnum()`|(작성중)|
|`isprint()`|(작성중)|
|`isgraph()`|(작성중)|

# 문자 조작(Locale)

|항목|내용|
|--|--|
|`toupper()`|(작성중)|
|`tolower()`|(작성중)|

# (C++11~) 문자열 현지화 변환

|항목|내용|
|--|--|
|`wstring_convert`(C++11~C++17)|(작성중)|
|`wbuffer_convert`(C++11~C++17)|(작성중)|
|`codecvt_utf8`(C++11~C++17)|(작성중)|
|`codecvt_utf16`(C++11~C++17)|(작성중)|
|`codecvt_utf8_utf16`(C++11~C++17)|(작성중)|
|`codecvt_mode`(C++11~C++17)|(작성중)|

