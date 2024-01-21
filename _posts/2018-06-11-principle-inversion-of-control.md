---
layout: single
title: "#11. [개체지향 원칙] 제어의 역전 원칙(Inversion of Control Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

제어의 역전 원칙은 ***외부 라이브러리의 제어를 따르라*** 라는 원칙입니다.

조금 풀어 쓰면,

1. [헐리우드 원칙](https://tango1202.github.io/principle/principle-hollywood/)과 유사하게
2. 프레임워크 라이브러리의 제어를 따르라.

라는 뜻입니다.

보통 라이브러리를 이용한 개발시 라이브러리에서 제공하는 함수들을 호출하여 작성합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cf4fb441-46d2-426a-9f9b-e5feaf157f13)

그런데, 프레임워크 라이브러리는 제어의 방법이 플랫폼과 밀접하게 연관되어 있어, 개발하면서 이를 직접 제어하기란 상당히 까다롭습니다. 

이에 대부분의 프레임워크 라이브러리는 모든 제어를 라이브러리 내에서 책임지고 수행하고, 꼭 필요한 것들만 개발자가 구현할 수 있도록 합니다.

하기의 방법을 이용해서요.

1. 프레임워크가 보내주는 이벤트 처리
2. 프레임워크가 허용하는 Listener 사용(*[헐리우드 원칙](https://tango1202.github.io/principle/principle-hollywood/) 참고*)
3. 프레임워크가 허용하는 **의존성 주입** (*[의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고*)
4. 프레임워크가 강제하는 [Template Method](https://tango1202.github.io/pattern/pattern-template-method/)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2ef49fd6-093d-4be9-b2b2-bdfb981d7b86)

상기와 같은 **코딩 계약** 에 의해 코드가 작성됨으로서, 프레임워크의 제어하에 있기에 제어의 역전이라 불립니다.

주로 프레임워크 라이브러리, UI 컨트롤 라이브러리 등에서 사용합니다.

