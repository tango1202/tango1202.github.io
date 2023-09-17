---
layout: single
title: "#19. [모던 C++] (C++11~) 사용자 정의 리터럴, (C++14~) 표준 사용자 정의 리터럴"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `int operator ""_km(long double val);`와 같은 사용자 정의 리터럴이 추가되어 단위계 처리가 쉬워졌습니다.

# 개요

C++11 부터는 `operator""_식별자()`를 이용하여 사용자가 리터럴을 직접 정의할 수 있습니다. 동일한 값을 여러 단위계로 표현할 때 유용합니다.

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

# 문자열 추론

기존 문자열을 `auto`로 초기화 하면 `const char*`로 추론되는데요, 사용자 정의 리터럴을 만들어 `std::string`으로 추론되게 할 수 있습니다.

```cpp
// const char* 형의 문자열을 std::string으로 변환
std::string operator ""_forced_string(const char* str, size_t) {return std::string(str);}

auto str1{"test"}; // const char*
auto str2{"test"_forced_string}; // std::string
EXPECT_TRUE(str2.size() == 4);
EXPECT_TRUE("hello"_forced_string.size() == 5); // 임시 개체도 가능
```

# 식별자 규칙

식별자는 `_`로 시작해야 하며, 이중 밑줄은 허용하지 않습니다.

# 인자 규칙

사용할 수 있는 인자의 형태는 다음과 같습니다.

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

# (C++14~) 표준 사용자 정의 리터럴

C++버전에 따라 다음의 표준 사용자 정의 리터럴이 제공됩니다.

**C++14**

|항목|내용|
|--|--|
|`operator""if`<br/>`operator""i`<br/>`operator""il`|`std::complex` 리터럴|
|`operator""h`|`std::chrono::duration`에서 시간|
|`operator""min`|`std::chrono::duration`에서 분|
|`operator""s`|`std::chrono::duration`에서 초|
|`operator""ms`|`std::chrono::duration`에서 밀리초|
|`operator""us`|`std::chrono::duration`에서 마이크로초|
|`operator""ns`|`std::chrono::duration`에서 나노초|
|`operator""s`|`std::basic_string` 변환|

**C++17**

|항목|내용|
|--|--|
|`operator""sv`|`string_view` 변환|

**C++20**

|항목|내용|
|--|--|
|`operator""y`|`std::chrono::year`리터럴|
|`operator""d`|`std::chrono::day`리터럴|

# (C++20~) char8_t

인자 규칙에 `char8_t` 이 추가되었습니다.

```cpp
ReturnType operator ""_u(char8_t); 
```