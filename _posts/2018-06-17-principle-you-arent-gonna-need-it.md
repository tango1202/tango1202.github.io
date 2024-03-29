---
layout: single
title: "#17. [개발 원칙] 필요할때 구현하라(You Aren't Gonna Need It)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[필요할때 구현하라](https://tango1202.github.io/principle/principle-you-arent-gonna-need-it/)는 ***항상 실제로 필요할때 구현해야 하며, 나중에 필요하다고 예상하고 미리 구현하지 마라*** 라는 원칙입니다.(*앞글자를 따서 **YAGNI** 라고도 합니다.*)

일반적으로,

1. 구현해도 결국 필요하지 않거나,
2. 예상해서 작성한 코드를 분석하지 못해 새로 만들거나,
3. 예상이 빗나갔거나,
4. 예상했던 환경이 변했거나

뭐 그래서 결국 다시 개발합니다.

그래서 필요할때 구현하면,

1. 필요하지 않은 코드를 작성하지 않기 때문에 시간이 절약됩니다.
2. 불필요한 코드가 없기 때문에, 코드 분석과 컴파일 시간이 절약됩니다.

그럼에도 불구하고, 예상하고 미리 구현하는 이유는,

1. 나중에 시간이 절약될 수 있다고 믿기 때문이거나,
2. 지금 작성한 코드를 잊은 뒤 나중에 활용할 때, 분석하는 시간이 두렵기 때문입니다.

그냥 분석하기 쉽게 지금 코드를 리팩토링 하세요.(*[단순하게 유지하라](https://tango1202.github.io/principle/principle-keep-it-simple/) 참고*)