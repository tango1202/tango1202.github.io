---
layout: single
title: "#17. [고전 C++ STL] 문자열"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `string`과 `wstring`은 소멸자가 `public` Non-Virtual 이므로 상속하여 재구현 하지 마라.
> * 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하라.(배열이나 `string`, `wstring` 을 쓰면 복제된다.)
> * `string`은 값 기반으로 복사/비교 하므로, 복사 생성자, `=`, `==`, `!=`, `+`, `+=`, 대소 비교, `assign()`, `append()` 등을 할때 메모리 부하와 속도 부하에 유의하라. 

> **모던 C++**
> * 유니코드를 지원하는 `u8string`, `u16string`, `u32string` 이 추가되었습니다.([문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) 참고)

# 개요

STL은 1byte 문자(`char`)로 구성된 문자열인 `string`과 와이드 문자(2byte 또는 4byte)로 구성된 문자열인 `wstring`이 있습니다. 다국어 처리를 하려면 `wstring`을 사용하여야 합니다.

두 타입 모두 `basic_string` 기반입니다.

```cpp
typedef basic_string<char>    string;   
typedef basic_string<wchar_t> wstring;  
```

> *C++11 부터는 유니코드를 지원하는 `u16string`, `u32string` 타입이 추가되었습니다.([문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) 참고)*

> *C++20 부터는 `u8string` 타입이 추가되었습니다.([문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) 참고)*


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

문자열의 세부 멤버 함수에 대해서는 모던 C++ STL의 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/) 을 참고하기 바랍니다.

# string 성능

STL 배포처에 따라 그 성능은 다를 수 있습니다. 보통 다음의 2가지 방식으로 성능을 개선합니다.

|항목|내용|
|--|--|
|짧은 문자열 최적화|한 2~3개의 문자로 구성된 짧은 문자열은 `new`로 메모리 공간을 할당하여 관리하는게 오히려 관리부하가 있습니다. 따라서 짧은 문자열은 개체 자체에 저장합니다.(문자열의 길이는 STL을 배포하는 곳 마음입니다.)|
|지연 복사|문자열의 포인터만 가지고 있다가 문자열의 수정이 발생하면 뒤늦게 복제한 뒤 수정합니다.(멀티 쓰레드 호환성을 위한 관리부하가 상당하기 때문에 C++11 부터는 이 방식을 쓰지 않는다고 합니다.)|



