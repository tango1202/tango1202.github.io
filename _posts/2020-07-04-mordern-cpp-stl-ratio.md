---
layout: single
title: "#4. [모던 C++ STL] (C++11~) ratio"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `ratio` 개체는 분자와 분모를 따로 저장하여 유리 분수를 표현하며, 유틸리티들을 이용하여 컴파일 타임 유리수(정수와 분수) 연산을 지원합니다.

# 개요

보통 유리수(정수와 분수)에서 분수는 산술 연산할때 실수로 바꿔서 계산하는데요, 부동 소수점 방식이라 연산에 오차가 있을 수 있습니다.

`ratio` 개체는 분자와 분모를 따로 저장하여 유리 분수를 표현하며, 유틸리티들을 이용하여 컴파일 타임 유리수(정수와 분수) 연산을 지원합니다.

|항목|내용|
|--|--|
|`ratio` (C++11~)|정확한 유리 분수를 표현합니다. `ratio::num`은 분자, `ratio::den`은 분모입니다.|
|`ratio_add` (C++11~)|컴파일 타임에 `ratio`개체를 더합니다.|
|`ratio_subtract` (C++11~)|컴파일 타임에 `ratio`개체를 뺍니다.|
|`ratio_multiply` (C++11~)|컴파일 타임에 `ratio`개체를 곱합니다.|
|`ratio_divide` (C++11~)|컴파일 타임에 `ratio`개체를 나눕니다.|
|`ratio_equal` (C++11~)|컴파일 타임에 두 `ratio` 개체가 `==`인지 비교합니다.|
|`ratio_not_equal` (C++11~)|컴파일 타임에 두 `ratio` 개체가 `!=`인지 비교합니다.|
|`ratio_less` (C++11~)|컴파일 타임에 두 `ratio` 개체가 `<`인지 비교합니다.|
|`ratio_less_equal` (C++11~)|컴파일 타임에 두 `ratio` 개체가 `<=`인지 비교합니다.|
|`ratio_greater` (C++11~)|컴파일 타임에 두 `ratio` 개체가 `>`인지 비교합니다.|
|`ratio_greater_equal` (C++11~)|컴파일 타임에 두 `ratio` 개체가 `>=`인지 비교합니다.|

다음은 `1 / 100` 과 `99 / 100`을 더했을 때 오차 없이 분자와 분모 모두 정확히 `1`로 계산해 줍니다.

```cpp
using Result = std::ratio_add< // 1/ 100 + 99 / 100 을 오차없이 계산 합니다.
    std::ratio<1, 100>, 
    std::ratio<99, 100>
>;
enum class MyEnum {Val1 = Result::num, Val2 = Result::den};

// 분자와 분모가 모두 1입니다.
EXPECT_TRUE(static_cast<int>(MyEnum::Val1) == 1 && static_cast<int>(MyEnum::Val2) == 1);
```

`ratio` 는 약분값을 사용하기 때문에, `1/3`이나 `2/3`은 같은 값으로 평가됩니다.

```cpp
using Result = std::ratio_equal<
    std::ratio<1, 3>, 
    std::ratio<2, 6>
>;
EXPECT_TRUE(Result::value == true);
```

# 미리 정의된 ratio 타입

`ratio`는 표준에 다음과 같이 미리 정의되어 있습니다.

유리수를 
```cpp
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
```

