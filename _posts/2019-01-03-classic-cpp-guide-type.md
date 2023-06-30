---
layout: single
title: "#3. [고전 C++ 가이드] 기본 자료형"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 자료형 종류

C++에는 하기의 기본 자료형들이 있습니다. 

|항목|내용|용량|
|--|--|--|
|`bool`|`true` 또는 <br/>`false`|`1 <= sizeof (bool) <= sizeof(long)`|
|`char`|1byte 문자|1byte|
|`wchar_t`|와이드 문자|시스템의 비트수에 따라 다르며, 대부분 2byte 또는 4byte.<br/>단, Windows는 2byte |
|`short`|2byte 정수| 2byte|
|`int`|기본 연산 단위 크기의 정수|16bit : 2byte,<br/>32bit : 4byte,<br/>64bit : 4byte|
|`unsigned`|부호 없는 `int`|`int` 와 동일|
|`long`|`int`보다 크거나 같은 정수|16bit : 4byte,<br/>32bit : 4byte,<br/>64bit : 8byte|
|`float`|단정밀도 부동 소수점 실수|4byte|
|`double`|배정밀도 부동 소수점 실수|8byte|
|`long double`|확장 정밀도 부동소수점 실수|16byte 또는 10byte 또는 8byte. 단, Visual C++ 은 `double` 과 동일|
|`*`|포인터|32bit : 4byte,<br/>64bit : 8byte|
|`&`|참조자|참조하는 개체의 별칭으로서 해당 용량은 스펙에 정의되지 않음.<br/>다만, `sizeof()`시 참조하는 개체와 동일 크기를 리턴하도록 스펙에 정의됨.(`sizeof(T&) == sizeof(T)`)|
|`enum`|열거형 상수|모든 열거자 값을 나타낼 수 있는 정수 형식의 크기. (보통 4byte)|

그외 `signed`, `unsigned`, `short`, `long` 와 결합하여 다양한 조합이 나올 수 있습니다.([https://en.cppreference.com/w/cpp/language/types](https://en.cppreference.com/w/cpp/language/types) 참고)

# 자료형 재정의(별칭)

`typedef` 를 사용하여 자료형의 별칭을 만들 수 있습니다.(절대 `define`쓰지 마세요!!!)

```cpp
// 단순하게
typedef unsigned long ulong; 
ulong myVal; // 자료형의 별칭으로 정의

// 구조체
typedef struct {int a; int b;} MyData, *pMyData;
MyData myData1;
MyData* myData2;
pMyData MyData3; // MyData* 와 같음

// template의 타입 재정의
template<class T> 
struct ClassT { 
    typedef T Type;
    typedef const T ConstType; 
}; 
ClassT<int>::ConstType constVal = 20;
```

# 자료형 크기

`sizeof()`를 이용하면 개체 용량을 바이트 단위로 구할 수 있습니다. 단, 참조자의 경우 참조하는 개체와 동일 크기가 리턴됩니다.

```cpp
class MyClass {
    int m_X;
    int m_Y;
};

MyClass myClass;
MyClass& myClassRef = myClass;

EXPECT_TRUE(sizeof(myClass) == 8);
EXPECT_TRUE(sizeof(myClass) == sizeof(myClassRef)); // sizeof() 시 참조하는 개체와 참조자는 크기가 같습니다.

```

# 자료형 최대/최소값

`numeric_limits`를 이용하여 자료형의 최대, 최소값을 알 수 있습니다.

```cpp
EXPECT_TRUE(std::numeric_limits<int>::max() == 2147483647);
EXPECT_TRUE(std::numeric_limits<int>::min() == -2147483648);
```


