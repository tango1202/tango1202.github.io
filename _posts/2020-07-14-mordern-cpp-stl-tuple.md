---
layout: single
title: "#14. [모던 C++ STL] (C++11~) tuple"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> `tuple`은 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다. 

# 개요

기존에는 데이터를 주고받는 가벼운 개체를 만들기 위해 `pair`를 사용했는데요, `pair`는 `first`와 `second`로 두개의 요소만 관리할 수 있다는 아쉬움이 있었습니다.([pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/) 참고)

C++11 부터 STL 에서는 `tuple`이 제공되어 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만들 수 있습니다.

|항목|내용|
|--|--|
|[pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)|`first`와 `second`로 2개의 요소를 관리합니다.|
|[make_pair()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)|`pair`를 생성합니다.|
[piecewise_construct](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#piecewise_construct) (~C++11)|`pair`의 개체 생성시 `tuple`의 요소들로 개체 생성자를 호출합니다.|
|[tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple) (C++11~)|다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.|
|`get()` (C++11~)|`tuple` 에서 주어진 인덱스 위치에 있는 요소의 참조자를 리턴합니다.<br/>(C++14~) 타입으로 찾을 수 있도록 보강되었습니다.|
|[make_tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#make_tuple) (C++11~)|`tuple` 을 생성합니다.|
|[tie()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tie) (C++11~)|좌측값 참조로 구성된 `tuple`을 만듭니다. |
|[forward_as_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#forward_as_tuple) (C++11~)|함수 인수 전달에 적합하도록, `tuple` 생성시 좌측값이나 우측값 참조가 변형되지 않도록 합니다.|
|[tuple_cat()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_cat) (C++11~)|두개의 `tuple` 을 합칩니다.|
|[tuple_size](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_size) (C++11~)|컴파일 타임에 `tuple`의 요소 갯수를 구합니다.|
|[tuple_element](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_element) (C++11~)|`tuple`의 각 요소에 대한 타입을 제공합니다.|
|`ignore()` (C++11~)|(작성중)|
|`basic_common_reference` (C++23~)|(작성중)|
|`common_type` (C++23~)|(작성중)|
|`interger_sequence` (C++14~)|(작성중)|
|`apply()` (C++17~)|(작성중)|
|`make_from_tuple()` (C++17~)|(작성중)|

# tuple

다수의 요소를 관리할 수 있는 데이터 전달용 개체를 만듭니다.

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

# make_tuple()

`make_pair`처럼([pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/) 참고) `tuple` 을 생성합니다.

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

`wise_construct`는 `pair`의 생성자의 오버라이드 버전을 호출하기 위한 더미 개체 인데요, `pair`의 `first`와 `second` 개체를 생성할때, 전달된 `tuple` 개체의 요소들로 초기화 해줍니다.

보통 `A`와 `B` 개체를 관리하는 `pair`를 만든다면, 

```cpp
std::pair<A, B> data{
    A{10, "Name", 'a'}, // A 개체 생성
    B{1, 2, 3, 4} // B 개체 생성
};
```

와 같이 만들텐데요, 

`piecewise_construct`를 사용한 오버로딩 버전을 실행하면, `A` 개체를 생성할때 사용할 인수들과 `B`개체를 생성할때 사용할 인수들을 `tuple`로 전달해서, `pair` 내부에서 `A`, `B`를 생성하도록 할 수 있습니다.

```cpp
class A {
    int m_Val;
    const char* m_Str;
    char m_Ch;
public:
    A(int val, const char* str, char ch) : 
        m_Val(val),
        m_Str(str),
        m_Ch(ch) {}
    int GetVal() const {return m_Val;}
    const char* GetStr() const {return m_Str;}
    char GetCh() const {return m_Ch;}
}; 

class B {
    int m_Val;
public:
    B(int a, int b, int c, int d) : m_Val(a + b + c + d) {}
    int GetVal() const {return m_Val;}
};

std::pair<A, B> data{
    std::piecewise_construct, // A, B 개체 생성시 tuple의 값을 인수로 전달함
    std::forward_as_tuple(10, "Name", 'a'), // A 생성자에 전달할 인수들을 tuple로 전달
    std::forward_as_tuple(1, 2, 3, 4) // B 생성자에 전달할 인수들을 tuple로 전달
};

EXPECT_TRUE(
    data.first.GetVal() == 10 &&
    data.first.GetStr() == "Name" &&
    data.first.GetCh() == 'a' &&
    data.second.GetVal() == 1 + 2 + 3 + 4
);
```

상기 예에서는 인수들의 참조성이 변하지 않도록 `forward_as_tuple()`을 사용했는데요, `make_tuple()`을 사용해도 결과는 같습니다.

하지만, 인자로 전달된 변수를 사용할 때에는 참조성이 변할 수도 있으므로, `piecewise_construct`를 사용할땐 `forward_as_tuple()`을 사용하는게 좋습니다.



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



