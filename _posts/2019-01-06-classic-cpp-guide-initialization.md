---
layout: single
title: "#6. [고전 C++ 가이드] 초기화"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> 초기화되지 않은 변수를 사용하지 마라.
> 생성하면서 초기화 하라.(대충 생성한 뒤 나중에 값을 설정하지 마라.)

**개요**

초기화에는 다음과 같은 방법들이 있습니다.

|항목|내용|
|--|--|
|기본 초기화|`T obj;`|
|값 초기화|`T obj();`|
|복사 초기화|`T obj = other;` 또는 `T obj(other);`|
|생성 후 대입(불필요한 부하)|`T obj; obj = other;`|
|배열 초기화|`T arr[] = {};`|

상기중 생성 후 대입하는 것은 불필요한 부하를 주기 때문에 좋지 않습니다. 또한 **예외 안정** 프로그래밍에도 좋지 않습니다.

다음과 같이 사용할 수 있습니다.

```cpp
namespace {
    class T {
    public:
        T() {} // 기본 생성자
        explicit T(int t) {} // int를 전달받는 생성자. explicit를 주어 복사 초기화를 막음
        T(const T& other) {} // 복사 생성자

        void operator =(const T& other) {} // 대입 연산자
    };
}
```

```cpp
// 기본 초기화
T obj1; // 기본 생성자가 있어야 함

// 값 초기화
T obj2(); // 초기화 아님. T를 리턴하는 obj2 함수정의
T obj3(1); // int를 전달받는 생성자 호출

// 복사 초기화
// T obj4 = 1; // explicit 가 아니라면 int를 전달받는 생성자
T obj5 = obj1; // 타입이 같다면 복사 생성자
T obj6(obj1); // 복사 생성자

// 생성후 대입 - 권장하지 않음
T obj7; // 기본 생성자
obj7 = obj1; // 대입연산자

T obj8 = T(2); // int를 전달받는 생성자 호출.(T(2) 로 개체 생성후 obj8의 복사 생성자 호출하지는 않음)

// 배열 초기화
// int arr1[] = {}; // 갯수가 지정되지 않음. 오류
int arr2[] = {0, 1, 2}; // 갯수만큼 초기화
int arr3[3] = {}; // 3개 모두 0으로 초기화
int arr4[3] = {0, 1, }; // 갯수가 적거나 같아야 함. 모자라면 0

EXPECT_TRUE(arr2[2] == 2);
EXPECT_TRUE(arr3[0] == 0 && arr3[1] == 0 && arr3[2] == 0);
EXPECT_TRUE(arr4[2] == 0);
```

