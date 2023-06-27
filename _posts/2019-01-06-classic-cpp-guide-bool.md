---
layout: single
title: "#6. [고전 C++ 가이드] BOOL과 bool"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `BOOL`보다는 `bool`을 사용하라.

**개요**

|항목|BOOL|bool|
|--|--|--|
|정의|Visual C++ `typedef int BOOL;`|C++ 표준 자료형|
|값|`#define TRUE 1`,<br>`#define FALSE 0`| true,<br>false|
|용량|`sizeof(BOOL) == sizeof(int)`|`1 <= sizeof(bool) <= sizeof(long)`(Visual C++ 은 1byte)|

Visual C++은 `bool`이 없던 시절부터 `BOOL`을 사용하여, 이전 `BOOL`의 잔재가 많이 남아 있습니다. `BOOL` 쓰지 마시고, `bool` 쓰세요. 그게 **코딩 계약** 상 안전합니다.

**BOOL이 만들어진 이유**

win32 API에 `bool`이 없어서 만들어 졌습니다.(C99가 되어서야 뒷북으로 `bool`이 들어갔데요.)

**여전히 BOOL을 사용하는 변명**

1. 이전에 사용된 코드에 `BOOL`이 너무 많이 있어서 `BOOL` 사용을 강제합니다.(`bool`로 리팩토링 하세요.)
    
    다음과 같이 `BOOL`을 리턴하는 함수가 있다면
    
    ```cpp
    BOOL result = f();
    ```

    아쉽지만 하기처럼 이라도 `bool`을 써보세요. 적어도 우리가 작성하는 코드에서는 `BOOL`로 강제하지 않게요.

    ```cpp
    bool result = f() ? true : false;
    ```

2. 이전 코드 리팩토링이 엄두가 안나요.(`bool`로 리팩토링 하세요. 오늘 시작해야 그나마 적습니다.)
3. `BOOL`이 시스템 데이터 처리 바이트 수(`int`)와 동일하여 `bool` 보다 빠릅니다.(이러한 미세한 속도조차 아쉬운 프로젝트라면, 어셈블리어를 사용하세요.)
4. 지독한 손맛이라 나도 모르게 `BOOL`을 사용해요.(습관을 바꾸세요.)

**BOOL의 문제점**

`bool`은 참/거짓만을 다루는 신뢰할 수 있는 **코딩 계약** 을 만들어 줍니다. 하지만 `BOOL`은 정수를 담을 수 있는 `int`입니다.

하기와 같이 단순하게 사용하면 `BOOL`은 큰 문제가 없어 보이죠.

```cpp
BOOL b = TRUE;
EXPECT_TRUE(!b == FALSE); // !b == false이고 false를 int로 캐스팅하면 FALSE(0)

b = FALSE;
EXPECT_TRUE(!b == TRUE); // !b == true이고 true를 int로 캐스팅하면 TRUE(1)
```

하지만 `BOOL`은 사실은 `int`형이라 `TRUE(1)`과 `FALSE(0)` 외에 다른 값을 가질 수 있어서 걱정이 되는 경우가 좀 생깁니다.

```cpp
BOOL result1 = f1();
BOOL result2 = f2();
```

상기와 같이 `result1`과 `result2`가 모두 참인 경우 진입하는 `if`문이 필요하다고 합시다. 간단히 다음과 같이 할 수 있습니다.

```cpp
// (O) 결과가 둘다 FALSE가 아니라면 진입합니다. 믿을 수 있어요.
if (result1 && result2) {}
```

이번에는 둘다 참이거나, 둘다 거짓일때 진입하는 `if`문이 필요하다고 합시다. 다음처럼 하면 될까요?

```cpp
// (O) 결과가 둘다 FALSE 라면 진입합니다.
// (X) 오동작. 결과가 둘다 FALSE 가 아니라면 진입할까요? 하나는 1이고, 하나는 2라면 진입 못합니다.
if (result1 == result2) {}
```

`BOOL`은 `int`형이라 `TRUE(1)`와 `FALSE(0)` 외에 다른 값을 가질 수 있다는 걸 상기해 보십시요. 실제로 Windows API는 `0`과 `1`외에 다른 정수값을 리턴하는 경우가 가끔 있습니다.

[Windows API `GetMessage()`](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getmessage) 를 보시면 `0`과 `-1`을 리턴합니다.

`BOOL`을 신뢰하지 마십시요.

억지로 하기와 같이 `!!`을 붙여 `bool`형으로 바꿔서 검사할 수는 있습니다만, 참 보기 싫죠. 

```cpp
// (O) 결과가 둘다 FALSE 라면 진입합니다.
// (O) 결과 둘다 FALSE 가 아니라면 진입합니다.
if (!!result1 == !!result2) {}
```

그러니 

1. 가독성을 위해
2. `true`/`false`만 저장하는 **코딩 계약** 을 위해

`bool` 을 사용하세요.
