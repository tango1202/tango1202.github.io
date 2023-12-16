---
layout: single
title: "#4. [모던 C++] (C++11~) 인라인 네임스페이스, (C++17~) 단순한 중첩 네임스페이스, (C++20~) 인라인 네임스페이스와 단순한 중첩 네임스페이스 결합"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)가 추가되어 API 버전 구성이 편리해 졌습니다.
> * (C++17~) [단순한 중첩 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#c17-%EB%8B%A8%EC%88%9C%ED%95%9C-%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)가 추가되어 `::` 로 표현할 수 있습니다.
> * (C++20~) [인라인 네임스페이스와 단순한 중첩 네임스페이스를 결합](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#c20-%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%99%80-%EB%8B%A8%EC%88%9C%ED%95%9C-%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4-%EA%B2%B0%ED%95%A9)하여 표시할 수 있습니다.

# 인라인 네임스페이스

[인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)로 정의하면, 바깥쪽 [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)에 암시적으로 `using namespace`가 추가됩니다. 즉, 바깥쪽 [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)에서는 [인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)를 자신의 것처럼 사용할 수 있습니다.  

```cpp
namespace MyLib {
    namespace V1 {
        int f() {return 1;} // #1
    }
    inline namespace V2_11 {
        int f() {return 2;} // #2
    }
}

EXPECT_TRUE(MyLib::V1::f() == 1);
EXPECT_TRUE(MyLib::V2_11::f() == 2);
EXPECT_TRUE(MyLib::f() == 2); // MyLib에 없으면 inline 사용
```

이는 API 버전 처리에 효과적입니다. 각각의 버전을 [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)로 분리하고, 사용하려는 버전에만 [inline](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/)을 추가하면 됩니다.

# (C++17~) 단순한 중첩 네임스페이스

기존의 [중첩 네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/#%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)는 다음과 같이 정의했는데요(*[중첩 네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/#%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4) 참고*),

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

# (C++20~) 인라인 네임스페이스와 단순한 중첩 네임스페이스 결합

C++20부터는 [인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)와 [단순한 중첩 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#c17-%EB%8B%A8%EC%88%9C%ED%95%9C-%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)를 결합하여 표시할 수 있습니다.

```cpp
namespace A_20 {
    namespace B_17 {
        inline namespace C_11 {
            void f() {}
        }
    }
}
```

은 다음과 같습니다.

```cpp
namespace A_20::B_17::inline C_11 {
    void f() {}
}
```