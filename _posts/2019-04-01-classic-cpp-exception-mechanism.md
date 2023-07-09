---
layout: single
title: "#1. [고전 C++ 가이드] 예외 안정 메커니즘"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


스택풀기
https://en.cppreference.com/w/cpp/language/throw 참고

**`throw`**

예외를 전파시킵니다. 자세한 내용은 [예외 안정 메커니즘](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/) 을 참고 바랍니다.

```cpp
try {
    std::string("abc").substr(10); // (X) 예외 발생. std::length_error
}
catch (const std::exception& e) {
    std::cout << e.what() << '\n';
//  throw e; // (△) 비권장. std::exception 을 복제하여 리턴
    throw;   // (O) std::length_error 를 재전파
}
```