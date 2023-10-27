---
layout: single
title: "#5. [고전 C++ 가이드] 네임스페이스(namespace)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이름 충돌을 피하기 위해 복잡한 접두어를 쓰지 말고, [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 사용하라.
> * [using](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/#%EA%B0%9C%EC%9A%94) 사용시 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)가 커지지 않도록 주의하라.(*전역 공간에 `using namespace`를 사용하면 망한다.*)

> **모던 C++**
> * (C++11~) [인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)가 추가되어 API 버전 구성이 편리해 졌습니다.
> * (C++17~) [중첩 네임스페이스 표현이 단순](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#c17-%EB%8B%A8%EC%88%9C%ED%95%9C-%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)해져 `::` 로 표현할 수 있습니다.

# 개요

프로젝트 규모가 커지면 직관적이고 단순한 이름들은 중복되기 쉽습니다. 예를 들면 `GetObject()`같은 것들이요.
이런 경우 접두어를 사용하여 이름을 구분지을 수도 있지만, C++에서는 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 이용하여 이름 충돌을 피하고, 코드를 논리적으로 응집합니다.


1. 이름 충돌 회피

    ```cpp
    namespace A {
        int f() {return 10;}
    }
    namespace B {
        int f() {return 20;}
    }

    EXPECT_TRUE(A::f() == 10); // 네임스페이스 A의 f() 호출
    EXPECT_TRUE(B::f() == 20); // 네임스페이스 B의 f() 호출
    ```

2. 구성 요소의 논리적 응집

    ```cpp
    // Parser 에 필요한 항목들을 논리적으로 묶음
    namespace Parser {
        void Tokenizer() {}
        void Load() {}
    }
    ```

사용법은 다음과 같습니다.

|항목|내용|
|--|--|
|정의|`namespace Test {`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`void f() {}`<br/>`}`|
|사용|`Test::f();`|
|[using 선언](??)|[네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)의 임의 항목 사용.<br/>`using Test::f;`|
|[using](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/#%EA%B0%9C%EC%9A%94)지시문|[네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)의 전체 항목 사용.<br/>`using namespace Test;`|

# 네임스페이스 항목의 선언과 정의 분리

[네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에서 선언한 함수를 정의하려면, 

1. `g()` 와 같이 같은 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에서 정의하거나,
2. `C::h()` 와 같이 함수 정의시 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) 명을 명시하면 됩니다. 

```cpp
namespace C {
    int f() {return 30;} // 정의
    int g(); // 선언
    int h(); // 선언
}
namespace C {
    int g() { // 네임스페이스에서 정의
        return f(); // 같은 네임스페이스이면 C::f() 와 같이 명시하지 않아도 됨
    }
}
int C::h() { // C::로 명시해서 정의할 수 있음
    return f(); 
}
EXPECT_TRUE(C::g() == 30); 
EXPECT_TRUE(C::h() == 30);
```

# 서로 다른 네임스페이스 항목 사용

같은 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)내에 있는 항목들 끼리는 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) 명을 생략해도 됩니다.

다른 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)의 항목은 

