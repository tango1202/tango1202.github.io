---
layout: single
title: "#18. [개발 원칙] 스스로 반복하지 마라(Don't Repeat Yourself)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

스스로 반복하지 마라 원칙은 **추상화를 통해 논리의 중복을 제거하라** 라는 원칙입니다. 흔히 **DRY** 라고도 불립니다.

1. 중복된 코드가 쌓이면, 눈덩이 처럼 불어납니다.
2. `if`의 반복, `switch`반복, 변수 정의, 연산처리 등 작은 코드 조각도 허용하지 마세요.

**준수 방법**

1. 코드를 복사-붙여넣기 하지 마세요.
2. [템플릿 메서드 패턴](https://tango1202.github.io/pattern/pattern-template-method/)으로 중복되지 않은 코드만 구현하세요. 
3. [제어의 역전 원칙](https://tango1202.github.io/principle/principle-inversion-of-control/)으로 제어 코드를 공통화 하고, 개발자가 꼭 필요한 코드만 구현할 수 있게 하세요. 
4. [인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/)으로 작은 단위 인터페이스를 사용하세요.

