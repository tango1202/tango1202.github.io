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
> * 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하라.(배열이나 `std::string`, `std::wstring` 을 쓰면 복제된다.)

> **모던 C++**
> * `long long`용 정수형 상수인 `ll`, `ull`, `LL`, `ULL` 리터럴이 추가되었습니다.([long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/) 참고)
> * 널 포인터를 위한 `nullptr` 리터럴이 추가되어 좀더 타입 안정적인 코딩 계약이 가능해 졌습니다.([nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) 참고)
> * 유니코드를 지원하는 `u8""`, `u""`, `U""` 리터럴이 추가되었습니다.([문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) 참고)
> * `R"()"`리터럴이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.([문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) 참고)
> * `int operator ""_km(long double val);`와 같은 사용자 정의 리터럴이 추가되어 단위계 처리가 쉬워졌습니다.([사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-user-literal/) 참고)

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

# 문자 상수

```cpp
char a = 'A';
wchar_t b = L'A'; // 와이드 문자 2byte 또는 4byte
```

# 문자열 상수

문자열 상수는 프로그램 수명만큼 존재합니다. 프로그램 용량이 커질 수도 있으니, 비슷한 문장을 쓸데없이 여러개 작성하지 마세요.(동일한 것을 여러번 사용하는 경우 컴파일러가 1개로 취합해 주긴 합니다만([데이터 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고), 코드 중복이니 1개만 정의해서 사용하세요.) 

**`const`형 포인터**

수정할 필요가 없는 문자열 상수는 `const`형 포인터를 사용합니다.

```cpp
const char* str1 = "abc"; // 문자열 상수
const wchar_t* str2 = L"abc"; // 와이드 문자열 상수
```

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
**문자열 상수 수정**

문자열 상수를 향후 수정할 예정이라면, 배열이나 `std::string`, `std::wstring`에 저장하여 복제본을 만들어야 합니다.

문자열 상수는 rodata 영역에 할당([데이터 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고)되기 때문에 수정할 수 없습니다. 따라서 포인터를 통해서 받은 문자열 상수를 수정하려 하면, 예외가 발생합니다. 하지만, 배열로 저장하면 복제본이므로 수정할 수 있습니다.

```cpp
const char* str1 = "abc"; // 문자열 상수
char* temp = const_cast<char*>(str1);
// (X) 예외 발생. 문자열 상수는 rodata에 있기 때문에 수정할 수 없습니다.
*temp = 'd';

char str2[] = "abc"; // {'a', `b`, 'c', '\0'};
// (O) 배열은 문자열 상수의 복제본이어서 항목을 수정할 수 있습니다.
str2[0] = 'd';
EXPECT_TRUE(str2[0] == 'd');
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4f98f2cc-9dff-428f-a639-42d42d9f701b)

문자열의 내용을 수정할 필요가 없다면, 배열이나 `std::string`, `std::wstring`에 저장하지 마세요. 불필요하게 복제되어 복제 부하만 생깁니다.

**결합**

문자열 상수가 너무 긴 경우에, 서로 연달아 배치하면 자동으로 이어 붙이기가 됩니다. 하기 예에서 `str1`, `str2`, `str3`은 모두 `Hello World` 문자열 상수를 가리키며, 동일한 메모리 주소를 사용합니다.(동일한 문자열을 여러번 사용하는 경우 컴파일러가 1개로 취합해줌. [데이터 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고)

```cpp
const char* str1 = "Hello World"; 
const char* str2 = "Hello " "World"; // (O) 두 문자열 상수 이어 붙이기
const char* str3 = "Hello " // (O) 개행된 문자열 상수 붙이기
                   "World";   
EXPECT_TRUE(str1 == str2 && str2 == str3); // (O) 동일한 문자열 상수는 컴파일러가 1개만 생성해 줍니다.   
```

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
|`\unnnn`|nnnn : 4자리의 유니코드 |![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ca05ccd7-8e34-4d7c-bb21-fb37441dfaca)|
|`\Unnnnnnnn`|nnnnnnnn : 8자리의 유니코드 |![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e2a84daa-45ef-4464-a488-670f18365511)|


