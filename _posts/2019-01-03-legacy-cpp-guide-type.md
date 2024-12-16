---
layout: single
title: "#3. [레거시 C++ 가이드] 기본 타입(자료형)"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 타입 크기에 의존하여 코딩하지 마라. OS에 따라, 컴파일러에 따라, 시스템 비트수에 따라 크기가 달라질 수 있다.
> * 대소 비교가 필요한 경우에는 정수 타입을 사용하라. [실수 비교](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%EC%8B%A4%EC%88%98-%EB%B9%84%EA%B5%90)는 오차가 있다.

> **모던 C++**
> * (C++11~) [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#decltype)이 추가되어 값으로부터 [타입을 추론](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)하며, 코딩이 간편해 졌습니다.
> * (C++11~) [using을 이용한 타입 별칭](https://tango1202.github.io/cpp/modern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)이 추가되어 [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 보다 좀 더 직관적인 표현이 가능해 졌습니다.
> * (C++11~) [long long 타입](https://tango1202.github.io/cpp/modern-cpp-type/#long-long)이 추가되어 최소 8byte크기를 보장합니다. 
> * (C++11~) [char16_t, char32_t 타입](https://tango1202.github.io/cpp/modern-cpp-type/#char16_t-%EC%99%80-char32_t)이 추가되어[UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자와 [UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 지원합니다.
> * (C++20~) [char8_t 타입](https://tango1202.github.io/cpp/modern-cpp-type/#c20-char8_t)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 지원합니다.
> * (C++20~) [정수에서 2의 보수 범위를 보장](https://tango1202.github.io/cpp/modern-cpp-type/#c20-%EC%A0%95%EC%88%98%EC%97%90%EC%84%9C-2%EC%9D%98-%EB%B3%B4%EC%88%98-%EB%B2%94%EC%9C%84-%EB%B3%B4%EC%9E%A5)합니다.

# 개요

C++에는 하기의 기본 타입들이 있습니다. 크기가 고정된 것은 `float`, `double` 밖에 없습니다. 표준에 의하면 `char`마저 ***적어도 1byte***입니다. 그냥 1byte가 아닙니다. 대부분 OS에 따라, 컴파일러에 따라, 시스템 비트수에 따라 달라지므로 크기에 의존해서 코딩하면 안됩니다.(*특히 `int`와 `wchar_t`요.*)


|항목|내용|용량|
|--|--|--|
|[bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/)|`true` 또는 <br/>`false`|`1 <= sizeof (bool) <= sizeof(long)`|
|`char`|적어도 1byte 문자|대부분 1byte|
|`wchar_t`|적어도 2byte 와이드 문자|시스템의 처리 방식에 따라 다르며, 2byte 또는 4byte.<br/>Windows는 2byte |
|`short`|적어도 2byte 정수| 대부분 2byte|
|`int`|적어도 2byte인 기본 연산 단위 크기의 정수|16bit : 2byte,<br/>32bit : 4byte,<br/>64bit : 4byte|
|`unsigned`|부호 없는 `int`|`int` 와 동일|
|`long`|적어도 4byte인 `int`보다 크거나 같은 정수|16bit : 4byte,<br/>32bit : 4byte,<br/>64bit : 8byte|
|`float`|단정밀도 부동 소수점 실수|4byte|
|`double`|배정밀도 부동 소수점 실수|8byte|
|`long double`|확장 정밀도 부동소수점 실수|16byte 또는 10byte 또는 8byte. 단, Visual C++ 은 `double` 과 동일|
|`*`|[포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/)|32bit : 4byte,<br/>64bit : 8byte|
|`&`|[참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|참조하는 개체의 별칭으로서 해당 용량은 스펙에 정의되지 않음.<br/>다만, `sizeof()`시 참조하는 개체와 동일 크기를 리턴하도록 스펙에 정의됨.(*`sizeof(T&) == sizeof(T)`*)|
|[enum](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)|[열거형 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)|모든 열거자 값을 나타낼 수 있는 정수 형식의 크기. (보통 4byte)|

그외 `signed`, `unsigned`, `short`, `long` 와 결합하여 다양한 조합이 나올 수 있습니다.(*[https://en.cppreference.com/w/cpp/language/types](https://en.cppreference.com/w/cpp/language/types) 참고*)

> *(C++11~) [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#decltype)이 추가되어 값으로부터 [타입을 추론](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)하며, 코딩이 간편해 졌습니다.*<br/>
> *(C++11~) [long long 타입](https://tango1202.github.io/cpp/modern-cpp-type/#long-long)이 추가되어 최소 8byte크기를 보장합니다.*<br/>
> *(C++11~) [char16_t, char32_t 타입](https://tango1202.github.io/cpp/modern-cpp-type/#char16_t-%EC%99%80-char32_t)이 추가되어[UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자와 [UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 지원합니다.*<br/>
> *(C++20~) [char8_t 타입](https://tango1202.github.io/cpp/modern-cpp-type/#c20-char8_t)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 지원합니다.*


# 타입 별칭

[typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)로 타입의 별칭을 만들 수 있습니다.(*절대 [매크로](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/) 쓰지 마세요!!! [매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98) 참고*)

```cpp
// 단순하게
typedef unsigned long ulong; 
ulong myVal; // 타입의 별칭으로 정의

// 배열
typedef int MyArray[5]; 
MyArray arr; // int arr[5]; 와 동일 
arr[0] = 10; // 첫번째 요소에 값 대입
EXPECT_TRUE(arr[0] == 10);

// 구조체
typedef struct {int a; int b;} MyData, *pMyData;
MyData myData1;
MyData* myData2;
pMyData myData3; // MyData* 와 같음
myData1.a = 10; // a에 값 대입
EXPECT_TRUE(myData1.a == 10);

// 함수 포인터
typedef int (*Func)(int, int); // 함수 포인터 typedef

int f(int a, int b) {return a + b;} // 함수 정의
Func func = f; // 함수 포인터 저장

// template의 타입 재정의
template<class T> 
struct ClassT { 
    typedef T Type;
    typedef const T ConstType; 
}; 
ClassT<int>::ConstType constVal = 20;
```

> *(C++11~) [using을 이용한 타입 별칭](https://tango1202.github.io/cpp/modern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)이 추가되어 [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 보다 좀 더 직관적인 표현이 가능해 졌습니다.*

# 타입 크기

[sizeof()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)를 이용하면 개체 용량을 byte 단위로 구할 수 있습니다.  [포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/)의 경우는 32bit 시스템에서는 4byte,
64bit 시스템에서는 8byte이고, [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 경우는 참조하는 개체와 동일 크기를 리턴합니다.

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

# 타입 최대/최소값

[numeric_limits](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C%EA%B0%92)로 타입의 최대, 최소값을 알 수 있습니다.

```cpp
EXPECT_TRUE(std::numeric_limits<int>::max() == 2147483647);
EXPECT_TRUE(std::numeric_limits<int>::min() == -2147483648);
```
# 실수 비교

실수는 부동 소수점 형태로 데이터를 저장합니다. 소수점 이하의 정밀도가 상황에 따라 다르며, `1.0`을 저장했는데, `1.0000001`이 저장되었을 수 있습니다. 

그래서 `==` 나 대소 비교가 부정확합니다. 손실되면 안되는 민감 정보나, ***정확한 계산을 필요로 하는 경우에는 정수형을 사용***하셔야 합니다.(*[로지컬 단위](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-logical/) 참고*) 


다음처럼 `epsilon()`을 이용하여 오차 범위를 고려해 비교할 수는 있지만, 권장하지는 않습니다.

```cpp
bool Equals(double a, double b) {
    double diff = fabs(a - b); // 두수의 차

    // (△) 비권장. 두수의 차가 오차 범위보다 작으면 같은 수
    return (diff < std::numeric_limits<double>::epsilon()) ? true : false;
}

double a = 10.0;
double b = a;
b += 1.0;
b -= 1.0;
EXPECT_TRUE(Equals(a, b)); 
```


