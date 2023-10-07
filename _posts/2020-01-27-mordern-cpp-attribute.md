---
layout: single
title: "#27. [모던 C++] (C++11~) attribute(작성중)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `attribute`가 추가되어 컴파일러에게 부가 정보를 전달하는 방식을 표준화 했습니다.
> * (C++14~) `[[deprecated]]`가 추가되었습니다.
> * (C++17~) `[[fallthrough]]`, `[[nodiscard]]`, `[[maybe_unused]]` 가 추가되었습니다.
> * (C++17~) `[[msvc::noinline]]` 와 같이 제조사 네임스페이스를 사용할 수 있습니다.

# 개요

컴파일러에게 최적화 힌트나 경고 처리 힌트등 문법적으로 전달하기 어려운 정보를 컴파일러에 전달할때 기존에는 컴파일러 마다 자체적인 방식을 사용했습니다.(`__attribute__()`, `__declspec()` 등) 

C++11 부터는 `attribute`가 추가되어 부가 정보 전달을 표준화 했습니다.

`[[attribute]]`와 같이 `[[]]`사이에 `attribute`를 기재하면 됩니다.

```cpp
// 함수가 항상 예외를 throw하거나 종료합니다. 
// 컴파일러는 호출후 제어 연결을 할 필요가 없으므로, 이에 따른 최적화가 가능합니다.
[[noreturn]] void f() {throw "error";}

// 실제로는 리턴하므로 컴파일러는 경고를 발생합니다.
[[noreturn]] int g() {return 0;} // 경고. function declared 'noreturn' has a 'return' statement
```
# (C++17~) 네임스페이스

C++17 부터는 `[[msvc::noinline]]` 와 같이 제조사 네임스페이스를 사용할 수 있습니다.

# 표준 attribute

C++버전에 따라 다음의 표준 속성이 제공됩니다. 세부 내용은 [cppreference.com](https://en.cppreference.com/w/cpp/language/attributes)를 참고하기 바랍니다.


|항목|내용|
|--|--|
|`noreturn` (C++11~)|함수가 항상 예외를 `throw`하거나 프로그램을 종료합니다.|
|`carries_dependency` (C++11~)|(작성중)|
|`optimize_for_synchronized` (C++11~)|(작성중)|
|`deprecated` (C++14~)<br/>`deprecated("이유")`  (C++14~)|더이상 사용되지 않음을 표시합니다.|
|`fallthrough`  (C++17~)|`case A: break;`<br/>`case B:`<br/>`case C: break;`<br/>와 같이 `case B:`가 의도적으로 `case B:`를 실행함을 알립니다.|
|`nodiscard` (C++17~)|속성이 지정된 개체나 반환값이 무시되면 안됩니다.(에러 코드 리턴하는 함수에 사용하면 좋습니다)|
|`maybe_unused` (C++17~)|자가 진단을 위한 임시 변수(디버깅 모드에서만 사용하고, 런타임에 사용되지 않으면, 컴파일 경고가 발생합니다.)등 사용되지 않은 개체의 경고를 막습니다.|
|`nodiscard("이유")` (C++20~)|속성이 지정된 개체나 반환값이 무시되면 안됩니다.(에러 코드 리턴하는 함수에 사용하면 좋습니다)|
|`likely` (C++20~)<br/>`unlikely` (C++20~)|`if`나 `switch`에서 자주 사용하는 코드 조각을 알려주어 최적화 힌트를 제공합니다.|
|`no_unique_address` (C++20~)|멤버 변수를 다른 멤버변수나 부모 클래스 개체와 오버랩합니다.(아무 멤버 변수가 없는 개체를 최적화 할 수 있습니다.)|
|`assume("표현식")` (C++23~)|특정 상황에 표현식이 `true`가 되도록 가정합니다. 컴파일러는 이 가정을 신뢰하고 이에 따른 최적화를 합니다.(가정이 거짓일때 동작은 정의되지 않았습니다.)|