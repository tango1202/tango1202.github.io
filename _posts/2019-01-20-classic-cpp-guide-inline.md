---
layout: single
title: "#20. [고전 C++ 가이드] 인라인 함수(inline)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 인라인은 요청일 뿐이다. 컴파일러가 인라인화를 판단한다.

> **모던 C++**
> * (C++17~) [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 `include` 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, 클래스 정적 멤버 변수 정의 및 초기화가 쉬워졌습니다.

# 개요

`inline`은 다음 2개의 역할을 합니다.

1. 컴파일러가 함수 호출 최적화를 할 수 있게 요청합니다.
2. 여러 파일에서 `include` 한 함수가 중복 정의되지 않도록 링커에게 알려줍니다. 

|항목|내용|
|--|--|
|정의|`inline void f() {}`|
|선언과 정의 분리|지원 안됨.|
|인라인화 판단|`inline`으로 정의된 함수나 클래스 선언부에 정의된 함수들 중 컴파일러가 판단하여 인라인화 됨|

함수 호출은 하기 단계를 거칩니다.

1. 인수를 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 쌓습니다.(함수 호출 부하)
2. 함수를 호출합니다.(함수 호출 부하)
3. 함수내의 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 쌓습니다.(함수 호출 부하)
4. 함수의 코드를 실행합니다.
5. 함수의 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 리턴합니다.(함수 호출 부하)
6. [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 쌓인 정보를 제거합니다.(함수 호출 부하)

인라인 함수는 상기의 함수 호출 부하(미세하겠지만)를 줄이기 위해 함수가 호출된 자리에 직접 코드를 삽입(코드 치환)하는 함수입니다.(비슷한 일을 `define` 함수가 합니다만, 어지간하면 쓰지 마세요!!! [`#define` 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#define-%ED%95%A8%EC%88%98) 참고)

```cpp
inline int Plus(int a, int b) {
    return a + b;
}

void f() {
    int x = 10;
    int y = 20;

    int z = Plus(x, y);
    // Plus 함수가 인라인화 되면 다음과 같이 변경됩니다.
    // int z = x + y;
}
```

**클래스 멤버 함수의 인라인화**

클래스(구조체) 선언부에 정의한 함수는 컴파일러 판단에 따라 `inline` 화 됩니다.([구조체와 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B5%AC%EC%A1%B0%EC%B2%B4%EC%99%80-%ED%81%B4%EB%9E%98%EC%8A%A4) 언급)

```cpp
// h에서
class T {
    int f1() {return 0;} // 컴파일러 판단에 따라 inline화 됨
    int f2(); // 선언만 되었기에 inline화 안됨
};

// cpp에서
int T::f2() {return 0;} // inline화 안됨
```

**여러 cpp에서 사용하는 인라인 함수 정의**

보통 헤더 파일에서 함수를 정의하고, 여러개의 `cpp`에서 `include`하면 함수가 중복 정의되었다며 오류가 발생합니다.

하지만 `inline`을 사용하면, 상기에 언급했듯 여러 파일에서 `include` 한 함수가 중복 정의되지 않도록 링커에게 알려주므로, 오류 없이 잘 빌드 됩니다.

```cpp
// MyInline.h 에서
#ifndef Test_Inline_h 
#define Test_Inline_h

namespace TestInline { // 이름 충돌을 피하기 위해 네임스페이스에서 정의

    // 헤더 파일에 함수를 정의하면, 이를 include한 여러 cpp에서 중복 정의되므로 
    // 컴파일 오류가 발생합니다. 
    // 그래서 보통 헤더 파일에서는 함수 선언만 합니다.
    // inline을 이용해 헤더 파일에 함수를 정의하면, 동일한 함수 정의를 사용하기 때문에
    // 컴파일 오류가 발생하지 않습니다.
    inline int Plus(int a, int b) {
        return a + b;
    }
}
#endif // Test_Inline_h
```

`#include "Test_Inline.h"`하여 사용할 수 있습니다.

```cpp
// cpp에서
#include "Test_Inline.h"

TEST(TestClassicCpp, Inline) {
    EXPECT_TRUE(TestInline::Plus(10, 20) == 30); // TestInline::Plus(10, 20)은 10 + 20 으로 인라인화 됨
}
```

**인라인 함수 특징**

인라인 함수도 함수이다 보니 함수 고유의 장점(함수 코드 재활용, [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)와의 타입에 기반한 **코딩 계약**, 디버깅 용이)은 유지합니다. 또한, 함수 호출부에 직접 코드를 삽입하기 때문에 다음과 같은 특징이 있습니다.

1. 함수 호출 부하가 줄어듭니다.
2. 실행 파일 용량이 커집니다.

따라서, 함수 실행 속도가 아주 아주 빠른데 자주 호출되는 함수라면, 함수 호출 부하를 줄이기 위해 사용하는 게 좋습니다.

**인라인화 판단**

`inline`을 지정했다고 해서 모두 인라인 함수가 되는 것은 아닙니다. 컴파일러에게 인라인화 가능한지 요청하는 것이고, 컴파일러 판단에 의해 인라인화 됩니다. 

하기의 경우 인라인화 되지 않습니다.

1. 인라인 처리하기 너무 큰 경우
2. 재귀 호출을 하는 경우
3. [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)를 통해 참조되는 함수인 경우



