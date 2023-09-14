---
layout: single
title: "#25. [모던 C++] (C++11~) extern 템플릿"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `extern` 템플릿을 추가하여 템플릿 선언을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다. 

# 개요

[템플릿 인스턴스 중복 생성](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EC%A4%91%EB%B3%B5-%EC%83%9D%EC%84%B1)에서 언급했듯, 템플릿을 헤더 파일에 정의해 두고, 여러 파일에서 인클루드 한다면, 템플릿 정의가 중복 정의되어 코드 크기가 커집니다.

```cpp
// Test_MordernCpp_ExternTemplate.h 에서

namespace ExternTemplate {
    template<typename T>
    T Add(T a, T b) {
        return a + b;
    }
}

// test1.cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate1) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(1, 2) == 3); // Add<int>()가 정의 되어 포함됩니다.
}

// test2. cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate2) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(10, 20) == 30); // Add<int>가 재정의 되어 포함됩니다.
}
```

C++11에서는 `extern` 템플릿을 추가하여 템플릿 선언만 할 수 있으며, 불필요한 코드 크기를 최소화 할 수 있습니다. 

```cpp
// Test_MordernCpp_ExternTemplate.h 에서

namespace ExternTemplate {
    template<typename T>
    T Add(T a, T b) {
        return a + b;
    }
}

// test1.cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate1) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(1, 2) == 3); // Add<int>()가 정의 되어 포함됩니다.
}

// test2. cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

// 이전에 정의된 템플릿을 사용합니다.
extern template int ExternTemplate::Add<int>(int, int);

TEST(TestMordern, ExternTemplate2) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(10, 20) == 30); // Add<int>가 재정의 되어 포함됩니다.
}
```

