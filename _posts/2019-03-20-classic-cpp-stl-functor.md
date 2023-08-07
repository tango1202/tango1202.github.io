---
layout: single
title: "#1. [고전 C++ STL] 함수자(Functor)"
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


# 템플릿 인자

템플릿 정의시에 작성한 인자(Parameter) 집합은 템플릿 인스턴스화 시 전달된 인수(Argument) 집합으로 대체되어 생성됩니다.

```cpp
// 템플릿 정의. T, U는 인자 집합입니다.
template<typename T, typename U>
class A {};

// 템플릿 인스턴스화. int, char는 인수 집합입니다.
A<int, char> a;
```

템플릿 인자는 다음으로 구성됩니다.

1. 타입
    ```cpp
    template<typename T>
    ```

   
3. 템플릿 개체
4. 비 템플릿 개체

