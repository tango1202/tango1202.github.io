---
layout: single
title: "#35. [모던 C++ STL] (C++17~) variant"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/)이 추가되어 값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 리턴값 성공 여부 처리를 좀더 단순하게 할 수 있습니다.

# 개요

기존 [무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/)를 통해 많은 제한이 풀렸으나, 위치 지정 생성이나 소멸자를 수동으로 처리해야 하기 때문에 많은 불편함이 있었습니다.

C++17 부터는 `variant`를 제공하여 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있게 합니다. `any`의 경우는 런타임에 동적으로 타입 캐스팅을 이용하여 관리하는 타입을 유연하게 변경할 수 있지만, `variant`는 컴파일 타입에 지정한 타입들중 하나로만 타입을 사용할 수 있습니다.

|항목|내용|
|--|--|
|`=` (C++17~)|복사 대입 또는 이동 대입합니다.|
|`index()` (C++17~)|현재 관리되는 타입의 인덱스를 리턴합니다.|
|`valueless_by_exception()` (C++17~)|`variant`가 유효한지 검사합니다.|
|`emplace()` (C++17~)|내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 `variant`내에서 내부 개체를 직접 생성합니다.|
|`swap()` (C++17~)|바꿔치기 합니다.|
|`==`, `!=` (~C++17)|관리하는 개체를 비교합니다.|
|`<, <=, >, >=` (C++17~)<br/>`<=>` (C++20~)|관리하는 개체를 비교합니다.|
|`visit()` (C++17~)|(작성중)|
|`holds_alternative()` (C++17~)|주어진 타입이 관리되고 있는지 검사합니다.|
|`get()` (C++17~)|주어진 인덱스나 타입의 값을 리턴합니다. 값이 없으면, `bad_variant_access` 예외를 발생합니다.|
|`get_if()` (C++17~)|주어진 타입의 값을 리턴합니다. 값이 없으면, `nullptr` 예외를 발생합니다.|
|`monostate()` (C++17~)|(작성중)|
|`bad_variant_access` (C++17~)|[variant](??)에서 주어진 타입이 없을때 예외를 발생합니다.|
|`variant_size`, `variant_size_t` (C++17~)|(작성중)|
|`variant_alternative`, `variant_alternative_t` (C++17~)|(작성중)|
|`variant_npos` (C++17~)|`variant` 인덱스가 잘못된 경우를 나타냅니다.`-1`입니다.| 

다음 예에서는 `any` 개체로 정수 타입과 `T`타입을 번갈아 가며 사용합니다. `reinterpret_cast` 대신 `any_cast()`를 사용하며, `delete`를 하지 않아도 됩니다. 

```cpp
std::optional<std::string> option{"Kim"};

// 값을 변경합니다.
option = "Lee";

// 값에 접근합니다.
EXPECT_TRUE(option.value() == "Lee");

// 빈값으로 초기화 합니다.
option.reset();

// 값이 있는지 검사합니다.
EXPECT_TRUE(option.has_value() == false);

// = {}으로 빈 값을 대입할 수 있습니다.
option = "Lee";
option = {}; 
EXPECT_TRUE(option.has_value() == false);

// 값이 없으면 예외를 발생합니다.
try {
    option.value();        
}
catch (std::bad_optional_access&) {
    std::cout << "bad_optional_access" << std::endl;
}

// 값이 없으면 사용할 기본값을 전달합니다.
EXPECT_TRUE(option.value_or("empty") == "empty");
```

# valueless_by_exception()

`variant`는 생성하면서 개체를 대입받지 못하면, `0`번째 타입의 개체의 기본 생성자로 초기화 해줍니다. 

```cpp
```

별도로 초기화 하는 함수도 없기 때문에, 일반적인 상황에선 항상 유효한 개체를 갖게 되는데요,

생성자나 대입과정에서 예외가 발생하면 공교롭게도 `variant`가 유효하지 않은 상태가 발생합니다. `valueless_by_exception()`는 이런 경우 `variant`의 유효성을 검사합니다.

```cpp
```

# monostate

# visit()


