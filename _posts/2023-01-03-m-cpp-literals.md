---
layout: single
title: "#3. [모던 C++] (C++11~) 사용자 정의 리터럴"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `int operator ""_km(long double val);`와 같이 사용자가 정의해서 사용할 수 있으며, 단위계 처리가 쉬워졌습니다.
> * (C++20~) [사용자 정의 리터럴 인자 규칙에 char8_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c20-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4-%EC%9D%B8%EC%9E%90-%EA%B7%9C%EC%B9%99-char8_t)이 추가되었습니다.

# 사용자 정의 리터럴

C++11 부터는 `operator""_식별자()`를 이용하여 사용자가 [리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/)을 직접 정의할 수 있습니다. 동일한 값을 여러 단위계로 표현할 때 유용합니다.

```cpp
// mm 단위로 리턴
int operator ""_km(long double val) {return std::round(val * 1000. * 1000.);}
int operator ""_m(long double val) {return std::round(val * 1000.);}
int operator ""_cm(long double val) {return std::round(val * 10.);}
int operator ""_mm(long double val) {return std::round(val);}   
int operator ""_mm(unsigned long long val) {return val;}  // 정수형 리터럴도 오버로딩

EXPECT_TRUE(1.5_km == 1500.0_m);
EXPECT_TRUE(1.5_m == 150.0_cm);
EXPECT_TRUE(1.5_cm == 15.0_mm);
EXPECT_TRUE(1.5_mm == 1.5_mm); // 반올림 하므로 2._mm와 동일합니다.

EXPECT_TRUE(1_km == 1000.0_m); // (X) 컴파일 오류. long double만 오버로딩 되었습니다.
EXPECT_TRUE(1_mm == 1); // mm는 정수형도 오버로딩 되었습니다.    
```
> *(C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 추가되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex) 관련 표현이 간편해 졌습니다.*

# 문자열 추론

기존 문자열을 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)로 초기화 하면 `const char*`로 추론되는데요, [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)을 만들어 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)으로 추론되게 할 수 있습니다.

```cpp
// const char* 형의 문자열을 std::string으로 변환
std::string operator ""_forced_string(const char* str, size_t size) {
    std::cout << "String Size : " << size << std::endl; // 전달된 문자열의 길이입니다.(널문자는 포함되지 않습니다.)
    return std::string(str);
}

auto chars_11 = "test"; // const char*
// EXPECT_TRUE(chars_11.size() == 4); // (X) 컴파일 오류. const char*이므로 size() 멤버 함수가 없습니다

auto str_11 = "test"_forced_string; // std::string
EXPECT_TRUE(str_11.size() == 4); // (O)

EXPECT_TRUE("hello"_forced_string.size() == 5); // 임시 개체도 가능합니다.
```
> *(C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 추가되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex) 관련 표현이 간편해 졌습니다. 상기와 같은 경우 `"test"s`로 사용하면 됩니다.*

# 식별자 규칙

식별자는 밑줄(`_`)로 시작해야 하며, 이중 밑줄은 허용하지 않습니다.

# 인자 규칙

사용할 수 있는 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 형태는 다음과 같습니다.

```cpp
// 정수 1_a
ReturnType operator ""_a(unsigned long long); 
// 실수 1.0_b
ReturnType operator ""_b(long double);              
// 문자 'a`_c
ReturnType operator ""_c(char);                    
ReturnType operator ""_d(wchar_t);                  
ReturnType operator ""_e(char16_t);                 
ReturnType operator ""_f(char32_t);                 
// 문자열 "abc"_g
ReturnType operator ""_g(const char*, size_t);      
ReturnType operator ""_h(const wchar_t*, size_t);   
ReturnType operator ""_i(const char16_t*, size_t); 
ReturnType operator ""_j(const char32_t*, size_t); 
// Raw : 3.14_k 처럼 정수나 실수 형태로 전달하고, 전달된 인자는 문자열 3.14로 전달됨
ReturnType operator ""_k(const char*);              
// template
template<char...> ReturnType operator ""_l();       
```

문자열과 Raw 타입이 모두 `const char*`를 사용해서 헷갈리는데요, 

* `"3.14"_a`와 같이 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)처럼 호출하면, `int operator ""_a(const char* str, size_t size)`이 호출되고,
* `3.14_a`와 같이 정수나 실수 형태로 사용하면 `int operator ""_a(const char* str)`가 호출됩니다.

```cpp
int operator ""_a(const char* str, size_t size) {
    return 1;
}
int operator ""_a(const char* str) {
    return 2;
}

EXPECT_TRUE("3.14"_a == 1); // int operator ""_a(const char* str, size_t size) 버전이 호출됩니다.
EXPECT_TRUE(3.14_a == 2); // int operator ""_a(const char* str)  버전이 호출됩니다.
```

Raw 타입은 우선 순위가 낮습니다. 만약 `const char*`와 `long double`이 같은 식별자로 정의되었다면, `long double`이 사용됩니다. 따라서 Raw 타입은 다른 타입과 동일한 식별자를 사용하지 말아야 합니다.

```cpp
int operator ""_a(const char* str) { // (△) 비권장. 우선 순위가 낮습니다. 다른 타입과 동일한 식별자를 사용하지 마세요.
    return 2;
}
int operator ""_a(long double val) { 
    return 3;
}
EXPECT_TRUE(3.14_a == 3); // int operator ""_a(long double val) 버전이 호출됩니다.
```

# (C++20~) 사용자 정의 리터럴 인자 규칙 char8_t

[인자 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%9D%B8%EC%9E%90-%EA%B7%9C%EC%B9%99)에 [char8_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c20-char8_t)가 추가되었습니다.

```cpp
ReturnType operator ""_m(char8_t); 
ReturnType operator ""_n(const char8_t*, size_t); 
```