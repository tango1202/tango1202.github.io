---
layout: single
title: "#7. [모던 C++ STL] 문자열, (C++17~) string_view, 숫자/문자열 변환(to_chars(), from_chars())"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `string`은 바이트 문자열을 지원합니다.
> * `wstring`은 와이드 문자열을 지원합니다.
> * (C++11~) `u16string`은 UTF-16을 지원합니다.
> * (C++11~) `u32string`은 UTF-32를 지원합니다.
> * `strerror()`는 `errorno`([오류 번호](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%A4%EB%A5%98-%EB%B2%88%ED%98%B8) 참고)를 문자열로 출력해 줍니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.
> * (C++17~) [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view)가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 `string`복제가 없도록 해줍니다.
> * (C++17~) 숫자와 문자열간의 변환을 위한 `to_char(), from_char()` 함수가 제공되며, 기존 C스타일(`itoa(), strtol()`등) 보다 안전합니다.
> * (C++20~) `u8string`은 UTF-8을 지원합니다.

# 개요

문자열을 처리하는 기본 클래스는 `basic_string`이며, 처리하는 문자 타입에 따라 재정의한 `string`, `wstring`등을 사용합니다.

`string`, `wstring`등의 사용 방법은 [문자열](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)을 참고하기 바랍니다.

|항목|내용|
|--|--|
|`basic_string`|문자열의 기본 클래스입니다.<br/> * `string` : `basic_string<char>` 입니다. 바이트 문자열을 지원합니다.<br/>* `wstring` : `basic_string<wchar_t>` 입니다. 와이드 문자열을 지원합니다.<br/>* `u8string` (C++20~) : `basic_string<char8_t>` 입니다. UTF-8을 지원합니다.<br/>* `u16string` (C++11~) : `basic_string<char16_t>` 입니다. UTF-16을 지원합니다.<br/>* `u32string` (C++11~) : `basic_string<char32_t>` 입니다. UTF-32를 지원합니다.|
|`char_traits`|`basic_string`에서 각 문자를 처리하는 타입 특성입니다.|
|`basing_string_view` (C++17~)|(작성중)|

# 널종료 문자열

이전 C스타일의 문자열은 문자열의 끝을 널문자로 표시합니다.

```cpp
char str1[] = "abc"; // (O) {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str1[0] == 'a');
EXPECT_TRUE(str1[1] == 'b');
EXPECT_TRUE(str1[2] == 'c');
EXPECT_TRUE(str1[3] == '\0'); // 널문자가 추가됨
```

따라서, C스타일 문자열 함수들은 문자열의 끝은 `\0` 문자라고 가정하고 개발되었습니다.

# 저장 방식 관점 : 바이트 문자열, 멀티 바이트 문자열, 와이드 문자열

영문자의 경우 0 ~ 127 까지의 7bit 만으로도 표현이 충분했기 때문에 1byte로도 저장이 가능합니다. 이렇게 1byte 단위로 문자를 관리하는 문자열을 **바이트 문자열** 이라 합니다.

그런데, 다양한 국가의 언어를 사용하다보니 1byte로는 처리가 불가능했고, 127 이상의 값을 가진 문자 코드는 2byte로 처리하게 되었습니다. 이처럼 1byte와 2byte를 혼용하는 문자열을 **멀티 바이트 문자열** 이라고 합니다.(Microsoft에서 초창기에 만들어 사용했지만, 표준화 되지 않았고, 현재는 비권고 되고 있습니다.)

