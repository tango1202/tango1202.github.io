---
layout: single
title: "#19. [Legacy C++ 가이드] 제어문"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 코드 분석을 위해 [제어의 중첩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-statements/#%EC%A4%91%EC%B2%A9%EB%90%9C-%EC%A0%9C%EC%96%B4%EB%AC%B8)을 최소화 하라.(*[조건 상태표](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-statements/#%EC%A0%9C%EC%96%B4-%ED%8C%90%EB%8B%A8%EA%B3%BC-%EC%8B%A4%ED%96%89%EC%9D%98-%EA%B5%AC%EB%B6%84%EC%A1%B0%EA%B1%B4-%EC%83%81%ED%83%9C%ED%91%9C)를 활용하라.*)
> * [예외에 안전](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)할 수 있도록 [사전 조건 검사](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-statements/#%EC%82%AC%EC%A0%84-%EC%A1%B0%EA%B1%B4-%EA%B2%80%EC%82%AC)를 수행하라.
> * `goto`는 코드 분석을 방해하니 사용하지 마라.

> **Mordern C++**
> * (C++11~) [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)가 추가되어 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 요소의 탐색 처리가 쉬워졌습니다.
> * (C++17~) [초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch)가 추가되어 함수 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 평가하고 소멸하는 코드가 단순해 졌습니다.
> * (C++20~) [범위 기반 for()에서 초기식](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c20-%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for%EC%97%90%EC%84%9C-%EC%B4%88%EA%B8%B0%EC%8B%9D)이 추가되었습니다.

# 개요

조건식에 따라 분기하거나 반복하는 제어문들을 제공합니다.

단순한 조건 검사에서 복잡한 로직 구현까지 두루두루 제어문이 사용되는데요, STL 에서는 [시퀀스 처리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85), [정렬](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%A0%95%EB%A0%AC-%EC%9E%91%EC%97%85), [검색](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%9D%B4%EC%A7%84-%EA%B2%80%EC%83%89-%EC%9E%91%EC%97%85), [수학](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85) 등 일반화된 [알고리즘](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/)들을 제공합니다. 한번 훑어보시고, 필요한 것이 있다면 [알고리즘](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/)을 활용하시는 게 유지보수 측면에서 좋습니다.

|항목|내용|
|--|--|
|`if (조건) {표현식;}`| 조건이 참일 경우 표현식을 실행|
|`if (조건1) {표현식1;}`<br/>`else if (조건2) {표현식2;}`<br/>`else {표현식3;}`| 조건1이 참일 경우 표현식1을 실행하고, 거짓일 경우 조건2가 참일 경우 표현식 2를 실행하고, 거짓일 경우 표현식3을 실행|
|`switch(조건) {`<br/>`case 상수1:`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`break;`<br>`case 상수2:`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`break;`<br/>`default:`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`break;`<br/>`}`|조건과 매칭되는 `case` 문 으로 분기.<br/>* 조건 : 정수 또는 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)<br/>* `break` : 해당 `case` 표현식을 실행하고 `switch`문 탈출<br/>* `default` : 매칭되는 `case`문이 없는 경우 실행|
|`while (조건) {표현식;}`|조건이 참이면 표현식 반복 실행|
|`do {표현식;} while(조건);`|표현식 실행후 조건 검사 하여, 조건이 참이면 표현식 반복 실행|
|`for (초기식; 조건식; 표현식2;) {표현식1;}`|`초기식;`<br/>`while (조건식) {`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식1;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`표현식2;`<br/>`}`<br/> 의 순서로 반복
|`continue;`|반복문의 범위 끝으로 이동하여 다음 조건 검사|
|`break;`|반복문 탈출|
|`return;`|함수 종료|
|`goto LABEL;`|지정한 `LABEL`로 강제 이동(`LABEL:`와 같이 콜론으로 표기함)<br/>코드 분석을 방해하기 때문에 비권장|

> *(C++17~) [초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch)가 추가되어 함수 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 평가하고 소멸하는 코드가 단순해 졌습니다.*

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

복잡한 제어 구조를 가진 경우, 상태를 판단하는 역할(*[조건 상태표](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-statements/#%EC%A0%9C%EC%96%B4-%ED%8C%90%EB%8B%A8%EA%B3%BC-%EC%8B%A4%ED%96%89%EC%9D%98-%EA%B5%AC%EB%B6%84%EC%A1%B0%EA%B1%B4-%EC%83%81%ED%83%9C%ED%91%9C) 기준으로 계산*)과 실행하는 역할을 분리하면, 구조가 간결해 질 수 있습니다.

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

# if와 else

`if`는 `else` 상황을 동반할 때가 많습니다. 단순한 널검사인 경우는 상관없습니다만, 복잡한 상황일 경우 `else` 인 경우의 처리가 정말 필요 없어서 생략한 건지, 실수로 빼먹은 건지 혼동될 수 있으니, `else` 상황에 대해 주석으로나마 고려한 흔적을 남기는게 좋습니다. 

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

함수내에서 본문을 실행하기 전에, 실행하기에 적합한 상태인지 검사(*[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 조건은 맞는지 등*)한 뒤 본문을 실행하는게 좋습니다. 그렇지 않고 뒤죽박죽 쓰다보면 [예외에 안전한 코드](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)를 만들기 어려워 집니다.

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


