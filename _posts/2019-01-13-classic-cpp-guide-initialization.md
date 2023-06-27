---
layout: single
title: "#13. [고전 C++ 가이드] 초기화"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 생성하면서 초기화 하라.
> * 초기화되지 않은 변수를 사용하지 마라.
> * 자동 제로 초기화를 활용하지 마라. 나중에 낭패 본다.

**개요**

초기화에는 다음과 같은 방법들이 있습니다.

|항목|내용|
|--|--|
|기본 초기화|`T obj;`|
|값 초기화|`T obj();`|
|복사 초기화|`T obj = other;` 또는 `T obj(other);`|
|생성 후 대입(불필요한 부하)|`T obj; obj = other;`|
|배열 초기화|`T arr[] = {};`, `char str[] = "abc";`|
|구조체 초기화|`struct T {int x; int y;}; T t = {0, 10};`|

생성 후 대입하는 건, 생성과 대입의 2개 과정을 거쳐서 낭비입니다. 또한 **예외 안정** 프로그래밍에도 좋지 않습니다. 생성 후 대입 과정에서 예외가 발생하면 난감해지니까요.([완전한 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-exception-perfect-constructor/) 참고)

또한, 개체 생성시 초기화를 하지 않으면, 초기화되지 않은 개체를 사용하는 실수를 할 수 있습니다. 그러니 항상 생성과 동시에 초기화 하는 것이 좋습니다. 

다음은 초기화 테스트용 클래스입니다. 기본 생성자, `int`를 전달받는 생성자(형변환이 되지 않게 `explicit`로 정의. [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/) 참고), 복사 생성자, 대입 연산자를 정의하였습니다.

```cpp
class T {
public:
    T() {} // 기본 생성자
    explicit T(int t) {} // int를 전달받는 생성자. explicit를 주어 암시적 형변환을 막음
    T(const T& other) {} // 복사 생성자

    void operator =(const T& other) {} // 대입 연산자
};
```

**기본 초기화**

기본 초기화는 하기와 같이 작성합니다.

```cpp
T obj; // 기본 생성자가 있어야 함
```

**값 초기화**

값 초기화는 값 생성자를 이용하여 특정한 값으로 생성할 때 사용합니다. 다만 전달할 인자가 없어 `T obj1();`와 같이 작성할 경우, 개체 생성이 아니라 T를 리턴하는 `obj1()` 함수로 인식됩니다. 전달할 인자가 없는 경우에는 `T obj1;`와 같이 작성해야 합니다.

```cpp
T obj1(); // (△) 비권장. 초기화 아님. T를 리턴하는 obj1 함수 정의
T obj2(1); // int를 전달받는 생성자 호출
```

하기는 `T(2);`로 개체 생성 후 `obj`에 대입하는 것 같은 모양입니다만, 사실 `obj`의 값 생성자를 호출합니다. 모양이 오해를 불러 일으키니 사용 안하시는게 좋습니다.(그냥 `T obj(2);`로 사용하세요.)

```cpp
// int를 전달받는 생성자 호출.
// (△) 비권장. T(2) 로 개체 생성후 obj의 복사 생성자 호출하는 느낌을 줌
T obj = T(2);
```
**복사 초기화**

복사 초기화는 복사 생성자나 인자가 1개인 값 생성자를 호출해 줍니다. 인자가 1개인 생성자는 뜻하지 않게 형변환이 될 수 있으므로 `explicit`로 정의하는게 좋습니다.
([형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/) 참고)

```cpp
T other;

// T obj = 1; // (X) 컴파일 오류. explicit 가 아니라면 int를 전달받는 값 생성자
T obj1 = other; // 타입이 같다면 복사 생성자
T obj2(other); // 복사 생성자
```

**생성 후 대입 : 하지 마라.**

하기와 같이 할 수 있습니다만, 서두에 말씀드렸듯이 하지 마십시요.

```cpp
T other;

T obj; // 기본 생성자
obj = other; // (△) 비권장. 생성하고 대입하지 말고, 완전하게 생성하세요.

```

**배열 초기화**

배열 초기화시 배열의 크기가 유추될 수 있어야 합니다. 배열 갯수를 명시적으로 지정하거나, 초기화 항목을 1개 이상 지정해 주면 됩니다.(갯수에 맞게 초기화 값을 제공하지 않으면, 나머지는 자동으로 기본값(`int`의 경우 `0`)으로 초기화 됩니다.)

```cpp
// int arr1[] = {}; // (X) 컴파일 오류. 갯수가 지정되지 않음. 오류
int arr2[] = {0, 1, 2}; // 갯수만큼 초기화
int arr3[3] = {}; // 3개 모두 0으로 초기화
int arr4[3] = {0, 1, }; // 갯수가 적거나 같아야 함. 모자라면 0

EXPECT_TRUE(arr2[2] == 2);
EXPECT_TRUE(arr3[0] == 0 && arr3[1] == 0 && arr3[2] == 0);
EXPECT_TRUE(arr4[2] == 0);
```

