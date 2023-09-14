---
layout: single
title: "#12. [모던 C++] (C++11~) static_assert"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `static_assert()`가 추가되어 컴파일 타임 진단이 가능해 졌습니다.

# 개요

[자가 진단과 진단 최소화](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/)에서 언급한 `assert()`를 이용하면 런타임 진단이 가능한데요, C++11부터는 `static_assert()`를 이용하여 컴파일 타임 진단이 가능합니다.

런타임 진단은 해당 코드가 실행되어야 진단이 가능하여 커버리지가 낮을 수 있지만, 컴파일 타임 진단은 컴파일 과정에서 이루어 지므로 100% 진단이 이루어집니다. 

특히 컴파일 타임 프로그래밍이나  [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 시에 인자의 타입을 코딩 계약으로 표현하기 힘든 경우,  [type_traits](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-traits/)를 사용하여 진단할때 유용합니다.([type_traits](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-traits/) 참고)

```cpp

// T는 포인터 타입만 전달되어야 합니다.
template<typename T>
void Func(T t) { 
    static_assert(std::is_pointer<T>::value, "only pointer"); // is_pointer는 C++11에 추가된 type_traits
}

int a = 20;
Func(&a);
Func(a); // (X) 컴파일 오류. error: static assertion failed: only pointer
```

