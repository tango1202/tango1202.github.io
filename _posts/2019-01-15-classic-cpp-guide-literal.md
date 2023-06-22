---
layout: single
title: "#15. [고전 C++ 가이드] 리터럴(Literal)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

**`bool` 과 숫자 리터럴**
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

**문자와 문자열 리터럴**

```cpp
char a = 'A';
wchar_t b = L`A`; // 와이드 문자 2byte 또는 4byte

const char* str1 = "abc"; // 문자열 상수
const wchar_t* str2 = L"abc" // 와이드 문자열 상수
```

문자열 상수를 이용하여 배열 초기화를 할 

```cpp
char str1[] = "abc"; // {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str1[0] == 'a');
EXPECT_TRUE(str1[1] == 'b');
EXPECT_TRUE(str1[2] == 'c');
EXPECT_TRUE(str1[3] == '\0');

wchar_t str2[] = L"abc"; // {L'a', L`b`, L'c', L'\0'};
EXPECT_TRUE(str2[0] == L'a');
EXPECT_TRUE(str2[1] == L'b');
EXPECT_TRUE(str2[2] == L'c');
EXPECT_TRUE(str2[3] == L'\0');
```

**이스케이프 시퀀스**

\
\u 유니코드
\o
\x

**문자열**
L
끝에 널문자.
프로그램 수명 만큼 존재함
배열을 문자열로 초기화 갯수가 널문자 포함함
문자열 상수를 포인터로 받아 수정하려면 오류.
배열로 받아 수정하면 OK
