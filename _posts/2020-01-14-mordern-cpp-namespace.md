---
layout: single
title: "#14. [모던 C++] (C++11~) 인라인 네임스페이스"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 인라인 네임스페이스가 추가되어 API 버전 구성이 편리해 졌습니다.
> * (C++17~) `::` 로 단순하게 중첩 네임스페이스를 표현할 수 있습니다.

# 인라인 네임스페이스

인라인 네임스페이스로 정의하면, 바깥쪽 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에 암시적으로 `using namespace`가 추가됩니다. 즉, 바깥쪽 네임스페이스에서는 인라인 네임스페이스를 자신의 것처럼 사용할 수 있습니다.  

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

# (C++17~) 단순화된 중첩 네임스페이스

기존의 [중첩 네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/#%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4) 는 다음과 같이 정의했는데요,

```cpp
namespace MyLib {
     namespace Parser {
        void Tokenizer() {}
    }
    namespace File {
        void Load() {};
        void Save() {};
    }
}
MyLib::Parser::Tokenizer();
MyLib::File::Load();
```

C++17 부터는 `::`을 이용하여 단순하게 표현할 수 있습니다.

```cpp
namespace MyLib_17::Parser {
    void Tokenizer() {}
}
namespace MyLib_17::File {
    void Load() {};
    void Save() {};
}
MyLib_17::Parser::Tokenizer();
MyLib_17::File::Load();
```
