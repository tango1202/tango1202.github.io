---
layout: single
title: "#4. [고전 C++ 가이드] 유효 범위"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이름의 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)는 짧게 유지하라. 중괄호(`{}`) 블록으로 짧게 만들 수 있다.

# 개요

전역 개체의 경우에는 전체 범위에 영향을 주며, 그렇치 않은 경우는 블록 범위 내에서만 영향을 줍니다.(*[클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/), [함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/), [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/), [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/), [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/) 등*)

다음은 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), 함수의 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), 블록내에 정의된 변수가 이름이 동일할 경우 어떻게 사용되는지 보여주는 예입니다. 가까운 쪽이 사용됩니다.

```cpp
namespace {
    int x = 1; // 전역 변수. namespace 내에 있어 현 파일에서만 접근 가능
}

TEST(TestClassicCpp, Scope) {
    EXPECT_TRUE(x == 1); // 전역 변수
    int x = 2; 
    EXPECT_TRUE(x == 2); // 지역 변수가 전역 x를 가림
    {
        int x = 3;
        EXPECT_TRUE(x == 3); // 블록이 지역 x를 가림. 지역 x에 접근할 방법이 없어요.
        ::x = 10; // :: 붙여 강제로 전역 접근
    } 
    EXPECT_TRUE(x == 2); // 지역 변수
    EXPECT_TRUE(::x == 10); // 전역 변수
}
```

# 이름의 유효 범위가 짧으면 좋은 이유

프로젝트 규모가 커지면, 서로 다른 영역에서 동일한 이름을 사용하게 될 확률이 높습니다. 특히 직관적이고 좋은 이름일수록요. ***이름이 서로 충돌나거나 가리지 않도록 이름의 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 짧게 유지***하는게 좋습니다.

특히 이름 충돌시 [매크로](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/)가 관여되면 대환장 파티가 열릴 수 있습니다.(*[매크로 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98) 참고*)

# 블록을 활용한 유효 범위 통제

정의한 곳과 사용한 곳이 서로 가까운 것이 여러모로 좋습니다.

과거의 C언어처럼 함수 앞 부분에 변수 정의(*인스턴스화*)를 몰아서 해두고, 한참 뒤에 사용하는 건 좋지 않습니다. 사용하지도 않았는데 미리 비용을 지불하지 마세요.(*[제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/) 참고*) 

특히 미리 정의된 변수가 사용되기 전에 `if()` 등의 제어문으로 함수가 중단되면 괜히 정의만 한 셈입니다.

```cpp
void f() {
    int a;
    int b;
    int c; // 미리 정의해 뒀습니다.

    if (a + b == 0) {
        return 0; // (△) 비권장. int c 를 정의했지만 사용하지 않습니다.
    }

    return a + b + c;

}
```

쓸데없는 비용 지불은 둘째치고, 나중에 위아래 스크롤 하기 바빠질 수도 있습니다.(*변수명이 뭐였지? 타입이 뭐였지? 그냥 [헝가리안 표기](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-naming/#%ED%83%80%EC%9E%85-%EB%AA%85%EC%8B%9C-%EA%B8%88%EC%A7%80)를 할까? 언제 초기화 됐지? 언제 값이 바뀌지?*) 
 
많은 변수를 사용하는 함수라면,

```cpp
void f() {
    int a;
    int b;
    ...
    int z;

    // a~z를 사용하는 코드들
}
```

[유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 짧게 해서 블록화 해보세요.

```cpp

void f() {
    {
        int a;
        int b;
        // a와 b만 사용하는 코드들
    }
    {
        int c;
        int d;
        // c와 d만 사용하는 코드들
    }
}
```

깔끔하게 나눠졌다면, 각 블록을 [함수화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/) 하시길 바랍니다.
