---
layout: single
title: "#17. [모던 C++] (C++11~) 문자열(char16_t, char32_t, u8/u/U/R 리터럴, std::u16string, std::u32string) (C++20~) char8_t, std::u8string"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
> * (C++11~) 유니코드를 지원하는 `char16_t`, `char32_t`, `u16string`, `u32string` 이 추가되었습니다.
> * (C++11~) 유니코드를 지원하는 `u8""`, `u""`, `U""`, `'u'`(문자), `'U'`(문자) 리터럴이 추가되었습니다. 
> * (C++11~) `R"()"`리터럴이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.
> * (C++17~) 유니코드를 지원하는 `'u8'`(문자) 리터럴이 추가되었습니다.
> * (C++17~) [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view)가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 `string`복제가 없도록 해줍니다.

# 유니코드

컴퓨터에서는 문자를 표현하기 위해서 0 ~ 127 까지의 7비트 체계의 아스키 코드(ASCII Code)를 사용했습니다.

하지만, 다양한 언어를 지원하다 보니 7비트로는 표현이 불가능하여 전 세계의 모든 문자에 고유 숫자를 부여한 유니코드를 만들었습니다. 

유니코드는 기본적으로 2byte를 사용하도록 했으나, 아시아권 문자를 포함하다 보니 3byte가 필요하게 되었고, 다양한 추가 문자들을 지원하다 보니 4byte가 필요(악보 기호, 이모지등 특수 기호 지원)하게 되었으며, 현대 한글의 모든 문자 11,172개는 U+AC00 ~ D7A3에 할당되어 있습니다.

유니코드는 `U+16진수`로 표기합니다. 예를 들어 한글 "가"는 `AC00`(10진수의 `44032`)인데, `U+AC00`으로 표기합니다.

# 유니코드 인코딩

유니코드를 최종본인 4byte로만 표현하기엔 메모리 낭비가 심하므로 인코딩을 통해 문자 변환을 합니다.

인코딩 방식은 다음의 3가지 방식이 있습니다.

|항목|내용|
|--|--|
|UTF-8|문자마다 다른 크기로 저장합니다. 이에 따라 문자 데이터 앞에 크기 정보가 필요하며, 최상위 비트가 `0`이면 7비트로 처리하고 `110`이면 2바이트, `1110`이면 3바이트, `11110`이면 4byte로 처리합니다.(웹 기본)<br/>* 영어 및 기호 : 1byte<br/>* 추가 라틴 및 중동 : 2byte<br/>* 한글 및 아시아 : 3byte(한글은 크기 정보 + 코드값을 하면 3바이트가 필요합니다.)<br/>* 추가 문자 : 4byte
|UTF-16|기본적으로 2byte로 처리하고, 2byte로 표현할 수 없는 것들은 4byte로 처리합니다.(java 기본)|
|UTF-32|모든 문자를 4byte로 처리합니다. 메모리 낭비가 심하여 잘 사용하지 않습니다.|

# char16_t 와 char32_t

기존 `wchar_t`는 시스템 비트수에 따라 가변적이어서([기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 참고),

C++11 부터는 2byte와 4byte 크기로 다루기 위한 `char16_t`와 `char32_t`가 추가되었습니다.

|항목|내용|용량|
|--|--|--|
|`char`|1byte 문자|1byte|
|`wchar_t`|와이드 문자|시스템의 비트수에 따라 다르며, 대부분 2byte 또는 4byte.<br/>Windows는 2byte|
|`char16_t` (C++11~)| UTF-16 문자|`16bit`(2byte) 보다 크거나 같음|
|`char32_t` (C++11~)| UTF-32 문자|`32bit`(4byte) 보다 크거나 같음|

# 유니코드 리터럴

기존에는 1byte 문자열과 와이드 문자열만 지원했는데요([문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98) 참고),

 C++11 부터는 UTF-8, UTF-16, UTF-32의 유니코드 문자열 지원을 합니다.(유니코드 UTF-8 문자열은 C++11에 추가됐지만, UTF-8 문자는 C++17에 추가되었습니다.) 

|항목|내용|
|--|--|
|1byte 문자열|"abc"|
|와이드 문자열|L"abc한글"|
|UTF-8 문자열 (C++11~)|`u8"abc한글"`|
|UTF-16 문자열 (C++11~)|`u"abc한글"`|
|UTF-32 문자열 (C++11~)|`U"abc한글"`|
|UTF-8 문자 (C++17~)|`u8'한'`|
|UTF-16 문자 (C++11~)|`u'한'`|
|UTF-32 문자 (C++11~)|`U'한'`|


# 유니코드 문자열 개체

기존에는 `char`, `wchar_t` 문자열 개체만 지원했는데요([문자열](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/) 참고),

C++11 부터는 `char16_t`, `char32_t` 문자열인 `u16string`과 `u32string`을 지원합니다.

|항목|내용|
|--|--|
|`string`|`basic_string<char>`|
|`wstring`|`basic_string<wchar_t>`|
|`u16string` (C++11~)|`basic_string<char16_t>`|
|`u32string` (C++11~)|`basic_string<char32_t>`|

```cpp
// C++11
std::u16string str1_11{u"한글"};
std::u32string str2_11{U"한글"};

// C++20
// std::u8string str3_20{u8"한글"};

EXPECT_TRUE(str1_11.size() == 2);
EXPECT_TRUE(str1_11[1] == u'글'); 

EXPECT_TRUE(str2_11.size() == 2);
EXPECT_TRUE(str2_11[1] == U'글');

// C++20
// EXPECT_TRUE(str3_20.size() == 2);
// EXPECT_TRUE(str3_20[1] == U'글');
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

`"()"` 자체를 출력할 때에는 파싱 오류가 날 수 있으므로, 임의 구분자를 지정하여 사용할 수 있습니다.

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

# (C++20~) char8_t, u8string

C++20 부터는 UTF8 을 관리할 수 있도록 `char8_t`와 `u8string`이 추가되었습니다.