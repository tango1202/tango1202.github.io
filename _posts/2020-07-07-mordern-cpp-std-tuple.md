---
layout: single
title: "#7. [모던 C++ STL] (C++11~) tuple"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
기존에는 데이터를 주고받는 가벼운 클래스를 만들기 위해 `pair`를 사용했는데요, `pair`는 `first`와 `second`로 두개의 요소만 관리할 수 있다는 아쉬움이 있었습니다.([pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-etc/#pair) 참고)

C++11 부터는 `tuple`이 제공되어 다수의 개체를 다룰 수 있습니다. 

1. `tuple` 정의시 사용할 타입을 나열하고,
2. `get()` 함수에 `0`, `1`, `2`등 인덱스를 이용하여 접근합니다.

```cpp
std::tuple<int, std::string, char> Func() {
    return std::tuple<int, std::string, char>{10, "Name", 'a'}; // return {10, "Name", 'a'}; 와 동일합니다.
}

// 3개의 데이터로 구성된 tuple 입니다.
std::tuple<int, std::string, char> result{Func()};

// get()함수를 이용하여 순서를 지정하여 데이터에 접근합니다.
EXPECT_TRUE(
    std::get<0>(result) == 10 && 
    std::get<1>(result) == "Name" && 
    std::get<2>(result) == 'a'
);
```

# get()

`tuple` 에서 주어진 인덱스 위치에 있는 요소의 참조자를 리턴합니다.

# make_tuple()

`make_pair`처럼([pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-etc/#pair) 참고) `tuple` 을 생성합니다.

```cpp
std::tuple<int, std::string, char> Func() {
    return std::make_tuple(10, "Name", 'a');
}
```

# tie()

좌측값 참조로 구성된 `tuple`을 만듭니다. `tuple`에서 `get()`함수를 이용해서 데이터를 확인하는데, 참 직관적이지 못합니다. 이럴때 다음처럼 `tie()` 함수를 이용하여 지역 변수의 참조자로 구성된 `tuple`을 만들면 좀더 직관적으로 데이터를 확인할 수 있습니다.

```cpp
int val;
std::string name;
char ch;

// tie 함수로 지역 변수의 참조자로 구성된 tuple을 만듭니다.
std::tie(val, name, ch) = Func();

EXPECT_TRUE(
    val == 10 && 
    name == "Name" && 
    ch == 'a'
); 
```

# forward_as_tuple()

함수 인수 전달에 적합하도록, `tuple` 생성시 좌측값이나 우측값 참조가 변형되지 않도록 합니다. [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward) 함수 처럼요.

# tuple_cat()

두개의 `tuple` 을 합칩니다.

```cpp
auto t1{std::make_tuple(10, "Name", 'a')};
auto t2{std::make_tuple("Hello", 20, 'b')};

auto t3{std::tuple_cat(t1, t2)};   
EXPECT_TRUE(
    std::get<0>(t3) == 10 && 
    std::get<1>(t3) == "Name" && 
    std::get<2>(t3) == 'a' &&
    std::get<3>(t3) == "Hello" && 
    std::get<4>(t3) == 20 && 
    std::get<5>(t3) == 'b'
); 
```

# tuple_size

컴파일 타임에 `tuple`의 요소 갯수를 구합니다.

```cpp
static_assert(std::tuple_size<std::tuple<int, std::string, char>>::value == 3);
```

# tuple_element

`tuple`의 각 요소에 대한 타입을 제공합니다.

```cpp
using MyTuple = std::tuple<int, std::string, char>;
MyTuple data{10, "Name", 'a'};

std::tuple_element<0, MyTuple>::type intVal{std::get<0>(data)};
std::tuple_element<1, MyTuple>::type stringVal{std::get<1>(data)};
std::tuple_element<2, MyTuple>::type charVal{std::get<2>(data)};

EXPECT_TRUE(
    intVal == 10 && 
    stringVal == "Name" && 
    charVal == 'a'
);
```




# piecewise_construct

# (C++14~) 타입 기반 get()

C++11 에서는 `get()` 함수가 인덱스 기반으로 `tuple` 요소에 접근할 수 있었으나, C++14 부터는 타입들이 서로 다르다면 타입을 이용하여 데이터에 접근할 수 있습니다. 

```cpp
std::tuple<int, std::string, char> Func() {
    return std::tuple<int, std::string, char>{10, "Name", 'a'}; // return {10, "Name", 'a'}; 와 동일합니다.
}

// 3개의 데이터로 구성된 tuple 입니다.
std::tuple<int, std::string, char> result{Func()};

// 타입들이 서로 다르다면 타입을 이용하여 데이터에 접근할 수 있습니다.
EXPECT_TRUE(
    std::get<int>(result) == 10 && 
    std::get<std::string>(result) == "Name" &&
    std::get<char>(result) == 'a' 
);
```



