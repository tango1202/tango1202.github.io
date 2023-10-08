---
layout: single
title: "#29. [모던 C++] (C++17~) 구조화된 바인딩"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 구조화된 바인딩을 이용하여 배열, `pair`, `tuple`, 클래스등의 하위 요소나 멤버 변수에 쉽게 접근할 수 있습니다.

# 개요

기존의 `tuple`등은 내부 요소에 접근하는게 좀 까다로웠는데요, C++11 부터는 구조화된 바인딩을 이용하여 개체의 하위 요소에 쉽게 접근할 수 있게 해줍니다.

|항목|내용|
|--|--|
|`auto [변수 목록]`|해당 개체의 복제본을 만들어 내부 요소를 참조합니다. 수정시 복제본이 수정됩니다.|
|`auto& [변수 목록]`|해당 개체의 내부 요소에 참조합니다. 내부 요소 수정이 가능합니다.|
|`const auto& [변수 목록]`|해당 개체의 내부 요소에 참조합니다. 내부 요소 수정이 불가능합니다.|
|`auto&& [변수 목록]`|해당 개체의 내부 요소에 우측값 참조합니다.|

다음과 같이 배열, `tuple`, 클래스등에 대해 적용할 수 있습니다. 

**배열**

```cpp
int arr[]{1, 2, 3};

auto [a, b, c]{arr}; // auto [a, b, c] = arr;
EXPECT_TRUE(a == 1 && b == 2 && c == 3); // arr을 임시 개체에 복제하고, 복제본에 a = &temp[0], b = &temp[1], c = &temp[2] 바인딩

auto& [x, y, z]{arr};
EXPECT_TRUE(x == 1 && y == 2 && z == 3); // x = &a[0], y = &a[1], z = &a[2] 에 바인딩

arr[0] = 10; // 원본 수정
arr[1] = 20;
arr[2] = 30;

EXPECT_TRUE(a == 1 && b == 2 && c == 3); // 복제본이므로 원본 내용이 반영되지 않습니다.
EXPECT_TRUE(x == 10 && y == 20 && z == 30); // 참조본이므로 원본 수정시 값이 동기화 됩니다.
```

**tuple**

```cpp
auto data{std::make_tuple(10, "John")};

auto [id, name]{data};
EXPECT_TRUE(id == 10 && name == "John");

auto& [idRef, nameRef]{data};
EXPECT_TRUE(idRef == 10 && nameRef == "John");

std::get<0>(data) = 20;

EXPECT_TRUE(id == 10 && name == "John");
EXPECT_TRUE(idRef == 20 && nameRef == "John");
```

**클래스**

```cpp
class T {
public:
    int m_Id{10};
    std::string m_Name{"John"};    
};
T data;

auto [id, name]{data};
EXPECT_TRUE(id == 10 && name == "John");

auto& [idRef, nameRef]{data};
EXPECT_TRUE(idRef == 10 && nameRef == "John");

data.m_Id = 20;

EXPECT_TRUE(id == 10 && name == "John");
EXPECT_TRUE(idRef == 20 && nameRef == "John");
```

# 컨테이너 활용

**고전 C++ 스타일**

```cpp
// 초기 데이터 입력
std::map<int, std::string> m;
m.insert(std::make_pair(0, "data0"));
m.insert(std::make_pair(1, "data1"));

// 요소 이터레이팅
std::map<int, std::string>::iterator itr = m.begin();
std::map<int, std::string>::iterator endItr = m.end();
for (; itr != endItr; ++itr) {
    std::cout << (*itr).first << (*itr).second << std::endl; // pair여서 first, second로 접근
}

// 삽입 실패 검사
std::pair<std::map<int, std::string>::iterator, bool> result = m.insert(std::make_pair(0, "data0"));
if (result.second != true) {
    std::cout << "insert fail" << std::endl;
} 
```

**C++11 스타일**

[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/), 
[범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 로 단순화 됐습니다.
```cpp
// 초기 데이터 입력 - 중괄호 초기화로 단순화 됐습니다.
std::map<int, std::string> m_11{
    {0, "data0"}, 
    {1, "data1"}
}; 

// 요소 이터레이팅 - 범위 기반 for로 단순화 됐습니다.
for (const auto& item : m_11) {
    std::cout << item.first << item.second << std::endl; // pair여서 first, second로 접근
}

// 삽입 실패 검사 - auto 사용으로 단순화 됐습니다.
auto result{m_11.insert(std::make_pair(0, "data0"))};
if (result.second != true) {
    std::cout << "insert fail" << std::endl;
} 
```

**C++17 스타일**

[초기식을 포함하는 if()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch) 와 구조화된 바인딩으로 단순화 되었고, 좀더 직관적입니다.

```cpp
// 초기 데이터 입력 - 중괄호 초기화로 단순화됐습니다.
std::map<int, std::string> m_17{
    {0, "data0"}, 
    {1, "data1"}
}; 

// 요소 이터레이팅 - 범위 기반 for와 구조화된 바인딩으로 단순화됐습니다.
for (const auto& [key, value]: m_17) {
    std::cout << key << value << std::endl; 
}

// 삽입 실패 검사 -  초기식을 포함하는 if()와 구조화된 바인딩으로 단순화됐습니다.
if (auto [itr, result]{m_17.insert(std::make_pair(0, "data0"))}; result != true) {
    std::cout << "insert fail" << std::endl;
} 
```
