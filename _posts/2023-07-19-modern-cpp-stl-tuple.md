---
layout: single
title: "#19. [모던 C++ STL] tuple(C++11)"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)이 추가되어 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 좀 더 간편하게 만들 수 있습니다.
> * (C++11~) [make_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#make_tuple), [tie()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tie), [forward_as_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#forward_as_tuple)이 추가되어 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 쉽게 생성할 수 있습니다.
> * (C++11~) [tuple_cat()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_cat)이 추가되어 두개의 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 합칠 수 있습니다.
> * (C++11~) [tuple_size](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_size)가 추가되어 컴파일 타임에 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)요소 갯수를 구할 수 있습니다.
> * (C++11~) [tuple_element](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_element)가 추가되어 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 각 요소에 대한 타입을 구할 수 있습니다.
> * (C++11~) [piecewise_construct](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#piecewise_construct)가 추가되어 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)의 개체 생성시 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)의 요소들로 개체 생성자를 호출할 수 있습니다.
> * (C++14~) [타입 기반 get()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c14-%ED%83%80%EC%9E%85-%EA%B8%B0%EB%B0%98-get)이 추가되어 타입들이 서로 다르다면 타입으로 데이터에 접근할 수 있습니다.
> * (C++17~) [apply()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-apply)가 추가되어 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 형식을 전달받아 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 호출할 수 있습니다.
> * (C++17~) [make_from_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-make_from_tuple)이 추가되어 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 형식을 전달받아 개체를 생성할 수 있습니다.

# 개요

기존에는 데이터를 주고받는 가벼운 개체를 만들기 위해 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)를 사용했는데요, [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)는 `first`와 `second`로 두개의 요소만 관리할 수 있다는 아쉬움이 있었습니다.

C++11 부터 STL 에서는 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)이 제공되어 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만들 수 있습니다.

