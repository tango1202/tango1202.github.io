---
layout: single
title: "#18. [Mordern C++] (C++11~) static_assert()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/)가 추가되어 컴파일 타임 진단이 가능해 졌습니다.
> * (C++17~) [static_assert()의 메시지 생략](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/#c17-static_assert%EC%9D%98-%EB%A9%94%EC%8B%9C%EC%A7%80-%EC%83%9D%EB%9E%B5)을 지원합니다.

# 개요

기존에는 [assert()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/)를 이용하여 런타임 진단만 가능했는데요(*[assert()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/) 참고*),

C++11 부터는 [static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/)를 이용하여 컴파일 타임 진단이 가능합니다.

런타임 진단은 해당 코드가 실행되어야 진단이 가능하여 커버리지가 낮을 수 있지만, 컴파일 타임 진단은 컴파일 과정에서 이루어 지므로 100% 진단이 이루어집니다. 

특히 컴파일 타임 프로그래밍이나  [템플릿 메타 프로그래밍](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-meta-programming/) 시에 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 타입을 코딩 계약으로 표현하기 힘든 경우,  [type_traits](https://tango1202.github.io/mordern-cpp/mordern-cpp-type_traits/)를 사용하여 진단할때 유용합니다.(*[타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp/mordern-cpp-type_-_traits/) 참고*)

```cpp
// T는 포인터 타입만 전달되어야 합니다.
template<typename T>
void Func_11(T t) { 
    static_assert(std::is_pointer<T>::value, "only pointer"); // is_pointer는 C++11에 추가된 type_traits
}

int a{20};
Func_11(&a); // (O) 포인터여서 문제없습니다.
Func_11(a); // (X) 컴파일 오류. error: static assertion failed: only pointer
```

# (C++17~) static_assert()의 메시지 생략

기존에는 [static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/) 사용시 항상 메시지를 작성해야 했으나, C++17 부터는 [static_assert()의 메시지를 생략](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/#c17-static_assert%EC%9D%98-%EB%A9%94%EC%8B%9C%EC%A7%80-%EC%83%9D%EB%9E%B5)해도 됩니다.

```cpp
template<typename T>
void Func_17(T t) { 
    static_assert(std::is_pointer<T>::value); 
}
int a{20};
Func_17(a); // (X) 컴파일 오류. error: static assertion failed
```
