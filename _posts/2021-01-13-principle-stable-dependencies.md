---
layout: single
title: "#13. [개체지향 원칙] 안정적인 의존성(Stable Dependencies Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

안정적인 의존성 원칙은 **패키지 간의 의존성은 안정성의 방향이어야 한다** 라는 원칙입니다.

다음과 같은 의존 관계에서는 `Module B`가 안정적이어야 한다는 뜻입니다. `Module B`가 불안정하여 자주 수정된다면, `Module A`는 덩달아 자주 변경되고, 반복해서 테스트되어야 하는 문제가 발생하니까요.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1086c489-8b1d-415f-bcfe-dad7a77e3203)


하지만, `Module A`와 `Module B`가 계속 개발되는 경우라면, `Module B`가 안정적일 수 없습니다. 이럴 경우  `Interface`를 우선 정립하고, `Module A`와 `Module B` 간의 의존성 관계를 끊고 개발하는 것이 좋습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e27a7b45-a1aa-4f43-b4bb-955edd7057e8)