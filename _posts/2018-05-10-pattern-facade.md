---
layout: single
title: "#10. [디자인 패턴-구조 패턴] Facade"
categories: "pattern"
tag: ["디자인 패턴", "구조 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Facade](https://tango1202.github.io/pattern/pattern-facade/)는 서브시스템의 클래스들이 복잡하게 얽혀있고 사용이 어려운 경우, 단순하고 일관된 상위 수준의 통합 인터페이스를 제공합니다. 외부에서 내부 서브시스템을 몰라도 되므로, 사용하기 쉬워집니다.

# 설명

다음 그림에서 `Client`는 서브시스템의 클래스인 `A, B, C, D, E`를 직접 실행하지 않고, `Facade`를 통해 실행합니다. [묻지 말고 말하라 원칙](https://tango1202.github.io/principle/principle-tell-dont-ask/)의 대표적인 구현 구조라 할 수 있겠습니다.

![Facade](https://github.com/tango1202/tango1202.github.io/assets/133472501/61da2aba-bb2f-4b3b-b2e4-f51af2c19799)


# 특징

서브시스템을 쉽게 사용할 수 있으며, 서브시스템과 `Client`코드간의 결합도를 약하게 만드는 효과가 있습니다.

또한, 모듈의 경계에서 [Facade](https://tango1202.github.io/pattern/pattern-facade/)를 만들어 [Facade](https://tango1202.github.io/pattern/pattern-facade/) 바깥으로 [예외가 전파](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)되지 않도록 [예외 레이어링](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%A0%88%EC%9D%B4%EC%96%B4%EB%A7%81)을 하면 좋습니다.