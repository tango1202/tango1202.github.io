---
layout: single
title: "#43. [모던 C++ STL] span(C++20)"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [span](https://tango1202.github.io/cpp-stl/modern-cpp-stl-span/)이 추가되어 [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/), [array](https://tango1202.github.io/cpp-stl/modern-cpp-stl-array/), [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/), 메모리, [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 등 연속된 메모리 시퀀스를 참조할 수 있습니다.

# 개요 

[span](https://tango1202.github.io/cpp-stl/modern-cpp-stl-span/)은 연속된 메모리 시퀀스의 참조를 전달받아 이터레이팅 하는 개체입니다. 

[C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)의 경우 전달받을 때 포인터로 변경되어 요소의 갯수 정보가 유실되어, 요소 갯수를 같이 전달받거나, [함수 템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)을 활용해야 했는데요, [span](https://tango1202.github.io/cpp-stl/modern-cpp-stl-span/)을 이용하면 요소 갯수 유실없이 전달받을 수 있습니다. 

```cpp
// 배열을 포인터로 전달받습니다.
int Sum1(const int* p, int size) {
    int result{0};

    for (int i{0}; i < size; ++i) {
        result += p[i];
    }    
    return result;
}

// 배열 참조자로 전달받습니다.
// const int (&arr)[]를 사용하면 sizeof(arr)시 크기를 알 수 없으므로 size를 전달받습니다.
int Sum2(const int (&arr)[], int size) { 
    int result{0};
    for (int i{0}; i < size; ++i) {
        result += arr[i];
    }    
    return result;    
}
// 템플릿으로 배열 참조자를 전달받습니다. 이때 배열 요소 갯수인 N을 추론할 수 있습니다.
template<typename T, size_t N>
int Sum3(const T (&arr)[N]) {
    int result{0};
    for (int i{0}; i < N; ++i) {
        result += arr[i];
    }    
    return result;    
}
// 배열을 span으로 전달받습니다.
int Sum4(std::span<int> span) {

    int result{0};
    auto itr{span.begin()};
    auto endItr{span.end()};

    for (; itr != endItr; ++itr) {
        result += *itr;
    }
    return result;
}    

int arr[]{1, 2, 3};
EXPECT_TRUE(Sum1(arr, sizeof(arr) / sizeof(arr[0])) == 1 + 2 + 3); // 요소 갯수를 전달해야 합니다.
EXPECT_TRUE(Sum2(arr, sizeof(arr) / sizeof(arr[0])) == 1 + 2 + 3); // 요소 갯수를 전달해야 합니다.
EXPECT_TRUE(Sum3(arr) == 1 + 2 + 3); // 함수 템플릿을 사용하면, 요소 갯수를 전달할 필요가 없습니다.
EXPECT_TRUE(Sum4(arr) == 1 + 2 + 3); // span을 사용하면, 요소 갯수를 전달할 필요가 없습니다.

```

[범위(Range)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-range/)를 이터레이팅하는 [뷰(View)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-range/#%EB%B7%B0view)와 비슷하지만, [span](https://tango1202.github.io/cpp-stl/modern-cpp-stl-span/)은 [뷰(View)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-range/#%EB%B7%B0view)와 달리 원본을 수정할 수 있습니다.

```cpp
std::vector<int> v{1, 2, 3};   
std::span<int> span{v};

span[0] = 10;

EXPECT_TRUE(v[0] == 10); // 원본이 수정됩니다. 
```

# span 초기화

[span](https://tango1202.github.io/cpp-stl/modern-cpp-stl-span/)은 [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/), [array](https://tango1202.github.io/cpp-stl/modern-cpp-stl-array/), [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/), 메모리, [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/) 등 연속된 메모리 시퀀스인 개체로 초기화 할 수 있습니다.

```cpp
int arr1[]{1, 2, 3};
std::span<int> a{arr1}; // C스타일 배열

std::array<int, 3> arr2{1, 2, 3};
std::span<int> b{arr2}; // C++ array

std::vector<int> v1{1, 2, 3};
std::span<int> c{v1}; // vector

std::vector<int> v2{1, 2, 3, 4, 5};
std::span<int> d{v2.data(), 3}; // 특정 메모리와 갯수

EXPECT_TRUE(
    std::ranges::equal(a, b) && // 요소들이 같으면 true입니다.
    std::ranges::equal(b, c) &&
    std::ranges::equal(c, d) 
);

std::string str{"abc"};
std::span<char> e{str}; // string
EXPECT_TRUE(e[0] == 'a' && e[1] == 'b' && e[2] == 'c'); 

```

또한 생성시 크기를 전달하여 `size()`를 컴파일 타임 함수로 사용할 수 있습니다.

```cpp
std::vector<int> v{1, 2, 3};
std::span<int> dynamicSpan{v}; // 런타임에 크기가 결정됩니다. 동적 크기 span
std::span<int, 3> staticSpan{v}; // 컴파일 타임에 크기가 결정됩니다. 정적 크기 span

// static_assert(dynamicSpan.size() == 3); (X) 컴파일 오류. 컴파일 타임 함수가 아닙니다.
static_assert(staticSpan.size() == 3); // (O) 컴파일 타임 함수입니다.
```

# span 멤버 함수

|항목|내용|
|--|--|
|`operator =()` (C++20~)|얕은 복사를 합니다.|
|`begin(), end()` (C++20~)|순방향 [이터레이터](https://tango1202.github.io/cpp-stl/modern-cpp-stl-iterator/)를 리턴합니다.|
|`rbegin(), rend()` (C++20~)|[역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`cbegin(), cend()` (C++23~)|순방향 [이터레이터](https://tango1202.github.io/cpp-stl/modern-cpp-stl-iterator/)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin(), crend()` (C++23~)|[역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`front()` (C++20~)|첫번째 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다.|
|`back()` (C++20~)|마지막 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다.|
|`at(position)` (C++20~)|`position`위치의 요소 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. `position`이 잘못된 위치이면 `[]` 과 달리 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`operator []()` (C++20~)|`position`위치의 요소 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. `position`이 잘못된 위치라면 아무 생각없이 실행되어 오동작 합니다.|
|`data()` (C++20~)|참조하는 개체가 관리하는 메모리 블록을 리턴합니다.(*첫번째 요소의 포인터를 리턴합니다.*)|
|`size()` (C++20~)|요소 갯수를 리턴합니다.|
|`size_bytes()` (C++20~)|바이트 수를 리턴합니다.|
|`empty()` (C++20~)|비었는지 확인합니다.|
|`first()` (C++20~)|처음부터 `N`개의 요소로 구성되는 `subspan`을 구합니다.|
|`last()` (C++20~)|끝에서부터 `N`개의 요소로 구성되는 `subspan`을 구합니다.|
|`subspan(offset, count)` (C++20~)|`offset`부터 `count`개의 `subspan`을 구합니다.|
|`as_bytes(), as_writable_bytes()` (C++20~)|(작성중)|
|`dynamic_extent` (C++20~)|(작성중)|