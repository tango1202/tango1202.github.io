---
layout: single
title: "#10. [고전 C++ 가이드] 열거형(enum)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 열거형은 [클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)나 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) 내에 정의하여 이름 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 축소하라.

# 개요

|항목|내용|
|--|--|
|열거형 정의| `enum Week {Sunday, Monday,};`|
|개체 정의| `Week obj = Sunday;`|
|용량|모든 열거자 값을 나타낼 수 있는 정수 형식의 크기. (보통 4byte)|

열거형은 정수형 상수 집합에 이름을 정의하며, 다음과 같이 사용할 수 있습니다.

```cpp
enum Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week = Sunday;
```

다만 상기와 같이 사용하면, 이름의 범위가 충돌되기 쉽기 때문에 [클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)(혹은 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)) 내에 정의하여 이름 범위 충돌을 최소화 하는 것이 좋습니다.(`enum Week {eSunday, eMonday...}`와 같이 열거형 전용으로 접두어를 쓰는 방법도 있습니다만, [헝가리안 표기](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-naming/#%ED%83%80%EC%9E%85-%EB%AA%85%EC%8B%9C-%EA%B8%88%EC%A7%80)같아서 권장하지는 않습니다.)

```cpp
class Week {
public:
    // 클래스내에 정의. 사용시 클래스명을 기재해야 함
    enum Val {
        Sunday, Monday, Tuesday, Wednesday, 
        Thursday, Friday, Saturday
    };
};

Week::Val val = Week::Sunday; // 클래스명 사용
EXPECT_TRUE(val == Week::Sunday);
```

# 열거형 값지정

열거형의 각 항목은 이전 항목 + 1 의 값입니다. 디폴트는 0 부터 시작하며, 임의 정수값을 지정할 수 있습니다.

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

# 열거형 상수 형변환

열거형 상수는 `int`로 형변환 되고, `int`는 `static_cast`를 이용하여 열거형 상수로 형변환 됩니다만, 타입에 기반한 **코딩 계약** 을 위반하기 때문에 형변환 하는건 좋지 않습니다.

```cpp
Week::Val val1 = Week::Saturday; // (△) 비권장. 형변환 하지 마세요.
int val2 = Week::Saturday; // (△) 비권장. 열거형 상수는 int 형변환 가능합니다만, 하지 마세요.
EXPECT_TRUE(val1 ==  Week::Saturday);
EXPECT_TRUE(val2 == 13);

val1 = static_cast<Week::Val>(val2 - 1); // (△) 비권장. int는 static_cast로 열거형으로 형변환 가능합니다만, 하지 마세요.
EXPECT_TRUE(val1 ==  Week::Friday);
```


