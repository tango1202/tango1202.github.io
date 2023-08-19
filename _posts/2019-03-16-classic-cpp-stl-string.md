---
layout: single
title: "#16. [고전 C++ STL] 문자열"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `string`과 `wstring`은 소멸자가 `public` Non-Virtual 이므로 상속하여 재구현 하지 마라.
> * 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하라.(배열이나 `std::string`, `std::wstring` 을 쓰면 복제된다.)
> * `string`은 포인터나 참조자 기반이 아니라 값 기반으로 관리되므로, 복사 생성자, `=`, `==`, `!=`, `+`, `+=`, 대소 비교, `assign()`, `append()` 등이 메모리 부하나 속도 부하가 있음을 유의하라. 

# 개요

STL은 1byte 문자(`char`)로 구성된 문자열인 `string`과 와이드 문자(2byte 또는 4byte)로 구성된 문자열인 `wstring`이 있습니다. 다국어 처리를 하려면 `wstring`을 사용하여야 합니다.

두 타입 모두 `basic_string` 기반입니다.

```cpp
typedef basic_string<char>    string;   
typedef basic_string<wchar_t> wstring;  
```

1. `vector`와 유사하게 연속된 메모리를 사용하며,
2. 컨테이너와 같이 이터레이터와 `operator []`를 지원합니다.
3. 소멸자가 `public` Non-Virtual 이므로 상속하여 재구현 하면 안됩니다.([public Non-Virtual](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고) 이는 흔히, 그리고 많이 사용하는 자료형이여서 가상 함수 테이블의 포인터를 저장하기 아깝기 때문입니다.( [가상 함수 테이블](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable) 참고)
4. 널로 끝나는 C 스타일의 문자열은 `c_str()`함수를 사용해야 합니다.(`data()`는 끝이 널문자가 아닐 수도 있습니다.)
5. 문자열 상수를 복제하여 관리합니다. 따라서, 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하는게 좋습니다.([문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98) 참고)
6.  `string`은 포인터나 참조자 기반이 아니라 값 기반으로 관리되므로, 복사 생성자, `=`, `==`, `!=`, `+`, `+=`, 대소 비교, `assign()`, `append()` 등이 메모리 부하나 속도 부하가 있습니다.

```cpp
std::string s1("OK"); // 문자열 상수는 새로운 메모리 공간에 복제되어 저장됩니다.
std::string s2 = "OK"; // s2("OK") 와 동일합니다.
std::string s3 = s1; // s3(s)로 복사 생성자를 호출합니다. 구현에 따라 지연 복사되는 버전도 있습니다. 
std::string s4; // 빈 string 개체를 생성합니다. 
std::string s5 = ""; // 빈 문자열을 생성합니다.
std::string s6(s1, 0, 2); // s1[0]부터 2개 문자를 복사하여 생성합니다.
// std::string s6(s1, 100, 2); // (X) 예외 발생. s1[99]부터 2개 복사. out_of_range
std::string s7(s1, 0, std::string::npos); // s[0] 부터 끝까지 복사하여 생성합니다.
std::string s8(3, 'a'); // 'a'문자를 3개 채웁니다.
EXPECT_TRUE(s8 == "aaa");
const char* ptr = "abcdefg";
std::string s9(ptr, ptr + 3); // ptr + 3 은 d 위치임
EXPECT_TRUE(s9 == "abc"); // d 위치 직전까지 복사하여 생성합니다.

s1.c_str(); // (O) c 스타일의 문자열을 리턴합니다.(끝에 널문자가 있습니다.)
s1.data(); // (△) 비권장. 내부 데이터를 리턴합니다. 끝에 널문자가 없을 수도 있습니다.
EXPECT_TRUE(s1 == "OK"); // (O) 단순 포인터 비교가 아니라 실제 문자들을 비교합니다.

s1.length() == 2; // s2.size() 와 같습니다.
s1[0] = 'a'; // [] 로 문자에 접근할 수 있습니다.
```

# string 정적 멤버 변수

|항목|내용|
|--|--|
|`npos`|"찾을 수 없음"이나 "나머지 모든 문자"를 나타내는 의미로 사용합니다.|

```cpp
static const size_type npos = -1;
```

# string 멤버 함수

대부분 `vector`와 동일하며, `string`에 필요한 요소가 추가되었습니다.

**생성자**

|항목|내용|
|--|--|
|`string()`|빈 `string` 개체를 생성합니다.|
|`string(const string& other)`|복사 생성합니다.|
|`string(const value_type* ptr)`|C 언어 스타일의 문자열로부터 문자들을 복사하여 `string` 개체를 생성합니다.|
|`string(const string& other, size_type, offset, size_type count = npos)`<br/>`string(const value_type* ptr, size_type count)`<br/>|`offset` 위치 부터 `count` 갯수만큼 문자들을 복사하여 `string`개체를 생성합니다.|
|`string(size_type count, value_type char_value)`|`char_value`를 `count`만큼 채운 `string`을 생성합니다.|
|`basic_string(const_iterator first, const_iterator last)`|`first`부터 `last` 직전까지의 요소(반개방 구조)를 복사하여 `string` 개체를 생성합니다.|

**연산자**

|항목|내용|
|--|--|
|`+`|두 문자열을 연결하여 새로운 문자열을 만듭니다.|
|`+=`|문자열에 문자나 문자열을 추가합니다.|
|`!=`<br/>`==`|두 문자열이 같은지, 다른지 검사합니다. 단순히 포인터 비교하는게 아니라 실제 문자들을 비교합니다.|
|`<`<br/>`<=`<br/>`>`<br/>`>=`|두 문자열을 대소 비교합니다.|
|`<<`<br/>`>>`|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

**할당과 문자열 관리**

|항목|`string` 특화|내용|
|--|--|--|
|`assign()`||`string`의 기존 내용을 지우고 새로운 문자열을 복사합니다.|
|`resize()`||`string`의 새 크기를 지정하며, 필요에 따라 문자를 추가하거나 지웁니다.|
|`size()`<br/>`length()`|O|`string`의 문자 갯수를 리턴합니다.<br/>`string`에서는 `length`를 추가로 제공합니다.|
|`empty()`||`string`이 비었는지 확인합니다.|
|`capacity()`||메모리를 더 할당하지 않고 `string`에 저장할 수 있는 문자 갯수를 리턴합니다.(`string`은 `vector`와 동일하게 메모리 할당이 빈번히 발생하지 않도록 실제 문자 갯수보다 큰 크기를 할당하기도 하고, 삽입/삭제에 따라 실제 문자 갯수보다 더 많은 메모리를 관리할 수 있습니다.)|
|`reserve()`||`string`의 메모리 공간을 최소한 `count` 갯수 만큼 문자를 저장할 수 있도록 예약합니다.|
|`max_size()`||`string`이 저장할 수 있는 최대 문자 갯수를 리턴합니다.|
|`swap()`|O|`vector`는 각 요소들을 바꿔치기 하지만 `string`은 내부 데이터 포인터를 바꿔치기 합니다.|

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

**요소 삽입/삭제/비교/추출**

|항목|`string` 특화|내용|
|--|--|--|
|`clear()`||모든 요소를 지웁니다. 이때 메모리 영역은 그대로 입니다.|
|`erase()`||`position`위치의 요소를 삭제하거나 `first`와 `last` 직전까지의 요소(반개방 구조)를 삭제합니다. 이터레이터가 유효하지 않다면, 아무 생각없이 실행되어 오동작 합니다. |
|`pop_back()`||마지막 요소를 삭제합니다. `string`이 비었다면 아무 동작 안합니다.|
|`push_back()`||`string`끝에 요소를 추가합니다.|
|`insert()`||`position`으로 지정한 위치 앞에 삽입합니다.|
|`append()`|O|문자열에 문자나 문자열을 추가합니다.|
|`compare()`|O|문자열을 대소 비교합니다.|
|`replace()`|O|`string`의 요소를 지정한 문자나 다른 시퀀스로 바꿉니다.|
|`substr()`|O|`string`에서 하위 문자열을 추출합니다.|
|`copy()`|O|`string`에서 하위 문자열을 문자 배열에 복사합니다.|







