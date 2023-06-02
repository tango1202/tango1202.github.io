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

이제, 강아지, 고양이, 물고기, 새를 구현하려면, 각 동물들의 특성에 맞게 할 수 있은 것과 없는 것이 있습니다.

예를 들어 날 수 있는 강아지는 없으니 억지로 하기와 같이 `Dog`을 구현할 수 밖에 없습니다.

자식에서 쓸데없이 함수 구현을 하므로, 인터페이스 분리 원칙 위반입니다. `IAnimal`에 동물들의 다양한 특성들을 넣어 뚱뚱한 인터페이스를 만들지 말고, 각 특성별로 하기 그림처럼 날씬하게 만들어 구성하는게 좋습니다.


IAnimal
born, dead

IEatable
IWalkable
ISwimmable
IFlyable

`Dog`은 살고, 죽고, 먹을 수 있고, 걸을 수 있고, 수영할 수 있으므로 하기와 같이 구현할 수 있습니다.

```cpp

class Dog :
   public IAnimal,
   public IEatable,
    
...

