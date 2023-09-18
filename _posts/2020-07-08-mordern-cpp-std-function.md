---
layout: single
title: "#8. [모던 C++ STL] (C++11~) function, bad_function_call"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

>

# 개요

기존에는 함수처럼 호출하는 개체를 만들기 위해 함수자를 이용했는데요([함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 참고),

```cpp
// 7보다 작은지 검사하는 함수
class Less_7 : std::unary_function<int, bool> {
public:
    result_type operator ()(argument_type val) {
        return val < 7 ? true : false;
    }       
};

std::vector<int> v{7, 4, 3};
auto itr = std::find_if(v.begin(), v.end(), Less_7{});
EXPECT_TRUE(*itr == 4);  
```

`unary_function`
C++11 부터는 람다 표현식을 사용하여 좀더 간단하게 만들 수 있습니다.

```cpp
std::vector<int> v{7, 4, 3};
std::find_if(
    v.begin(), 
    v.end(), 
    [](int val) -> int {return val < 7 ? true : false;}
);
```

C++11 부터는 범위 기반 `for()`를 이용하여 좀더 간단하게 구현할 수 있습니다.

3. `function`(캡쳐도 지원하고, 함수 인자로 사용할 수도 있습니다.)
    
    ```cpp
    std::function<int(int, int)> f;
    int g(const std::function<int(int, int)>& lambda, int a, int b) { // 함수의 인자로 람다를 지정합니다.
        return lambda(a, b);
    }
    int c{30};
    f = [=](int a, int b) -> int {return a + b + c;}; // 캡쳐를 사용하는 람다 표현식도 사용할 수 있습니다.      
    EXPECT_TRUE(g(f, 10, 20) == 60); // g() 함수에 클로져 개체를 저장한 f를 전달합니다. 
    ```