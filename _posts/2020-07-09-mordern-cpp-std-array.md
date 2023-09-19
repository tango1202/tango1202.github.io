---
layout: single
title: "#10. [모던 C++ STL] (C++11~) array"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `array`는 기존 C스타일의 배열처럼 컴파일 타임에 크기가 결정되는 연속된 메모리를 사용하는 컨테이너 입니다. 요소 추가/삭제가 없는 경우 사용할 수 있으며, `vector` 보다 성능이 좋습니다.

# 개요

기존의 `vector`도 C스타일의 배열과 호환성이 좋았으나, 요소 추가/삭제등이 필요하여 동적 메모리를 사용하다 보니, 아무래도 배열보다 성능일 떨어졌습니다.

`array`는 기존 C스타일의 배열처럼 컴파일 타임에 크기가 결정되는 연속된 메모리를 사용하는 컨테이너 입니다. 요소 추가/삭제가 없는 경우 `vector` 보다 성능이 좋습니다.

|항목|설명|C스타일 배열|`array`|`vector`|
|--|--|--|--|--|
|연속된 메모리|모두 연속된 메모리를 사용합니다.|O|O|O|
|동적 메모리 할당|배열과 `array`는 컴타일 타임에 크기가 결정되어 스택에 할당됩니다.|X|X|O|
|요소 추가/삭제|배열과 `array`는 요소를 추가/삭제할 수 없습니다.|X|X|O|
|요소 접근 `[]`|인덱스 범위 검사를 하지 않습니다.|O|O|O|
|요소 접근 `at()`|인덱스 범위 검사를 합니다.|X|O|O|
|이터레이터|`array`와 `vector`는 순방향 및 역방향 탐색을 지원합니다.|X|O|O|
|배열 주소|데이터의 첫번째 요소의 주소를 구할 수 있습니다.|`arr` 또는 `arr[0]`|`arr.data()`|`&v[0]`|
|포인터로의 암시적 변환|배열은 `int* p = arr;` 와 같이 포인터로 암시적 변환 됩니다.<br/>[오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)에 따라 함수 인자로 배열 사용시 포인터로 취급됩니다.|O|X|X|
|[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)를 통한 요소 갯수 유추|배열은 초기화 요소의 갯수로 배열의 요소 갯수를 유추하지만, `array`는 명시적으로 지정해야 합니다.|O|X|O|
|[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)를 통한 [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)|요소의 갯수보다 적게 초기화하면 나머지 요소는 `0`으로 초기화 됩니다.|O|O|O|
|속도|크기가 컴파일 타임에 결정되기 때문에 `vector`보다는 C스타일 배열과 `array`가 빠릅니다.|1|2|3|
|대입|배열은 대입을 지원하지 않으며, `array`는 요소의 갯수가 같으면 대입됩니다.|X|O|O|


```cpp
std::array<int, 3> a{1, 2, 3};
std::array<int, 3> b{4, 5}; 
std::array<int, 2> c{4, 5};   

// int* ptr = a; // (X) 컴파일 오류. array는 포인터로 암시적 변환이 되지 않습니다.
int* ptr = a.data(); // data를 이용하면 포인터로 변환됩니다.  

EXPECT_TRUE(b[0] == 4 && b[1] == 5 && b[2] == 0); // 요소의 갯수보다 적게 초기화하면 나머지 요소는 0으로 초기화 됩니다.

// c = a; // (X) 컴파일 오류. 요소의 갯수가 다르면 컴파일 오류가 발생합니다.
b = a; // 요소의 갯수가 같으면 대입이 가능합니다.
EXPECT_TRUE(b[0] == 1 && b[1] == 2 && b[2] == 3);
```
