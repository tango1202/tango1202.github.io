---
layout: single
title: "#13. [모던 C++] (C++11~) 인라인 네임스페이스"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `inline namespace` 가 추가되어 API 버전 구성이 편리해 졌습니다.

# 개요

`inline namespace`로 정의하면, 바깥쪽 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에 암시적으로 `using namespace`가 추가됩니다. 즉, 바깥족 네임스페이스에서는 `inline namespace`를 자신의 것처럼 사용할 수 있습니다.  

```cpp
namespace MyLib {
    namespace V1 {
        int f() {return 1;} // #1
    }
    inline namespace V2 {
        int f() {return 2;} // #2
    }
}

EXPECT_TRUE(MyLib::V1::f() == 1);
EXPECT_TRUE(MyLib::V2::f() == 2);
EXPECT_TRUE(MyLib::f() == 2); // MyLib에 없으면 inline 사용
```

이는 API 버전 처리에 효과적입니다. 각각의 버전을 네임스페이스로 분리하고, 사용하려는 버전에만 `inline` 을 추가하면 됩니다.