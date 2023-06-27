---
layout: single
title: "#5. [고전 C++ 가이드] 네임스페이스(namespace)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이름 충돌을 피하기 위해 복잡한 접두어를 쓰지 말고, 네임스페이스를 사용하라.
> * `using` 사용시 유효범위가 커지지 않도록 주의하라. 전역 공간에 `using namespace`를 사용하면 망한다.

**개요**

|항목|내용|
|--|--|
|정의|`namespace Test {`<br>`void f();`<br>`}`|
|사용|`Test::f();`|
|`using`선언|네임스페이스의 임의 항목 사용.<br>`using Test::f;`|
|`using`지시문|전체 네임스페이스 사용.<br>`using namespace Test;`|

네임스페이스는 하기와 같이 이름 충돌을 피하기 위해 사용합니다.

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

또한 하기와 같이 구성 요소를 논리적으로 묶을 때 사용할 수도 있습니다.

```cpp
// Parser 에 필요한 항목들을 논리적으로 묶음
namespace Parser {
   void Tokenizer() {}
   void Load() {}
}
```

**네임스페이스 항목의 함수 선언과 정의 분리**

네임스페이스에서 선언한 함수를 정의하려면, 하기 `C::g()` 와 같이 함수 정의시 네임스페이스명을 명시하면 됩니다. 

```cpp
namespace C {
    int f() {return 30;} // 정의
    int g(); // 선언
}
int C::g() { // C::명시해서 정의할 수 있음
    return f(); // 같은 네임스페이스이면 C::f() 와 같이 명시하지 않아도 됨
}
EXPECT_TRUE(C::g() == 30); // 네임스페이스 C의 f()를 호출
```

**서로 다른 네임스페이스 항목 사용**

같은 네임스페이스내에 있는 항목은 네임스페이스 명을 생략해도 되지만,  다른 네임스페이스의 항목을 사용하려면, 명시적으로 네임스페이스명을 지정하거나, `using`선언이나, `using`지시문을 사용해야 합니다.

```cpp
namespace D {
    void d() {}

    void Func() {
        d(); // 같은 네임스페이스 내의 항목은 네임스페이스 명 생략
    }
}
namespace E {
    void e() {
        D::d(); // 다른 네임스페이스의 것은 명시적으로 네임스페이스명 지정 
    }
    void f() {
        using D::d; // using 선언으로 d를 가져옴
        d(); // d는 네임스페이스명 없이 사용
        D::Func(); // Func은 네임스페이스명 지정
    }
}
namespace F {
    using namespace D; // D의 것은 다 가져옴

    void f() {
        d(); // D의 것은 그냥 사용
        Func(); // D의 것은 그냥 사용
    }
}
```

**전역 공간 `using`지시문 금지**

`using`지시문 사용시 전역 공간에 사용하면, 하기처럼 이름 충돌의 여지가 있으니 사용하지 마세요.

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

**무기명 네임스페이스**

이름 없이 네임스페이스를 정의하면, 해당 파일에서만 사용할 수 있습니다.

```cpp
namespace {
   void f(); // 정의된 파일에서만 사용가능 함
}
```

**별칭과 합성**

기존 네임스페이스의 별칭을 정의할 수 있습니다.

```cpp
namespace MyTestLibrary {
    void f() {}
}
namespace MTL = MyTestLibrary; // 별칭 정의
```

별칭인 네임스페이스에서는 새로운 정의나 선언을 추가할 수 없습니다.

```cpp
namespace MTL { 
    void g() {} // (X) 컴파일 오류. 별칭으로 정의한 namespace에 새로운 정의는 추가할 수 없다.
}
```

하기처럼 여러개의 네임스페이스를 합성할 수도 있습니다.

```cpp
namespace MyModule { // 여러개의 namespace를 합성할 수 있음
   using namespace A;
   using namespace B;
}
```

**이름 탐색 규칙**

이름 탐색은 다음의 순서를 따른다고는 하는데, GCC 8.1에서는 컴파일 오류가 나오네요. 아무튼 명시적으로 네임스페이스명을 쓰거나 `using`선언이나, `using`지시문을 사용하시기 바랍니다.

1. 호출한 위치의 유효범위에서 탐색
2. 주어진 인자들의 네임스페이스 탐색(GCC 8.1에서 컴파일 오류)
3. 오버로딩 모호성 해결
   
```cpp
namespace MyModule {
    class Date {};
    void MyFunc() {}
} 
namespace { 
    void f(MyModule::Date d) {
        // (X) 컴파일 오류. 인자중 MyModule 네임스페이스가 있어서 탐색 가능하다고 하는데 
        // GCC 8.1에서 컴파일 오류가 발생합니다.
        MyFunc(); 
    }
}
```

