---
layout: single
title: "#3. [모던 C++] (C++11~) noexcept, noexcept 연산자"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `noexcept` 를 이용하여 예외 발생이 없는 함수인 nothrow 보증을 할 수 있습니다.
> * 소멸자는 기본적으로 `noexcept`로 동작합니다.
> * `noexcept` 연산자를 이용하여 해당 함수가 `noexcept`인지 컴파일 타임에 검사할 수 있습니다.

# 개요 
기존에는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)을 이용하여 함수가 발생하는 예외를 표시했는데요, 사실 명시한 예외 이외에는 `unexpected()` 핸들러로 분기하므로 사용하지 않는게 낫다고 말씀드렸습니다. 

C++11 부터는 이를 보완하여 `noexcept`를 이용하여 예외 발생이 없는 함수인 nothrow 보증을 할 수 있습니다. 

|항목|내용|
|--|--|
|`noexcept`|nothrow 보증을 합니다. 예외를 발생하지 않습니다. 만약 발생시키면 `std::terminate()`를 호출합니다.|
|`noexcept(true)`|`noexcept`와 동일합니다.|
|`noexcept(false)`|예외를 발생시킵니다.|

이에 따라 컴파일러는 어떤 예외를 발생하느냐 보다는 예외 발생 여부만 보고 최적화를 진행합니다. 예를 들면 스택 풀기등의 작업이요.

```cpp
// 기존 
// 나열된 예외 이외에는 unexpected() 핸들러로 분기. 
// 나열된 예외가 없으므로 예외 발생시 무조건 unexpected() 핸들러 호출
void f() throw() {
}

// C++11. 예외 발생 안함. 
// 컴파일러는 이 사양을 믿고 스택 풀기등을 안해도 되므로 최적화 수행 
// 혹여나 예외가 발생하면 terminate()를 호출
void g() noexcept {
}
```

만약 예외를 발생시킨다면 컴파일시에 경고가 발생하고, 런타임에서 `std::terminate()`를 호출하여 프로그램을 종료합니다.

```cpp
void f() noexcept {
    throw 'a'; // (X) 컴파일 경고. terminate 된다고 경고하고, 런타임에서 terminate()를 호출하여 종료합니다.
}
```

한가지 아쉬운 점은 `noexcept`인 함수 내에서 사용하는 함수가 예외를 발생시키면 컴파일 경고 없이, `std::terminate()`를 호출한다는 점입니다. 따라서 `noexcept` 함수를 만들때는 사용되는 하위 함수들을 면밀히 검토하고, `noexcept` 추가해 두어야 하며, 향후 하위 함수들이 리팩토링 될 때 혹여나 예외를 발생시키면, 사용되는 모든 곳의 예외 처리를 재검토 해야 합니다.

```cpp
void f() {
    throw 'a'; 
}
void g() noexcept {
    f(); // f() 함수에서 예외를 발생시킵니다. 컴파일 경고가 없습니다.
}
g(); // noexcept 함수 내에서 사용하는 함수 f()가 예외를 발생시켜 terminate()를 호출합니다.
```
# 소멸자 예외

기존에는 예외 안정을 위해 소멸자에서는 예외를 발생시키지 않아야 한다고 말씀 드렸는데요([스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80) 와 [소멸자에서 예외 발생 금지](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90%EC%97%90%EC%84%9C-%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D-%EA%B8%88%EC%A7%80) 참고), 

C++11 부터는 소멸자는 `noexcept` 가 생략되었더라도 `noexcept`로 동작하여 코딩 계약이 좀더 단단해 졌습니다.

# noexcept() 연산자

컴파일 타임에 `noexcept` 인지 검사합니다.

```cpp
void f() {
    throw 'a'; 
}
void g() noexcept {
}
EXPECT_TRUE(noexcept(f()) == false);   
EXPECT_TRUE(noexcept(g()) == true); 
```

