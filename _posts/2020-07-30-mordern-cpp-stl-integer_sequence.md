---
layout: single
title: "#30. [모던 C++ STL] (C++14~) integer_sequence"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++14~) [integer_sequence](https://tango1202.github.io/mordern-cpp-stl/)는 컴파일 타임에 정수 타입의 시퀀스를 만듭니다.

# 개요

[integer_sequence](https://tango1202.github.io/mordern-cpp-stl/)는 컴파일 타임에 정수 타입의 시퀀스를 만듭니다. [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용하여 [파라메터 팩 배포 및 확장](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)에 사용할 수 있습니다.

|항목|내용|
|--|--|
|`integer_sequence<type, 0, 1, 2>` (C++14~)|`type` 타입으로 `0`, `1`, `2` 시퀀스를 만듭니다.|
|`index_sequence<0, 1, 2>` (C++14~)|타입이 `size_t`인 [integer_sequence](https://tango1202.github.io/mordern-cpp-stl/) 입니다.|
|`make_integer_sequence<type, N>` (C++14~)|`type` 타입으로 `0` ~ `N-1` 시퀀스를 만듭니다.|
|`make_index_sequence<N>` (C++14~)|`size_t` 타입으로 `0` ~ `N-1` 시퀀스를 만듭니다.|
|`index_sequence_for<params...>` (C++14~)|`0` ~ [파라메터 팩](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)의 요소 갯수만큼 시퀀스를 만듭니다.<br/>즉, `make_index_sequence(sizeof...(params))`입니다.|

# array 에서 tuple 변환

다음은 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)의 일부 요소를 추출하여 [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)을 생성하는 예입니다. 나쁘진 않습니다만, 추출할 요소가 많고 복잡하면 코드가 어지러워 질 수 있습니다.

```cpp
std::array<int, 5> arr{10, 20, 30, 40, 50};
std::tuple<int, int> data{std::make_tuple(arr[0], arr[1])};
EXPECT_TRUE(
    std::get<0>(data) == 10 &&
    std::get<1>(data) == 20 
); 
```

[integer_sequence](https://tango1202.github.io/mordern-cpp-stl/)를 이용하면, 추출할 인덱스만 지정하여 [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)를 [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)로 변환하는 코드를 좀더 깔끔하게 만들 수 있습니다.

```cpp
template<size_t N, std::size_t... params>
auto ArrayToTuple_14(const std::array<int, N>& arr, std::integer_sequence<size_t, params...>) {
    return std::make_tuple(arr[params]...); // arr[params[1]], arr[params[2]], arr[params[3]] 등으로 전개됨 
}

std::array<int, 5> arr{10, 20, 30, 40, 50};    

// arr 에서 0, 1 인덱스만 tuple로 변환
std::tuple<int, int> data1{ArrayToTuple_14(arr, std::integer_sequence<size_t, 0, 1>{})};
EXPECT_TRUE(
    std::get<0>(data1) == 10 &&
    std::get<1>(data1) == 20 
); 

// arr 에서 2, 3, 4 인덱스만 tuple로 변환
std::tuple<int, int, int> data2{ArrayToTuple_14(arr, std::integer_sequence<size_t, 2, 3, 4>{})};
EXPECT_TRUE(
    std::get<0>(data2) == 30 &&
    std::get<1>(data2) == 40 &&
    std::get<2>(data2) == 50 
); 
```