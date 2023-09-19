---
layout: single
title: "#8. [모던 C++ STL] (C++11~) function, bad_function_call"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `function`은 `()`로 호출 가능한 개체를 저장합니다.
> * [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)가 deprecate 되었습니다.

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

`unary_function`등의 [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 사용하는 방식이 좀 까다로웠습니다.

C++11 부터는 좀 더 간단한 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)을 사용할 수 있는데요,

```cpp
std::vector<int> v{7, 4, 3};
std::find_if(
    v.begin(), 
    v.end(), 
    [](int val) -> int {return val < 7 ? true : false;}
);
```

이에 따라 C++11 부터는 기존의 까다로웠던 [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 deprecate 하고,  `function` 개체를 추가하여 `()`로 호출 가능한 개체들을 다룰 수 있게 했습니다.

`function` 개체는 다음 개체들을 저장할 수 있습니다.

1. [함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/)
2. [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)
3. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)
4. 바인드 표현식

```cpp
std::function<int(int)> func{[](int val) -> int {return val < 7 ? true : false;}};

std::vector<int> v{7, 4, 3};
auto itr{std::find_if(
    v.begin(), 
    v.end(), 
    func) 
};
EXPECT_TRUE(*itr == 4); 
```

# bad_function_call

`function`에서 `()`을 호출할 대상이 없을 때 발생하는 예외입니다.

```cpp
std::function<void()> func{nullptr};

try {
    func();
}
catch (std::bad_function_call&) {
}
```

mem_fn
reference_wrapper

is_bind_expression  
is_placeholder
bind
ref
cref

