---
layout: single
title: "#14. [모던 C++] (C++11~) 문자열(char16_t, char32_t, u8/u/U/R 리터럴, u16string, u32string) (C++20~) char8_t, u8string"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
> * 유니코드를 지원하는 `char8_t`, `char16_t`, `char32_t`, `u8string`, `u16string`, `u32string` 이 추가되었습니다.
> * 유니코드를 지원하는 `u8""`, `u""`, `U""` 리터럴이 추가되었습니다. 
> * `R"()"`리터럴이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.

# 유니코드

컴퓨터에서는 문자를 표현하기 위해서 0 ~ 127 까지의 7비트 체계의 아스키 코드(ASCII Code)를 사용했습니다.

하지만, 다양한 언어를 지원하다 보니 7비트로는 표현이 불가능하여 전 세계의 모든 문자에 고유 숫자를 부여한 유니코드를 만들었습니다. 

유니코드는 기본적으로 2byte를 사용하도록 했으나, 아시아권 문자를 포함하다 보니 3byte가 필요하게 되었고, 다양한 추가 문자들을 지원하다 보니 4byte가 필요(악보 기호, 이모지등 특수 기호 지원)하게 되었으며, 현대 한글의 모든 문자 11,172개는 U+AC00 ~ D7A3에 할당되어 있습니다.

유니코드는 U+16진수로 표기합니다. 예를 들어 한글 "가"는 AC00(10진수의 44032)인데, U+AC00으로 표기합니다.

모든 문자를 4byte로 표현하면 메모리 낭비가 심하므로 인코딩을 통해 문자 변환을 합니다.

인코딩 방식은 다음의 3가지 방식이 있습니다.

|항목|내용|
|--|--|
|UTF-8|문자마다 다른 크기로 저장합니다. 이에 따라 문자 데이터 앞에 크기 정보가 필요하며, 최상위 비트가 `0`이면 7비트로 처리하고 `110`이면 2바이트, `1110`이면 3바이트, `11110`이면 4byte로 처리합니다.(웹 기본)<br/>* 영어 및 기호 : 1byte<br/>* 추가 라틴 및 중동 : 2byte<br/>* 한글 및 아시아 : 3byte(한글은 크기 정보 + 코드값을 하면 3바이트가 필요합니다.)<br/>* 추가 문자 : 4byte
|UTF-16|기본적으로 2byte로 처리하고, 2byte로 표현할 수 없는 것들은 4byte로 처리합니다.(java 기본)|
|UTF-32|모든 문자를 4byte로 처리합니다. 메모리 낭비가 심하여 잘 사용하지 않습니다.|

# char16_t 와 char32_t

기존 `wchar_t`는 시스템 비트수에 따라 가변적이어서 2byte와 4byte 크기로 다루기 위한 `char16_t`와 `char32_t`가 추가되었습니다.

|항목|내용|용량|
|--|--|--|
|`char`|1byte 문자|1byte|
|`wchar_t`|와이드 문자|시스템의 비트수에 따라 다르며, 대부분 2byte 또는 4byte.<br/>단, Windows는 2byte|
|`char16_t`| UTF-16 문자|`16bit`(2byte) 보다 크거나 같음|
|`char32_t`| UTF-32 문자|`32bit`(4byte) 보다 크거나 같음|

# 유니코드 리터럴

기존에는 1byte 문자열과 와이드 문자열만 지원했지만, C++11부터  UTF-8, UTF-16, UTF-32의 유니코드 문자열 지원을 합니다.

|항목|내용|
|--|--|
|1byte 문자열|"abc"|
|와이드 문자열|L"abc한글"|
|UTF-8 문자열|u8"abc한글"|
|UTF-16 문자열|u"abc한글"|
|UTF-32 문자열|U"abc한글"|


# 유니코드 문자열 개체

기존에는 `char`, `wchar_t` 문자열 개체만 지원했지만, C++11부터 `char16_t`, `char32_t` 문자열을 지원합니다.

|항목|내용|
|--|--|
|`std::string`|`std::basic_string<char>`|
|`std::wstring`|`std::basic_string<wchar_t>`|
|`std::u16string`|`std::basic_string<char16_t>`|
|`std::u32string`|`std::basic_string<char32_t>`|

```cpp
// C++11
std::u16string str1 = u"한글";
std::u32string str2 = U"한글";

// C++20
// std::u8string str3 = U"한글";

EXPECT_TRUE(str1.size() == 2);
EXPECT_TRUE(str1[1] == u'글'); 

EXPECT_TRUE(str2.size() == 2);
EXPECT_TRUE(str2[1] == U'글');

// C++20
// EXPECT_TRUE(str3.size() == 2);
// EXPECT_TRUE(str3[1] == U'글');
```

# (C++20~) char8_t, u8string

C++20부터 UTF8 을 관리할 수 있도록 `char8_t`와 `std::u8string`이 추가되었습니다.

# Raw String 리터럴

기존 문자열은 개행을 하기 위해 다음처럼 이스케이프 문자(`\r\n`)를 추가해야 했습니다.

```cpp
std::cout<<"abc\r\ndef"<<std::endl;
```

```cpp
abc
def
```

`R"()"`은 `()`안에 표기된 그대로를 문자열로 처리해 줍니다. 이스케이프 문자도 그대로 출력합니다.

```cpp
// 이스케이프 문자와 개행을 소스코드에 기재된 그대로 출력합니다.
std::cout<<R"(abc\r\n
def)"
<<std::endl;
```

```cpp
abc\r\n
def
```

`"()"` 자체를 출력할 때에는 파싱 오류가 날 수 있으므로, 임의 구분자를 지정하여 사용할 수 있습니다.

```cpp
// 임의 구분자 aaa 사용
std::cout<<R"aaa(abc"()"
def)aaa"
<<std::endl;
```

```cpp
abc"()"
def
```