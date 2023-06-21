---
layout: single
title: "#5. [고전 C++ 가이드] 네임스페이스(Namespace)"
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
|정의|`namespace Test {void f();}`|
|사용|`Test::f();`|
|`using`지시문|전체 네임스페이스 사용. `using namespace Test;`|
|`using`선언|임의 항목 사용. `using namespace::f;`|

구성 요소를 논리적으로 묶을 수 있습니다.

```cpp
namespace Paser {
   void Token();
   void f();
}
void g();

void Parser::f() {
  Token(); // 동일 namespace. 한정자 불필요
}
void g() {
  Paser::Token(); // 다른 namespace. 한정자 필요
}
```

하기와 같이 사용할 수 있음
```cpp
혹은 using을 사용하여,
using Parser::Token;
Token();

혹은 using namespace를 사용하여,
using namepace Parser;
Token();
```

**무기명 네임스페이스**

정의된 파일에서만 사용 가능

```cpp
namespace {
   void f(); // 정의된 파일에서만 사용가능하다.
}
```

**별칭과 합성**

별칭을 정의하면, 새로운 정의나 선언을 추가할 수 없다.

```cpp
namespace HncTestLibrary {...}
namespace HTL = HncTestLibrary;
namespace HTL { 
    void f() {}   //(X) 별칭으로 정의한 namespace에 새로운 정의는 추가할 수 없다.
}
```

여러개의 네임스페이스를 합성할 수 있다.

```cpp
namespace My { // 여러개의 namespace를 합성할 수 있다.
   using namespace Harry;
   using namespace Sally;
}
```

**이름 탐색 규칙**

1. 호출한 위치의 유효범위에서 탐색
2. 주어진 인자들의 namepace 탐색(기본클래스 포함)
3. 오버로딩 모호성 해결
   
```cpp
namespace test {
   class Date;
   void format();
}
void f(test::Date d ) {
   format(); // 인자중 test 네임스페이스가 있어서 탐색 가능
}
```

인자중 `test` 네임스페이스가 있어서....

