---
layout: single
title: "#1. [고전 C++ 가이드] 동적 예외 사양"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 나열된 예외 이외에는 `unexpected`로 강제 변환한다. 사용하지 마라.

**개요**

함수 정의시 하기와 같이 `throw`를 사용하여 함수가 발생하는 예외 목록을 한정할 수 있습니다.

```cpp
void f() throw(error1, error2) {

}
```

하지만 상기 코드는 사실 하기와 같습니다.

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
    catch(...) {
        throw unexpected(); 
    }
}
```

즉, `error1`과 `error2` 만 방출하게 하기 위해서, 억지로 다른 예외를 unexpected로 강제 변환합니다. 이러다 보니 불필요하게 `try`-`catch` 만 추가되어 실행속도만 느려집니다. 사용하지 마세요.