1. 명시적으로 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) 명을 지정하거나, 
2. [using 선언](??)이나, 
3. [using](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/#%EA%B0%9C%EC%9A%94) 지시문을 사용해야 합니다.

```cpp
namespace D {
    void d1() {}
    void d2() {
        d1(); // 같은 네임스페이스 내의 항목은 네임스페이스 명 생략
    }
}
namespace E {
    void e() {
        D::d1(); // 다른 네임스페이스의 것은 명시적으로 네임스페이스 명 지정 
    }
    void f() {
        using D::d1; // using 선언으로 d를 가져옴
        d1(); // d1는 using 선언으로 가져왔으니 네임스페이스 명 없이 사용
        D::d2(); // d2는 가져오지 않았으니 네임스페이스 명을 지정하여 사용
    }
}
namespace F {
    using namespace D; // using 지시문으로 D의 것은 다 가져옴

    void f() {
        d1(); // D의 것은 그냥 사용
        d2(); // D의 것은 그냥 사용
    }
}
```

# 전역 공간 using지시문 금지

[using](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/#%EA%B0%9C%EC%9A%94) 지시문을 전역 공간에 사용하면, 이름 충돌의 가능성이 높아 집니다. 전역 공간에서 사용하지 마세요.

```cpp
namespace MyModule1 {
    void Test() {}
}
namespace MyModule2 {
    void Test() {} 
}
using namespace MyModule1; // 전역 공간에 MyModule1의 항목들을 모두 가져옵니다.
using namespace MyModule2; // 전역 공간에 MyModule2의 항목들을 모두 가져옵니다.

namespace MyModule3 {
    void f() {
        Test(); // (X) 컴파일 오류. MyModule1::Test() 인지, MyModule2::Test() 인지 모릅니다.
    } 
}
```

# 무기명 네임스페이스

이름 없이 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 정의하면, 해당 파일에서만 사용할 수 있습니다.

```cpp
namespace {
   void f(); // 정의된 파일에서만 사용가능 함
}
```

# 중첩 네임스페이스

[네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 구조적으로 그룹핑 하기 위해 중첩할 수 있습니다.

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

> *(C++17~) [중첩 네임스페이스 표현이 단순](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#c17-%EB%8B%A8%EC%88%9C%ED%95%9C-%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)해져 `::` 로 표현할 수 있습니다.*

# 별칭과 합성

기존 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)의 별칭을 정의할 수 있습니다. 이때, 별칭인 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에서는 새로운 정의나 선언을 추가할 수 없습니다.

```cpp
namespace MyTestLibrary {
    int f() {return 40;}
}
namespace MTL = MyTestLibrary; // 별칭 정의

namespace MTL { 
    void g() {} // (X) 컴파일 오류. 별칭으로 정의한 namespace에 새로운 정의는 추가할 수 없다.
}

EXPECT_TRUE(MTL::f() == 40);
```

여러개의 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 합성할 수도 있습니다.

```cpp
namespace G {
    int f() {return 50;}
}
namespace H {
    int g() {return 60;}
}
namespace MyModule { // 여러개의 네임스페이스를 합성할 수 있음
   using namespace G;
   using namespace H;
}
EXPECT_TRUE(MyModule::f() == 50);
EXPECT_TRUE(MyModule::g() == 60);
```

# 네임스페이스 명시시 유지보수성

[네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 어떻게 사용하느냐에 따라 유지보수성에 차이가 있습니다.

먼저 `swap()`을 명시적으로 사용했다가 성능 개선을 위해 리팩토링 하는 경우를 살펴봅시다.

```cpp
namespace My {
    class T {};   
    void f() {
        T a, b;
        std::swap(a, b); // std를 명시적으로 작성했습니다.
    } 
}
```

이제 `My` [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에 `std::swap()`보다 성능이 좋은 `swap()`함수를 만들었다 칩시다. 새로운 `swap()`함수를 사용하도록 리팩토링 해야 하는데요, 같은 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에 있는 함수라 그냥 `std::`만 삭제하면 되죠. 혹시 1,000군데 사용했다면, 찾기/바꾸기로 하면 되고요. 그런데, `std::`버전이 성능이 더 좋아 되돌린다면, 또 찾기/바꾸기를 해야 겠군요. 어딘지 좀 미련한 느낌입니다.

```cpp
namespace My {
    class T {};   
    void swap(T& left, T& right);
    void f() {
        T a, b;
        swap(a, b); // 같은 네임스페이스의 함수라 명시하지 않았습니다.
    } 
}
```

다음처럼 애초에 [using 선언](??)만 하여 암시적으로 사용한다면, 코드 본문은 수정할 필요없이 [using 선언](??)만 수정하면 되므로 뭔가 좀더 그럴싸 합니다.

```cpp
namespace My {
    using std::swap;
    class T {};   
    void f() {
        T a, b;
        swap(a, b); // using 선언에 따라 std::swap을 사용합니다.
    } 
}
```

이제 리팩토링하면, `using std::swap;` 만 삭제하면 됩니다.

```cpp
namespace My {
    // using std::swap; // 요것만 삭제하면 됩니다.
    class T {};   
    void swap(T& left, T& right);
    void f() {
        T a, b;
        swap(a, b);
    } 
}
```



