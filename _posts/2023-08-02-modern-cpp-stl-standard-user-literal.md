---
layout: single
title: "#32. [모던 C++ STL] 표준 사용자 정의 리터럴(C++14)"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/cpp-stl/modern-cpp-stl-standard-user-literal/)이 추가되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/cpp-stl/modern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/cpp-stl/modern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/cpp-stl/modern-cpp-stl-numeric/#complex) 관련 표현이 간편해 졌습니다.
> * (C++17~) [operator ""sv](https://tango1202.github.io/cpp-stl/modern-cpp-stl-string/)가 추가되어 [string_view](https://tango1202.github.io/cpp-stl/modern-cpp-stl-string_view) 표현이 간편해 졌습니다.
> * (C++20~) [operator ""y, operator ""d](https://tango1202.github.io/cpp-stl/modern-cpp-stl-string/)가 추가되어 [year](https://tango1202.github.io/cpp-stl/modern-cpp-stl-chrono/#c20-calendar), [day](https://tango1202.github.io/cpp-stl/modern-cpp-stl-chrono/#c20-calendar) 표현이 간편해 졌습니다.

# 개요

[사용자 정의 리터럴](https://tango1202.github.io/cpp/modern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)을 이용하여, C++14 부터 다음의 [표준 사용자 정의 리터럴](https://tango1202.github.io/cpp-stl/modern-cpp-stl-standard-user-literal/)이 제공됩니다.

|항목|내용|
|--|--|
|`operator ""if` (C++14~)<br/>`operator""i` (C++14~)<br/>`operator""il` (C++14~)|`complex` 리터럴, `std::complex<float> z = 3.0f + 4.0if`|
|`operator ""h` (C++14~)|`chrono::duration`에서 시간|
|`operator ""min` (C++14~)|`chrono::duration`에서 분|
|`operator ""s` (C++14~)|`chrono::duration`에서 초, `0.1s`|
|`operator ""ms` (C++14~)|`chrono::duration`에서 밀리초|
|`operator ""us` (C++14~)|`chrono::duration`에서 마이크로초|
|`operator ""ns` (C++14~)|`chrono::duration`에서 나노초|
|`operator ""s` (C++14~)|[basic_string](https://tango1202.github.io/cpp-stl/modern-cpp-stl-string/#basic_string) 변환, `"abcdef"s`|
|`operator ""sv` (C++17~)|[string_view](https://tango1202.github.io/cpp-stl/modern-cpp-stl-string_view) 변환|
|`operator ""y` (C++20~)|`chrono::year`리터럴|
|`operator ""d` (C++20~)|`chrono::day`리터럴|