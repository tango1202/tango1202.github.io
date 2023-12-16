---
layout: single
title: "#36. [Mordern C++ STL] (C++17~) string_view"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string_view)가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 문자열 복제가 없도록 해줍니다.

# 개요

문자열에 대한 읽기만 필요한 함수가 있는 경우 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이나 `const char*`로 전달받을 수 있는데요, 둘다 문제가 좀 있습니다.

1. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 로 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)을 사용한 경우 : 암시적으로 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)개체가 생성됩니다.

    ```cpp
    std::size_t Func(const std::string& str) {return str.length();}

    std::string str1{"Hello"};
    const char* str2{"World"};

    EXPECT_TRUE(Func(str1) == 5);
    EXPECT_TRUE(Func(str2) == 5); // (△) 비권장. 암시적으로 string 개체가 생성됩니다.
    ```

2. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 `const char*`를 사용한 경우 : [널종료 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 강제로 만들어야 합니다.  
    
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

C++17 부터는 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string_view)가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 문자열 복제가 없도록 해줍니다.

내부적으로 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이나 `const char*`를 참조만 하여 메모리 낭비를 없애고, 읽기 전용 함수들만 제공하여 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 관련 편의 기능들을 그대로 사용할 수 있습니다.

```cpp
std::size_t Func(std::string_view sv) {
    return sv.length();
}

std::string str1{"Hello"};
const char* str2{"World"};

EXPECT_TRUE(Func(str1) == 5); // (O) 불필요하게 string 개체를 생성하지 않습니다.
EXPECT_TRUE(Func(str2) == 5); // (O) 불필요하게 string 개체를 생성하지 않습니다.
```

[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)처럼 기본 클래스는 `basic_string_view` 이며, 처리하는 문자 타입에 따라 재정의한 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string_view), `wstring_view`등을 사용합니다.

|항목|내용|
|--|--|
|[string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string_view) (C++17~)|`basic_string_view<char>`|
|`wstring_view` (C++17~)|`basic_string_view<wchar_t>`|
|`u16string_view ` (C++17~)|`basic_string_view<char16_t>`|
|`u32string_view ` (C++17~)|`basic_string_view<char32_t>`|
|`u8string_view ` (C++20~)|`basic_string_view<char16_t>`|

# 생성자

|항목|내용|
|--|--|
|`basic_string_view()` (C++17~)|빈 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string_view) 개체를 생성합니다.|
|`basic_string_view(const basic_string_view& other)` (C++17~)|[복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)합니다. 이때 내부 관리 문자열은 참조만 하고 복제하지는 않습니다.|
|`basic_string_view(const value_type* ptr)` (C++17~)|C 언어 스타일의 문자열을 참조합니다.|
|`basic_string_view(const value_type* ptr, size_type count)` (C++17~)|`count`길이([널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4) 포함)인 C 언어 스타일의 문자열을 참조합니다.|

# 연산자

|항목|내용|
|--|--|
|`operator =(const basic_string_view& other)` (C++17~)|`other`를 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)합니다.|
|`==` (C++17~)<br/>`!=` (~C++20)|두 문자열이 같은지, 다른지 검사합니다. 단순히 포인터 비교하는게 아니라 실제 문자들을 비교합니다.|
|`<, <=, >, >=` (~C++20)<br/>`<=>` (C++20~)|두 문자열을 대소 비교합니다.|
|`<<, >>` (C++17~)|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

# 할당과 문자열 관리

|항목|내용|
|--|--|
|[size()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B8%B8%EC%9D%B4size-length-empty) (C++17~)<br/>[length()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B8%B8%EC%9D%B4size-length-empty) (C++17~)|문자 갯수를 리턴합니다.|
|[empty()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B8%B8%EC%9D%B4size-length-empty) (C++17~)|문자열이 비었는지 확인합니다.|
|`max_size()` (C++17~)|저장할 수 있는 최대 문자 갯수를 리턴합니다.|
|`swap()` (C++17~)|두 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string_view)의 내부 데이터를 바꿔치기 합니다.|

 # 문자 검색

|항목|내용|
|--|--|
|`[]` (C++17~)|요소에 접근합니다.|
|`at()` (C++17~)|`position`위치의 요소 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. `position`이 잘못된 위치이면 `[]` 과 달리 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`begin(), end()` (C++17~)|순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다.|
|`rbegin(), rend()` (C++17~)|[역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`cbegin(), cend()` (C++17~)|순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin() crend()` (C++17~)|[역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|[data()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4%EA%B3%BC%EC%9D%98-%ED%98%B8%ED%99%98data-c_str) (C++17~)|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)가 관리하는 메모리 블록을 리턴합니다. 끝에 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)가 없을 수도 있습니다.|
|`front()` (C++17~)|첫번째 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. 문자열이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`back()` (C++17~)|마지막 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. 문자열이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|[find()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%83%90%EC%83%89find-find_first_of-find_last_of) (C++17~)<br/>`rfind()` (C++17~)|지정된 문자 시퀀스와 일치하는 첫번째 인덱스를 찾습니다.|
|[find_first_of()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%83%90%EC%83%89find-find_first_of-find_last_of) (C++17~)<br/>`find_first_not_of()` (C++17~)|지정된 문자들중 일치하는 문자가 있는 첫번째 인덱스를 찾습니다.|
|[find_last_of()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%83%90%EC%83%89find-find_first_of-find_last_of) (C++17~)<br/>`find_last_not_of()` (C++17~)|지정된 문자들중 일치하는 문자가 있는 마지막 인덱스를 찾습니다.|

# 요소 삽입/삭제/비교/추출

|항목|내용|
|--|--|
|[compare()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A0%EB%8B%B9-%ED%95%A9%EC%84%B1-%EB%B9%84%EA%B5%90assign-append-compare) (C++17~)|문자열을 대소 비교합니다.|
|`starts_with()` (C++20~)|(작성중)|
|`ends_with()` (C++20~)|(작성중)|
|`contains()` (C++23~)|(작성중)|
|[substr()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%B6%80%EB%B6%84-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%B2%98%EB%A6%ACreplace-substr-copy) (C++17~)|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)에서 부분 문자열을 복사하여 리턴합니다.|
|[copy()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/#%EB%B6%80%EB%B6%84-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%B2%98%EB%A6%ACreplace-substr-copy) (C++17~)|[string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)에서 부분 문자열을 문자[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)에 복사합니다.|
