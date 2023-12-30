---
layout: single
title: "#1. [개체지향 원칙] 5대 원칙(SOLID)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

좋은 설계는 유지보수의 편의성을 위하여

* 가독성,
* 유연성, 확장성, 이식성
* 모듈성, 캡슐화(*잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*)
 
가 좋아야 합니다.

이를 위한 다양한 설계 방법론들이 있으며, 그중 SOLID(다음 5개 원칙의 앞글자들)가 가장 기본적으로 사용됩니다. 개체 지향 구조 설계나 개발시 다음 항목들을 유념해서 진행하시기 바랍니다.

|항목|내용|
|--|--|
|[단일 책임 원칙(Single Responsibility Principle)](https://tango1202.github.io/principle/principle-single-responsibility/)|개체는 단 하나의 책임만 가져야 한다.|
|[개방-폐쇄 원칙(Open/Closed Principle)](https://tango1202.github.io/principle/principle-open-closed/)|확장에는 열려 있되, 수정에는 닫혀 있게 작성하라.|
|[리스코프 치환 원칙(Liskov Substitution Principle)](https://tango1202.github.io/principle/principle-liskov-substitution/)|자식 개체는 부모 개체를 완전하게 치환할 수 있어야 한다.(그러니, 자식은 부모와 다른 행동을 하지 말고, 같은 행동을 하게 하라.)|
|[인터페이스 분리 원칙(Interface Segregation Property)](https://tango1202.github.io/principle/principle-interface-segregation/)|클라이언트는 자신이 사용하지 않는 것에 강제로 의존하지 말아야 한다.(그러니, 뚱뚱한 인터페이스는 날씬한 인터페이스들로 분리하라.)|
|[의존성 역전 원칙(Dependency Inversion)](https://tango1202.github.io/principle/principle-dependency-inversion/)|상위 수준 모듈은 하위 수준 모듈에 의존하지 말아야 한다.(그러니, 상위 수준과 하위 수준 모두 인터페이스에 의존하라.)|

각 원칙을 준수하면 하기가 개선됩니다.

1. 코드 가독성
2. 코드 분석 용이성
3. [예외 안전성](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)
4. 기능 수정 및 추가 등 유연성
5. 유지보수시 복잡성(응집력 강화, 결합도 및 의존성 저하)
6. 계약 준수에 따른 사용성, 구현 편의성, 신뢰성, 안정성


