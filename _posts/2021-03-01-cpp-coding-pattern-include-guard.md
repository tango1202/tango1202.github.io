---
layout: single
title: "#1. [C++ 코딩 패턴] 인클루드 가드"
categories: "cpp-coding-pattern"
tag: ["C++ 코딩 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---


헤더 파일들이 서로 서로 `include`하다 보면, 동일한 헤더 파일이 다시 포함되는 경우가 생깁니다. 

```cpp
// Test_IncludeGuard1.h
namespace TestIncludeGuard { 
    int g_Value = 1; // 전역 변수 정의
}

// Test_IncludeGuard2.h
#include "Test_IncludeGuard1.h"

// cpp 에서
#include "Test_IncludeGuard2.h"

TEST(TestClassicCpp, IncludeGuard) {
    EXPECT_TRUE(TestIncludeHeader::g_Value == 1);
}
```

cpp에서 `Test_IncludeGuard2.h`을 포함하고, `Test_IncludeGuard2.h`에서 `Test_IncludeGuard1.h`을 포함했기에 `g_Value`를 잘 사용할 수 있습니다. 

하지만, cpp에서 두파일을 모두 포함시켜 버린다면, 

```cpp
// cpp 에서
#include "Test_IncludeGuard1.h"
#include "Test_IncludeGuard2.h"

TEST(TestCppPattern, IncludeGuard) {
    EXPECT_TRUE(TestIncludeGuard::g_Value == 1); // (X) 컴파일 오류. g_Value 가 재정의됨
}
```

`g_Value` 정의가 2회 반복해서 `include` 되기 때문에 컴파일 오류가 발생합니다.


이런 경우 조건부 컴파일 기능을 활용한 인클루드 가드를 작성하여 1회만 `include`되도록 할 수 있습니다.

```cpp
// Test_IncludeGuard1.h 에서

#ifndef Test_IncludeGuard1_h 
#define Test_IncludeGuard1_h 

namespace TestIncludeGuard { 
    int g_Value = 1; // 전역 변수 정의
}

#endif // Test_IncludeGuard1_h

// Test_IncludeGuard2 에서
#ifndef Test_IncludeGuard2_h 
#define Test_IncludeGuard2_h 

#include "Test_IncludeGuard1.h"

#endif // Test_IncludeGuard2_h
```

파일이 처음 `include` 될 때는 `Test_IncludeGuard1_h`와 `Test_IncludeGuard2_h` 가 `define`되면서 코드를 포함하고, 그렇지 않으면 포함하지 않습니다.

`#pragma once` 를 이용한 방법도 있으나, `#pragma`는 비표준이므로 사용하지 않는게 좋습니다.

```cpp
// Test_IncludeGuard1.h 에서

#pragma once // 1회만 포함
namespace TestIncludeGuard { 
    int g_Value = 1; // 전역 변수 정의
}
```

