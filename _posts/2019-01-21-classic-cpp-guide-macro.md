---
layout: single
title: "#21. [고전 C++ 가이드] 매크로(Macro)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 매크로를 쓰지 말고 열거형 상수를 사용하라.
> * 매크로 함수를 쓰지 말고 인라인 함수를 사용하라.
> * typedef
>
```cpp
 #ifdef MACRO_NAME
expr;
#else
expr;
#endif
```
전처리 컴파일 매크로. 매크로 정의 상태에 따라 선택적 컴파일. C++ 문법적으로 극복할 수는 있으나, 아직은 참을 수 없는 유혹


동료들을 미치게 하는 방법
```cpp
#define public private 
#define class enum
#define GetData 
#define std hello 
#define GetObject // 이건 정말 WindowAPI 에 있다
#define GetForm // 이것도 정말 WindowAPI 에 있네
```