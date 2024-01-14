---
layout: single
title: "#44. [모던 C++ STL] (C++20~) 포맷팅"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [format(), format_to(), format_to_n()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/??#%EC%84%9C%EC%8B%9D%ED%99%94-%ED%95%A8%EC%88%98)이 추가되어 `%d, %s` 처럼 간편하고, `<<` 처럼 확장성 있는 서식화 방법을 지원합니다.
> * (C++20~) [표준 서식 지정자](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/??#%ED%91%9C%EC%A4%80-%EC%84%9C%EC%8B%9D-%EC%A7%80%EC%A0%95%EC%9E%90)가 추가되어 채움, 정렬, 부호, 너비, 정밀도를 지정할 수 있습니다.
> * (C++20~) [chrono 서식 지정자](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/??#chrono-%EC%84%9C%EC%8B%9D-%EC%A7%80%EC%A0%95%EC%9E%90)가 추가되었습니다.
> * (C++20~) [formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter)가 추가되어 특정 타입에 대한 서식을 정의할 수 있습니다.

# 개요 

기존에는 개체의 내용을 서식화할때 [C스타일 입출력](https://tango1202.github.io/cpp-stl/modern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5)의 [printf()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5) 계열에서 `%d, %s`등을 이용한 방법과 `<<`에서 [setprecision()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-input-output/#%EC%84%9C%EC%8B%9D-%EC%A1%B0%EC%A0%95%EC%9E%90)등 [서식 조정자](https://tango1202.github.io/cpp-stl/modern-cpp-stl-input-output/#%EC%84%9C%EC%8B%9D-%EC%A1%B0%EC%A0%95%EC%9E%90)를 이용한 방법이 있었습니다.(*[서식 조정자](https://tango1202.github.io/cpp-stl/modern-cpp-stl-input-output/#%EC%84%9C%EC%8B%9D-%EC%A1%B0%EC%A0%95%EC%9E%90) 참고*)

하지만, `%d, %s`는 간편하지만 [기본 타입](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/)만 사용할 수 있기 때문에 확장성이 낮고, `<<`은 사용자 정의 타입도 확장할 수 있지만 너무 코드가 장황해 지는 문제가 있었습니다.

C++20 부터는 [포맷팅 라이브러리](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/)가 추가되어 `%d, %s` 처럼 간편하고, `<<` 처럼 확장성 있는 방법을 지원합니다.

```cpp
int a{1};
int b{2};

// {0}, {1}, {2}에 인수를 순서대로 출력합니다. {2}는 16진수로 출력합니다.
// a : 1, b : 2, add : 0x3
std::cout << std::format("a : {0}, b : {1}, add : {2:#x}", a, b, a + b) << std::endl;        
```

# 서식화 함수

[format(), format_to(), format_to_n()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/??#%EC%84%9C%EC%8B%9D%ED%99%94-%ED%95%A8%EC%88%98)는 서식화된 결과를 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이나 [이터레이터](https://tango1202.github.io/cpp-stl/modern-cpp-stl-iterator/)로 제공합니다.

|항목|내용|
|--|--|
|`format()` (C++20~)|서식화한 문자열을 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)으로 리턴합니다.|
|`format_to()` (C++20~)|서식화한 문자열을 [이터레이터](https://tango1202.github.io/cpp-stl/modern-cpp-stl-iterator/)에 기록합니다.|
|`format_to_n()` (C++20~)|서식화한 문자열을 N개 만큼 [이터레이터](https://tango1202.github.io/cpp-stl/modern-cpp-stl-iterator/)에 기록합니다.|
|`formatted_size()` (C++20~)|서식화한 문자열의 길이를 구합니다.|

다음 예는 [서식화 함수](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#%EC%84%9C%EC%8B%9D%ED%99%94-%ED%95%A8%EC%88%98)를 사용한 예입니다. [이터레이터](https://tango1202.github.io/cpp-stl/modern-cpp-stl-iterator/)사용시 [back_inserter()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%82%BD%EC%9E%85-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 이용하여 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)개체 뒤에 서식화한 문자열을 삽입합니다.


```cpp
int a{10};
std::string result1{std::format("a : {0}", a)};
EXPECT_TRUE(result1 == "a : 10");

std::string result2;
std::format_to(
    std::back_inserter(result2), // 출력할 이터레이터
    "a : {0}", // 서식 문자열
    a // 출력할 인수
);
EXPECT_TRUE(result2 == "a : 10");

std::string result3;
std::format_to_n(
    std::back_inserter(result3), // 출력할 이터레이터
    3, // 문자수 
    "a : {0}", // 서식 문자열
    a // 출력할 인수
);
EXPECT_TRUE(result3 == "a :"); // 문자 3개만 출력합니다.

auto length{std::formatted_size("a : {0}", a)}; // 서식화 했을때의 길이를 구합니다.
std::string result4;
std::format_to_n(
    std::back_inserter(result4), // 출력할 이터레이터
    length, // 문자수 
    "a : {0}", // 서식 문자열
    a // 출력할 인수
);
EXPECT_TRUE(result4 == "a : 10");
```

# 서식 문자열

[서식 문자열](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#%EC%84%9C%EC%8B%9D-%EB%AC%B8%EC%9E%90%EC%97%B4)은 [format(), format_to(), format_to_n()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/??#%EC%84%9C%EC%8B%9D%ED%99%94-%ED%95%A8%EC%88%98)에서 서식을 지정하기 위해 사용하며, 일반적인 문자열은 그대로 표시하고, `{}`인 부분은 인수로 대체되어 출력됩니다. 

`{}`에 인수의 인덱스를 지정할 수 있으며, 모두 생략하면 순서대로 표시됩니다. 다만, 인덱스 지정과 생략을 섞어서 사용할 수는 없습니다.

```cpp
EXPECT_TRUE(std::format("data:{},{},{}", 0, 10, 20) == "data:0,10,20");
EXPECT_TRUE(std::format("data:{2},{1},{0}", 0, 10, 20) == "data:20,10,0");
EXPECT_TRUE(std::format("data:{0},{0},{1}", 0, 10, 20) == "data:0,0,10"); // 특정 인덱스를 여러번 사용할 수 있습니다.

// std::string str{std::format("data:{},{1},{2}", 0, 10, 20)}; // (X) 컴파일 오류. 인덱스 지정과 생략을 섞어서 사용할 수는 없습니다.
```

|항목|내용|
|--|--|
|`basic_format_string, format_string, wformat_string` (C++20~)|서식 문자열입니다.|
|`runtime_format()` (C++26~)|(작성중)|

# 표준 서식 지정자

[서식 문자열](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#%EC%84%9C%EC%8B%9D-%EB%AC%B8%EC%9E%90%EC%97%B4)의 `{}`안에 `:`뒤에 [표준 서식 지정자](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/??#%ED%91%9C%EC%A4%80-%EC%84%9C%EC%8B%9D-%EC%A7%80%EC%A0%95%EC%9E%90)을 적용하여 채움, 정렬, 부호, 너비, 정밀도를 지정할 수 있습니다.

`{[인수 인덱스]:[채움과 정렬][부호][#][0][너비와 정밀도][타입]}`

* 모든 항목은 생략 가능합니다.
* `[채움과 정렬][부호][# 과 0][너비와 정밀도][타입]`을 지정하고 싶은 경우엔 `:`을 사용합니다.

|항목|내용|사용예|결과|
|--|--|--|--|
|채움과 정렬|채울 문자를 지정합니다. 기본으로 공백 문자가 사용되며, `{`와 `}`는 사용할 수 없습니다. 문자 뒤에는 `<, >, ^`의 정렬 옵션이 와야 합니다.<br/>* `<` : 왼쪽으로 정렬합니다.(*문자열 기본값*)<br/>* `>` : 오른쪽으로 정렬합니다.(*숫자 기본값*)<br/>* `^` : 가운데로 정렬합니다.|`std::format("{0:*<5}", 3)`|`3****`|
|부호|* `+` : `0`과 양수인 경우 `+`를 표시합니다.<br/>* `-` : 음수인 경우 `-`를 표시합니다.(*기본값*)|`std::format("{0:*>+5}", 3)`|`***+3`|
|`#`|정수인 경우 진법에 따라, `0b`(*이진수*), `0`(*8진수*), `0x`(*16진수*)를 표시하고, 부동 소수점인 경우 항상 소수점 이하를 표시합니다.|`std::format("{0}, {0:#b}, {0:#o}, {0:#x}", 2)`|`2, 0b10, 02, 0x2`|
|`0`|정수나 부동소수점에서 빈 공간에 맞춰 `0`을 표시합니다.|`std::format("{0:+05}", 3)`|`+0003`|
|너비와 정밀도|최소 너비나 정밀도(*소수점 이하 자리수*)를 지정합니다.<br/>정밀도는 앞에 `.`을 사용합니다.<br/>`{}`을 사용하여 인수로 너비와 정밀도를 전달받을 수 있습니다.|`std::format("{0:*>8.4f}", 3.141592)`|`**3.1416`|
|문자열 타입|`s`(*기본값*)|||
|문자 타입|`c`(*기본값*)|||
|정수 타입|* `b, B` : 이진수<br>* `d` : 십진수(*기본값*)<br/>* `o` : 8진수<br/>*`x, X` : 16진수|`std::format("{0:#x}", 2)`|`0x2`|
|[bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/) 타입|`s`(*기본값*)|`std::format("{0:s}", true)`|`true`|
|부동 소수점 타입|* `a, A` : 16진수로 표시합니다.<br> * `e, E` : 지수로 표시합니다.<br/>* `f, F` : 고정폭 으로 표시합니다. 기본 정밀도 6입니다.(*기본값 `.8f`*)<br/>* `g, G` : 자동으로 소수점 이하가 없으면 정수로 표시하고, 상황에 따라 지수나 고정폭으로 표시합니다. 기본 정밀도는 소수점을 포함하여 6입니다.|`std::format("{0:.8f}", 123456789.123456789)`|`123456789.12345679`|
|포인터 타입|`p`|||

다음은 [표준 서식 지정자](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/??#%ED%91%9C%EC%A4%80-%EC%84%9C%EC%8B%9D-%EC%A7%80%EC%A0%95%EC%9E%90)를 사용한 예입니다.

```cpp
// 채움과 정렬
EXPECT_TRUE(std::format("{0:*<5}", 3) == "3****"); // 5자리 너비에서 빈공간은 *로 채웁니다.
EXPECT_TRUE(std::format("{0:*>5}", 3) == "****3");
EXPECT_TRUE(std::format("{0:*^5}", 3) == "**3**");

// 인수 인덱스 생략
EXPECT_TRUE(std::format("{:*<5}, {:*>5}, {:*^5}", 3, 4, 5) == "3****, ****4, **5**"); // 인수 인덱스를 생략 할 수 있습니다.

// 부호
EXPECT_TRUE(std::format("{0:*>+5}", 3) == "***+3"); // 양수인 경우 +를 표시합니다.
EXPECT_TRUE(std::format("{0:+5}", 3) == "   +3"); // 빈공간은 디폴트로 공백 문자로 채웁니다. 숫자는 기본적으로 오른쪽 정렬입니다.
EXPECT_TRUE(std::format("{0:<+5}", 3) == "+3   "); // 왼쪽 정렬로 표시합니다.

// #
EXPECT_TRUE(std::format("{0}, {0:#b}, {0:#o}, {0:#x}", 2) == "2, 0b10, 02, 0x2"); // #은 정수에서 진법을 출력합니다.
EXPECT_TRUE(std::format("{0:g}", 3.) == "3"); // g는 소수점 이하가 없으면, 정수로 표시됩니다. 
EXPECT_TRUE(std::format("{0:#g}", 3.) == "3.00000"); // g에서 항상 소수점 이하를 출력합니다. 기본 정밀도는 소수점을 포함하여 6입니다.

// 0
EXPECT_TRUE(std::format("{0:0>+5}", 3) == "000+3"); // 채움과 정렬은 부호 앞에 표시됩니다.
EXPECT_TRUE(std::format("{0:+05}", 3) == "+0003"); // 대체 문자 0은 부호 뒤에 표시됩니다.

// 너비와 정밀도
EXPECT_TRUE(std::format("{0:*>10f}", 3.14) == "**3.140000"); // 너비만 지정
EXPECT_TRUE(std::format("{0:.4f}", 3.141592) == "3.1416"); // 정밀도만 지정
EXPECT_TRUE(std::format("{0:*>8.4f}", 3.141592) == "**3.1416"); // 너비와 정밀도 지정. 반올림하여 표시됨.

// 너비와 정밀도의 f, g 차이
EXPECT_TRUE(std::format("{0:*>8.4f}", 3.14) == "**3.1400"); // 소수점 이하가 14밖에 없어서 나머지는 0으로 채웁니다.
EXPECT_TRUE(std::format("{0:*>8.4g}", 3.14) == "****3.14"); // 소수점 이하가 14밖에 없어서 정밀도 4를 모두 채우지는 못합니다.
EXPECT_TRUE(std::format("{0:*>#8.4g}", 3.14) == "***3.140"); // #을 이용하여 0으로 채울 수 있습니다. 이때 정밀도는 .까지 포함합니다.      

// 너비와 정밀도를 외부에서 전달받을 수 있습니다.
EXPECT_TRUE(std::format("{:*>{}.{}f} {:s}", 3.14, 8, 4, "test") == "**3.1400 test"); // {}로 너비와 정밀도를 전달할 수 있습니다. 다만, 이때 인수 인덱스 지정은 안됩니다.

// 너비는 최소 길이 입니다. 주어진 너비보다 수치가 크면 무시됩니다.
EXPECT_TRUE(std::format("{0:6.4f}", 123.456789) == "123.4568"); // 최소 크기 6을 지정했고, 8자리로 출력됩니다.

// 문자열
EXPECT_TRUE(std::format("{0}, {0:s}", "hello") == "hello, hello"); 

// 정수
EXPECT_TRUE(std::format("{0}, {0:b}, {0:o}, {0:x}", 2) == "2, 10, 2, 2");
EXPECT_TRUE(std::format("{0}, {0:#b}, {0:#o}, {0:#x}", 2) == "2, 0b10, 02, 0x2"); // #을 붙여 진법을 표시합니다.
EXPECT_TRUE(std::format("{0}, {0:#0b}, {0:#0o}, {0:#0x}", 2) == "2, 0b10, 02, 0x2"); // #0을 붙여 진법과 빈자리는 0으로 채웁니다. 하지만 빈자리가 없어 0은 표시되지 않습니다.
EXPECT_TRUE(std::format("{0}, {0:#05b}, {0:#05o}, {0:#05x}", 2) == "2, 0b010, 00002, 0x002"); // #0을 붙이고, 5를 붙여 5자리에 진법과 0을 표시합니다.

// bool
EXPECT_TRUE(std::format("{0}, {0:s}", true) == "true, true");        

// 실수

// 실수 기본값은 .8f와 같습니다.
EXPECT_TRUE(std::format("{0}, {0:.8f}", 123456789.123456789) == "123456789.12345679, 123456789.12345679");

// 16진수 표기, 지수, 고정폭, 자동을 지원합니다.
EXPECT_TRUE(std::format("{0}, {0:a}, {0:e}, {0:f}, {0:g}", 3.141592) == "3.141592, 1.921fafc8b007ap+1, 3.141592e+00, 3.141592, 3.14159");

// f와 g의 차이. f는 항상 소수점으로 출력하고, g는 소수점 이하의 내용이 있는 경우만 출력합니다., 고정폭으로 표시하기 힘들면 지수로 출력합니다.
EXPECT_TRUE(std::format("{0:f}", 3.) == "3.000000"); // 소수점 이하를 0으로 표시합니다. 기본 정밀도는 6입니다.
EXPECT_TRUE(std::format("{0:g}", 3.) == "3"); // g는 소수점 이하가 없으면, 정수로 표시됩니다. 
EXPECT_TRUE(std::format("{0:f}, {0:g}", 123456789.123456789) == "123456789.123457, 1.23457e+08"); // 고정폭으로 표시하기 힘들면 지수로 출력합니다.
// f의 정밀도는 .을 포함하지 않은 갯수이고, g의 정밀도는 .을 포함한 갯수입니다.
EXPECT_TRUE(std::format("{0:f}, {0:g}", 3.123456789) == "3.123457, 3.12346"); // f와 g의 기본 정밀도는 6입니다. 하지만, g는 소수점을 포함한 정밀도 입니다. 
EXPECT_TRUE(std::format("{0:.3f}, {0:.3g}", 3.123456789) == "3.123, 3.12");

// 포인터
EXPECT_TRUE(std::format("{0:p}", nullptr) == "0x0"); // 주소를 16진수로 표시합니다.
```

# chrono 서식 지정자

[chrono](https://tango1202.github.io/cpp-stl/modern-cpp-stl-chrono/)관련 서식 지정자가 특수화되어 제공됩니다. 자세한 내용은 [https://en.cppreference.com/w/cpp/chrono/system_clock/formatter#Format_specification](https://en.cppreference.com/w/cpp/chrono/system_clock/formatter#Format_specification)을 참고하시기 바랍니다.

|항목|내용|
|--|--|
|`%y`|연도를 2자리 10진수로 표시합니다. 1자리이면 앞에 `0`이 붙습니다.|
|`%Y`|연도를 4자리 10진수로 표시합니다. 4자리가 아니면, 앞에 `0`이 붙습니다.|
|`%m`|월을 10진수로 표시합니다. 1자리이면 앞에 `0`이 붙습니다.|
|`%d`|일을 10진수로 표시합니다. 1자리이면 앞에 `0`이 붙습니다.|
|`%a`|축약된 요일 이름을 표시합니다.|
|`%A`|전체 요일 이름을 표시합니다.|
|`%D`|`%m/%d/%y` 와 동일합니다.|
|`%F`|`%Y-%m-%d` 와 동일합니다.|
|`%x`|로케일의 날짜 형식으로 표시합니다.|
|`%H`|시간을 24시간제 10진수로 표시합니다. 1자리이면 앞에 `0`이 붙습니다.|
|`%I`|시간을 12시간제 10진수로 표시합니다. 1자리이면 앞에 `0`이 붙습니다.|
|`%M`|분을 10진수로 표시합니다. 1자리이면 앞에 `0`이 붙습니다.|
|`%S`|초를 10진수로 표시합니다. 1자리이면 앞에 `0`이 붙습니다.|
|`%p`|12시간제일때 `AM/PM` 표시를 합니다.|
|`%R`|`%H:%M` 와 동일합니다.|
|`%T`|`%H:%M:%S` 와 동일합니다.|
|`%r`|로케일의 12시간제로  표시합니다.|
|`%X`|로케일의 시간 형식으로 표시합니다.|

다음은 사용예입니다.

```cpp
using namespace std::chrono; // 표준 사용자 정의 리터럴인 23y, 5d, 14h, 5min, 3s를 사용하기 위해서 추가합니다.

std::chrono::year_month_day date{23y, std::chrono::December, 5d};
EXPECT_TRUE(std::format("{:%Y/%m/%d (%A)}", date) == "0023/12/05 (Tuesday)");
EXPECT_TRUE(std::format("{:%Y-%m month, day = %d}", date) == "0023-12 month, day = 05"); // / 대신 다양한 문자 표현이 가능합니다.
EXPECT_TRUE(std::format("{:%D}", date) == "12/05/23"); // %m/%d/%y 와 동일합니다.
EXPECT_TRUE(std::format("{:%F}", date) == "0023-12-05"); // %Y-%m-%d 와 동일합니다.
EXPECT_TRUE(std::format("{:%x}", date) == "12/05/23"); // 로케일의 날짜 표시 형식입니다.

std::chrono::hh_mm_ss time{14h + 5min + 3s};
EXPECT_TRUE(std::format("{:%H:%M:%S (%p)}", time) == "14:05:03 (PM)"); // 24시간제로 표시합니다.
EXPECT_TRUE(std::format("{:%I:%M:%S (%p)}", time) == "02:05:03 (PM)"); // 12시간제로 표시합니다.
EXPECT_TRUE(std::format("{:%R}", time) == "14:05"); // %H:%M 과 동일합니다.
EXPECT_TRUE(std::format("{:%T}", time) == "14:05:03"); // %H:%M:%S와 동일합니다.
EXPECT_TRUE(std::format("{:%r}", time) == "02:05:03 PM"); // 로케일의 12시간제로 표시합니다.
EXPECT_TRUE(std::format("{:%X}", time) == "14:05:03"); // 로케일의 시간 형식으로 표시합니다.
```

# formatter

[formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter)는 [템플릿 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)를 이용하여 특정 타입에 대한 서식을 정의하는 개체입니다.

|항목|내용|
|--|--|
|[formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter) (C++20~)|특정 타입에 대한 서식을 정의합니다. [chrono](https://tango1202.github.io/cpp-stl/modern-cpp-stl-chrono/)관련 개체들을은 C++20에 [템플릿 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)되어 있습니다.|
|`basic_format_parse_context, format_parse_context, wformat_parse_context` (C++20~)|[formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter)의 `parse()`함수에 전달되는 정보입니다.|
|`basic_format_context, format_context, wformat_context` (C++20~)|[formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter)의 `format()`함수에 전달되는 정보입니다.|
|[format_error](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter) (C++20~)|[formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter)의 `parse()`시 문제가 발생했습니다.|
|`vformat()` (C++20~)|(작성중)|
|`vformat_to()` (C++20~)|(작성중)|
|`make_format_args(), make_wformat_args()` (C++20~)|(작성중)|
|`visit_format_arg()` (C++20~C++26)|(작성중)|
|`basic_format_arg` (C++20~)|(작성중)|
|`basic_format_args, format_args, wformat_args` (C++20~)|(작성중)|
|`range_formatter` (C++23~)|(작성중)|
|`range_format` (C++23~)|(작성중)|
|`format_kind` (C++23~)|(작성중)|

다음은 `MyClass` 타입의 서식을 [formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter)를 이용하여 정의한 예입니다. 멤버 변수 출력시 `d`를 사용하면 10진수로 출력하고, `x`를 전달하면 16진수로 출력합니다.

1. #1 : `std` [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)의 [formatter](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter)를 [템플릿 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 합니다.
2. #2 : `parse()`함수를 구현합니다. 이때 [constexpr 함수](https://tango1202.github.io/cpp/modern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)로 구현해야 합니다.
3. #3 : `ctx`의 첫 문자만 사용합니다.
4. #4 : 오류가 있으면, [format_error](https://tango1202.github.io/cpp-stl/modern-cpp-stl-formatting/#formatter) [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킵니다.
5. #5 : `}`의 위치를 리턴합니다.
6. #6 : `format()`함수를 구현합니다. #3에서 저장한 문자를 분석하여 출력합니다.

```cpp
namespace MyFormatterTest {
    struct MyClass {
        int m_X;
        int m_Y;
    public:
        MyClass(int x, int y) : m_X{x}, m_Y{y} {}
        int GetX() const {return m_X;}
        int GetY() const {return m_Y;}
    };
}

// MyClass의 값을 서식화 합니다. d : 10진수(디폴트), x : 16진수 
template <> 
struct std::formatter<MyFormatterTest::MyClass> { // #1
private:
    std::string m_FormatSpec; // 사용자가 지정한 서식 문자열에서 {0:abcd} 중 a만 저장합니다.
public:    
    // ctx : 형식 문자열의 {인수 인덱스: 뒷부분이 전달됩니다. } 전까지 파싱하고, }위치를 리턴합니다.
    // format("{0:d}, {1:x}", a, b) 로 호출시 
    //  * 처음에는 `d}, {1:x}`가 전달되고, 
    //  * 다음번은 `x}` 가 전달됩니다.
    // 만약 오류시 std::format_error를 발생시켜야 합니다. 
   //  template<typename ParseContext>
    constexpr std::format_parse_context::iterator parse(std::format_parse_context& ctx) { // #2
        auto result{std::find(ctx.begin(), ctx.end(), '}')};
        if (result != ctx.end()) {
            m_FormatSpec = std::string(ctx.begin(), result); // } 전까지 저장합니다.
        }
        else {
            throw std::format_error("invalid format string"); // #4. 예외를 발생시킵니다.
        }
        return result; // #5. } 위치를 리턴합니다.
    }
    
    // 형식 문자열에서 사용자가 입력한 내용을 각 멤버 변수에 반영하여 서식화 합니다.
    // template <typename FormatContext>
    std::format_context::iterator format(const MyFormatterTest::MyClass& obj, std::format_context& ctx) const { // #6
        if (m_FormatSpec[0] == 'x') {
            return std::format_to(ctx.out(), "MyClass : x = {:#x} y = {:#x}", obj.GetX(), obj.GetY());
        }
        else {
            return std::format_to(ctx.out(), "MyClass : x = {:d} y = {:d}", obj.GetX(), obj.GetY());
        }
    }
};

using namespace MyFormatterTest;

EXPECT_TRUE(
    std::format("{0}, {1:d}, {2:x}", 
    MyClass{1, 2}, // 기본은 10진수로 출력합니다.
    MyClass{3, 4}, // d는 10진수로 출력합니다.
    MyClass{5, 6}) == // x는 0x 접두어를 붙여 16진수로 출력합니다.
    "MyClass : x = 1 y = 2, MyClass : x = 3 y = 4, MyClass : x = 0x5 y = 0x6"
);
```








  

  






