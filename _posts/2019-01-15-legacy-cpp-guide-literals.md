---
layout: single
title: "#15. [Legacy C++ 가이드] 리터럴들(Literals), 이스케이프 문자"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)를 많이 만들면, 프로그램 용량이 커진다. 비슷한 문장을 쓸데없이 여러개 작성하지 마라.

> **모던 C++**
> * (C++11~) [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)용 정수형 상수인 [`ll`, `ull`, `LL`, `ULL` 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)이 추가되었습니다.
> * (C++11~) [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr) [리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/)이 추가되어 좀더 타입에 안전한 코딩 계약이 가능해 졌습니다.
> * (C++11~)  [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [char16_t, char32_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t)용 문자 상수를 제공합니다.
> * (C++11~) [R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행이나 [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)를 좀더 편하게 입력할 수 있습니다.
> * (C++11~) [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `int operator ""_km(long double val);`와 같이 사용자가 정의해서 사용할 수 있으며, 단위계 처리가 쉬워졌습니다.
> * (C++14~) [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c14-%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.
> * (C++14~) [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어  `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있으며, 가독성이 좋아졌습니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 추가되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex) 관련 표현이 간편해 졌습니다.
> * (C++17~) [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 1byte 크기의 문자 상수를 제공합니다.
> * (C++17~) [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0xA.9p11`과 같이 16진수로 실수를 표현할 수 있습니다.

# 개요

[리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/)은 각 타입에 따른 상수값입니다. 컴파일 타임에 값이 결정되며, 대부분 [코드 세그면트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%BD%94%EB%93%9C-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)에 저장되어 그 값을 수정할 수 없습니다. [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)만 [데이터 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)의 `rodata`에 저장됩니다.

# bool 과 숫자

`true`, `false`와 8진수/10진수/16진수 정수, `unsigned`, `long`, 고정 소수점 실수와 부동 소수점 실수가 제공됩니다.

```cpp
// bool 형
bool a = true; // 참
bool b = false; // 거짓

// 정수형
int c = 10; // 10진수.
int d = 010; // 8진수.
int e = 0x1a; // 16진수. x 또는 X. a~f 또는 A~F
unsigned int f = 10U; // unsigned. u 또는 U
unsigned long g = 10U;
long h = 10L; // long. l 또는 L
unsigned long i = 10UL; // unsigned long. u 또는 U, l 또는 L

// 실수형
double j = 3.14; // 고정 소수점
double k = 3.14e10; // 부동 소수점 e 또는 E
float l = 3.14F; // f 또는 F
float m = 3.14e10F; 
long double n = 3.14L; // l 또는 L
long double o = 3.14e10L;
```

> *(C++11 ~) [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)용 정수형 상수인 [`ll`, `ull`, `LL`, `ULL` 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)이 추가되었습니다.*<br/>
> *(C++14~) [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c14-%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.*<br/>
> *(C++14~) [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어  `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있으며, 가독성이 좋아졌습니다.*<br/>
> *(C++17~) [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0xA.9p11`과 같이 16진수로 실수를 표현할 수 있습니다.*

# 문자 상수

[문자 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90-%EC%83%81%EC%88%98)는 1byte 크기인 `''`와 `wchar_t` 크기(*Windows 에서는 2byte이고 리눅스에서는 4byte. [기본 타입](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/) 참고*)
인`L''`이 있습니다.

```cpp
char a = 'A';
wchar_t b = L'A'; // 와이드 문자 2byte 또는 4byte
```

# 문자열 상수

[문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)는 [문자 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90-%EC%83%81%EC%88%98)들의 집합으로서, 문자열의 끝을 나타내는 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)가 추가되어 있으며, [데이터 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)의 `rodata`에서 프로그램 수명만큼 존재합니다.

프로그램 용량이 커질 수도 있으니, 비슷한 문장을 쓸데없이 여러개 작성하지 마세요.(*동일한 것을 여러번 사용하는 경우 컴파일러가 1개로 취합해 주긴 합니다만([데이터 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고), 코드 중복이니 1개만 정의해서 사용하세요.*) 

문자열이 커봐야 얼마나 크겠어? 하실 수도 있지만, 다국어로 번역되다 보면 무시 못할 크기로 커집니다.

`const char*`나 `const wchar_t*`로 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)가 있는 영역을 참조하거나, [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)을 이용하여 복사할 수 있습니다. 

> *(C++11~)  [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [char16_t, char32_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t)용 문자 상수를 제공합니다.*<br/>
> *(C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 추가되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex) 관련 표현이 간편해 졌습니다.*<br/>
> *(C++17~) [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 1byte 크기의 문자 상수를 제공합니다.*

**const형 포인터**

수정할 필요가 없는 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)는 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)형 포인터를 사용합니다.

```cpp
const char* str1 = "abc"; // 문자열 상수
const wchar_t* str2 = L"abc"; // 와이드 문자열 상수
```

**배열**

[배열 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94)에서 언급했듯, [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)을 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 초기화 하면, 끝에 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)가 추가됩니다.

```cpp
char str3[] = "abc"; // {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str3[0] == 'a');
EXPECT_TRUE(str3[1] == 'b');
EXPECT_TRUE(str3[2] == 'c');
EXPECT_TRUE(str3[3] == '\0'); // 널문자가 추가됨

wchar_t str4[] = L"abc"; // {L'a', L`b`, L'c', L'\0'};
EXPECT_TRUE(str4[0] == L'a');
EXPECT_TRUE(str4[1] == L'b');
EXPECT_TRUE(str4[2] == L'c');
EXPECT_TRUE(str4[3] == L'\0'); // 널문자가 추가됨
```

**결합**

[문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)가 너무 긴 경우에, 서로 연달아 배치하면 자동으로 이어 붙이기가 됩니다. 하기 예에서 `str1`, `str2`, `str3`은 모두 `Hello World` [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)를 가리키며, 동일한 메모리 주소를 사용합니다.(*동일한 문자열을 여러번 사용하는 경우 컴파일러가 1개로 취합해 줍니다. [데이터 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고*)

```cpp
const char* str1 = "Hello World"; 
const char* str2 = "Hello " "World"; // (O) 두 문자열 상수 이어 붙이기
const char* str3 = "Hello " // (O) 개행된 문자열 상수 붙이기
                   "World";   
EXPECT_TRUE(str1 == str2 && str2 == str3); // (O) 동일한 문자열 상수는 컴파일러가 1개만 생성해 줍니다.   
```

> *(C++11~) [R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행이나 [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)를 좀더 편하게 입력할 수 있습니다*

# 이스케이프 문자

언어 내부에서 사용하는 기호여서 [문자 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90-%EC%83%81%EC%88%98)나 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 컴파일러가 파싱할 수 없거나, 개행등 키보드로 표시할 수 없는 특수 문자들은 `\`을 이용하여 표현할 수 있습니다.(*[널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)도 [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)로서 8진수 `0`입니다.*)

|항목|내용|[아스키 코드(https://www.ascii-code.com/ASCII)](https://www.ascii-code.com/ASCII)|
|--|--|--|
|`\'`|작은 따옴표|0x27|
|`\"`|큰 따옴표|0x22|
|`\?`|물음표|0x3F|
|`\\`|백슬래시|0x5C|
|`\a`|비프음|0x07|
|`\b`|백스페이스|0x08|
|`\f`|Form Feed|0x0C|
|`\n`|라인 피드(line feed,LF) - 개행|0x0A|
|`\r`|캐리지 리턴(carriage return, CR) - 줄의 앞으로 이동|0x0d|
|`\t`|가로탭|0x09|
|`\v`|세로탭|0x0b|
|`\nnn`|nnn : 3자리의 8진수||
|`\xn...`|n... : 임의 자리 16진수 ||
|`\unnnn`|nnnn : 2byte(*16진수 4자리*)의 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)|![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ca05ccd7-8e34-4d7c-bb21-fb37441dfaca)|
|`\Unnnnnnnn`|nnnnnnnn : 4byte(*16진수 8자리*)의 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)|![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e2a84daa-45ef-4464-a488-670f18365511)|


**개행 문자**

line feed로 줄을 바꾸고, carriage return으로 줄의 제일 앞으로 이동하는 CR-LF(*엔터키의 동작이죠.*)의 경우 OS에 따라 사용하는 [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)가 다릅니다.

|항목|내용|
|--|--|
|Windows|`\r` + `\n`, CR-LF를 모두 입력합니다.<br/>`\n`만 입력해도 됩니다.|
|Linux, MAC OS X 이상|`\n`, LF 만 입력합니다.|
|MAC OS X 이전|`\r`, CR 만 입력합니다.|

현재는 대부분이 `\n`으로 CR-LF를 지원하기 때문에 별 문제는 없습니다만, 혹시나 예전 OS와의 호환성이 필요하다면 `\r\n`을 함께 사용해야 될 수도 있습니다.

> *(C++11~) [R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행이나 [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)를 좀더 편하게 입력할 수 있습니다.*