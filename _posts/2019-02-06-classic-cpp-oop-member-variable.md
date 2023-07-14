---
layout: single
title: "#6. [고전 C++ 개체 지향] 멤버 변수"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 멤버 변수 초기화시, 생성후 대입하지 말고 초기화 리스트를 사용하라.(초기화 리스트의 순서는 멤버 변수 정의 순서에 맞춰라.)
> * 생성자에서 필요한 인자를 모두 나열하고 초기화하라.
> * 복사 생성자가 필요하다면, 암시적 복사 생성자가 정상 동작하도록 멤버 개체 Handler를 구현하고, 필요없다면 못쓰게 만들어라.

> * 메모리 패딩을 고려하여 멤버 변수 정의 순서를 정하라.
 
**개요** 

|항목|내용|수명|
|--|--|--|
|멤버 변수|특정 개체에 속하는 변수|개체 생성시 생성자 실행전 초기화 리스트에서 생성 ~ 소멸자 실행 후 종료|
|참조자 멤버 변수||멤버 변수와 동일|
|포인터 멤버 변수||멤버 변수와 동일|
|상수 멤버 변수||멤버 변수와 동일|
|정적 멤버 변수|특정 개체에 속하지 않는 변수.<br/>클래스에 응집되어 있는 전역 변수|`main` 호출 ~ `main` 종료|
|정적 상수 멤버 변수||멤버 변수와 동일

```cpp
class T {
    int m_Val1; // 멤버 변수
    int& m_Val2; // 참조자 멤버 변수
    int* m_Val3; // 포인터 멤버 변수
    const int m_Val4; // 상수 멤버 변수
    static int s_m_Val5; // 정적 멤버 변수
    static const int s_c_m_Val6; // 정적 상수 멤버 변수
};






복사 생성자 호환성

대입 연산자 호환성

소멸자 호환


# 메모리 할당에 따른 멤버 변수 정의 순서

컴파일러는 클래스나 구조체의 멤버 변수를 할당하는데 있어, 메모리 접근 편의를 위해 4byte단위로 멤버 변수를 할당합니다.(이를 패딩(padding)이라 합니다. [`#pragma`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma) 언급) 

다음 코드는 패딩 작업에 의해 빈공간이 생겨 16byte 크기이지만,

```cpp
class T {
    char m_Char1; // 1byte, 메모리 접근 편의를 위해 32bit(4byte) 단위로 할당(패딩). 3byte 빈공간이 생김 
    int m_Int1; // 4byte
    char m_Char2; // 1byte, 메모리 접근 편의를 위해 32bit(4byte) 단위로 할당(패딩). 3byte 빈공간이 생김 
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 16);
```

다음 코드는 패딩이 최소화되어 12byte입니다.

```cpp
class T {
    char m_Char1; // 1byte
    char m_Char2; // 1byte, 패딩을 위해 2byte 빈공간이 생김
    int m_Int1; // 4byte
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 12);      
```

멤버 변수가 4byte 단위로 할당되므로, `char`와 같이 4byte 이하인 멤버 변수들은 몰아서 정의하는게 좋습니다.(`pragma pack`을 이용하면, 데이터 버스 크기를 줄여 메모리 낭비는 줄일 수 있으나, 메모리 접근 속도는 저하됩니다. [`#pragma`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma) 참고) 



