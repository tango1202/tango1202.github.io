---
layout: single
title: "#31. [모던 C++ STL] (C++14~) 연관 컨테이너의 이종 탐색"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++14~) 연관 컨테이너의 이종 탐색을 지원하여 **Key**와 다른 타입이더라도 탐색이 가능합니다. 

# 개요

기존에는 `map`과 `set`등의 연관 컨테이너에서 **Key**와 동일한 타입으로만 탐색이 가능했습니다.

다음 예에서는 `Lee`의 데이터를 탐색하고 싶어 `Data{"Lee", 10}`와 같이 **Key**와 동일한 `Data` 타입 개체를 만들어 `find()`함수를 호출합니다.

```cpp
class Data {
public:
    std::string m_Name;
    int m_Val;
    bool operator <(const Data& other) const {
        if (m_Val < other.m_Val) return true;
        if (other.m_Val < m_Val) return false;

        return m_Name < other.m_Name;
    }
};

std::set<Data> dataSet{
    Data{"Lee", 10},
    Data{"Park", 20}
};
auto result{
    dataSet.find(
        Data{"Lee", 10} // 같은 타입만 탐색이 가능하므로 "Lee"를 탐색하고 싶어도 Data 개체를 동일하게 만들어 탐색해야 합니다.
    )
}; 
EXPECT_TRUE((*result).m_Name == "Lee" && (*result).m_Val == 10);
```

C++14 부터는 연관 컨테이너의 이종 탐색을 지원하여  **Key**와 다른 타입이더라도 탐색이 가능합니다.

1. 이종 타입에 대해 비교할 수 있게 정적 비교 함수를 제공합니다.
2. 컨테이너 선언시 `find()` 에서 **Key** 타입외에 다른 것을 사용하려면 `std::less<>`를 사용합니다.
3. `find()` 함수에 탐색하고 싶은 이종 타입의 개체를 전달합니다.

```cpp
class Data {
public:
    std::string m_Name;
    int m_Val;
    bool operator <(const Data& other) const {
        if (m_Val < other.m_Val) return true;
        if (other.m_Val < m_Val) return false;

        return m_Name < other.m_Name;
    }
};
// 이종 타입에 대해 비교할 수 있게 정적 비교 함수를 제공합니다.
bool operator <(const Data& left, const char* right) {
    return left.m_Name < right;
}
bool operator <(const char* left, const Data& right) {
    return left < right.m_Name;
}

// find() 에서 키 타입외에 다른 것을 사용하려면 std::less<>를 사용합니다.
std::set<Data, std::less<>> dataSet{
    Data{"Lee", 10},
    Data{"Park", 20}
};
auto result{
    dataSet.find(
        "Lee" // find() 함수에 탐색하고 싶은 이종 타입의 개체를 전달합니다.
    )
}; 

EXPECT_TRUE((*result).m_Name == "Lee" && (*result).m_Val == 10);
```
