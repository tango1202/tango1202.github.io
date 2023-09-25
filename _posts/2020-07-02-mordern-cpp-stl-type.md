---
layout: single
title: "#2. [모던 C++ STL] 타입"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 기본 타입

|항목|내용|
|--|--|
|`size_t`|`unsigned int` 으로서 크기나 갯수를 나타냅니다.|
|`ptrdiff_t`|`signed int` 으로서 포인터간의 차를 나타냅니다.|
|[nullptr_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) (C++11~)|`nullptr`을 저장하는 타입입니다.|
|NULL|널포인터의 값이며, `#define NULL 0` 입니다.|
`max_align_t` (C++11~)|(작성중)|
|`offsetof()`|(작성중)|
|`byte` (C++17~)|(작성중)|

# 고정 너비 정수 타입

|항목|내용|
|--|--|
|`int8_t` (C++11~)<br/>`int16_t` (C++11~)<br>`int32_t` (C++11~)<br/>`int64_t` (C++11~)|(작성중)|
|`int_fast8_t` (C++11~)<br/>`int_fast16_t` (C++11~)<br>`int_fast32_t` (C++11~)<br/>`int_fast64_t` (C++11~)|(작성중)|
|`int_least8_t` (C++11~)<br/>`int_least16_t` (C++11~)<br>`int_least32_t` (C++11~)<br/>`int_least64_t` (C++11~)|(작성중)|
|`intmax_t` (C++11~)|(작성중)|
|`intptr_t` (C++11~)|(작성중)|
|`uint8_t` (C++11~)<br/>`uint16_t` (C++11~)<br>`uint32_t` (C++11~)<br/>`uint64_t` (C++11~)|(작성중)|
|`uint_fast8_t` (C++11~)<br/>`uint_fast16_t` (C++11~)<br>`uint_fast32_t` (C++11~)<br/>`uint_fast64_t` (C++11~)|(작성중)|
|`uint_least8_t` (C++11~)<br/>`uint_least16_t` (C++11~)<br>`uint_least32_t` (C++11~)<br/>`uint_least64_t` (C++11~)|(작성중)|
|`uintmax_t` (C++11~)|(작성중)|
|`uintptr_t` (C++11~)|(작성중)|

# 고정 너비 실수 타입

|항목|내용|
|--|--|
|`float16_t` (C++23~)|(작성중)|
|`float32_t` (C++23~)|(작성중)|
|`float64_t` (C++23~)|(작성중)|
|`float128_t` (C++23~)|(작성중)|
|`bfloat16_t` (C++23~)|(작성중)|

# 타입 최대/최소값

|항목|내용|
|--|--|
|[numeric_limits](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C%EA%B0%92)| 타입의 최대, 최소값을 알 수 있습니다.|


# 런타임 타입

|항목|내용|
|--|--|
|`type_info`|개체의 타입 정보입니다.([typeid 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)|
|`bad_typeid`|널포인터를 역참조하여 typeid를 구할때 발생합니다.([표준 예외](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-object/#%ED%91%9C%EC%A4%80-%EC%98%88%EC%99%B8) 참고)<br/>`T* p = NULL;`<br/>`typeid(*p).name();`|
|`bad_cast`|`dynamic_cast`의 실패시 발생합니다.([표준 예외](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-object/#%ED%91%9C%EC%A4%80-%EC%98%88%EC%99%B8) 참고)|
|[type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_index/) (C++11~)|`type_info`의 래퍼로서 `type_info`를 컨테이너에서 관리할 수 있게 합니다.|

