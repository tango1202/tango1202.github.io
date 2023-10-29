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
> * 예외를 해결할 수 있는 곳에서 탐지하라.
> * `catch()`에서 예외 개체가 복사 생성되지 않도록 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 받아라.
> * 예외를 그대로 전파할 경우에는 [throw;](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 사용하라.
> * 소멸자에서 예외를 방출하지 마라.(필요하다면 `Release()`함수를 구현하라.)
> * [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)와 호환되도록 스마트 포인터를 사용하라.
> * 함수 정의시 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 사용하지 마라.

> **모던 C++**
> * (C++11~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 함수의 예외 방출 여부를 보증합니다.
> * (C++11~) 소멸자는 기본적으로 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 동작합니다.
> * (C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept)로 예외를 방출하느냐 안하느냐만 관심을 둡니다.
> * (C++11~) [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 이 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되면서 함께 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.
> * (C++17~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 관련해서 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)가 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 이제 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)만 사용해야 합니다. 

# 개요

예외 상황이 발생했을때 오류 코드를 리턴하면, 호출한 곳에서 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 확인하고 예외 처리를 해야 하는데, 강제성이 없어서 빼먹을 수 있습니다.

```cpp

int f() {
    return 1; // 함수 호출 결과로 오류 코드를 리턴합니다.
}

void g() {
    f(); // (△) 비권장. 함수를 호출하고 리턴된 오류 코드를 무시합니다.
}
```

 C++은 좀더 예외 처리의 강제성을 줄 수 있도록 다음의 메커니즘으로 예외를 발생, 복귀, 전파, 탐지 합니다. 예외 처리를 하지 않으면 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 호출하여 프로그램이 종료되므로, 예외 처리를 할 수 밖에 없습니다.

1. [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 문으로 예외를 발생시킵니다. 이때 예외 정보를 담은 개체로 예외 정보를 제공합니다. `catch()`로 예외를 처리하지 않으면, [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 호출하여 프로그램이 종료됩니다.
2. **[스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)**에 의해 예외 발생전의 상태로 복귀합니다.
3. 아무런 작업을 안하거나 [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 예외를 상위 개체로 전파합니다.
4. `try-catch()`로 예외를 탐지합니다. 

# 예외 발생과 탐지(try-catch(), throw)

예외 발생은 현재 함수에서 해결할 수 없는 예외 상황을 보고하는 일이며, 탐지는 어디선가 해당 예외를 진짜로 처리하는 일입니다.

다음 `ToChar()` 함수는 `int`를 `unsigned char`로 변환하는 역할을 합니다.

```cpp
unsigned char ToChar(int val) {
    return static_cast<char>(val); // 0~255 의 범위를 벗어나면 오동작합니다.
}
```

상기 코드는 `val`이 `0 <= val && val < 256` 일때만 정상 동작하므로, [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 벗어나는 인수가 전달되면, [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 이용하여 예외를 발생시켜야 합니다.

```cpp
unsigned char ToChar(int val) {
    if (!(0 <= val && val <= 255)) {
            throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    }
    return static_cast<char>(val);
}
```

진단 코드를 추가하여, 사용자가 실수하지 않도록 해주면 더 좋습니다.

```cpp
unsigned char ToChar(int val) {
    assert(0 <= val && val < 256); // 인자가 유효한지 검사합니다.

    if (!(0 <= val && val < 256)) {
        throw std::out_of_range("Fail : val range"); // 예외를 발생시킵니다.
    }
    return static_cast<char>(val);
}
```

상기 `ToChar()`함수와 같이 예외를 발생시키는 함수가 있다면, 호출전에 조건 검사를 실행해서 호출할 수 있고, `try-catch()`를 이용하여 탐지할 수 있습니다. 이때 [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한 예외 개체는 `catch()`에서 받아서 사용할 수 있는데, 예외 개체가 복사 생성되지 않도록 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 사용하는게 좋습니다.(그냥 복사 생성하게 하면, 예외 상황에서 또다른 예외가 발생할 수도 있거든요.)

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

`try-catch()`로 

1. 탐지하지 않으면, 예외는 상위 개체에 그대로 전파되고, 
2. 탐지하면, 더이상 예외는 상위 개체에 전파되지 않는데요, 이를 다시 재전파하려면 `throw;`를 사용합니다.

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

`catch()`는 다음처럼 여러개의 예외를 탐지하거나, 모든 예외를 탐지할 수 있습니다. 단 예외가 상속 관계라면 자식 개체 타입을 먼저 사용해야 합니다. 특별히 `...`을 사용하면 모든 타입을 캐치할 수 있습니다.

```cpp
class Base : public std::exception {};
class Derived : public Base {};
try {
    f();
}
catch (const Derived&) {} // 자식 개체 타입을 먼저 사용
catch (const Base&) {}
catch (const std::exception&) {}
catch (...) {} // 그외 모든 예외 타입
```

# 스택 풀기(예외 복귀)

C++ 은 예외 상황이 발생하면,  `catch()` 될때까지 거꾸로 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)을 하나하나 풀면서(해제하면서, 소멸시키면서) 생성한 개체의 소멸자를 호출하고, 메모리를 해제합니다. 이러한 과정을 거쳐 예외 발생 전의 상태로 복귀합니다.

다음 코드에서 `h()` 함수는 예외를 발생시키는 함수 `f()`와 `g()`를 호출하지만, `f()`가 예외를 발생시키면 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)을 풀고 `catch()`문으로 이동하기 때문에, `g()`함수는 호출되지 않습니다.

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
        int c = g(a);
    }
    catch (std::out_of_range& e) {
        // 아무 작업도 안합니다.
    }
}
```

[스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)의 상황은 다음과 같이 변경됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e9cbb724-c094-4cb0-851f-e753e1afbbd0)


[스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)에 따른 개체 소멸시에는 소멸자가 호출되므로, 소멸자예서 예외를 방출하면, 정상적인 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)를 방해합니다. 따라서 소멸자에서는 예외를 방출하면 안됩니다.([소멸자에서 예외 방출 금지](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90%EC%97%90%EC%84%9C-%EC%98%88%EC%99%B8-%EB%B0%A9%EC%B6%9C-%EA%B8%88%EC%A7%80) 참고)

이때, [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 생성된 개체는 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)에 의해 해제되지 않으므로,

```cpp
    try {
        int a = new int(10); // (△) 비권장. 힙에 생성된 개체입니다.
        int b = f(*a);
        int c = g(*a);
    }
    catch (std::out_of_range& e) {
        // 아무 작업도 안합니다.
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
        // 아무 작업도 안합니다.
    }
```

# 동적 예외 사양

함수 정의시 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)를 사용하여 방출하는 예외를 명세화 할 수 있는데요, 명시한 예외 이외의 예외가 발생하면, `unexpected()` 함수를 호출하며, `unexpected()` 함수에서는 `unexpected_handler` 를 호출합니다. 즉, 명시한 예외 이외에는 `unexpected_handler` 로 분기하므로 사용하지 않는게 낫습니다.

```cpp
void f() throw(error1, error2) 
```

는 사실 다음과 유사합니다.

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
       throw std::bad_exception();
    }
}
```

즉, `error1`과 `error2` 만 방출하기 위해서, 억지로 다른 예외인 [bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)으로 강제 변환합니다. 이러다 보니 불필요하게 `try-catch()` 만 추가되어 실행 속도만 느려집니다. 사용하지 마세요.

다음 코드에서 `f()`에서 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)에 없는 예외가 발생했을때,

1. #1 의 `UnexpectedHandler()` 핸들러가 호출되고,
2. `UnexpectedHandler()`에서 별다른 처리없이 예외를 전파하면, `catch()` 될때까지 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)를 합니다. 하기 예에서는 #2 인 [bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)으로 `catch()`됩니다.

```cpp
void UnexpectedHandler() {
    throw; // #1. 예외를 전파합니다 
}

void f() throw(int, std::bad_exception) {
    throw 'x'; // 그냥 char 형 오류를 발생합니다. 
}

void g() {
    std::set_unexpected(UnexpectedHandler); // unexpected 핸들러를 설정합니다.
    try {
        f();
    }
    catch (int) {}
    catch (std::bad_exception& e) {} // #2.
    catch (...) {}
}
```
> *(C++11~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 함수의 예외 방출 여부를 보증합니다.*<br/>
> *(C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept)로 예외를 방출하느냐 안하느냐만 관심을 둡니다.*<br/>
> *(C++11~) [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 이 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되면서 함께 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.*

# terminate()

예외를 `catch()`하지 않으면 최종적으로 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 함수를 호출하며, [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 함수에서는 `terminate_handler` 를 호출하고, 기본 `terminate_handler` 에서는 `abort()`를 호출하여 프로그램을 강제 종료합니다. 

[terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 핸들러는 `set_terminate()` 함수로 사용자 정의 할 수 있으며, `abort()`등의 프로그램 종료 코드가 포함되어야 합니다.

```cpp
#include <iostream>
#include <exception>

void TerminateHandler() {
    std::cout << "TerminateHandler 호출" << std::endl;
    std::abort(); // 프로그램 종료
}
int main() {
    std::set_terminate(TerminateHandler); // terminate_handler를 설정합니다.
    throw 0; // 예외 발생. 내부적으로 매칭되는 catch() 가 없어 terminate()를 호출하고, TerminateHandler()가 호출 됩니다. 
    return 0;
}
```


