---
layout: single
title: "#15. [고전 C++ 가이드] 리터럴들(Literals), 이스케이프 문자"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 문자열 상수를 많이 만들면, 프로그램 용량이 커진다. 비슷한 문장을 쓸데없이 여러개 작성하지 마라.

> **모던 C++**
> * (C++11~) [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)용 정수형 상수인 [`ll`, `ull`, `LL`, `ULL` 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)이 추가되었습니다.
> * (C++11~) [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) 리터럴이 추가되어 좀더 타입에 안전한 코딩 계약이 가능해 졌습니다.
> * (C++11~) [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.
> * (C++11~) [R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.
> * (C++11~) `int operator ""_km(long double val);`와 같은 [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 단위계 처리가 쉬워졌습니다.
> * (C++14~) [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.
> * (C++14~) 가독성을 위한 [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어 `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.
> * (C++17~) [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.
> * (C++17~) `0xA.9p11`과 같은 [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)을 제공합니다.

# bool 과 숫자

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
> *(C++14~) [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.*<br/>
> *(C++14~) 가독성을 위한 [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어 `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.*<br/>
> *(C++17~) `0xA.9p11`과 같은 [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)을 제공합니다.*

# 문자 상수

문자 상수는 1byte 크기인 `''`와 `wchar_t` 크기인 `L''`이 있습니다.
```cpp
char a = 'A';
wchar_t b = L'A'; // 와이드 문자 2byte 또는 4byte
```

# 문자열 상수

문자열 상수는 문자 상수들의 집합으로서, 문자열의 끝을 나타내는 널문자(`\0`)가 추가되어 있으며, [데이터 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)의 `rodata`에서 프로그램 수명만큼 존재합니다. 프로그램 용량이 커질 수도 있으니, 비슷한 문장을 쓸데없이 여러개 작성하지 마세요.(동일한 것을 여러번 사용하는 경우 컴파일러가 1개로 취합해 주긴 합니다만([데이터 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고), 코드 중복이니 1개만 정의해서 사용하세요.) 

`const char*`나 `const wchar*`로 문자열 상수가 있는 영역을 참조하거나, 배열을 이용하여 복사할 수 있습니다. 

**const형 포인터**

수정할 필요가 없는 문자열 상수는 `const`형 포인터를 사용합니다.

```cpp
const char* str1 = "abc"; // 문자열 상수
const wchar_t* str2 = L"abc"; // 와이드 문자열 상수
```

> *(C++11~) [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.*<br/>
> *(C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.*<br/>
> *(C++17~) [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.*

**배열**

배열을 문자열 상수로 초기화 하면, 끝에 널문자(`\0`) 가 추가됩니다.([배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94/) 언급)

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

문자열 상수가 너무 긴 경우에, 서로 연달아 배치하면 자동으로 이어 붙이기가 됩니다. 하기 예에서 `str1`, `str2`, `str3`은 모두 `Hello World` 문자열 상수를 가리키며, 동일한 메모리 주소를 사용합니다.(동일한 문자열을 여러번 사용하는 경우 컴파일러가 1개로 취합해줌. [데이터 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고)

```cpp
const char* str1 = "Hello World"; 
const char* str2 = "Hello " "World"; // (O) 두 문자열 상수 이어 붙이기
const char* str3 = "Hello " // (O) 개행된 문자열 상수 붙이기
                   "World";   
EXPECT_TRUE(str1 == str2 && str2 == str3); // (O) 동일한 문자열 상수는 컴파일러가 1개만 생성해 줍니다.   
```

> *(C++11~) [R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.*

# 이스케이프 문자

`\`로 특수문자를 표시할 수 있습니다.

|항목|내용|아스키코드|
|--|--|--|
|`\'`|작은 따옴표|0x27|
|`\"`|큰 따옴표|0x22|
|`\?`|물음표|0x3F|
|`\\`|백슬래시|0x5C|
|`\a`|비프음|0x07|
|`\b`|백스페이스|0x08|
|`\f`|Form Feed|0x0C|
|`\n`|줄바꿈|0x0A|
|`\r`|캐리지 리턴|0x0d|
|`\t`|가로탭|0x09|
|`\v`|세로탭|0x0b|
|`\nnn`|nnn : 3자리의 8진수||
|`\xn...`|n... : 임의 자리 16진수 ||
|`\unnnn`|nnnn : 2byte(16진수 4자리)의 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)|![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ca05ccd7-8e34-4d7c-bb21-fb37441dfaca)|
|`\Unnnnnnnn`|nnnnnnnn : 4byte(16진수 8자리)의 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)|![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e2a84daa-45ef-4464-a488-670f18365511)|



