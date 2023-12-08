---
layout: single
title: "#2. [고전 C++ 가이드] 예외 메카니즘(try-catch, throw와 스택 풀기, terminate)"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 해결할 수 없는 예외 상황은 오류 코드 리턴보다는 강제성이 있는 [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 보고하라.
> * 예외를 해결할 수 있는 곳에서 [탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하라.
> * [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)에서 예외 개체가 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)되지 않도록 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 받아라.
> * [예외를 그대로 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)할 경우에는 [throw;](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 사용하라.
> * [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 예외를 방출하지 마라.(*필요하다면 `Release()`함수를 구현하라.*)
> * [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)와 호환되도록 스마트 포인터를 사용하라.
> * 함수 정의시 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 사용하지 마라.

> **모던 C++**
> * (C++11~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 추가되어 함수의 예외 방출 여부를 보증하며, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)는 기본적으로 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 동작합니다.
> * (C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept)로 예외를 방출하느냐 안하느냐만 관심을 둡니다.
> * (C++11~) [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)이 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되면서 함께 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.
> * (C++17~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 함수 유형에 포함되어 예외 처리에 대한 코딩 계약을 좀더 단단하게 할 수 있습니다.
> * (C++17~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 관련해서 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)가 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 이제 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)만 사용해야 합니다. 
> * (C++17~) [[[nodiscard]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard)가 추가되어 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 무시하지 않도록 컴파일 경고를 해줍니다.

# 개요

예외 상황이 발생했을때 오류 코드를 리턴하면, 호출한 곳에서 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 확인하고 예외 처리를 해야 하는데, 강제성이 없어서 빼먹을 수 있습니다.

> *(C++17~) [[[nodiscard]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard)가 추가되어 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 무시하지 않도록 컴파일 경고를 해줍니다.*

```cpp

int f() {
    return 1; // 함수 호출 결과로 오류 코드를 리턴합니다.
}

void g() {
    f(); // (△) 비권장. 함수를 호출하고 리턴된 오류 코드를 무시합니다.
}
```

 C++은 좀더 예외 처리의 강제성을 줄 수 있도록 다음의 메커니즘으로 예외를 [발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)/[복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)/[전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)/[탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 합니다. 예외 처리를 하지 않으면 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 호출하여 프로그램이 종료되므로, 예외 처리를 할 수 밖에 없습니다.

 |항목|내용|
 |--|--|
 |[예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)|[throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 문으로 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킵니다. 이때 예외 정보를 담은 개체를 제공합니다. [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 예외를 처리하지 않으면, [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 호출하여 프로그램이 종료됩니다.|
 |[예외 복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)|[스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 의해 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)전의 상태로 [예외를 복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)합니다.|
 |[예외 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)|[catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 예외를 처리하지 않으면 자동으로 상위 개체로 [예외가 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)됩니다. 만약, [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했다면 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 블록내에서 [throw;](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 사용하여 상위 개체로 [예외를 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)할 수 있습니다.|
 |[예외 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)|[try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.|


# 예외 발생과 탐지(try-catch(), throw)

[예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)은 현재 함수에서 해결할 수 없는 예외 상황을 보고하는 일이며, [예외 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)는 어디선가 해당 예외를 진짜로 처리하는 일입니다.

다음 `ToChar()` 함수는 `int`를 `unsigned char`로 변환하는 역할을 합니다.

```cpp
unsigned char ToChar(int val) {
    return static_cast<char>(val); // 0~255 의 범위를 벗어나면 오동작합니다.
}
```

상기 코드는 `val`이 `0 <= val && val < 256` 일때만 정상 동작하므로, [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 벗어나는 인수가 전달되면, [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 이용하여 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜야 합니다.

```cpp
unsigned char ToChar(int val) {
    if (!(0 <= val && val <= 255)) {
            throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    }
    return static_cast<char>(val);
}
```

이때 [진단](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/) 코드를 추가하여, 사용자가 실수하지 않도록 해주면 더 좋습니다.

```cpp
unsigned char ToChar(int val) {
    assert(0 <= val && val < 256); // 인자가 유효한지 진단합니다.

    if (!(0 <= val && val < 256)) {
        throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    }
    return static_cast<char>(val);
}
```

상기 `ToChar()`함수와 같이 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키는 함수가 있다면, 

* 호출전에 조건 검사를 실행해서 알맞는 범위의 인수값을 전달하여 호출할 수 있고, 
* 그냥 아무값이나 전달해서 실행한뒤 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 이용하여 [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수 있습니다. 이때 [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한 예외 개체는 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)에서 받아서 사용할 수 있는데, 예외 개체가 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)되지 않도록 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 사용하는게 좋습니다.(*그냥 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하게 하면, 예외 상황에서 또다른 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수도 있거든요.*)


```cpp
int val = 'A';
unsigned char ch = ToChar(val + 1); 
EXPECT_TRUE(ch == 'B');

// (X) 예외 발생. 예외를 처리하는 곳이 없으면 프로그램이 종료됩니다.
int val = 256;
ch = ToChar(val); 

// (O) 호출전에 조건을 검사하여 실행할 수 있습니다.
if (0 <= val && val < 256) {
    ch = ToChar(val); 
}

// (O) 그냥 실행하고 예외가 발생하면 이를 탐지하여 뭔가를 처리할 수 있습니다.
try {
    ch = ToChar(val); 
}
catch (std::out_of_range& e) { // 참조자를 사용합니다.
    // 예외가 발생했을때 뭔가 처리하는 코드
}
```

아무래도 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 사용하는 것 보다는 호출전에 조건 검사를 미리하는게 논리의 흐름상 깔끔합니다만, 여러 함수에 걸쳐 호출되는 경우에는 적합하지 않습니다.

다음 예에서 `h()` -> `g()` -> `f()` -> `ToChar()`로 호출하는데요, 매 함수마다 [진단](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/) 코드와 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 코드를 넣으면 오히려 눈을 어지럽힙니다.(*관련 코드는 주석으로 했습니다.*) 이런 경우는 그냥 가장 하위 함수인 `f()`에서 예외를 발생시키고 가장 상위 개체에서 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하는게 좋습니다.

```cpp
unsigned char ToChar(int val) {
    assert(0 <= val && val < 256); // 인자가 유효한지 진단합니다.

    if (!(0 <= val && val < 256)) {
        throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    }
    return static_cast<char>(val);
}

unsigned char f(int val) {
    // (△) 비권장. 매번 진단과 예외 발생 코드를 넣으면 눈을 어지럽힙니다.
    // assert(0 <= val && val < 256); // 인자가 유효한지 진단합니다.
    // if (!(0 <= val && val < 256)) {
    //     throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    // }
    return ToChar(val);
}
unsigned char g(int val) {
    // (△) 비권장. 매번 진단과 예외 발생 코드를 넣으면 눈을 어지럽힙니다.
    // assert(0 <= val && val < 256); // 인자가 유효한지 진단합니다.
    // if (!(0 <= val && val < 256)) {
    //     throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    // }
    return f(val);
}
unsigned char h(int val) {
    // (△) 비권장. 매번 진단과 예외 발생 코드를 넣으면 눈을 어지럽힙니다.
    // assert(0 <= val && val < 256); // 인자가 유효한지 진단합니다.
    // if (!(0 <= val && val < 256)) {
    //     throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    // }
    return g(val);
}    

// 가장 상위 개체에서 try-catch 합니다.
try {
    int val = 'A';
    unsigned char ch = h(val); 
}
catch (std::out_of_range& e) { // 참조자를 사용합니다.
    // 예외가 발생했을때 뭔가 처리하는 코드
}
```

다음처럼 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 나열하여 여러개의 [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수 있습니다.

```cpp
class MyError {};
class YourError {};

try {
    f();
}
catch (const MyError&) {} 
catch (const YourError&) {}
```

 단, 예외가 [상속](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/) 관계라면 자식 개체 타입을 먼저 사용해야 합니다. 만약 부모 개체를 먼저 [탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면 자식 개체 타입은 [탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)되지 않습니다.

```cpp
class Base : public std::exception {};
class Derived : public Base {};
try {
    f();
}
catch (const std::exception&) {} // (△) 비권장. std::exception에서 Derived와 Base를 모두 탐지합니다.
catch (const Derived&) {} // (X) 컴파일 경고 
catch (const Base&) {}
```

즉, 다음과 같이 자식 개체의 순서대로 [탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)해야 합니다.

```cpp
class Base : public std::exception {};
class Derived : public Base {};
try {
    f();
}
catch (const Derived&) {} // (O) 자식 개체를 먼저 탐지해야 합니다.
catch (const Base&) {}    
catch (const std::exception&) {} 
```

또한, 특별히 `...`을 사용하면 모든 [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수 있습니다.

```cpp
class Base : public std::exception {};
class Derived : public Base {};
try {
    f();
}
catch (const Derived&) {} 
catch (const Base&) {}
catch (const std::exception&) {}
catch (...) {} // 그외 모든 예외 타입
```

# 예외 전파

[try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 

1. [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않으면, 예외는 상위 개체에 그대로 [전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)되고, 
2. [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, 예외는 더이상 상위 개체에 [전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)되지 않는데요, 이를 다시 [재전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)하려면 [throw;](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 사용합니다.

```cpp
try {
    std::string("abc").substr(10); // (X) 예외 발생. std::length_error
}
catch (const std::exception& e) {
    std::cout << e.what() << '\n';
    // throw e; // (△) 비권장. std::exception 을 복제하여 리턴
    throw; // (O) std::length_error 를 재전파
}
```

# 스택 풀기(예외 복원)

C++ 은 예외 상황이 발생하면,  [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 될때까지 거꾸로 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)을 하나하나 풀면서(*해제하면서, 소멸시키면서*) 생성한 개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출하고, 메모리를 해제합니다. 이러한 과정을 거쳐 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 전의 상태로 [복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)합니다.

다음 코드에서 `h()` 함수는 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키는 함수 `f()`와 `g()`를 호출하지만, `f()`가 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키면 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)을 풀고 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)문으로 이동하기 때문에, `g()`함수는 호출되지 않습니다.

```cpp
int f(int val) {
    throw std::out_of_range("Fail : f()"); // 예외를 발생시킵니다.
    return 0;
}
int g(int val) {
    throw std::out_of_range("Fail : g()"); // 예외를 발생시킵니다.
    return 0;
}
void h() {
    try {
        int a = 10;
        int b = f(a); // f() 함수에서 예외가 발생하여 g()는 호출되지 않습니다. 스택 풀기를 하고 catch문으로 이동합니다.
        int c = g(a); // f()에서 예외 발생시 호출되지 않습니다.
    }
    catch (std::out_of_range& e) {
        // 예외 처리를 합니다.
    }
}
```

[스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)의 상황은 다음과 같이 변경됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e9cbb724-c094-4cb0-851f-e753e1afbbd0)


[스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 따른 개체 소멸시에는 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출되므로, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 예외를 방출하면, 정상적인 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)를 방해합니다. 따라서 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서는 예외를 방출하면 안됩니다.(*[소멸자에서 예외 방출 금지](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90%EC%97%90%EC%84%9C-%EC%98%88%EC%99%B8-%EB%B0%A9%EC%B6%9C-%EA%B8%88%EC%A7%80) 참고*)

> *(C++11~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 추가되어 함수의 예외 방출 여부를 보증하며, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)는 기본적으로 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 동작합니다.*

이때, [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 생성된 개체는 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 의해 해제되지 않으므로,

```cpp
try {
    int a = new int(10); // (△) 비권장. 힙에 생성된 개체입니다.
    int b = f(*a);
    int c = g(*a);
}
catch (std::out_of_range& e) {
    // 예외 처리를 합니다.
}
```

스마트 포인터를 사용해야 합니다.

```cpp
try {
    std::auto_ptr<int> a(new int(10)); // (O) 
    int b = f(*a);
    int c = g(*a);
}
catch (std::out_of_range& e) {
    // 예외 처리를 합니다.
}
```

[스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 의해 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)전의 상태로 잘 [복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)하려면,

* [완전한 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90)로 만들고,
* [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)는 스마트 포인터로 만들고, 
* [swap을 이용한 예외 보증 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 만들고, 
* [nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94)을 구현하고,
* 함수내 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)는 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여 자동 소멸되도록 하고,
* [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서는 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)없이 자원을 정리해야 합니다.

자세한 내용은 [예외 안전에 좋은 클래스 설계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84%EC%97%90-%EC%A2%8B%EC%9D%80-%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%84%A4%EA%B3%84)를 참고하세요.

# 동적 예외 사양

함수 정의시 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)를 사용하여 방출하는 예외를 명세화 할 수 있는데요, 명시한 예외 이외의 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 못해서 시스템이 종료됩니다.

다음 예는 `int`와 `double`을 예외 명세했지만, `char` [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜, [catch(...)](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)으로도 [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 못해 시스템이 종료됩니다.

```cpp
void f() throw(int, double) { 
    throw 'e'; // (△) 비권장. 예외 명세에 없는 char 형 예외를 발생시킵니다. 
}
try {
    f(); // (△) 비권장.  int와 double 예외 명세가 있지만, char 예외를 발생시킵니다.
    }
catch (char) {
    std::cout << "throw : char" << std::endl;
}       
catch (int) {
    std::cout << "throw : int" << std::endl;
}
catch (double) {
    std::cout << "throw : double" << std::endl;
} 
catch (...) {
    std::cout << "throw : ..." << std::endl; // 예외를 탐지하지 못합니다.
} 
```

하지만, 명시한 예외 이외의 예외를 억지로 [탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하는 방법이 있습니다.

1. #1 : [bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)을 예외 명세에 포함합니다.
2. #2 : `set_expected()`로 [unexpected_handler](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)를 설정합니다. 이때 리턴된 값은 이전 핸들러이므로 나중에 복원합니다.
3. #3 : `MyUnexpectedHandler`에서 [throw;](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 [예외를 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)합니다.
4. #4 : 그러면 [bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)으로 [예외를 탐지](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수 있습니다.



```cpp
void MyUnexpectedHandler() {
    throw; // #3. 예외를 전파합니다 
}

void f() throw(int, double, std::bad_exception) { // #1
    throw 'e'; // (△) 비권장. 예외 명세에 없는 char 형 예외를 발생시킵니다. 
}

std::unexpected_handler old = std::set_unexpected(MyUnexpectedHandler); // #2. unexpected 핸들러를 설정합니다.
try {
    f(); // (△) 비권장.  int와 double 예외 명세가 있지만, char 예외를 발생시킵니다.
}
catch (char) {
    std::cout << "throw : char" << std::endl;
}       
catch (int) {
    std::cout << "throw : int" << std::endl;
}
catch (double) {
    std::cout << "throw : double" << std::endl;
} 
catch (std::bad_exception&) {
    std::cout << "throw : bad_exception" << std::endl; // #4. bad_exception으로 예외를 탐지합니다.
} 
catch (...) {
    std::cout << "throw : ..." << std::endl; 
} 

std::set_unexpected(old); // #2
```

즉, 다음과 같이 예외 명세를 하면,

```cpp
void f() throw(error1, error2) 
```

는 사실 다음과 유사해 집니다.

```cpp
void f() {
    try {
    }
    catch (error1&) {
       throw;
    } 
    catch (error2&) {
       throw;
    }
    catch (...) {
        // 명시한 예외 이외에는 unexpected()를 호출하고, 
        // unexpected_handler를 설정해서 throw;를 하면 
        // 억지로 bad_exception으로 변환된 예외를 탐지할 수 있습니다.
        throw std::bad_exception(); 
    }
}
```

`error1`과 `error2` 만 방출하는게 아니라, 억지로 다른 예외인 [bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)까지 발생되는데요, 이러다 보니 불필요하게 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 만 추가되어 실행 속도만 느려집니다. 사용하지 마세요.

> *(C++11~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 추가되어 함수의 예외 방출 여부를 보증하며, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)는 기본적으로 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 동작합니다.*<br/>
> *(C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept)로 예외를 방출하느냐 안하느냐만 관심을 둡니다.*<br/>
> *(C++11~) [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)이 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되면서 함께 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.*

# terminate()

예외를 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않으면 최종적으로 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 함수를 호출하며, [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 함수에서는 `terminate_handler` 를 호출하고, 기본 `terminate_handler` 에서는 [abort()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90)를 호출하여 프로그램을 강제 종료합니다. 

[terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 핸들러는 `set_terminate()` 함수로 사용자 정의 할 수 있으며, [abort()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90)등의 프로그램 종료 코드가 포함되어야 합니다.

```cpp
#include <iostream>
#include <exception>

void TerminateHandler() {
    std::cout << "TerminateHandler 호출" << std::endl;
    std::abort(); // 프로그램 종료
}
int main() {
    std::set_terminate(TerminateHandler); // terminate_handler를 설정합니다.
    throw 0; // 예외 발생. 내부적으로 매칭되는 catch()가 없어 terminate()를 호출하고, TerminateHandler()가 호출 됩니다. 
    return 0;
}
```