문자 배열의 경우 특별히 문자열 상수를 이용하여 초기화 할 수 있습니다. 이때 배열의 마지막 요소에 널문자(`\0`)가 추가됩니다.

```cpp
char str1[] = "abc"; // {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str1[0] == 'a');
EXPECT_TRUE(str1[1] == 'b');
EXPECT_TRUE(str1[2] == 'c');
EXPECT_TRUE(str1[3] == '\0'); // 널문자가 추가됨

wchar_t str2[] = L"abc"; // {L'a', L`b`, L'c', L'\0'};
EXPECT_TRUE(str2[0] == L'a');
EXPECT_TRUE(str2[1] == L'b');
EXPECT_TRUE(str2[2] == L'c');
EXPECT_TRUE(str2[3] == L'\0'); // 널문자가 추가됨
```

**구조체 초기화**

구조체는 별도로 값 초기화를 위한 생성자를 구현하지 않더라도 중괄호를 이용하여 초기화 할 수 있습니다.

```cpp
struct T {int x; int y;}; 
T t = {10, 20}; // 중괄호로 초기화

EXPECT_TRUE(t.x == 10 && t.y == 20);
```

**자동 제로 초기화**

대부분의 변수들은 생성시 초기화하지 않으면 기존 메모리에 있는 쓰레기 값을 갖게 되지만, 하기의 경우는 메모리 영역을 제로(`0`)로 만들어 초기화 합니다.

1. 전역 변수, 정적 전역 변수, 정적 멤버 변수, 정적 지역 변수
2. !!!생성자가 없는!!! 클래스, 구조체, 공용체의 멤버 변수

상수형 정적 멤버 변수는 초기화를 해야 컴파일되기 때문에, 자동 제로 초기화된다고 할 수 없습니다. 

```cpp
int g_Val; // 전역 변수. 명시적 초기화 가능
static int s_Val; // 정적 전역 변수. 명시적 초기화 가능

class T {
public:
    static int s_m_Val; // 정적 멤버 변수. 명시적 초기화 불가능. 선언과 정의 분리. 단 const 형은 선언에서 초기화 가능
    static const int s_c_m_Val = 0; // 상수형 정적 멤버 변수. 선언과 정의를 같이 해야 하며, 명시적으로 초기화 해야 함
    int m_Val; // 멤버 변수. 명시적 초기화 불가능.

    int f1() const {
        static int s_l_Val; // 정적 지역 변수. 명시적 초기화 가능
        return s_l_Val;
    }

    int f2() const {
        int l_Val; // 지역 변수. 명시적 초기화 가능
        return l_Val;
    }
};
int T::s_m_Val; // 선언과 정의 분리

T t;

EXPECT_TRUE(g_Val == 0); // 전역 변수는 0으로 자동 초기화
EXPECT_TRUE(s_Val == 0); // 정적 전역 변수는 0으로 자동 초기화
EXPECT_TRUE(T::s_m_Val == 0); // 정적 멤버 변수는 0으로 자동 초기화
EXPECT_TRUE(T::s_c_m_Val == 0); // 상수형 정적 멤버 변수는 명시적 초기화
EXPECT_TRUE(t.m_Val == 0); // 생성자가 없는 개체의멤버 변수는 0으로 자동 초기화
EXPECT_TRUE(t.f1() == 0); // 정적 지역 변수는 0으로 자동 초기화
// EXPECT_TRUE(t.f2() != 0); // 지역 변수는 쓰레기값이 될 수도 있음
```

복잡하죠? 복잡하니, 모든 변수를 초기화 한다는 대원칙을 가지고 작성하세요. 개발 초기에서 생성자가 없어서 자동 제로 초기화를 활용해서 개발했다가, 누군가 나중에 생성자를 넣어버리면 낭패를 보게 되니까요.

|항목|자동 제로 초기화 지원|선언에서 명시적 초기화 지원|권장 초기화 방법|
|--|:--:|:--:|--|
|전역 변수|O|O|선언시 초기화|
|정적 전역 변수|O|O|선언시 초기화|
|정적 멤버 변수|O|X|사용 비권장. 정적 지역 변수 사용|
|상수형 정적 멤버 변수|X|O|선언시 초기화|
|생성자가 없는 개체의 멤버 변수|O|X|생성자|
|생성자가 있는 개체의 멤버 변수|X|X|생성자|
|정적 지역 변수|O|O|선언시 초기화|
|지역 변수|X|O|선언시 초기화|