---
layout: single
title: "#23. [모던 C++] (C++17~) 구조화된 바인딩"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/)을 이용하여 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/), [pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/), [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/), 클래스등의 하위 요소나 멤버 변수에 쉽게 접근할 수 있습니다.

# 개요

기존의 [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)등은 내부 요소에 접근하는게 좀 까다로웠는데요(*[tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/) 참고*), 

C++17 부터는 [구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/)을 이용하여 개체의 하위 요소에 쉽게 접근할 수 있게 해줍니다.

|항목|내용|
|--|--|
|`auto [변수 목록]`|해당 개체의 복제본을 만들어 내부 요소를 참조합니다. 수정시 복제본이 수정됩니다.|
|`auto& [변수 목록]`|해당 개체의 내부 요소에 참조합니다. 내부 요소 수정이 가능합니다.|
|`const auto& [변수 목록]`|해당 개체의 내부 요소에 참조합니다. 내부 요소 수정이 불가능합니다.|
|`auto&& [변수 목록]`|해당 개체의 내부 요소를 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)합니다.|

다음과 같이 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/), [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/), 클래스등에 대해 적용할 수 있습니다. 

**배열**

```cpp
int arr[]{1, 2, 3};

auto [a_17, b_17, c_17]{arr}; // auto [a_17, b_17, c_17] = arr; 과 동일
EXPECT_TRUE(a_17 == 1 && b_17 == 2 && c_17 == 3); // arr을 임시 개체에 복제하고, 복제본에 a_17 = &temp[0], b_17 = &temp[1], c_17 = &temp[2] 바인딩

auto& [x_17, y_17, z_17]{arr};
EXPECT_TRUE(x_17 == 1 && y_17 == 2 && z_17 == 3); // x_17 = &a[0], y_17 = &a[1], z_17 = &a[2] 에 바인딩

arr[0] = 10; // 원본 수정
arr[1] = 20;
arr[2] = 30;

EXPECT_TRUE(a_17 == 1 && b_17 == 2 && c_17 == 3); // 복제본이므로 원본 내용이 반영되지 않습니다.
EXPECT_TRUE(x_17 == 10 && y_17 == 20 && z_17 == 30); // 참조본이므로 원본 수정시 값이 동기화 됩니다.
```

**tuple**

```cpp
auto data_11{std::make_tuple(10, "John")};

auto [id_17, name_17]{data_11}; // 복제본에 바인딩
EXPECT_TRUE(id_17 == 10 && name_17 == "John");

auto& [idRef_17, nameRef_17]{data_11}; // 원본에 바인딩
EXPECT_TRUE(idRef_17 == 10 && nameRef_17 == "John");

std::get<0>(data_11) = 20;

EXPECT_TRUE(id_17 == 10 && name_17 == "John");
EXPECT_TRUE(idRef_17 == 20 && nameRef_17 == "John");
```

**클래스**

```cpp
class T {
public:
    int m_Id_11{10}; // C++11의 멤버 선언부 초기화
    std::string m_Name_11{"John"};    
};
T data_11;

auto [id_17, name_17]{data_11}; // 복제본에 바인딩
EXPECT_TRUE(id_17 == 10 && name_17 == "John");

auto& [idRef_17, nameRef_17]{data_11}; // 원본에 바인딩
EXPECT_TRUE(idRef_17 == 10 && nameRef_17 == "John");

data_11.m_Id_11 = 20;

EXPECT_TRUE(id_17 == 10 && name_17 == "John");
EXPECT_TRUE(idRef_17 == 20 && nameRef_17 == "John");
```

# 컨테이너 활용

[구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/)을 컨터이너와 함께 사용하면 코드가 간결해 집니다.

**고전 C++ 스타일**

`insert()` 로 데이터를 추가하고, `iterator`를 이용하며, [pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)로 삽입 실패를 검사합니다.

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

[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94), 
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

[초기식을 포함하는 if()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch)와 [구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/)으로 단순화 되었고, 좀더 직관적입니다.

```cpp
// 초기 데이터 입력 - 중괄호 초기화로 단순화 됐습니다.
std::map<int, std::string> m_11{
    {0, "data0"}, 
    {1, "data1"}
}; 

// 요소 이터레이팅 - 범위 기반 for와 구조화된 바인딩으로 단순화 됐습니다.
for (const auto& [key_17, value_17]: m_11) {
    std::cout << key_17 << value_17 << std::endl; 
}

// 삽입 실패 검사 -  초기식을 포함하는 if()와 구조화된 바인딩으로 단순화 됐습니다.
if (auto [itr_17, result_17]{m_11.insert(std::make_pair(0, "data0"))}; result_17 != true) {
    std::cout << "insert fail" << std::endl;
} 
```
