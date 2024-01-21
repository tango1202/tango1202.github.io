---
layout: single
title: "#18. [개발 원칙] 스스로 반복하지 마라(Don't Repeat Yourself)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[스스로 반복하지 마라 원칙](https://tango1202.github.io/principle/principle-dont-repeat-yourself/)은 ***추상화를 통해 논리의 중복을 제거하라*** 라는 원칙입니다. 흔히 ***DRY*** 라고도 불립니다.

이를 위반하면 다음과 같은 문제가 발생합니다.

1. 중복된 코드가 쌓이면, 눈덩이처럼 불어납니다.
2. 한군데에서 수정된 코드가 중복된 다른 코드로 전파되지 않아 코드 파편화를 초래하고 잠재적 오류가 됩니다.

**위반 사례**

1. 게을러서 [복사 붙여넣기](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B3%B5%EC%82%AC-%EB%B6%99%EC%97%AC%EB%84%A3%EA%B8%B0copy-paste-programming) 된 코드
2. 주석, 코드, 설계서의 설명 반복
3. 잘못된 인터페이스 설계에 의한 강요(*[인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/) 위반*)
4. 개발자간 의사소통 부재에 따른 중복

등이 있을 수 있습니다.

**준수 방법**

1. 코드를 [복사 붙여넣기](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B3%B5%EC%82%AC-%EB%B6%99%EC%97%AC%EB%84%A3%EA%B8%B0copy-paste-programming) 하지 마세요.
2. `if`의 반복, `switch`반복, 변수 정의, 연산처리 등 작은 코드 조각도 허용하지 마세요. 함수화 하세요. 모든 지식을 총 동원해서 중복을 제거하세요.
   * 비율 계산 연산인 `a * b / c` 가 반복된다면, `MulDiv(a, b, c)` 함수를 만드세요.
3. [Template Method 패턴](https://tango1202.github.io/pattern/pattern-template-method/)으로 중복되지 않은 코드만 구현하세요. 
4. [제어의 역전 원칙](https://tango1202.github.io/principle/principle-inversion-of-control/)으로 제어 코드를 공통화 하고, 개발자가 꼭 필요한 코드만 구현할 수 있게 하세요. 
5. [인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/)으로 작은 단위 인터페이스를 사용하세요.
6. 팀원간 의사소통 채널을 마련하세요.

