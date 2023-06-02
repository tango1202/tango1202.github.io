---
layout: single
title: "#5. [개체지향 원칙] 인터페이스 분리 원칙(Interface Segregation Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

인터페이스 분리 원칙은 **클라이언트는 자신이 사용하지 않는 함수에 강제로 의존하지 말아야 한다** 는 원칙입니다.

조금 풀어 쓰면,

1. 부모 클래스에서 자식에게 쓸데없는 함수 구현을 강요하지 말고,

2. 자식이 꼭 필요한 것만 구현하게 하며,

3. 이를 위해 뚱뚱한 인터페이스는 날씬한 인터페이스들로 분리하라

라는 뜻입니다. !!!자식이 꼭 필요한 것만 구현하도록!!!말이죠.

동물에 관련된 lAnimal 인터페이스를 하기와 같이 구현했다고 합시다.


IAnimal

Born
Dead
Eat
Walk
Swim
Fly

이제, 강아지, 고양이, 물고기, 새를 구현하려면,



