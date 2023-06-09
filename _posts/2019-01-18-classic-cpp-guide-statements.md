---
layout: single
title: "#18. [고전 C++ 가이드] 제어문"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 코드 분석을 위해 제어의 중첩을 최소화 하라.(조건 상태표를 활용하라.)
> * 예외에 안정적일 수 있도록 사전 조건 검사를 수행하라.
> * `goto`는 코드 분석을 방해하니 사용하지 마라.
 
# 개요

|항목|내용|
|--|--|
|`if (조건) {표현식;}`| 조건이 참일 경우 표현식을 실행|
|`if (조건) {표현식1;}`<br/> `else {표현식2;}`| 조건이 참일 경우 표현식1을 실행하고, 거짓일 경우 표현식2를 실행|
|`switch(조건) {`<br/>`case 상수1:`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`break;`<br>`case 상수2:`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`break;`<br/>`default:`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`break;`<br/>`}`|조건과 매칭되는 `case` 문 으로 분기.<br/>* 조건 : 정수 또는 열거형 상수<br/>* `break` : 해당 `case` 표현식을 실행하고 `switch`문 탈출<br/>* `default` : 매칭되는 `case`문이 없는 경우 실행|
|`while (조건) {표현식;}`|조건이 참이면 표현식 반복 실행|
|`do {표현식;} while(조건);`|표현식 실행후 조건 검사 하여, 조건이 참이면 표현식 반복 실행|
|`for (초기문; 조건문; 표현식2;) {표현식1;}`|`초기문;`<br/>`while (조건문) {`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식1;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식2;`<br/>`}`<br/> 의 순서로 반복
|`continue;`|반복문의 범위 끝으로 이동하여 다음 조건 검사|
|`break;`|반복문 탈출|
|`return;`|함수 종료|
|`goto LABEL;`|지정한 `LABEL`로 강제 이동(`LABEL:`와 같이 콜론으로 표기함)<br/>코드 분석을 방해하기 때문에 비권장|

# 중첩된 제어문

제어문이 중첩되면, 코드 분석이 어려워 집니다.

```cpp
if (a) {
    if (b) {
        if (c) {
            f(); // (△) 비권장. 분석하기 어렵습니다.
        }
    }
}
```

보다는 

```cpp
if (a && b && c) {
    f();
}
```

가 낫습니다.

# 제어 판단과 실행의 구분(조건 상태표)

복잡한 제어 구조를 가진 경우, 상태를 판단하는 역할(조건 상태표 기준으로 계산)과 실행하는 역할을 분리하면, 구조가 간결해 질 수 있습니다.

```cpp
if (a && b) { // (△) 비권장. if 중첩이 많아 분석하기 힘듭니다.
    if (c || d) {
    }
    else {
        if (b && e) {
        }
        else {
        }
    }
}
else {
    if (c || d) {
        if (e || f) {
        }
        else {
        }
    }
    else {
    }
}
```

보다는

```cpp
State CalcState() {
    if (a && b && (c || d)) {return State::A;}
    if (a && b && !(c || d) && (b && e)) {return State::B;}
    if (a && b && !(c || d) && !(b && e)) {return State::C;}
  
    if (!(a && b) && (c || d) && (e || f)) {return State::D;}
    if (!(a && b) && (c || d) && !(e || f)) {return State::E;}
    if (!(a && b) && !(c || d)) {return State::F;}

    assert(false);
}

State state = CalcState(); // 조건 상태표에 따라 상태를 리턴합니다.

switch (state) {
    case State::A: break;
    case State::B: break;
    case State::C: break;
    case State::D: break;
    case State::E: break;
    case State::F: break;
    default: assert(false); break;
}
```

가 낫습니다.

# `if`와 `else`

`if`는 `else` 상황을 동반할 때가 많습니다. 단순한 `NULL` 검사인 경우는 상관없습니다만, 복잡한 상황일 경우 `else` 인 경우의 처리가 정말 필요 없어서 생략한 건지, 실수로 빼먹은 건지 혼동될 수 있으니, `else` 상황에 대해 주석으로나마 고려한 흔적을 남기는게 좋습니다. 

```cpp
if (condition) { // (△) 비권장. else문이 고려되었는지 판단하기 어렵습니다.
} 
```

보다는

```cpp
if (condition) {
}
else {
    assert(false); // (O) "해당 case 절대 없음"
}
```

가 낫습니다.

# 사전 조건 검사

함수내에서 본문을 실행하기 전에, 실행하기에 적합한 상태인지 검사하고(인자 조건은 맞는지 등) 본문을 실행하는게 좋습니다. 그렇지 않고 뒤죽박죽 쓰다보면 예외 안정적인 함수를 만들기 어려워 집니다.

```cpp
ErrorCode f(param1, param2) {
    if (param1 is OK) {
        표현식1;
    }
    if (param2 is FAIL) {
        return ErrorCode; // (△) 비권장. 이미 실행된 표현식1을 되돌리기 어렵습니다.
    }
    else {
        표현식2;
    }
}
```

위와 같이 작성한다면, `param2`가 부적절한 상황이어서 `ErrorCode` 를 리턴할때 이미 실행된 `표현식1`을 되돌리기가 어렵습니다. 

이렇게 작성하기 보다는,

```cpp
ErrorCode f(param1, param2) {
    if (param1 is FAIL) {
       return ErrorCode;
    }
    if (param2 is FAIL) {
        return ErrorCode; 
    }

    표현식1;
    표현식2;
    ...
}
```

가 낫습니다.

# 일관된 조건 검사

조건의 참/거짓이 일관된 표현으로 작성되는게 좋습니다.

```cpp
if (!condition1) {return;}
if (!condition2) {return;}
if (condition3) {return;} // (△) 비권장. 코딩 실수처럼 보입니다.
if (!condition4) {return;}
```

보다는

```cpp
if (!condition1) {return;}
if (!condition2) {return;}
if (!condition3) {return;} 
if (!condition4) {return;}
```

으로 `condition3`의 참/거짓 논리를 수정하는게 좋습니다.

# 일관된 대소 비교

대소 비교는 일관된 표현으로 작성하는게 코드 분석에 도움이 됩니다.

```cpp
if (a < b) {
}
if (c > d) { // (△) 비권장. 비교에 일관성이 없어 코드 분석시 헷갈리게 됩니다.
}
```

보다는

```cpp
if (a < b) {
}
if (d < c) { 
}
```
가 낫습니다.


