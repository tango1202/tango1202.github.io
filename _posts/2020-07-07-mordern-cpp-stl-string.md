---
layout: single
title: "#7. [모던 C++ STL] 문자열"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

문자열을 처리하는 기본 클래스는 `basic_stirng`이며, 처리하는 문자 타입에 따라 타입을 재정의하여 사용합니다.

|항목|내용|
|--|--|
|`basic_string`|문자열의 기본 클래스입니다.<br/> * `string` : `basic_string<char>` 입니다.<br/>* `wstring` : `basic_string<wchar_t>` 입니다.<br/>* `u8string` (C++20~) : `basic_string<char8_t>` 입니다.<br/>* `u16string` (C++11~) : `basic_string<char16_t>` 입니다.<br/>* `u32string` (C++11~) : `basic_string<char32_t>` 입니다.|
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

따라서, 문자열 관련 함수들은 문자열의 끝은 `\0` 문자라고 가정하고 개발되었습니다.

# 바이트 문자열, 멀티 바이트 문자열, 와이드 문자열

# C스타일 문자열 함수



# basic_string

대부분 `vector`와 동일하며, `string`에 필요한 기능이 추가되었습니다.

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
|`basic_string(const string& other, size_type, offset, size_type count = npos)`<br/>`string(const value_type* ptr, size_type count)`<br/>|`offset` 위치 부터 `count` 갯수만큼 문자들을 복사하여 `string`개체를 생성합니다.|
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
|`begin()`||첫번째 요소의 이터레이터를 리턴합니다.|
|`end()`||마지막 요소의 다음 위치의 이터레이터를 리턴합니다.|
|`rbegin()`||반전된 `string`의 첫번째 요소의 역방향 이터레이터를 리턴합니다.|
|`rend()`||반전된 `string`의 마지막 요소의 다음 위치의 역방향 이터레이터를 리턴합니다.|
|`cbegin()`||첫번째 요소의 이터레이터를 리턴합니다.|
|`cend()`||마지막 요소의 다음 위치의 이터레이터를 리턴합니다.|
|`crbegin()`||반전된 `string`의 첫번째 요소의 역방향 이터레이터를 리턴합니다.|
|`crend()`||반전된 `string`의 마지막 요소의 다음 위치의 역방향 이터레이터를 리턴합니다.|
|`data()`||내부 데이터를 리턴합니다. 끝에 널문자가 없을 수도 있습니다.|
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
