---
layout: single
title: "#22. [모던 C++ STL] (C++11~) C++스타일 배열(array)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 추가되어 기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)를 제공합니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.
> * (C++20) [to_array()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/#array-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 추가되어 기존 [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)로부터 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)를 생성할 수 있습니다.

# 개요

기존의 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)과 호환성이 좋았으나, 요소 추가/삭제등이 필요하여 동적 메모리를 사용하다 보니, 메모리 할당시 [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)을 사용합니다. 이 때문에 아무래도 메모리 할당 측면에서는 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되는 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)보다 성능이 떨어졌습니다.

C++11 부터는 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 추가되어 기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)를 제공합니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.

|항목|설명|[C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)|[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)|
|--|--|--|--|--|
|연속된 메모리|모두 연속된 메모리를 사용합니다.|O|O|O|
|동적 메모리 할당|[C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)과 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)는 컴타일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당됩니다.|X|X|O|
|요소 추가/삭제|[C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)과 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)는 요소를 추가/삭제할 수 없습니다.|X|X|O|
|요소 접근 `[]`|인덱스 범위 검사를 하지 않습니다.|O|O|O|
|요소 접근 `at()`|인덱스 범위 검사를 합니다.|X|O|O|
|[이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)|[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)와 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 순방향 및 역방향 탐색을 지원합니다.|X|O|O|
|배열 주소|데이터의 첫번째 요소의 주소를 구할 수 있습니다.|`arr` 또는 `arr[0]`|`arr.data()`|`&v[0]`|
|포인터로의 암시적 변환|[C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)은 `int* p = arr;` 와 같이 포인터로 암시적 변환 됩니다.<br/>[오버로딩된 함수 결정 규칙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)에 따라 함수 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 사용시 포인터로 취급됩니다.|O|X|X|
|[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)를 통한 요소 갯수 유추|[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)은 초기화 요소의 갯수로 [C스타일 배열의 요소 갯수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)를 유추하지만, [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)는 명시적으로 지정해야 합니다.|O|X|O|
|[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)를 통한 [자동 제로 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)|요소의 갯수보다 적게 초기화하면 나머지 요소는 `0`으로 초기화 됩니다.|O|O|O|
|속도|[C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)과 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되기 때문에, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)보다 빠릅니다.|1|2|3|
|대입|[C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)은 대입을 지원하지 않으며, [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)는 요소의 갯수가 같으면 대입됩니다.|X|O|O|


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
# array 멤버 함수

|항목|내용|
|--|--|
|`at()` (C++11~)|(작성중)|
|`front()` (C++11~)|(작성중)|
|`back()` (C++11~)|(작성중)|
|`data()` (C++11~)|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)가 관리하는 메모리 블록을 리턴합니다.|
|`begin(), end()` (C++11~)|(작성중)|
|`cbegin(), cend()` (C++11~)|(작성중)|
|`rbegin(), rend()` (C++11~)|(작성중)|
|`crbegin(), crend()` (C++11~)|(작성중)|
|`empty()` (C++11~)|(작성중)|
|`size()` (C++11~)|(작성중)|
|`max_size()` (C++11~)|(작성중)|
|`fill()` (C++11~)|(작성중)|
|`swap()` (C++11~)|(작성중)|
|`==` (C++11~)<br/>`!=` (C++11~C++20)|(작성중)|
|`<, <=, >, >=` (C++11~C++20)<br/>`<=>` (C++20~)|(작성중)|
|[to_array()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/#array-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) (C++20~)|기존 [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)로부터 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)를 생성합니다.|

# 배열, array, vector 속도 비교

다음은 [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/), [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/), [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 생성/소멸과 접근 속도를 테스트한 예입니다. 시간 측정을 위해 [chrono 라이브러리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/)를 사용했습니다.

1. 십만개의 요소를 십만번 생성/소멸했고,
2. 십만개의 요소에 접근하여 값을 대입합니다.

```cpp
// 생성/소멸 테스트
void CStyleArrayConstruct() {
    for(int i{0}; i < 100000; ++i) {
        int arr[100000];
    }        
}
void STLArrayConstruct() {
    for(int i{0}; i < 100000; ++i) {
        std::array<int, 100000> arr;
    }        
}
void STLVectorConstruct() {
    for(int i{0}; i < 100000; ++i) {
        std::vector<int> arr(100000);
    }        
}      
// 접근 테스트
void CStyleArrayAccess() {
    int arr[100000];

    for(int i{0}; i < 100000; ++i) {
        arr[i] = i;
    }        
}
void STLArrayAccess() {
    std::array<int, 100000> arr;

    for(int i{0}; i < 100000; ++i) {
        arr[i] = i;
    }        
}
    void STLVectorAccess() {
    std::vector<int> arr(100000);

    for(int i{0}; i < 100000; ++i) {
        arr[i] = i;
    }        
}  

template<typename Func>
std::chrono::microseconds Measure(Func f) {
    std::chrono::system_clock::time_point start{std::chrono::system_clock::now()};    

    f();

    std::chrono::system_clock::time_point end{std::chrono::system_clock::now()};
    std::chrono::microseconds val{std::chrono::duration_cast<std::chrono::microseconds>(end - start)};

    return val;
}

// 생성/소멸 테스트
std::cout << "CStyleArray : " << Measure(CStyleArrayConstruct).count() << std::endl;
std::cout << "STLArray : " << Measure(STLArrayConstruct).count() << std::endl;
std::cout << "STLVector : " << Measure(STLVectorConstruct).count() << std::endl;

// 접근 테스트
std::cout << "CStyleArray : " << Measure(CStyleArrayAccess).count() << std::endl;
std::cout << "STLArray : " << Measure(STLArrayAccess).count() << std::endl;
std::cout << "STLVector : " << Measure(STLVectorAccess).count() << std::endl;

```

실행 결과는 다음과 같습니다.
[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 생성/소멸이 [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 보다 빠른게 좀 의외이긴 합니다.

```cpp
CStyleArray : 660
STLArray : 228 // C스타일 배열보다 빠릅니다.
STLVector : 18612352 // 동적 할당을 하다보니 느립니다.

CStyleArray : 209
STLArray : 404 // operator[]을 이용하다 보니 C스타일 배열보다는 느립니다.
STLVector : 487 // operator[]을 이용하다 보니 C스타일 배열보다는 느립니다.
```