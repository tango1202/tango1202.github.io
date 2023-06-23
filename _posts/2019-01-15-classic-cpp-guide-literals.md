---
layout: single
title: "#15. [고전 C++ 가이드] 리터럴들(Literals)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 문자열 상수를 많이 만들면, 프로그램 용량이 커진다. 비슷한 문장을 여러개 작성하지 마라.
> * 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하라.(배열이나 `std::string`, `std::wstring` 을 쓰면 복제된다.)

**`bool` 과 숫자**

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

**문자 상수**

```cpp
char a = 'A';
wchar_t b = L'A'; // 와이드 문자 2byte 또는 4byte
```

**문자열 상수**

문자열 상수는 프로그램 수명 만큼 존재합니다. 프로그램 용량이 커질 수도 있으니, 쓸데없이 많이 만들지는 마세요. 특히 수정될 필요가 없다면, 배열이나 `std::string`,`std::wstring`에 저장하지 마세요. 복제되어 용량만 커집니다.

```cpp
const char* str1 = "abc"; // 문자열 상수
const wchar_t* str2 = L"abc"; // 와이드 문자열 상수
```

배열을 문자열 상수로 초기화 하면, 끝에 널문자(`\0`) 가 추가됩니다.([초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/) 참고)

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

문자열 상수를 포인터로 받아 수정하면 예외가 발생하고, 배열로 저장하면 복제본이므로 수정할 수 있습니다.

```cpp
char* temp = const_cast<char*>(str1);
// (X) 예외발생. 문자열 상수을 가리키는 포인터를 이용하여 값을 변경할 수 없습니다.
*temp = 'd';

// (O) 배열은 문자열 상수의 복제본이어서 항목을 수정할 수 있습니다.
str3[0] = 'd';
EXPECT_TRUE(str3[0] == 'd');
```

**이스케이프 문자**

`\`를 이용하여 특수문자를 표시할 수 있습니다.

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
|`\unnnn`|nnnn : 4자리의 유니코드 |![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c104b290-3dbd-4951-941f-bca277553147)|
|`\Unnnnnnnn`|nnnnnnnn : 8자리의 유니코드 |![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d102f9f4-3de6-4f4b-b9fd-9c0db3c1d0df)|





