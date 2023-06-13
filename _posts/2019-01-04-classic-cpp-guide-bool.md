---
layout: single
title: "#4. [고전 C++ 가이드] BOOL과 bool"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

|항목|BOOL|bool|
|--|--|--|
|정의|Visual C++ `typedef int BOOL;`|C++ 표준 자료형|
|값|`#define TRUE 1`, `#define FALSE 0`| true, false|
|용량|`sizeof(BOOL) == sizeof(int)`|`1 <= sizeof(bool) <= sizeof(long)`(Visual C++ 은 1byte)|

Visual C++는 `bool`이 없던 시절부터 `BOOL`을 사용하여, 이전 `BOOL`의 잔재가 많이 남아 있습니다. 하지만 `bool`을 쓰는게 더 합리적입니다.

**BOOL이 만들어진 이유**

1. win32 API에 bool이 없어서 만들어짐.(C99 에서 뒷북으로 bool이 들어갔데요)

**여전히 BOOL을 사용하는 이유**

1. 이전에 사용된 코드에 너무 많이 있어서(`bool`로 리팩토링 하세요.)
2. 이전 코드 리팩토링이 엄두가 안남(`bool`로 리팩토링 하세요. 오늘 시작해야 그나마 적습니다.)
3. 지독한 손맛(습관을 바꾸세요)

**bool로 고쳐야 하는 이유**

1. 참/거짓만을 다룹니다.
    BOOL은 다른 값을 가질 수 있어서 안전하게 비교하려면 하기와 같이 해야 합니다.
    ```cpp
    BOOL result 
    ```
3. BOOL 

