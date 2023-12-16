---
layout: single
title: "#4. [Legacy C++ 가이드] 자가 진단과 진단 최소화"
categories: "legacy-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [사전 가정](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8)과 [사후 가정](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8)을 진단하라.
> * 진단 코드가 최소화 되도록, 포인터 보다는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용하고, 코딩 계약을 단단하게 만들어라.

> **Mordern C++**
> * (C++11~)[static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/)가 추가되어 컴파일 타임 진단이 가능해 졌습니다.

# 개요 

설계한 기능이 올바르게 사용되는지 진단하기 위해 [assert()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/)를 사용할 수 있습니다. [assert()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/)는 디버그 모드에서만 작동하고, 그외에는 작동하지 않습니다.

```cpp
#ifdef NDEBUG
#  define assert(condition) ((void)0)
#else
#  define assert(condition) /*implementation defined*/
#endif
```

[assert()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/)는 `condition`이 `0`(`false`) 이면 주어진 수식이 표시됩니다. 메시지를 함께 표시하고 싶으면, `&&`을 사용할 수 있습니다.

```cpp
int a = 10;
assert(a == 10); // 참이므로 표시 안함
assert(a == 11); // 거짓이므로 표시함

// 문자열 포인터 값은 0이 아닌 임의의 수치값인 참이므로,
assert(a == 10 && "Test Message"); // 참 && 참 은 참이므로 표시 안함
assert(a == 11 && "Test Message"); // 거짓 && 참은 거짓이므로 표시함
```

# 공격적 자가진단

코드의 품질 향상과 디버깅시 문제 지점을 쉽게 찾기 위해 함수에 진단 코드를 삽입하는게 좋습니다. 꼼꼼하게 작성할 수록 나중에 디버깅이 편해집니다. 

특히 

1. [사전 가정](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8) : [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 전달된 인수가 유효한 값인지, 가정한 값과 같은지,
2. [사후 가정](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8) : 연산 결과나 수행 결과가 기대치와 맞는지
3. 오버 플로우, 실수 연산 가정 : 주어진 연산의 결과가 오버 플로우는 없는지, 실수 연산시 오차 범위 내인지 
4. 인프라 가정 : 파일, 네트워크, 데이터베이스 상황이 가정과 맞는지

등의 진단 코드를 작성해 두는게 좋습니다.

만약 다음 `f()` 함수의 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)인 `a`, `b`, `c`가 특정 범위 값만 지원한다면,

```cpp
int f(int a, int b, int* c) {
    int result = a + b + *c;

    return result;
}
```

상기 코드를 다음처럼 [assert()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/)와 [throw](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 작성하는게 안전합니다.

```cpp
int f(int a, int b, int* c) {

    // 사전 가정
    assert(0 <= a && a < 10);
    assert(10 <= b && b < 20);
    assert(c != NULL);
    assert(20 <= *c && *c < 30);

    if (!(0 <= a && a < 10)) {
        throw std::range_error("Fail : a");
    }
    if (!(10 <= b && b < 20)) {
        throw std::range_error("Fail : b");
    }
    if (!(c != NULL)) {
        throw std::invalid_argument("Fail : c != NULL");
    }  
    if (!(20 <= *c && *c < 30)) {
        throw std::range_error("Fail : c");
    }    

    int result = a + b + *c;

    // 사후 가정
    assert(30 <= result && result <= 9 + 19 + 29);

    return result;
}
```

# 최소 가정

가정이 최소화 되면 진단하는 부분도 줄어들며 코드의 가독성, 유지보수성, 사용 용이성은 향상됩니다.

1. 불필요한 널검사가 없도록 합니다. 
   
    ```cpp
    void f(T* obj) {
        assert(obj != NULL); 
        if (!obj) return; // 런타임 시에 이런 탈출 코드가 없다면, 다운될 수 있습니다.
        ...
    } 
    ```
    
    보다는, [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용하는게 좋습니다.

    ```cpp
    void f(T& obj) {
        // assert가 필요없습니다.
        // 널검사 후 탈출 코드가 필요없습니다.
        ...
    }
    ```

2. 데이터를 [추상화](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/)하여 코딩 계약을 좀더 단단하게 만듭니다.

    ```cpp
    void f(int angle) {
        assert(0<= angle && angle < 360);
        if (!(0 <= angle && angle < 360)) return;
        ...
    }   
    ```

    보다는, 내부에서 처리할 수 있는 보정을 수행하여,

    ```cpp
    void f(int angle) {
        angle = Normalize(angle); // 보정할 수 있다면 보정합니다.
    }                                          
    ```

    보다는, 인수가 유효한 값임을 보증할 수 있도록 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하는게 낫습니다.

    ```cpp
    void f(const Degree& angle) {
        // assert가 필요없습니다.
        // 널검사 후 탈출 코드가 필요없습니다.
        // 보정은 Degree 개체에서 합니다.
        ...
    }   
    ```

