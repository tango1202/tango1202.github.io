---
layout: single
title: "#4. [모던 C++ STL] (C++11~) ratio"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `type_traits`가 추가되어 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 할 수 있습니다.

# 개요

보통 유리수(정수와 분수)는 실수로 표현되는데요, 부동 소수점 방식이라 연산에 오차가 있을 수 있습니다.

`ratio` 개체는 정확한 유리 분수를 표현하며, 유틸리티들을 이용하여 컴파일 타임 유리수(정수와 분수) 연산을 지원합니다.

|항목|내용|
|--|--|
|`ratio`|정확한 유리 분수를 표현합니다.|
|`ratio_add`|컴파일 타임에 `ratio`개체를 더합니다.|
|`ratio_subtract`|컴파일 타임에 `ratio`개체를 뺍니다.|
|`ratio_multiply`|컴파일 타임에 `ratio`개체를 곱합니다.|
|`ratio_divide`|컴파일 타임에 `ratio`개체를 나눕니다.|
|`ratio_equal`|컴파일 타임에 두 `ratio` 개체가 `==`인지 비교합니다.|
|`ratio_not_equal`|컴파일 타임에 두 `ratio` 개체가 `!=`인지 비교합니다.|
|`ratio_less`|컴파일 타임에 두 `ratio` 개체가 `<`인지 비교합니다.|
|`ratio_less_equal`|컴파일 타임에 두 `ratio` 개체가 `<=`인지 비교합니다.|
|`ratio_greater`|컴파일 타임에 두 `ratio` 개체가 `>`인지 비교합니다.|
|`ratio_greater_equal`|컴파일 타임에 두 `ratio` 개체가 `>=`인지 비교합니다.|

다음은 `1 / 100` 과 `99 / 100`을 오차 없이 정확히 1로 계산해 줍니다.
```cpp
enum class MyEnum {Val = 
    std::ratio_add< // 1/ 100 + 99 / 100 을 오차없이 계산 합니다.
        std::ratio<1, 100>, 
        std::ratio<99, 100>
    >::num
};    

EXPECT_TRUE(static_cast<int>(MyEnum::Val) == 1);
```

# ratio

유리수를 
```cpp
quecto (C++26)	std::ratio<1, 1000000000000000000000000000000> (10-30), if std::intmax_t can represent the denominator
ronto (C++26)	std::ratio<1, 1000000000000000000000000000> (10-27), if std::intmax_t can represent the


typedef ratio<1,       1000000000000000000> atto;
typedef ratio<1,          1000000000000000> femto;
typedef ratio<1,             1000000000000> pico;
typedef ratio<1,                1000000000> nano;
typedef ratio<1,                   1000000> micro;
typedef ratio<1,                      1000> milli;
typedef ratio<1,                       100> centi;
typedef ratio<1,                        10> deci;
typedef ratio<                       10, 1> deca;
typedef ratio<                      100, 1> hecto;
typedef ratio<                     1000, 1> kilo;
typedef ratio<                  1000000, 1> mega;
typedef ratio<               1000000000, 1> giga;
typedef ratio<            1000000000000, 1> tera;
typedef ratio<         1000000000000000, 1> peta;
typedef ratio<      1000000000000000000, 1> exa;

ronna (C++26)	std::ratio<1000000000000000000000000000, 1> (1027), if std::intmax_t can represent the numerator
quetta (C++26)	std::ratio<1000000000000000000000000000000, 1> (1030), if std::intmax_t can represent the numerator
```

# ratio_add 등 산술 연산