|항목|내용|
|--|--|
|[pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)|`first`와 `second`로 2개의 요소를 관리합니다.|
|[make_pair()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)|[pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)를 생성합니다.|
[piecewise_construct](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#piecewise_construct) (C++11~)|[pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)의 개체 생성시 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)의 요소들로 개체 생성자를 호출합니다.|
|[tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple) (C++11~)|다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.|
|`get()` (C++11~)|[tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)에서 주어진 인덱스 위치에 있는 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다.<br/>(C++14~) 타입으로 찾을 수 있도록 보강되었습니다.|
|[make_tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#make_tuple) (C++11~)|[tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 생성합니다.|
|[tie()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tie) (C++11~)|[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 구성된 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 만듭니다. |
|[forward_as_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#forward_as_tuple) (C++11~)|함수 인수 전달에 적합하도록, [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 생성시 좌측값이나 [우측값 참조](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)가 변형되지 않도록 합니다.|
|[tuple_cat()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_cat) (C++11~)|두개의 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 합칩니다.|
|[tuple_size](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_size) (C++11~)|컴파일 타임에 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)의 요소 갯수를 구합니다.|
|[tuple_element](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_element) (C++11~)|[tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)의 각 요소에 대한 타입을 제공합니다.|
|`ignore()` (C++11~)|(작성중)|
|`interger_sequence` (C++14~)|(작성중)|
|[apply()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-apply) (C++17~)|[tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 형식을 전달받아 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 호출합니다.|
|[make_from_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-make_from_tuple) (C++17~)|[tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 형식을 전달받아 개체를 생성합니다.|
|`basic_common_reference` (C++23~)|(작성중)|
|`common_type` (C++23~)|(작성중)|

# tuple

다수의 요소를 관리할 수 있는 데이터 전달용 개체를 만듭니다.

1. [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 정의시 사용할 타입을 나열하고,
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

> *(C++17~) [구조화된 바인딩](https://tango1202.github.io/cpp/modern-cpp-structured-binding/)이 추가되어 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/), [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/), [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/), 클래스등의 내부 요소나 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)에 쉽게 접근할 수 있습니다.*

# make_tuple()

`make_pair`처럼([pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/) 참고) [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 생성합니다.

```cpp
std::tuple<int, std::string, char> Func() {
    return std::make_tuple(10, "Name", 'a');
}
```

# tie()

[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 구성된 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 만듭니다. [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)에서 `get()`함수를 이용해서 데이터를 확인하는데, 참 직관적이지 못합니다. 이럴때 다음처럼 [tie()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tie) 함수를 이용하여 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 구성된 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 만들면 좀더 직관적으로 데이터를 확인할 수 있습니다.

```cpp
std::tuple<int, std::string, char> Func() {
    return std::make_tuple(10, "Name", 'a');
}

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

함수 인수 전달에 적합하도록, [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 생성시 좌측값이나 [우측값 참조](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)가 변형되지 않도록 합니다. [forward()](https://tango1202.github.io/cpp/modern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수 처럼요.

# tuple_cat()

두개의 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)을 합칩니다.

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

컴파일 타임에 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)의 요소 갯수를 구합니다.

```cpp
static_assert(std::tuple_size<std::tuple<int, std::string, char>>::value == 3);
```

# tuple_element

[tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)의 각 요소에 대한 타입을 제공합니다.

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

[piecewise_construct](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#piecewise_construct)는 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)의 생성자의 오버라이드 버전을 호출하기 위한 더미(*Dummy*) 개체 인데요, [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)의 `first`와 `second` 개체를 생성할때, 전달된 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 개체의 요소들로 초기화 해줍니다.

보통 `A`와 `B` 개체를 관리하는 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)를 만든다면, 

```cpp
std::pair<A, B> data{
    A{10, "Name", 'a'}, // A 개체 생성
    B{1, 2, 3, 4} // B 개체 생성
};
```

와 같이 만들텐데요, 

[piecewise_construct](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#piecewise_construct)를 사용한 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전을 실행하면, `A` 개체를 생성할때 사용할 인수들과 `B`개체를 생성할때 사용할 인수들을 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)로 전달해서, [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/) 내부에서 `A`, `B`를 생성하도록 할 수 있습니다.

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

상기 예에서는 인수들의 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)이 변하지 않도록 [forward_as_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#forward_as_tuple)을 사용했는데요, [make_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#make_tuple)을 사용해도 결과는 같습니다.

하지만, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달된 개체를 사용할 때에는 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)이 변할 수도 있으므로, [piecewise_construct](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#piecewise_construct)를 사용할땐 [forward_as_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#forward_as_tuple)을 사용하는게 좋습니다.

# (C++14~) 타입 기반 get()

C++11 에서는 `get()` 함수가 인덱스 기반으로 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 요소에 접근할 수 있었으나, C++14 부터는 타입들이 서로 다르다면 타입으로 데이터에 접근할 수 있습니다. 

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

# (C++17~) apply()

[apply()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-apply)는 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 형식을([array](https://tango1202.github.io/cpp-stl/modern-cpp-stl-array/)나 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)도 가능합니다. `get()`과 [tuple_size](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_size)와 호환되면 됩니다.) 전달받아 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 호출하는 유틸리티 함수 입니다.

기존에는 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/)의 데이터를 함수에 전달할때 요소의 전개가 필요했습니다.

```cpp
int Sum(int a, int b, int c) {
    return a + b + c;
}

std::tuple<int, int, int> data{1, 2, 3};

// tuple의 각 요소를 전개해야 합니다.
EXPECT_TRUE(Sum(std::get<0>(data), std::get<1>(data), std::get<2>(data)) == 1 + 2 + 3);
```

[apply()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-apply)를 이용하면, 요소의 전개를 은닉할 수 있습니다.

```cpp
int Sum(int a, int b, int c) {
    return a + b + c;
}

std::tuple<int, int, int> data{1, 2, 3};

// tuple의 요소를 전개하지 않아도 됩니다.
EXPECT_TRUE(std::apply(Sum, data) == 1 + 2 + 3);
```

# (C++17~) make_from_tuple

[make_from_tuple()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-make_from_tuple)은 [tuple](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/) 형식을([array](https://tango1202.github.io/cpp-stl/modern-cpp-stl-array/)나 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)도 가능합니다. `get()`과 [tuple_size](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#tuple_size)와 호환되면 됩니다.) 전달받아 개체를 생성하는 유틸리티 함수 입니다. 이를 이용하면, [apply()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-tuple/#c17-apply)처럼 요소의 전개를 은닉할 수 있습니다.

```cpp
class T {
public:
    T(int a, int b, int c) {}
};

std::tuple<int, int, int> data{1, 2, 3};
// tuple의 요소를 전개하지 않아도 됩니다.
T t{std::make_from_tuple<T>(std::move(data))};
```



