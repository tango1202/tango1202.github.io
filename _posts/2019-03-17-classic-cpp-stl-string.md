---
layout: single
title: "#17. [고전 C++ STL] 문자열"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [string](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)과 [wstring](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)은 [public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)이므로 [상속](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/)하여 재구현 하지 마라.
> * 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하라.(*[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)이나 [string](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/), [wstring](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)을 쓰면 복제된다.*)
> * [string](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)은 값 기반으로 복사/비교 하므로, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), `=`, `==`, `!=`, `+`, `+=`, 대소 비교, [assign()](??), [append()](??) 등을 할때 메모리 부하와 속도 부하에 유의하라. 
> * [data()]()는 [널종료 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)이 아닐 수도 있다. [c_str()](??)을 사용하라.

> **모던 C++**
> * (C++11~) [u16string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.
> * (C++11~) [u32string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-32 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex) 관련 표현이 쉬워졌습니다.
> * (C++17~) [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string_view)가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 문자열 복제가 없도록 해줍니다.
> * (C++20~) [u8string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.

# 개요

STL은 1byte 문자로 구성된 문자열인 [string](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)과 와이드 문자(*2byte 또는 4byte*)로 구성된 문자열인 [wstring](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)이 있습니다. 다국어 처리를 하려면 [wstring](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)을 사용하여야 합니다.

두 타입 모두 [basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string) 기반입니다.

```cpp
typedef basic_string<char> string;   
typedef basic_string<wchar_t> wstring;  
```

> *(C++11~) [u16string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.*<br/>
> *(C++11~) [u32string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-32 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.*<br/>
> *(C++20~) [u8string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열을 지원합니다.*

1. [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)와 유사하게 연속된 메모리를 사용하며,

    ```cpp
    std::string s("OK");
    EXPECT_TRUE(*(&s[0] + 0) == 'O');
    EXPECT_TRUE(*(&s[0] + 1) == 'K'); // 연속된 메모리를 사용합니다.    
    ```

2. [컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/)와 같이 [이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/)와 `operator []`를 지원합니다.

    ```cpp
    std::string s("abc");
    
    EXPECT_TRUE(s[0] == 'a' && s[1] == 'b'&& s[2] == 'c'); // []로 접근할 수 있습니다.  

    std::string::iterator itr = s.begin();
    std::string::iterator endItr = s.end();
    for (;itr != endItr; ++itr) { // 이터레이터로 탐색할 수 있습니다.
        *itr = *itr + 1; // 다음 문자로 바꿉니다.
    }
    EXPECT_TRUE(s[0] == 'b' && s[1] == 'c'&& s[2] == 'd'); 
    ```
3. [public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)이므로 [상속](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/)하여 재구현 하면 안됩니다. 문자열은 많이 사용하는 자료형이여서 [가상 함수 테이블](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)의 포인터를 저장하면 메모리 낭비가 심하기 때문입니다.(*[가상 함수 테이블](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable) 참고*)
4. [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)를 복제하여 관리합니다. 따라서, 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하는게 좋습니다.(*[문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98) 참고*)
5. [string](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)은 [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/) 기반이 아니라 값 기반으로 관리되므로, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), `=`, `==`, `!=`, `+`, `+=`, 대소 비교, [assign()](??), [append()](??)등이 메모리 부하나 속도 부하가 있습니다.

    ```cpp
    std::string s1("abc");
    std::string s2 = s1; // s2 는 s1으로부터 복사 생성합니다.

    s1 = "def"; 
    EXPECT_TRUE(s1 == "def" && s2 == "abc"); // 서로 다른 메모리 공간을 사용하여 데이터를 공유하지 않습니다.

    s2 = "def";
    EXPECT_TRUE(s1.data() != s2.data() && s1 == s2); // 서로 다른 메모리 공간을 사용하더라도 값이 같으면 상등하다고 비교합니다.
    ```

[string](??)은 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)와 유사한 [컨테이너](??)입니다. 따라서 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)인 [size()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#size%EC%99%80-capacity), [capacity()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#size%EC%99%80-capacity), [reserve()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#%EC%A0%80%EC%9E%A5-%EA%B3%B5%EA%B0%84-%EC%98%88%EC%95%BD), [clear()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#clear%EC%99%80-swap), [erase()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98-erase%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-remove-%ED%95%A8%EC%88%98), `begin()`, `end()`, [at()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#-%EA%B3%BC-at), [push_back()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C), [insert()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C) 등을 사용할 수 있습니다.(*[string](??)의 세부 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)에 대해서는 모던 C++ STL의 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/)을 참고하시기 바랍니다.*)

# string 초기화

[string](??)은 다른 [string](??)개체나 문자, [문자열 상수](??), 부분 문자열로 초기화 할 수 있습니다.

**기본 생성**

```cpp
std::string s; // 빈 string 개체를 생성합니다. 
```

**다른 string 개체**

```cpp
std::string s1("OK"); // 문자열 상수는 새로운 메모리 공간에 복제되어 저장됩니다.
std::string s2 = s1; // s2(s1)로 복사 생성자를 호출합니다. 구현에 따라 지연 복사되는 버전도 있습니다.
```

**문자**

```cpp
std::string s(3, 'a'); // 'a'문자를 3개 채웁니다.
EXPECT_TRUE(s == "aaa");
```

**문자열 상수**

```cpp
std::string s1("OK"); // 문자열 상수는 새로운 메모리 공간에 복제되어 저장됩니다.
std::string s2 = "OK"; // s2("OK") 와 동일합니다.
std::string s3 = ""; // 빈 문자열을 생성합니다.
```

**부분 문자열**

```cpp
std::string s1 = "abc";

std::string s2(s1, 1, 2); // s1[1]부터 2개 문자를 복사하여 생성합니다.
EXPECT_TRUE(s2 == "bc");

// std::string s3(s1, 100, 2); // (X) 예외 발생. s1[100]부터 2개 복사. out_of_range

std::string s4(s1, 0, std::string::npos); // s1[0] 부터 끝까지 복사하여 생성합니다.
EXPECT_TRUE(s4 == s1);

std::string s5(s1.begin(), s1.end()); // s1.begin()부터 s1.end() 직전 까지 복사하여 생성합니다. 즉, s1[0]부터 끝까지 복사하여 생성합니다.
EXPECT_TRUE(s5 == s1);

const char* ptr = "abc";
std::string s6("abc", 2); // ptr에서 2개 복사하여 생성합니다.
EXPECT_TRUE(s6 == "ab");
```

# 문자열 할당, 합성, 비교(assign(), append(), compare())

`=`과 [assign()](??)을 이용하여 문자열을 할당할 수 있고, 

```cpp
std::string s = "abc";

s = "abcd";
EXPECT_TRUE(s == "abcd"); // = 로 문자열을 할당합니다.

s.assign("abced");
EXPECT_TRUE(s == "abced"); // assign()으로 문자열을 할당합니다.
```

`+=`, `+`, [append()](??)을 이용하여 문자열을 합성할 수 있으며, 

```cpp
std::string s1 = "Hello";
std::string s2 = "World";

EXPECT_TRUE(s1 + s2 == "HelloWorld"); // +는 두 문자열을 합성한 임시 개체를 생성합니다.

s1 += s2; // += 은 s1에 s2를 더합니다.
EXPECT_TRUE(s1 == "HelloWorld");

s1.append("!!!"); // append()는 문자열을 더합니다.
EXPECT_TRUE(s1 == "HelloWorld!!!"); 
```

`==`, `!=`, `<`, `>`, `<=`, `>=`과 [compare()](??)를 이용하여 대소 비교를 할 수 있습니다.

```cpp
std::string s = "abc";

EXPECT_TRUE(s == "abc");
EXPECT_TRUE(s != "def");
EXPECT_TRUE(s < "ccc");
EXPECT_TRUE(s.compare("ccc") < 0); // s < "ccc"
EXPECT_TRUE(s.compare("abc") == 0); // s == "abc"
EXPECT_TRUE(s.compare("aaa") > 0); // s > "aaa"
```

# 문자열 길이(size(), length(), empty())

[size()](??) 와 [length()](??) 모두 [널문자](??)를 제외한 문자열의 길이를 리턴합니다. 또한 [empty()](??)은 문자열이 비었는지 검사합니다.

```cpp
std::string s = "abc";
EXPECT_TRUE(s.size() == 3); // 널문자를 제외한 문자의 갯수 입니다.
EXPECT_TRUE(s.length() == 3); // 널문자를 제외한 문자의 갯수 입니다.
EXPECT_TRUE(s.empty() == false); // 문자열이 비었는지 검사합니다.
```

# 문자열 탐색(find(), find_first_of(), find_last_of())

[find()](??)는 주어진 부분 문자열의 위치를 리턴합니다. 이때 인덱스를 주어 특정 인덱스 뒤부터 찾을 수 있습니다.

```cpp
std::string s = "abced abcde";
EXPECT_TRUE(s.find("ab") == 0); // "ab"와 일치하는 첫번째 인덱스를 리턴합니다.
EXPECT_TRUE(s.find("ab", 1) == 6); // 1 인덱스 이후로 "ab와 일치하는 첫번째 인덱스를 리턴합니다.
```

[find_first_of()](??)`와 [find_last_of()](??)는 전달한 문자들중 일치하는 문자가 있는 첫번째 위치나 마지막 위치를 리턴합니다.

```cpp
std::string s = "This is test. Hello World";
EXPECT_TRUE(s.find_first_of("ieo") == 2); // 'i', 'e', 'o'중에 일치하는 문자가 있는 첫번째 인덱스를 찾습니다. 
EXPECT_TRUE(s.find_last_of("ieo") == 21); // 'i', 'e', 'o'중에 일치하는 문자가 있는 마지막 인덱스를 찾습니다.   
```

# 부분 문자열 처리(replace(), substr(), copy()) 

[replace()](??)는 부분 문자열로 대체하고, [substr()](??)은 부분 문자열을 복사하여 리턴하고, [copy()](??)는 부분 문자열을 문자 배열에 복사합니다.

```cpp
std::string s = "Hello. My name is Tango.";

std::string::size_type pos = s.find("Tango");
std::string::size_type count = std::string("Tango").size();

s.replace(pos, count, "Sam"); // Tango를 Sam으로 바꿉니다.
EXPECT_TRUE(s == "Hello. My name is Sam."); 

EXPECT_TRUE(s.substr(pos, 3) == "Sam"); // 부분 문자열을 복사하여 리턴합니다.

char arr[3]; // 문자 배열
s.copy(arr, 3, pos); // 크기가 3인 arr 배열에 pos 위치에서부터 배열 크기만큼 부분 문자열을 복사 합니다.
EXPECT_TRUE(arr[0] == 'S' && arr[1] == 'a' && arr[2] == 'm');
```

# C스타일 문자열과의 호환(data(), c_str())

C++ 문자열은 [C스타일의 문자열과의 호환성](??)을 위하여 기본적으로 [널종료 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 지원합니다. [널종료 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)로 생성할 수 있으며, [c_str()](??) 함수를 이용하여 [널종료 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)로 변환할 수 있습니다. 

[c_str()](??) 함수의 변환 효율성을 위해 STL 구현에 따라 내부 메모리 블록에 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)를 저장하기도 합니다. 이런 경우 내부 메모리 블록을 구하는 [data()](??) 함수도 [널종료 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 리턴할 수도 있습니다. 하지만, 공식적으로 [data()](??)는 내부 메모리 블록을 리턴하는 함수이고, [c_str()](??)은 [널종료 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 리턴하는 함수이니 [널종료 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)이 필요하다면 꼭 [c_str()](??) 함수를 사용하시기 바랍니다.

```cpp
std::string str = "abc"; // 널종료 문자열로 생성할 수 있습니다.

EXPECT_TRUE(strlen(str.data()) == 3); // (△) 비권장. 내부 데이터 메모리 블록을 리턴합니다. STL 구현에 따라 끝에 널문자가 있을 수도, 없을 수도 있습니다.
EXPECT_TRUE(strlen(str.c_str()) == 3); // 널종료 문자열을 리턴하므로 안전합니다.
```

# string 성능

STL 배포처에 따라 [string 성능](??)은 다를 수 있습니다. 보통 다음의 2가지 방식으로 성능을 개선합니다.

|항목|내용|
|--|--|
|짧은 문자열 최적화(*Small String Optimization, SSO*)|대충 15개 이하의 문자로 구성된 짧은 문자열은 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 [힙](??)에 메모리 공간을 할당하여 관리하는게 오히려 관리부하가 있습니다. 따라서 짧은 문자열은 내부 버퍼에 저장합니다.(*문자열의 길이는 STL을 배포하는 곳 마음입니다.*)|
|지연 복사|문자열의 포인터만 가지고 있다가 문자열의 수정이 발생하면 뒤늦게 복제한 뒤 수정합니다.(*멀티 쓰레드 호환성을 위한 관리부하가 상당하기 때문에 C++11 부터는 이 방식을 쓰지 않는다고 합니다.*)|

컴파일러가 지원하는 짧은 문자열 최적화의 문자 갯수는 빈 문자열의 [capacity()](??)를 구하면 확인할 수 있습니다.

```cpp
std::string s;
EXPECT_TRUE(s.capacity() == 15); // GCC에서는 15개의 문자 입니다.
```






