---
layout: single
title: "#35. [모던 C++ STL] variant(C++17)"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)가 추가되어 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다.

# 개요

C++11에서 추가된 [무제한 공용체](https://tango1202.github.io/cpp/modern-cpp-unrestricted-union/)는 많은 제한이 풀려 타입이 다른 데이터를 [공용체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4)로 다룰 수 있는 자유도가 높아졌으나, 위치 지정 생성이나 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)를 수동으로 처리해야 하기 때문에 많은 불편함이 있었습니다.

C++17 부터는 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)가 추가되어 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다. [any](https://tango1202.github.io/cpp-stl/modern-cpp-stl-any/)의 경우는 런타임에 동적으로 캐스팅하여 관리하는 타입을 유연하게 변경할 수 있지만, [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)는 컴파일 타임에 지정한 타입들중 하나로만 사용할 수 있습니다.

|항목|내용|
|--|--|
|`=` (C++17~)|[복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 또는 [이동 대입](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.|
|`index()` (C++17~)|현재 관리되는 타입의 인덱스를 리턴합니다.|
|[valueless_by_exception()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/#valueless_by_exception) (C++17~)|[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)가 유효한지 검사합니다.|
|`emplace()` (C++17~)|내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)내에서 내부 개체를 직접 생성합니다.|
|`swap()` (C++17~)|바꿔치기 합니다.|
|`==`, `!=` (~C++17)|관리하는 개체를 비교합니다.|
|`<, <=, >, >=` (C++17~)<br/>`<=>` (C++20~)|관리하는 개체를 비교합니다.|
|[visit()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/#visit) (C++17~)|[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)가 관리하는 값으로 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 호출합니다.|
|`holds_alternative()` (C++17~)|주어진 타입이 관리되고 있는지 검사합니다.|
|`get()` (C++17~)|주어진 인덱스나 타입의 값을 리턴합니다. 값이 없으면, `bad_variant_access` [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.|
|`get_if()` (C++17~)|주어진 타입의 값을 리턴합니다. 값이 없으면, [nullptr](https://tango1202.github.io/cpp/modern-cpp-type/#nullptr)을 리턴합니다.|
|[monostate](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/#monostate) (C++17~)|초기값 없이 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)를 생성합니다.|
|`bad_variant_access` (C++17~)|[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)에서 주어진 타입이 없을때 `bad_variant_access` [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.|
|`variant_size, variant_size_t` (C++17~)|(작성중)|
|`variant_alternative, variant_alternative_t` (C++17~)|(작성중)|
|`variant_npos` (C++17~)|[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/) 인덱스가 잘못된 경우를 나타냅니다.`-1`입니다.| 

다음 예에서는 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)를 이용하여 `int` 또는 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)으로 개체를 관리합니다.

1. 기본 생성시에는 0번째 타입의 기본 생성값으로 초기화 합니다.
2. `holds_alternative()`으로 주어진 타입이 관리되고 있는지 검사합니다.
3. `index()` 로 현재 관리되는 타입의 인덱스를 알 수 있습니다.
4. `get<인덱스>()` 와 `get<타입>()`으로 값에 접근할 수 있습니다. 만약 해당 값이 없으면 `bad_variant_access` [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.
5. `get_if()`는 값이 없으면 [nullptr](https://tango1202.github.io/cpp/modern-cpp-type/#nullptr)을 리턴합니다.

```cpp
// 기본 생성하면 0번째 타입의 기본 생성값으로 초기화 합니다.
std::variant<int, std::string> var{};
EXPECT_TRUE(std::holds_alternative<int>(var) == true);
EXPECT_TRUE(var.index() == 0 && std::get<0>(var) == 0 && std::get<int>(var) == 0);

// 정수 값을 입력하여 인덱스는 0입니다.
var = 1;
EXPECT_TRUE(std::holds_alternative<int>(var) == true);
EXPECT_TRUE(var.index() == 0 && std::get<0>(var) == 1 && std::get<int>(var) == 1);

// 문자열을 입력하여 인덱스는 1입니다.
std::string str{"Hello"};
var = str;
EXPECT_TRUE(std::holds_alternative<std::string>(var) == true);
EXPECT_TRUE(var.index() == 1 && std::get<1>(var) == "Hello" && std::get<std::string>(var) == "Hello");

// 값이 없으면 예외를 발생합니다.
try {
    var = 1;
    std::get<std::string>(var);
}
catch (std::bad_variant_access&) {
    std::cout << "bad_variant_access" << std::endl;
}

// 값이 없으면 널을 리턴합니다.
EXPECT_TRUE(std::get_if<std::string>(&var) == nullptr);    
```

# variant의 타입 제약

[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)는 동일한 타입을 여러개 사용할 수 없습니다.

```cpp
std::variant<int, int> var{};
var = 1; // (X) 컴파일 오류. 타입이 동일하면 사용할 수 없습니다.
```

# valueless_by_exception()

[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)는 생성시에 `0`번째 타입의 개체의 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)로 초기화 해주고, 별도로 초기화 하는 함수도 없기 때문에, 일반적인 상황에선 항상 유효한 개체를 갖게 되는데요,

생성자나 대입 과정에서 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)가 유효하지 않은 상태가 됩니다. `valueless_by_exception()`는 이런 경우 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)의 유효성을 검사합니다.

```cpp
class T {
public:
    T() {}
    T(const T&) {throw std::exception();}
};

std::variant<int, T> var{10}; 
EXPECT_TRUE(var.valueless_by_exception() == false);

try {
    var = T{}; // 내부적으로 복사 생성되면서 예외를 발생합니다.
}
catch (std::exception&) {}

EXPECT_TRUE(var.valueless_by_exception() == true); // 유효하지 않습니다.
```

# monostate

[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)는 기본 생성시에 0번째 타입으로 초기화 합니다. 만약 아무값도 없는 상태로 생성하려면 특별히 [monostate](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/#monostate)로 생성합니다.

유효한 값은 아니지만 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한 것은 아니므로 `valueless_by_exception`은 `false` 입니다.

```cpp
std::variant<std::monostate, int, std::string> var{};
EXPECT_TRUE(var.valueless_by_exception() == false); 
```

# visit()

[variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)가 관리하는 값으로 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 호출합니다.

예를들어 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)를 저장한 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 각 요소에 `Func()`을 호출하고 싶다면 다음과 같이 `holds_alternative()`로 [variant](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/)가 주어진 타입의 값을 저장했는지 확인 후 `Func()`에 해당 값을 전달해야 합니다.

```cpp
std::vector<std::variant<int, std::string>> v{
    10, // v[0]
    20, // v[1]
    "hello" // v[2]
};

// 각 타입에 따라 검사한후 타입에 맞게 get해야 합니다.
for (auto& var : v) {
    if (std::holds_alternative<int>(var)) {
        Func(std::get<int>(var));            
    }
    if (std::holds_alternative<std::string>(var)) {
        Func(std::get<std::string>(var));            
    }
}   
```

[visit()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-variant/#visit)를 이용하면 상기 과정을 은닉하여 다음과 같이 호출할 수 있습니다.

```cpp
std::vector<std::variant<int, std::string>> v{
    10, // v[0]
    20, // v[1]
    "hello" // v[2]
};

// 타입에 따라 검사하는 코드를 visit() 함수 안에 은닉합니다.
for (auto& element : v) {
    std::visit([](auto&& val){Func(val);}, element);
}  
```
