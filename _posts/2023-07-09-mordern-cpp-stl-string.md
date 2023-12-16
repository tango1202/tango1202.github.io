---
layout: single
title: "#9. [Mordern C++ STL] 문자열"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [u16string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.
> * (C++11~) [u32string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.
> * (C++11~) [isblank()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)가 추가되었습니다.
> * (C++11~) [atoll(), strtoll(), strtoull(), strtoimax(), strtoumax()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)가 추가되었습니다.
> * (C++11~) [mbrtoc16(), mbrtoc32(), c32rtomb()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8)가 추가되었습니다.
> * (C++11~) [`__STDC_UTF_16__`, `__STDC_UTF_32__`](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8)가 추가되었습니다.
> * (C++11~) [숫자 변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c11-%EC%88%AB%EC%9E%90-%EB%B3%80%ED%99%98)이 추가되었습니다.
> * (C++17~) 숫자와 문자열간의 변환을 위한 [to_char(), from_char()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-%EC%88%AB%EC%9E%90%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%B3%80%ED%99%98) 함수가 제공되며, 기존 C스타일([atoi(), strtol()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98 )등) 보다 안전합니다.
> * (C++17~) [chars_format](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-%EC%88%AB%EC%9E%90%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%B3%80%ED%99%98)이 추가되었습니다.
> * (C++20~) [u8string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.

# 개요

문자열을 처리하는 기본 클래스는 [basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)입니다. `char_traits`를 사용하여 각 문자 타입별로 문자열을 처리합니다.

|항목|내용|
|--|--|
|[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)|문자열의 기본 클래스입니다.|
|`char_traits`|[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)에서 각 문자를 처리하는 타입 특성입니다.|

[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)은 각 타입 별로 별칭을 사용합니다.

|항목|내용|정의|
|--|--|--|
|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)|[바이트 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|`basic_string<char>`|
|[wstring](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)|[와이드 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|`basic_string<wchar_t>`|
|`u16string` (C++11~)|[UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열|`basic_string<char16_t>`|
|`u32string` (C++11~)|[UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열|`basic_string<char32_t>`|
|`u8string` (C++20~)|[UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열|`basic_string<char8_t>`|

```cpp
// C++11
std::u16string str1_11{u"abc가나다"};
std::u32string str2_11{U"abc가나다"};

// C++20
std::u8string str3_20{u8"abc가나다"};

EXPECT_TRUE(str1_11.size() == 6);
EXPECT_TRUE(str1_11[3] == u'가'); 

EXPECT_TRUE(str2_11.size() == 6);
EXPECT_TRUE(str2_11[3] == U'가');

// C++20
EXPECT_TRUE(str3_20.size() == 12); // abc : 3byte, 가 : 3byte, 나 : 3byte, 다 : 3byte
EXPECT_TRUE(str3_20[3] == 0xEA); // 한글 '가'는 UTF-8에서 3byte. 0xEA, 0xB0, 0x80
EXPECT_TRUE(str3_20[4] == 0xB0);
EXPECT_TRUE(str3_20[5] == 0x80);
```

자세한 사용 방법은 [문자열](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)을 참고하시기 바랍니다.

> *(C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 추가되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex) 관련 표현이 간편해 졌습니다.*


# C스타일 문자열 함수

**문자 분류**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`isalnum()`|`iswalnum()`|(작성중)|
|`isalpha()`|`iswalpha()`|(작성중)|
|`islower()`|`iswlower()`|(작성중)|
|`islower()`|`iswupper()`|(작성중)|
|`isupper()`||(작성중)|
|`isdigit()`|`iswdigit()`|(작성중)|
|`isxdigit()`|`iswxdigit()`|(작성중)|
|`iscntrl()`|`iswcntrl()`|(작성중)|
|`isgraph()`|`iswgraph()`|(작성중)|
|`isspace()`|`iswspace()`|(작성중)|
|`isblank()` (C++11~)|`iswblank()` (C++11~)|(작성중)|
|`isprint()`|`iswprint()`|(작성중)| 
|`ispunct()`|`iswpunct()`|(작성중)| 
||`iswctype()`|(작성중)| 
||`wctype()`|(작성중)|  
||`locale()`|(작성중)| 
 
**문자 조작**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`tolower()`|`towlower()`|(작성중)|
|`toupper()`|`towupper()`|(작성중)|
||`towctrans()`|(작성중)|
||`wctrans()`|(작성중)|

**문자열 조작**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`strcpy()`|`wcscpy()`|(작성중)|
|`strncpy()`|`wcsncpy()`|(작성중)|
|`strcat()`|`wcscat()`|(작성중)|
|`strncat()`|`wcsncat()`|(작성중)|
|`strxfrm()`|`wcsxfrm()`|(작성중)|

**문자열 검사**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`strlen()`|`wcslen()`|(작성중)|
|`strcmp()`|`wcscmp()`|(작성중)|
|`strncmp()`|`wcsncmp()`|(작성중)|
|`strcoll()`|`wcscoll()`|(작성중)|
|`strchr()`|`wcschr()`|(작성중)|
|`strrchr()`|`wcsrchr()`|(작성중)|
|`strspn()`|`wcsspn()`|(작성중)|
|`strcspn()`|`wcscspn()`|(작성중)|
|`strpbrk()`|`wcspbrk()`|(작성중)|
|`strstr()`|`wcsstr()`|(작성중)|
|`strtok()`|`wcstok()`|(작성중)|

**메모리 조작**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`memchr()`|`wmemchr()`|(작성중)|
|`memcmp()`|`wmemcmp()`|(작성중)|
|`memset()`|`wmemset()`|(작성중)|
|[memcpy()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)|`wmemcpy()`|(작성중)| 
|`memmove()`|`wmemmove()`|(작성중)| 

**숫자 포맷**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`atof()`||(작성중)|
|`atoi()`<br/>`atol()`<br/>`atoll()` (C++11~)||(작성중)|
|`strtol()`<br/>`strtoll()` (C++11~)|`wcstol()`<br/>`wcstoll()`|(작성중)|
|`strtoul()`<br/>`strtoull()` (C++11~)|`wcstoul()`<br/>`wcstoull()`|(작성중)|
|`strtof()`<br/>`strtod()`<br/>`strtold()`|`wcstof()`<br/>`wcstod()`<br/>`wcstold()`|(작성중)|
|`strtoimax()` (C++11~)<br/>`strtoumax()` (C++11~)|`wcstoimax()` (C++11~)<br/>`wcstoumax()` (C++11~)|(작성중)|

# 와이드 문자열

**타입**

|항목|내용|
|--|--|
|`wctrans_t`|(작성중)|
|`wctype_t`|(작성중)|
|`wint_t`|(작성중)| 

**매크로**

|항목|내용|
|--|--|
|`WEOF`|(작성중)|
|`WCHAR_MIN`|(작성중)|
|`WCHAR_MAX`|(작성중)| 

# 멀티 바이트

**멀티 바이트 문자열과 와이드 문자열간 변환**

|항목|내용|
|--|--|
|[mblen(str)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|`str` 주소의 문자가 몇 바이트 크기인지 구합니다.|
|`mbtowc()`|(작성중)|
|`wctomb()`|(작성중)|
|[mbstowcs()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|해당 주소의 문자들의 코드를 `wchar_t`로 변환합니다.|
|`wcstombs()`|(작성중)|
|`wcstombs()`|(작성중)|
|`mbsinit()`|(작성중)|
|`btowc()`|(작성중)|
|`wctob()`|(작성중)|
|`mbrlen()`|(작성중)| 
|`mbrtowc()`|(작성중)| 
|`wcrtomb()`|(작성중)| 
|`mbsrtowcs()`|(작성중)| 
|`wcsrtombs()`|(작성중)| 
|`mbrtoc8()` (C++20~)|(작성중)| 
|`c8rtomb()` (C++20~)|(작성중)| 
|`mbrtoc16()` (C++11~)|(작성중)| 
|`c16rtomb()` (C++11~)|(작성중)| 
|`mbrtoc32()` (C++11~)|(작성중)| 
|`c32rtomb()` (C++11~)|(작성중)| 

**타입**

|항목|내용|
|--|--|
|`mbstate_t`|(작성중)|

**매크로**

|항목|내용|
|--|--|
|`MB_LEN_MAX`|(작성중)|
|[MB_CUR_MAX](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|현재 [locale()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#c%EC%8A%A4%ED%83%80%EC%9D%BC-locale)에서 멀티 바이트 문자의 최대 크기입니다.|
|`__STDC_UTF_16__` (C++11~)|(작성중)| 
|`__STDC_UTF_32__` (C++11~)|(작성중)| 

# strerror()

[errorno](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%A4%EB%A5%98-%EB%B2%88%ED%98%B8)를 문자열로 출력해 줍니다.

# basic_string

대부분 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 동일하며, [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)에 필요한 기능이 추가되었습니다. 

**정적 멤버 변수**

|항목|내용|
|--|--|
|`npos`|"찾을 수 없음"이나 "나머지 모든 문자"를 나타내는 의미로 사용합니다.|

```cpp
static const size_type npos = -1;
```

**생성자**

|항목|내용|
|--|--|
|`basic_string()`|빈 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 개체를 생성합니다.|
|`basic_string(const string& other)`|[복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.|
|`basic_string(const value_type* ptr)`|C 언어 스타일의 문자열로부터 문자들을 복사하여 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 개체를 생성합니다.|
|`basic_string(const string& other, size_type, offset, size_type count = npos)`<br/>`basic_string(const value_type* ptr, size_type count)`<br/>|`offset` 위치 부터 `count` 갯수만큼 문자들을 복사하여 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)개체를 생성합니다.|
|`basic_string(size_type count, value_type char_value)`|`char_value`를 `count`만큼 채운[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)을 생성합니다.|
|`basic_string(const_iterator first, const_iterator last)`|`first`부터 `last` 직전까지의 요소(반개방 구조)를 복사하여 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 개체를 생성합니다.|

**연산자**

|항목|내용|
|--|--|
|`operator =(const basic_stiring& other)`|`other`를 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)합니다.|
|`operator =(const basic_stiring&& other) noexcept` (C++11~)|`other`를 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.|
|`+`|두 문자열을 연결하여 새로운 문자열을 만듭니다.|
|`+=`|문자열에 문자나 문자열을 추가합니다.|
|`==`<br/>`!=` (~C++20)|두 문자열이 같은지, 다른지 검사합니다. 단순히 포인터 비교하는게 아니라 실제 문자들을 비교합니다.|
|`<, <=, >, >=` (~C++20)<br/>`<=>` (C++20~)|두 문자열을 대소 비교합니다.|
|`<<, >>`|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

**할당과 문자열 관리**

|항목|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 특화|내용|
|--|--|--|
|[assign()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A0%EB%8B%B9-%ED%95%A9%EC%84%B1-%EB%B9%84%EA%B5%90assign-append-compare)||[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)의 기존 내용을 지우고 새로운 문자열을 복사합니다.|
|`assign_range()` (C++11~)||(작성중)|
|`get_allocator()` (C++23~)||(작성중)|
|`resize()`||[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)의 새 크기를 지정하며, 필요에 따라 문자를 추가하거나 지웁니다.|
|[size()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B8%B8%EC%9D%B4size-length-empty)<br/>`[length()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B8%B8%EC%9D%B4size-length-empty)|O|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)의 문자 갯수를 리턴합니다.<br/>[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)에서는 `length`를 추가로 제공합니다.|
|[empty()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B8%B8%EC%9D%B4size-length-empty)||[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이 비었는지 확인합니다.|
|[capacity()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)||메모리를 더 할당하지 않고 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)에 저장할 수 있는 문자 갯수를 리턴합니다.([string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)은 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 동일하게 메모리 할당이 빈번히 발생하지 않도록 실제 문자 갯수보다 큰 크기를 할당하기도 하고, 삽입/삭제에 따라 실제 문자 갯수보다 더 많은 메모리를 관리할 수 있습니다.)|
|`shrink_to_fit()`||(작성중)|
|[reserve()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#%EC%A0%80%EC%9E%A5-%EA%B3%B5%EA%B0%84-%EC%98%88%EC%95%BD)||[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)의 메모리 공간을 최소한 `count` 갯수 만큼 문자를 저장할 수 있도록 예약합니다.|
|`max_size()`||[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이 저장할 수 있는 최대 문자 갯수를 리턴합니다.|
|`swap()`||두 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)의 내부 데이터를 바꿔치기 합니다.|

**문자 검색**

|항목|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 특화|내용|
|--|--|--|
|`[]`||요소에 접근합니다.|
|[at()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#-%EA%B3%BC-at)||`position`위치의 요소 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. `position`이 잘못된 위치이면 `[]` 과 달리 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`begin(), end()`||순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다.|
|`rbegin(), rend()`||[역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`cbegin(), cend()`||순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin() crend()`||[역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|[data()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4%EA%B3%BC%EC%9D%98-%ED%98%B8%ED%99%98data-c_str)||[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)가 관리하는 메모리 블록을 리턴합니다. STL 구현에 따라 끝에 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)가 있을 수도 있지만, 표준이 아니기 때문에 [널종료 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 구할 때는 [c_str()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4%EA%B3%BC%EC%9D%98-%ED%98%B8%ED%99%98data-c_str)을 이용해야 합니다.|
|[c_str()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4%EA%B3%BC%EC%9D%98-%ED%98%B8%ED%99%98data-c_str)|O|c 스타일의 [널종료 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 리턴합니다.(끝에 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)가 있습니다.)|
|`front()`||첫번째 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`back()`||마지막 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|[find()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%83%90%EC%83%89find-find_first_of-find_last_of)<br/>`rfind()`|O|지정된 문자 시퀀스와 일치하는 첫번째 인덱스를 찾습니다.|
|[find_first_of()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%83%90%EC%83%89find-find_first_of-find_last_of)<br/>`find_first_not_of()`|O|지정된 문자들중 일치하는 문자가 있는 첫번째 인덱스를 찾습니다.|
|[find_last_of()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%83%90%EC%83%89find-find_first_of-find_last_of)<br/>`find_last_not_of()`|O|지정된 문자들중 일치하는 문자가 있는 마지막 인덱스를 찾습니다.|
|`operator basic_string_view()` (C++17~)|O|(작성중)|

**요소 삽입/삭제/비교/추출**

|항목|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 특화|내용|
|--|--|--|
|[clear()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#clear%EC%99%80-swap)||모든 요소를 지웁니다. 이때 메모리 영역은 그대로 입니다.|
|[erase()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98-erase%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-remove-%ED%95%A8%EC%88%98)||`position`위치의 요소를 삭제하거나 `first`와 `last` 직전까지의 요소(반개방 구조)를 삭제합니다. [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)가 유효하지 않다면, 아무 생각없이 실행되어 오동작 합니다. |
|`erase_if()` (C++20~)||(작성중)|
|`pop_back()`||마지막 요소를 삭제합니다. [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이 비었다면 아무 동작 안합니다.|
|[push_back()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C)||[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)끝에 요소를 추가합니다.|
|[insert()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C)||`position`으로 지정한 위치 앞에 삽입합니다.|
|`insert_range()` (C++23~)||(작성중)|
|[append()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A0%EB%8B%B9-%ED%95%A9%EC%84%B1-%EB%B9%84%EA%B5%90assign-append-compare)|O|문자열에 문자나 문자열을 추가합니다.|
|`append_range()` (C++23~)||(작성중)|
|[compare()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A0%EB%8B%B9-%ED%95%A9%EC%84%B1-%EB%B9%84%EA%B5%90assign-append-compare)|O|문자열을 대소 비교합니다.|
|`starts_with()` (C++20~)|O|(작성중)|
|`ends_with()` (C++20~)|O|(작성중)|
|`contains()` (C++23~)|O|(작성중)|
|[replace()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%B6%80%EB%B6%84-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%B2%98%EB%A6%ACreplace-substr-copy)|O|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)의 요소를 지정한 문자나 다른 시퀀스로 바꿉니다.|
|`replace_with_range()` (C++23~)|O|(작성중)|
|[substr()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%B6%80%EB%B6%84-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%B2%98%EB%A6%ACreplace-substr-copy)|O|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)에서 부분 문자열을 복사하여 리턴합니다.|
|[copy()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%B6%80%EB%B6%84-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%B2%98%EB%A6%ACreplace-substr-copy)|O|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)에서 부분 문자열을 문자 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)에 복사합니다.|
|`getline()`|O|IO 스트림으로부터 데이터를 읽습니다.|

# (C++11~) 숫자 변환

|항목|내용|
|--|--|
|`stoi(), stol(), stoll()` (C++11~)|부호 있는 정수를 문자열로 바꿉니다.|
|`stoul(), stoull()` (C++11~)|부호 없는 정수를 문자열로 바꿉니다.| 
|`stof(), stod(), stold()` (C++11~)|실수를 문자열로 바꿉니다.| 
|`to_string()` (C++11~)|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)을 정수 또는 실수로 바꿉니다.| 
|`to_wstring()` (C++11~)|[wstring](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)을 정수 또는 실수로 바꿉니다.|  

# (C++17~) 숫자/문자열 변환

C++17 부터 숫자와 문자열간의 변환을 위한 [to_char(), from_char()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-%EC%88%AB%EC%9E%90%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%B3%80%ED%99%98) 함수가 제공되며, 기존 C스타일([atoi(), strtol()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98 )등) 보다 안전합니다.

|항목|내용|
|--|--|
|`to_chars()` (C++17~)|정수, 실수를 문자열로 변환합니다.|
|`to_chars_result` (C++17~)|`to_chars()`의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)입니다. 변환된 문자열을 끝 포인터(`ptr`)와 에러 코드(`ec`, [errc](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98) 타입)로 구성됩니다.|
|`from_chars()` (C++17~)|문자열을 정수, 실수로 변환합니다.|
|`from_chars_result` (C++17~)|`from_chars()`의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)입니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달된 문자열에서 숫자로 해석할 수 없는 위치(`ptr`)와 에러 코드(`ec`, [errc](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98) 타입)로 구성됩니다.|
|`chars_format` (C++17~)|`scientific, fixed, hex, general(fixed 와 scientific)` 옵션을 제공합니다.|

```cpp
char buf[10];
{      
    // 11을 10진수 문자열로 변환    
    auto [ptr, ec]{std::to_chars(buf, buf + sizeof(buf), 11, 10)}; // 구조화된 바인딩. std::to_chars_result result{std::to_chars()} 와 동일
    if (ec == std::errc{}) {
        EXPECT_TRUE(std::string(buf, ptr - buf) == "11");
    }
}
{
    // 11을 16진수 문자열로 변환
    auto [ptr, ec]{std::to_chars(buf, buf + sizeof(buf), 11, 16)};
    EXPECT_TRUE(std::string(buf, ptr - buf) == "b");
}
{
    char str[]{"11year"}; // 숫자와 일반 문자로 구성됩니다.
    int result{0};
    auto [ptr, ec]{std::from_chars(str, str + sizeof(str), result)};
    if (ec == std::errc{}) {
        EXPECT_TRUE(result == 11); // 숫자 부분만 잘 변환합니다.
    }
    EXPECT_TRUE(ptr == &str[2]); // year는 숫자가 아니므로 첫 문자 주소를 리턴합니다.
}
```

# (C++20~)u8string

C++20 부터는 UTF8 을 관리할 수 있도록 `u8string`이 추가되었습니다.