**와이드 문자열** 은 영문자이건, 다국어 문자이건 모두 `wchar_t`로 관리하는 문자열입니다. 안타깝게도 이또한 시스템의 처리방식에 따라 크기가 다릅니다.(리눅스는 4byte이고, Windows 2byte 입니다. [기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 참고)

**와이드 문자열** 은 고정된 크기(2byte또는 4byte) 이기 때문에 [유니코드](??)를 효율적으로 처리할 수 없습니다.

이에 C++11 부터 STL에서는 유니코드를 지원하는 타입인 `char16_t`, `char_32_t`를 제공하고 있습니다.

# 코드 관점 : 유니코드

바이트 문자열, 멀티 바이트 문자열, 와이드 문자열은 문자를 어떤 크기로 저장할 것인가에 관한 내용이고, 실제 문자의 코드값을 처리하는 것은 역사적인 이유로 상당히 복잡해 졌습니다.

앞서 언급했듯 컴퓨터에서는 문자를 표현하기 위해서 0 ~ 127 까지의 7비트 체계의 아스키 코드(ASCII Code)를 사용했습니다.

하지만, 다양한 국가에서 다양한 언어를 사용하다 보니 7비트로는 표현이 불가능하여 각 국가별로 자체적인 코드 테이블을 사용했고, 서로 호환되지 않았습니다. 이에 전 세계의 모든 문자에 고유 숫자를 부여한 유니코드를 만들었는데요, 이것도 점진적으로 추가되고, 하위 호환을 유지하다보니 처리 방식이 좀 복잡합니다.

유니코드는 기본적으로 2byte를 사용하도록 했으나, 아시아권 문자를 포함하다 보니 3byte가 필요하게 되었고, 다양한 추가 문자들을 지원하다 보니 4byte가 필요(악보 기호, 이모지등 특수 기호 지원)하게 되었으며, 현대 한글의 모든 문자 11,172개는 U+AC00 ~ D7A3에 할당되어 있습니다. 이러다 보니 유니코드는 2byte다 4byte다 혼선이 있는데, 결과적으로는 2byte 이상이다가 맞겠습니다.

유니코드는 `U+16진수`로 표기합니다. 예를 들어 한글 "가"는 `AC00`(10진수의 `44032`)인데, `U+AC00`으로 표기합니다.

# 유니코드 인코딩

유니코드를 최종본인 4byte로만 표현하기엔 메모리 낭비가 심하므로 인코딩을 통해 문자 변환을 합니다.

인코딩 방식은 다음의 3가지 방식이 있습니다.

|항목|내용|
|--|--|
|UTF-8|문자마다 다른 크기로 저장합니다. 이에 따라 문자 데이터 앞에 크기 정보가 필요하며, 최상위 비트가 `0`이면 7비트로 처리하고 `110`이면 2byte, `1110`이면 3byte, `11110`이면 4byte로 처리합니다.(웹 기본)<br/>* 영어 및 기호 : 1byte<br/>* 추가 라틴 및 중동 : 2byte<br/>* 한글 및 아시아 : 3byte(한글은 코드값은 2byte이지만, 크기 정보를 포함하면 3byte가 필요합니다.)<br/>* 추가 문자 : 4byte
|UTF-16|기본적으로 2byte로 처리하고, 2byte로 표현할 수 없는 것들은 4byte로 처리합니다.(java 기본)|
|UTF-32|모든 문자를 4byte로 처리합니다. 메모리 낭비가 심하여 잘 사용하지 않습니다.|

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
|`memcpy()`|`wmemcpy()`|(작성중)| 
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
|`mblen()`|(작성중)|
|`mbtowc()`|(작성중)|
|`wctomb()`|(작성중)|
|`mbstowcs()`|(작성중)|
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
|`MB_CUR_MAX`|(작성중)|
|`__STDC_UTF_16__` (C++11~)|(작성중)| 
|`__STDC_UTF_32__` (C++11~)|(작성중)| 

# strerror()

`errorno`([오류 번호](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%A4%EB%A5%98-%EB%B2%88%ED%98%B8) 참고)를 문자열로 출력해 줍니다.

# basic_string

대부분 `vector`와 동일하며, `string`에 필요한 기능이 추가되었습니다. 각 문자 타입별로 별칭을 만들어 사용합니다.

기존에는 `char`, `wchar_t` 문자열 개체만 지원했는데요([문자열](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/) 참고),

C++11 부터는 `char16_t`, `char32_t` 문자열인 `u16string`과 `u32string`을 지원합니다.

|항목|내용|별칭|
|--|--|
|`string`|바이트 문자열|`basic_string<char>`|
|`wstring`|와이드 문자열|`basic_string<wchar_t>`|
|`u16string` (C++11~)|UTF-16 문자열|`basic_string<char16_t>`|
|`u32string` (C++11~)|UTF_32 문자열|`basic_string<char32_t>`|

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
|`basic_string()`|빈 `string` 개체를 생성합니다.|
|`basic_string(const string& other)`|복사 생성합니다.|
|`basic_string(const value_type* ptr)`|C 언어 스타일의 문자열로부터 문자들을 복사하여 `string` 개체를 생성합니다.|
|`basic_string(const string& other, size_type, offset, size_type count = npos)`<br/>`basic_string(const value_type* ptr, size_type count)`<br/>|`offset` 위치 부터 `count` 갯수만큼 문자들을 복사하여 `string`개체를 생성합니다.|
|`basic_string(size_type count, value_type char_value)`|`char_value`를 `count`만큼 채운 `string`을 생성합니다.|
|`basic_string(const_iterator first, const_iterator last)`|`first`부터 `last` 직전까지의 요소(반개방 구조)를 복사하여 `string` 개체를 생성합니다.|

**연산자**

|항목|내용|
|--|--|
|`operator =(const basic_stiring& other)`|`other`를 복사 대입합니다.|
|`operator =(const basic_stiring&& other) noexcept` (C++11~)|`other`를 이동 대입합니다.|
|`+`|두 문자열을 연결하여 새로운 문자열을 만듭니다.|
|`+=`|문자열에 문자나 문자열을 추가합니다.|
|`==`<br/>`!=` (~C++20)|두 문자열이 같은지, 다른지 검사합니다. 단순히 포인터 비교하는게 아니라 실제 문자들을 비교합니다.|
|`<, <=, >, >=` (~C++20)<br/>`<=>` (C++20~)|두 문자열을 대소 비교합니다.|
|`<<, >>`|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

**할당과 문자열 관리**

|항목|`string` 특화|내용|
|--|--|--|
|`assign()`||`string`의 기존 내용을 지우고 새로운 문자열을 복사합니다.|
|`assign_range()` (C++11~)||(작성중)|
|`get_allocator()` (C++23~)||(작성중)|
|`resize()`||`string`의 새 크기를 지정하며, 필요에 따라 문자를 추가하거나 지웁니다.|
|`size()`<br/>`length()`|O|`string`의 문자 갯수를 리턴합니다.<br/>`string`에서는 `length`를 추가로 제공합니다.|
|`empty()`||`string`이 비었는지 확인합니다.|
|`capacity()`||메모리를 더 할당하지 않고 `string`에 저장할 수 있는 문자 갯수를 리턴합니다.(`string`은 `vector`와 동일하게 메모리 할당이 빈번히 발생하지 않도록 실제 문자 갯수보다 큰 크기를 할당하기도 하고, 삽입/삭제에 따라 실제 문자 갯수보다 더 많은 메모리를 관리할 수 있습니다.)|
|`shrink_to_fit()`||(작성중)|
|`reserve()`||`string`의 메모리 공간을 최소한 `count` 갯수 만큼 문자를 저장할 수 있도록 예약합니다.|
|`max_size()`||`string`이 저장할 수 있는 최대 문자 갯수를 리턴합니다.|
|`swap()`||두 `string`의 내부 데이터를 바꿔치기 합니다.|

**문자 검색**

|항목|`string` 특화|내용|
|--|--|--|
|`[]`||요소에 접근합니다.|
|`at()`||`position`위치의 요소 참조자를 리턴합니다. `position`이 잘못된 위치이면 `[]` 과 달리 예외를 발생시키며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`begin(), end()`||순방향 이터레이터를 리턴합니다.|
|`rbegin(), rend()`||역방향 이터레이터를 리턴합니다.|
|`cbegin(), cend()`||방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin() crend()`||역방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`data()`||컨테이너가 관리하는 메모리 블럭을 리턴합니다. 끝에 널문자가 없을 수도 있습니다.|
|`c_str()`|O|c 스타일의 문자열을 리턴합니다.(끝에 널문자가 있습니다.)|
|`front()`||첫번째 요소의 참조자를 리턴합니다. `string`이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`back()`||마지막 요소의 참조자를 리턴합니다. `string`이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`find()`<br/>`rfind()`|O|지정된 문자 시퀀스와 일치하는 첫번째 인덱스를 찾습니다.|
|`find_first_of()`<br/>`find_first_not_of()`|O|지정된 문자들중 일치하는 문자가 있는 첫번째 인덱스를 찾습니다.|
|`find_last_of()`<br/>`find_last_not_of()`|O|지정된 문자들중 일치하는 문자가 있는 마지막 인덱스를 찾습니다.|
|`operator basic_string_view()` (C++17~)|O|(작성중)|

**요소 삽입/삭제/비교/추출**

|항목|`string` 특화|내용|
|--|--|--|
|`clear()`||모든 요소를 지웁니다. 이때 메모리 영역은 그대로 입니다.|
|`erase()`||`position`위치의 요소를 삭제하거나 `first`와 `last` 직전까지의 요소(반개방 구조)를 삭제합니다. 이터레이터가 유효하지 않다면, 아무 생각없이 실행되어 오동작 합니다. |
|`erase_if()` (C++20~)||(작성중)|
|`pop_back()`||마지막 요소를 삭제합니다. `string`이 비었다면 아무 동작 안합니다.|
|`push_back()`||`string`끝에 요소를 추가합니다.|
|`insert()`||`position`으로 지정한 위치 앞에 삽입합니다.|
|`insert_range()` (C++23~)||(작성중)|
|`append()`|O|문자열에 문자나 문자열을 추가합니다.|
|`append_range()` (C++23~)||(작성중)|
|`compare()`|O|문자열을 대소 비교합니다.|
|`starts_with()` (C++20~)|O|(작성중)|
|`ends_with()` (C++20~)|O|(작성중)|
|`contains()` (C++23~)|O|(작성중)|
|`replace()`|O|`string`의 요소를 지정한 문자나 다른 시퀀스로 바꿉니다.|
|`replace_with_range()` (C++23~)|O|(작성중)|
|`substr()`|O|`string`에서 하위 문자열을 추출합니다.|
|`copy()`|O|`string`에서 하위 문자열을 문자 배열에 복사합니다.|
|`getline()`|O|IO 스트림으로부터 데이터를 읽습니다.|

# 숫자 변환

|항목|내용|
|--|--|
|`stoi(), stol(), stoll()` (C++11~)|부호 있는 정수를 문자열로 바꿉니다.|
|`stoul(), stoull()` (C++11~)|부호 없는 정수를 문자열로 바꿉니다.| 
|`stof(), stod(), stold()` (C++11~)|실수를 문자열로 바꿉니다.| 
|`to_string()` (C++11~)|`string`을 정수 또는 실수로 바꿉니다.| 
|`to_wstring()` (C++11~)|`wstring`을 정수 또는 실수로 바꿉니다.|  

# (C++17~) string_view

문자열에 대한 읽기만 필요한 함수가 있는 경우 `string`이나 `const char*`로 전달받을 수 있는데요, 둘다 문제가 좀 있습니다.

1. `string`으로 인자를 만든 경우 : 암시적으로 `string`개체가 생성됩니다.

    ```cpp
    std::size_t Func(const std::string& str) {return str.length();}

    std::string str1{"Hello"};
    const char* str2{"World"};

    EXPECT_TRUE(Func(str1) == 5);
    EXPECT_TRUE(Func(str2) == 5); // (△) 비권장. 암시적으로 string 개체가 생성됩니다.
    ```

2. `const char*` 으로 인자를 만든 경우 : 널 종료 문자열을 강제로 만들어야 합니다.  
    
    ```cpp
    std::size_t Func(const char* str) {
        std::size_t i{0};
        while(str[i] != '\0') {
            ++i;
        } 
        return i;
    }

    std::string str1{"Hello"};
    const char* str2{"World"};

    EXPECT_TRUE(Func(str1.c_str()) == 5); // (△) 비권장. 널종료 문자열을 만듭니다.
    EXPECT_TRUE(Func(str2) == 5); // (△) 비권장. 문자열의 길이는 매번 다시 '\0'까지 카운팅해야 합니다.
    ```

C++17 부터는 `string_view`가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 `string`복제가 없도록 해줍니다.

내부적으로 `string`이나 `const char*`를 참조만 하여 메모리 낭비를 없애고, 읽기 전용 함수들만 제공하여 `string` 관련 편의 기능들을 그대로 사용할 수 있습니다.

```cpp
std::size_t Func(std::string_view sv) {
    return sv.length();
}

std::string str1{"Hello"};
const char* str2{"World"};

EXPECT_TRUE(Func(str1) == 5); // (O) 불필요하게 string 개체를 생성하지 않습니다.
EXPECT_TRUE(Func(str2) == 5); // (O) 불필요하게 string 개체를 생성하지 않습니다.
```

`basic_string`처럼 기본 클래스는 `basic_string_view` 이며, 처리하는 문자 타입에 따라 재정의한 `string_view`, `wstring_view`등을 사용합니다.

|항목|내용|
|--|--|
|`string_view` (C++17~)|`basic_string_view<char>`|
|`wstring_view` (C++17~)|`basic_string_view<wchar_t>`|
|`u16string_view ` (C++17~)|`basic_string_view<char16_t>`|
|`u32string_view ` (C++17~)|`basic_string_view<char32_t>`|
|`u8string_view ` (C++20~)|`basic_string_view<char16_t>`|

**생성자**

|항목|내용|
|--|--|
|`basic_string_view()` (C++17~)|빈 `string_view` 개체를 생성합니다.|
|`basic_string_view(const basic_string_view& other)` (C++17~)|복사 생성합니다. 이때 내부 관리 문자열은 참조만 하고 복제하지는 않습니다.|
|`basic_string_view(const value_type* ptr)` (C++17~)|C 언어 스타일의 문자열을 참조합니다.|
|`basic_string_view(const value_type* ptr, size_type count)` (C++17~)|`count`길이(널문자 포함)인 C 언어 스타일의 문자열을 참조합니다.|

**연산자**

|항목|내용|
|--|--|
|`operator =(const basic_string_view& other)` (C++17~)|`other`를 복사 대입합니다.|
|`==` (C++17~)<br/>`!=` (~C++20)|두 문자열이 같은지, 다른지 검사합니다. 단순히 포인터 비교하는게 아니라 실제 문자들을 비교합니다.|
|`<, <=, >, >=` (~C++20)<br/>`<=>` (C++20~)|두 문자열을 대소 비교합니다.|
|`<<, >>` (C++17~)|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

**할당과 문자열 관리**

|항목|내용|
|--|--|
|`size()` (C++17~)<br/>`length()` (C++17~)|문자 갯수를 리턴합니다.|
|`empty()` (C++17~)|문자열이 비었는지 확인합니다.|
|`max_size()` (C++17~)|저장할 수 있는 최대 문자 갯수를 리턴합니다.|
|`swap()` (C++17~)|두 `string_view`의 내부 데이터를 바꿔치기 합니다.|

**문자 검색**

|항목|내용|
|--|--|
|`[]` (C++17~)|요소에 접근합니다.|
|`at()` (C++17~)|`position`위치의 요소 참조자를 리턴합니다. `position`이 잘못된 위치이면 `[]` 과 달리 예외를 발생시키며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`begin(), end()` (C++17~)|순방향 이터레이터를 리턴합니다.|
|`rbegin(), rend()` (C++17~)|역방향 이터레이터를 리턴합니다.|
|`cbegin(), cend()` (C++17~)|방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin() crend()` (C++17~)|역방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`data()` (C++17~)|컨테이너가 관리하는 메모리 블럭을 리턴합니다. 끝에 널문자가 없을 수도 있습니다.|
|`front()` (C++17~)|첫번째 요소의 참조자를 리턴합니다. 문자열이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`back()` (C++17~)|마지막 요소의 참조자를 리턴합니다. 문자열이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`find()` (C++17~)<br/>`rfind()` (C++17~)|지정된 문자 시퀀스와 일치하는 첫번째 인덱스를 찾습니다.|
|`find_first_of()` (C++17~)<br/>`find_first_not_of()` (C++17~)|지정된 문자들중 일치하는 문자가 있는 첫번째 인덱스를 찾습니다.|
|`find_last_of()` (C++17~)<br/>`find_last_not_of()` (C++17~)|지정된 문자들중 일치하는 문자가 있는 마지막 인덱스를 찾습니다.|

**요소 삽입/삭제/비교/추출**

|항목|내용|
|--|--|
|`compare()` (C++17~)|문자열을 대소 비교합니다.|
|`starts_with()` (C++20~)|(작성중)|
|`ends_with()` (C++20~)|(작성중)|
|`contains()` (C++23~)|(작성중)|
|`substr()` (C++17~)|`string`에서 하위 문자열을 추출합니다.|
|`copy()` (C++17~)|`string`에서 하위 문자열을 문자 배열에 복사합니다.|

# (C++17~) 숫자/문자열 변환

C++17 부터 숫자와 문자열간의 변환을 위한 `to_char(), from_char()` 함수가 제공되며, 기존 C스타일(`itoa(), strtol()`등) 보다 안전합니다.

|항목|내용|
|--|--|
|`to_chars()` (C++17~)|정수, 실수를 문자열로 변환합니다.|
|`to_chars_result` (C++17~)|`to_chars()`의 리턴값입니다. 변환된 문자열을 끝 포인터(`ptr`)와 에러 코드(`ec`, [errc](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98) 타입)로 구성됩니다.|
|`from_chars()` (C++17~)|문자열을 정수, 실수로 변환합니다.|
|`from_chars_result` (C++17~)|`from_chars()`의 리턴값입니다. 인자로 전달된 문자열에서 숫자로 해석할 수 없는 위치(`ptr`)와 에러 코드(`ec`, [errc](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98) 타입)로 구성됩니다.|
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