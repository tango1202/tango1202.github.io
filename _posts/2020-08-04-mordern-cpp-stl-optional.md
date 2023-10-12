---
layout: single
title: "#34. [모던 C++ STL] (C++17~) optional"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/)이 추가되어 값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 리턴값 성공 여부 처리를 좀더 단순하게 할 수 있습니다.

# 개요

UI 의 체크박스를 보면, 

1. 사용자에 의해 체크한 상태 : 체크 상태로 표시합니다.
2. 사용자에 의해 체크를 해제한 상태 : 언체크 상태로 표시합니다.
3. 사용자가 아무런 선택도 안한 기본 상태(미확정 상태) : 미확정 상태로 표시합니다.

의 3가지 상태가 필요할 수 있습니다.

혹은 문서내의 여러 개체들을 선택하고 너비를 수집했을때

1. 너비가 모두 동일한 상태 : 너비를 표시합니다.
2. 너비가 서로 다른 상태(값을 표시하기 부적절한 상태) : 너비를 표시하지 않습니다.

일 수 있습니다.

이런 미확정 상태나 값을 표시하기 부적절한 상태 처리를 위해 `nullptr`을 활용할 수 있는데요,

```cpp
bool* option = nullptr;

*option = true; // check 상태
*option = false; // uncheck 상태
 
 delete option;
 option = nullptr; // 값이 없음. 미확정 상태, 값을 표시하기 부적절한 상태
```

아무래도 멀쩡한 값을 포인터로 처리하다 보니 사용이 불편하고, `delete`를 해야 하는 부담이 있었습니다.


또한 실패할 수 있는 리턴값 처리시에도 다음과 같이 성공시의 값과, 성공 여부를 리턴해야 하는 불편함도 있었습니다.

```cpp
std::map<int, std::string> m;

// first : 성공시에 사용할 값
// second : 성공 여부
std::pair<std::map<int, std::string>::iterator, bool> result = m.insert(std::make_pair(0, "data0"));
if (result.second != true) {
    std::cout << "insert fail" << std::endl;
} 
```

C++17 부터는 `optional`이 추가되어 값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 리턴값 성공 여부 처리를 좀더 단순하게 할 수 있습니다.

|항목|내용|
|--|--|
|`=` (C++17~)|복사 대입 또는 이동 대입합니다.|
|`emplace()` (C++17~)|내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 `any`내에서 내부 개체를 직접 생성합니다.|
|`->()`, `*` (C++17~)|값에 접근합니다.|
|`(bool)`, `has_value()` (C++17~)|값이 있는지 검사합니다.|
|`value()` (C++17~)|값을 리턴합니다. 값이 없으면, `bad_optional_access` 예외를 발생합니다.|
|`value_or(default)` (C++17~)|값이 있으면 값을 리턴하고, 없으면 인자로 전달한 `default`를 리턴합니다.|
|`emplace()` (C++17~)|내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 `optional`내에서 내부 개체를 직접 생성합니다.|
|`reset()` (C++17~)|내부 개체를 초기화합니다.|
|`swap()` (C++17~)|바꿔치기 합니다.|
|`==`, `!=` (~C++17)|관리하는 개체를 비교합니다.|
|`<, <=, >, >=` (C++17~)<br/>`<=>` (C++20~)|관리하는 개체를 비교합니다.|
|`make_optional()` (C++17~)|(작성중)|
|`nullopt_t` (C++17~)|`optional`이 초기화되지 않았을때의 타입입니다.|
|`bad_optional_access` (C++17~)|[optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/)에서 `value()` 사용시 값이 없을때 예외를 발생합니다.|
|`and_then()` (C++23~)|(작성중)|
|`transform()` (C++23~)|(작성중)|
|`or_else()` (C++23~)|(작성중)|


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