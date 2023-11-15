---
layout: single
title: "#10. [고전 C++ 가이드] 열거형(enum)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)은 [클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)나 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)내에 정의하여 이름 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 축소하라.

> **모던 C++**
> * (C++11~) 이름 범위를 한정하는 [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)이 추가되어 이름 충돌 회피가 쉬워졌고, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하며, [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)도 지원합니다.
> * (C++11~) [열거형의 기반 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#%EA%B8%B0%EB%B0%98-%ED%83%80%EC%9E%85)을 지정할 수 있습니다.

# 개요

[열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)은 정수형 상수 집합의 별칭을 정의합니다.

|항목|내용|
|--|--|
|[열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 정의| `enum Week {Sunday, Monday};`|
|개체 정의| `Week obj = Sunday;`|
|용량|모든 열거자 값을 나타낼 수 있는 정수 형식의 크기.(*보통 4byte*)|

다음과 같이 사용할 수 있습니다.(*절대 `define`쓰지 마세요!!! [매크로 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98) 참고*)

```cpp
enum Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week = Sunday;
```

다만 상기와 같이 사용하면, 이름의 범위가 충돌되기 쉽기 때문에 [클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)(*혹은 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)*) 내에 정의하여 이름 범위 충돌을 최소화 하는 것이 좋습니다.(*`enum Week {eSunday, eMonday...}`와 같이 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 전용으로 접두어를 쓰는 방법도 있습니다만, [헝가리안 표기](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-naming/#%ED%83%80%EC%9E%85-%EB%AA%85%EC%8B%9C-%EA%B8%88%EC%A7%80)같아서 권장하지는 않습니다.*)

```cpp
class Week {
public:
    // 클래스내에 정의. 사용시 클래스명을 기재해야 함
    enum Val {
        Sunday, Monday, Tuesday, Wednesday, 
        Thursday, Friday, Saturday
    };
};

Week::Val val = Week::Sunday; // 범위 확인 연산자와 클래스명 사용
EXPECT_TRUE(val == Week::Sunday);
```

> *(C++11~) 이름 범위를 한정하는 [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)이 추가되어 이름 충돌 회피가 쉬워졌고, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하며, [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)도 지원합니다. 클래스 내에 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)을 정의하지 마시고 [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)을 사용하세요.*

# 열거형 값지정

[열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 각 항목은 이전 항목 `+ 1` 의 값입니다. 디폴트는 `0`부터 시작하며, 임의 정수값을 지정할 수 있습니다.

```cpp
class Week {
public:
    enum Val {
        Sunday, // 기본적으로 0부터 시작하여 1씩 증가
        Monday, 
        Tuesday,
        Wednesday = 10, // 값 지정한 이후로 1씩 증가
        Thursday, 
        Friday, 
        Saturday
    };
};

EXPECT_TRUE(
    Week::Sunday == 0 && 
    Week::Monday == 1 && 
    Week::Tuesday == 2 &&
    Week::Wednesday == 10 && // 값 지정한 이후로 1씩 증가
    Week::Thursday == 11 &&
    Week::Friday == 12 && 
    Week::Saturday == 13
);
```

# 열거형의 크기

[열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)은 모든 열거자 값을 나타낼 수 있는 정수 형식의 크기로 설정됩니다. 따라서 다음 예는 `char`, `short`, `int` 크기중 하나가 될 수 있습니다.

```cpp
enum MyEnum {a, b, c};

EXPECT_TRUE(sizeof(MyEnum) == sizeof(char) || sizeof(MyEnum) == sizeof(short) || sizeof(MyEnum) == sizeof(int));
```

따라서, 열거자가 추가되다 보면 어느 순간 [sizeof()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 달라질 수 있습니다. 그래서, 파일 저장시에 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 크기만큼 오프셋을 이동하는 코드를이 있으면 호환성이 깨질 수도 있죠. 그래서 크기가 중요한 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)은 열거자중 `dummy`를 만들어 충분히 큰값을 지정하여 사용합니다.

```cpp
enum MyEnum {a, b, c, MyEnumMax = 0XFFFFFFFF};
```

> *(C++11~) [열거형의 기반 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#%EA%B8%B0%EB%B0%98-%ED%83%80%EC%9E%85)을 지정할 수 있습니다.*

# 열거형 상수 형변환

[열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)은

1. `int`로 형변환 되고, 
2. `int`는 [static_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)로 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)으로 형변환 됩니다만, 

타입에 기반한 **코딩 계약** 을 위반하기 때문에 형변환 하는건 좋지 않습니다.

```cpp
Week::Val val1 = Week::Saturday; // (△) 비권장. 형변환 하지 마세요.
int val2 = Week::Saturday; // (△) 비권장. 열거형 상수는 int 형변환 가능합니다만, 하지 마세요.
EXPECT_TRUE(val1 ==  Week::Saturday);
EXPECT_TRUE(val2 == 13);

val1 = static_cast<Week::Val>(val2 - 1); // (△) 비권장. int는 static_cast로 열거형으로 형변환 가능합니다만, 하지 마세요.
EXPECT_TRUE(val1 ==  Week::Friday);
```

> *(C++11~)이름 범위를 한정하는 [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)이 추가되어 이름 충돌 회피가 쉬워졌고, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하며, [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)도 지원합니다.*

