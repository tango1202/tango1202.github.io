---
layout: single
title: "#12. [개체지향 원칙] 관심사의 분리 원칙(Separation of Concerns Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

관심사의 분리 원칙은 **구별되는 부분들로 분리시켜라** 라는 원칙입니다.

조금 풀어 쓰면,

1. [단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/) 과 유사하지만, 좀더 상위 레벨에서, 서로간에 책임을 공유하지 않도록,
2. 모듈(개체, 컴포넌트, 서비스, 함수), 프로젝트, 폴더 등을 레이어링하라.

라는 뜻입니다.

이 원칙을 준수하여 설계하면,

1. 레이어링 한 부분에 구현과 책임이 응집되어 유지보수성이 향상됩니다.
2. 레이어링 한 부분에만 관심사가 집중되어 안정성이 향상됩니다.
3. 각각의 관심사가 독립적으로 응집되어, 다른 시스템과의 확장이 용이합니다.
4. 대규모 프로젝트의 경우 역할 분담이 용이합니다.

**준수 방법 : 가로 분리**

어플리케이션 내에 기반과 응용의 계층으로 분리하는 경우입니다. 주로 리소스 레이어 - 비지니스 레이어 - 프레젠테이션 레이어로 분리합니다. 상위 레이어는 하위 레이어에 의존합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d0881244-8f2c-4a62-bd7f-67958abfac6f)

**준수 방법 : 세로 분리**
어플리케이션 내에서 각각을 기능 모듈로 분리하는 경우에 사용합니다. 수평적인 관계에서는 상호 의존하지 말아야 합니다. 기능을 플러그인(혹은 애드인)으로 추가할 수 있듯이, 각각의 기능을 독립시키는게 좋습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0bcda4b1-6759-47ec-a9a1-ca85c99ac148)

**준수 방법 : 가로/세로 분리**

각 모듈들을 가로 분리를 적용하면, 하기와 같이 분리할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2f2a242c-4eb0-4891-8319-c02bc8518e9c)

또한, 각 모듈이 공통으로 사용하는 기반 모듈이나 인터페이스가 있다면, 하기와 같이 분리할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1dd70803-1338-4156-97f1-4155a2e3ddda)


