---
layout: single
title: "#1. [고전 C++ 가이드] 함수자(Functor)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---



```cpp
class Add {
public: 
    int m_Val;
    explicit Add(int i) : m_Val(i) {} 
    void operator ()(int val) { // 호출시마다 m_Val에 누적
        m_Val += val; 
    }
};
class Sum {
public:
    static int Run(const int* p, int count) { 
        Add sum(0); 
        for (int i = 0; i < count; i++) { 
            sum(p[i]); // operator()(int add) 호출
        }
        return sum.m_Val;
    }
};

int arr[] = {1, 2, 3, 4};

EXPECT_TRUE(Sum::Run(arr, 4) == 10); // 1, 2, 3, 4의 합
```
