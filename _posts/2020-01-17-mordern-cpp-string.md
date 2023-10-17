---
layout: single
title: "#17. [모던 C++] (C++11~) 유니코드 문자(char16_t, char32_t) 유니코드 리터럴(u8/u/U), R 리터럴, (C++20~) char8_t"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
> * (C++11~) (C++11~) 유니코드를 지원하는 [char16_t, char32_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#char16_t-%EC%99%80-char32_t)이 추가되었습니다.
> * (C++11~) 유니코드를 지원하는 [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다. 
> * (C++11~) [R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.
> * (C++17~) 유니코드를 지원하는 [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.
> * (C++20~) 유니코드를 지원하는 [char8_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#c20-char8_t)이 추가되었습니다.

# 개요

유니코드는 전 세계의 모든 문자에 고유 숫자를 부여한 코드 체계 입니다. 자세한 내용은 [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%BD%94%EB%93%9C-%EA%B4%80%EC%A0%90--%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 참고하시기 바랍니다.

# char16_t 와 char32_t

기존 `wchar_t`는 시스템에 따라 2byte 또는 4byte로 가변적이어서([기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 참고), [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%BD%94%EB%93%9C-%EA%B4%80%EC%A0%90--%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C) 처리가 어려웠습니다.

C++11 부터는 [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%BD%94%EB%93%9C-%EA%B4%80%EC%A0%90--%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C) 지원을 위해 2byte 고정 크기인 `char16_t`와 4byte 고정 크기인 `char32_t`가 추가되었습니다.

|항목|내용|용량|
|--|--|--|
|`char`|[1byte 문자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%A0%80%EC%9E%A5-%EB%B0%A9%EC%8B%9D-%EA%B4%80%EC%A0%90--%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|1byte|
|`wchar_t`|[와이드 문자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%A0%80%EC%9E%A5-%EB%B0%A9%EC%8B%9D-%EA%B4%80%EC%A0%90--%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|시스템의 비트수에 따라 다르며, 대부분 2byte 또는 4byte.<br/>Windows는 2byte|
|`char16_t` (C++11~)|[UTF-16 문자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%9D%B8%EC%BD%94%EB%94%A9)|`16bit`(2byte) 보다 크거나 같음|
|`char32_t` (C++11~)|[UTF-32 문자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%9D%B8%EC%BD%94%EB%94%A9)|`32bit`(4byte) 보다 크거나 같음|

# 유니코드 리터럴

기존에는 [바이트 문자열과 와이드 문자열]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%A0%80%EC%9E%A5-%EB%B0%A9%EC%8B%9D-%EA%B4%80%EC%A0%90--%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4))만 지원했는데요([문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98) 참고),

 C++11 부터는 [UTF-8, UTF-16, UTF-32](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%9D%B8%EC%BD%94%EB%94%A9)의 유니코드 문자 및 문자열을 지원합니다.(유니코드 UTF-8 문자열은 C++11에 추가됐지만, UTF-8 문자는 C++17에 추가되었습니다.) 

|항목|내용|
|--|--|
|[1byte 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%A0%80%EC%9E%A5-%EB%B0%A9%EC%8B%9D-%EA%B4%80%EC%A0%90--%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|"abc"|
|[와이드 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%A0%80%EC%9E%A5-%EB%B0%A9%EC%8B%9D-%EA%B4%80%EC%A0%90--%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|L"abc한글"|
|[UTF-8 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%9D%B8%EC%BD%94%EB%94%A9) (C++11~)|`u8"abc한글"`|
|[UTF-16 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%9D%B8%EC%BD%94%EB%94%A9) (C++11~)|`u"abc한글"`|
|[UTF-32 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%9D%B8%EC%BD%94%EB%94%A9) (C++11~)|`U"abc한글"`|
|UTF-8 문자 (C++17~)|`u8'한'`|
|UTF-16 문자 (C++11~)|`u'한'`|
|UTF-32 문자 (C++11~)|`U'한'`|

```cpp
char16_t ch_11 = u'한';
const char16_t* str_11 = u"abc한글";
```

# Raw String 리터럴

기존 문자열은 개행을 하기 위해 다음처럼 이스케이프 문자(`\r\n`)를 추가해야 했는데요([이스케이프 문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90) 참고),

```cpp
const char* str = "abc\r\ndef";
std::cout << str << std::endl;
```

```cpp
abc
def
```

C++11 부터는 `R"()"`은 `()`안에 표기된 그대로를 문자열로 처리해 줍니다. 이스케이프 문자도 그대로 출력합니다.

```cpp
// 이스케이프 문자와 개행을 소스코드에 기재된 그대로 출력합니다.
const char* str_11 = R"(abc\r\n
def)";     
std::cout << str_11 << std::endl;
```

```cpp
abc\r\n
def
```

`"()"` 자체를 출력할 때에는 파싱 오류가 날 수 있으므로, 다음 예의 `aaa`처럼 임의 구분자를 지정하여 사용할 수 있습니다.

```cpp
// 임의 구분자 aaa 사용
const char* str_11 = R"aaa(abc"()"
def)aaa";     
std::cout << str_11 << std::endl;
```

```cpp
abc"()"
def
```

# (C++20~) char8_t

C++20 부터는 [UTF-8](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%9D%B8%EC%BD%94%EB%94%A9)을 관리할 수 있도록 `char8_t`가 추가되었습니다.