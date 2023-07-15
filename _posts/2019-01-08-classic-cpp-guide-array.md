---
layout: single
title: "#8. [고전 C++ 가이드] 배열(Array) 와 비트 필드(작성중)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 배열보다는 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)를 사용하라. 동적 요소 할당을 지원하며, 여전히 엑세스는 빠르다.

# 개요

|항목|내용|
|--|--|
|정의| `int arr[5];`|
|동적 생성|`int* p = new int[5];`|
|동적 소멸|`delete[] p;`|
|초기화|`int arr[3] = {1, 2, 3};`,<br/>`char str[] = "abc";`<br/>([배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 참고)|
|초기화를 이용한 크기 유추|`int arr[] = {1, 2, 3};`<br/>([배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 참고)|

배열은 타입이 같은 여러 데이터를 집합으로 묶어 연속적인 메모리에 관리할 수 있게 해줍니다. 단, `void` 형과 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)와 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 데이터로 가질 순 없습니다. 

배열은 배열의 크기를 지정하거나, 초기화 갯수로 부터 크기가 유추될 수 있게 정의합니다.([배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 참고)

```cpp
// int arr1[]; // (X) 컴파일 오류. 갯수가 지정되지 않음. 오류
int arr1[3]; // (△) 비권장. int 형 3개 정의. 초기화 되지 않아 비권장 
int arr2[] = {0, 1, 2}; // (O) 갯수만큼 초기화
int arr3[3] = {}; // (O) 3개 모두 0으로 초기화
int arr4[3] = {1, }; // (O) 갯수가 적거나 같아야 함. 모자라면 0

EXPECT_TRUE(sizeof(arr1) == sizeof(int) * 3); // 배열 항목의 갯수와 같음
EXPECT_TRUE(arr2[2] == 2);
EXPECT_TRUE(arr3[0] == 0 && arr3[1] == 0 && arr3[2] == 0);
EXPECT_TRUE(arr4[0] == 1 && arr4[1] == 0 && arr4[2] == 0);
```

# 배열과 `vector`

타입이 같은 여러 데이터 집합은 배열 보다는 표준 템플릿 라이브러리의 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)를 사용하는 편이 좋습니다. 동적 요소 할당을 지원하며, 여전히 엑세스는 빠릅니다.

`vector`와 배열간 코딩 호환성도 나쁘지 않습니다. 정의와 초기화만 다르고, 요소 접근하는 방법이나 요소 주소를 얻는 것은 동일합니다.

|항목|배열|`vector`|
|--|--|--|
|정의|`int a[5];`|`vector<int> v(5);`|
|초기화|`int a[5] = {};`|X|
|요소 접근|`a[0]`|`v[0]`|
|요소 주소|`&a[0]`|`&v[0]`|

# `typedef`로 타입 재정의

`typedef`를 사용하여 배열 타입을 재정의할 수 있습니다.

```cpp
typedef int MyArray[5]; 
MyArray arr; // int arr[5]; 와 동일 
arr[0] = 10; // 첫번째 요소에 값 대입
EXPECT_TRUE(arr[0] == 10);
```

# 배열 동적 생성과 삭제

`new`와 `delete` 대신 `new[]` 와 `delete[]`로 생성하고 소멸시킵니다.

```cpp
int* p = new int[5]; // int[5] 배열 생성
delete[] p; // 삭제. delete가 아님에 유의. delete p;를 하면 배열 요소 갯수만큼 소멸자를 호출하지 않고 한번만 호출합니다.
```

# 비트 필드

https://en.cppreference.com/w/cpp/language/bit_field
비트 필드
