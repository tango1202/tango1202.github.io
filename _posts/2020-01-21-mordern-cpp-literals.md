---
layout: single
title: "#21. [모던 C++] (C++11~) 사용자 정의 리터럴, (C++14~) 이진 리터럴, 숫자 구분자, (C++17~) 16진수 부동 소수점 리터럴"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) `int operator ""_km(long double val);`와 같은 [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 단위계 처리가 쉬워졌습니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.
> * (C++14~) [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.
> * (C++14~) 가독성을 위한 [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어 `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.
> * (C++17~) `0xA.9p11`과 같은 [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)을 제공합니다.

# 사용자 정의 리터럴

C++11 부터는 `operator""_식별자()`를 이용하여 사용자가 [리터럴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/)을 직접 정의할 수 있습니다. 동일한 값을 여러 단위계로 표현할 때 유용합니다.

```cpp
// mm 단위로 리턴
int operator ""_km(long double val) {return std::round(val * 1000. * 1000.);}
int operator ""_m(long double val) {return std::round(val * 1000.);}
int operator ""_cm(long double val) {return std::round(val * 10.);}
int operator ""_mm(long double val) {return std::round(val);}   
int operator ""_mm(unsigned long long val) {return val;}  // 정수형 리터럴도 오버로딩

EXPECT_TRUE(1.0_km == 1000.0_m);
EXPECT_TRUE(1.0_m == 100.0_cm);
EXPECT_TRUE(1.0_cm == 10.0_mm);
EXPECT_TRUE(1.0_mm == 1_mm);
```
> *(C++14~) `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공됩니다.*

# 문자열 추론

기존 문자열을 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)로 초기화 하면 `const char*`로 추론되는데요, [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)을 만들어 `string`으로 추론되게 할 수 있습니다.

```cpp
// const char* 형의 문자열을 std::string으로 변환
std::string operator ""_forced_string(const char* str, size_t) {return std::string(str);}

auto chars_11{"test"}; // const char*
auto str_11{"test"_forced_string}; // std::string
EXPECT_TRUE(str_11.size() == 4);
EXPECT_TRUE("hello"_forced_string.size() == 5); // 임시 개체도 가능
```
> *(C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.*

# 식별자 규칙

식별자는 `_`로 시작해야 하며, 이중 밑줄은 허용하지 않습니다.

# 인자 규칙

사용할 수 있는 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 형태는 다음과 같습니다.

```cpp
// 정수
ReturnType operator ""_a(unsigned long long); 
// 실수
ReturnType operator ""_b(long double);              
// 문자
ReturnType operator ""_c(char);                    
ReturnType operator ""_d(wchar_t);                  
ReturnType operator ""_e(char16_t);                 
ReturnType operator ""_f(char32_t);                 
// 문자열
ReturnType operator ""_g(const char*, size_t);      
ReturnType operator ""_h(const wchar_t*, size_t);   
ReturnType operator ""_i(const char16_t*, size_t); 
ReturnType operator ""_g(const char32_t*, size_t); 
// Raw : 3.14_r 처럼 정수나 실수 형태로 전달하고, 전달된 인자는 문자열 3.14로 전달됨
ReturnType operator ""_r(const char*);              
// template
template<char...> ReturnType operator ""_t();       
```

# (C++14~) 이진 리터럴

`0b`, `0B` 접두어를 이용하여 이진수 상수를 표현할 수 있습니다.

```cpp
int val1_14{0b11};
int val2_14{0b1111};
int val3_14{0B11111111};

EXPECT_TRUE(val1_14 == 0x03);
EXPECT_TRUE(val2_14 == 0x0F);
EXPECT_TRUE(val3_14 == 0xFF);
```

# (C++14~) 숫자 구분자

작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.

큰 숫자인 경우 가독성이 좋아집니다.

```cpp
int val{1000000};
int val_14{1'000'000};

EXPECT_TRUE(val == val_14);
```

# (C++17~) 16진수 부동 소수점 리터럴

기존에는 16진수 정수 [리터럴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/)만 제공했는데요, 

```cpp
int val = 0x1a; // 16진수. x 또는 X. a~f 또는 A~F
```

C++17 부터는 [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)도 제공합니다.

`0x[소수점 이상 16진수값].[소수점 이하 16진수값]p[십진수 지수]`

가수 `p`또는 `P`는 10진수 `2`입니다.

예를들어 `0xA.9p11`은,

1. `A` : `10진수 10`
2. `9` : `1/16 * 9 = 0.5625` 
3. `p11` : `2의 11승 == 2048`

이어서 

`10.5625 * 2 ^ 11 =  21632`

입니다.

```cpp
// A(10진수 10), 9(1/16 * 9 = 0.5625), (p11은 2의 11승 == 2048)
// 즉, 10.5625 * 2 ^ 11 =  21632
float floatVal_17 = 0xA.9p11; // 21632 
```

# (C++20~) 사용자 정의 리터럴 인자 규칙 char8_t

[인자 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%9D%B8%EC%9E%90-%EA%B7%9C%EC%B9%99)에 `char8_t`이 추가되었습니다.

```cpp
ReturnType operator ""_u(char8_t); 
```