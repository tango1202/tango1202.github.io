---
layout: single
title: "#35. [모던 C++ STL] (C++17~) any"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/)가 추가되어 타입의 변동 가능성이 있는 데이터를 비교적 안전하게 사용할 수 있습니다.

# 개요

기존에는 타입의 변동 가능성이 있는 데이터는 `void*`로 저장한뒤 이를 사용할때 실제 사용할 타입으로 변환하기 위해 [reinterpret_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)를 사용했었습니다. 

```cpp
void* data{new int}; // int 타입을 사용합니다.
*reinterpret_cast<int*>(data) = 10;
delete reinterpret_cast<int*>(data);

class T {};
data = new T; // T 타입을 사용합니다.
delete reinterpret_cast<T*>(data);  
```

타입 안전 측면에서는 권장하지 않는데요,

C++17 부터는 [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/)가 추가되어 타입의 변동 가능성이 있는 데이터를 비교적 안전하게 사용할 수 있습니다.

|항목|내용|
|--|--|
|`=` (C++17~)|[복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 또는 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.|
|`emplace()` (C++17~)|내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 `any`내에서 내부 개체를 직접 생성합니다.|
|`reset()` (C++17~)|내부 개체를 초기화합니다.|
|`swap()` (C++17~)|바꿔치기 합니다.|
|`has_value()` (C++17~)|내부 개체가 있는지 검사합니다.|
|`type()` (C++17~)|(작성중)|
|`any_cast()` (C++17~)|내부 개체 타입으로 변경하여 내부 개체를 접근합니다. 타입이 다르면, `bad_any_cast` 예외를 발생합니다.|
|`make_any()` (C++17~)|(작성중)|
|`bad_any_cast` (C++17~)|[any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/) 사용시 타입이 일치하지 않을때 예외를 발생합니다.|

다음 예에서는 [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/) 개체로 정수 타입과 `T`타입을 번갈아 가며 사용합니다. [reinterpret_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 대신 `any_cast()`를 사용하며, [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 하지 않아도 됩니다. 

```cpp
std::any data{10}; // 정수 타입을 사용합니다.
EXPECT_TRUE(std::any_cast<int>(data) == 10); // 데이터를 사용할때 any_cast를 이용합니다.

class T {
public:
    int m_Val{1};
};

data = T{}; // T 타입을 사용합니다.
EXPECT_TRUE(std::any_cast<T>(data).m_Val == 1); 

data.reset(); // 데이터를 초기화합니다.

EXPECT_TRUE(data.has_value() == false);
```