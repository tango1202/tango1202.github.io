---
layout: single
title: "#6. [개체지향 원칙] 의존성 역전 원칙(Dependency Inversion Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

의존성 역전 원칙은 **상위 수준 모듈은 하위 수준 모듈에 의존하지 말아야 한다** 는 원칙입니다.

조금 풀어 쓰면,

1. 상위 수준 모듈은 하위 수준 모듈을 직접 의존하지 말고,

2. 상위 수준과 하위 수준 모두 인터페이스에 의존하라

라는 뜻입니다. !!!둘 다!!!말이죠.

**위반 사례**

저장 기능이 필요한 Shape 개체가 있다고 합시다. Save()함수를 구현하고, 저장시에는 Xml을 이용한다고 봅시다. 그러면 다음과 같은 의존관계가 성립됩니다.

Shape->XmlWriter

하위 수준을 직접 참조하였으므로 의존성 역전원칙 위반입니다.

**해결 방법**

상위 수준과 하위 수준 모두 인터페이스를 의존하도록 하기와 같이 변경합니다.

Shape. IWriter XmlWriter

**의존성 주입**

Shape 이 Json등 다른 포맷을 지원하려고 할 경우, 하기와 같이 의존성 주입을 통해 손쉽게 확장할 수 있습니다. IWriter 인터페이스를 지원하는 JsonWriter만 개발하면 됩니다.








