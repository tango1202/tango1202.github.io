---
layout: single
title: "#5. [개체지향 원칙] 인터페이스 분리 원칙(Interface Segregation Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/)은 ***클라이언트는 자신이 사용하지 않는 것에 강제로 의존하지 말아야 한다*** 는 원칙입니다.

조금 풀어 쓰면,

1. 부모 클래스에서 자식에게 쓸데없는 함수 구현을 강요하지 말고,
2. 자식이 꼭 필요한 것만 구현하게 하며,
3. 이를 위해 뚱뚱한 인터페이스는 날씬한 인터페이스들로 분리하라.

라는 뜻입니다. !!!자식이 꼭 필요한 것만 구현하도록!!!말이죠.

이 원칙을 준수하면,

1. 기능 수정 및 추가가 단위 인터페이스로 간소해져 유연성이 향상됩니다.
2. 단위 기능에 집중하므로 응집력이 강화되고 복잡성이 낮아집니다.
3. **코딩 계약** 에 의한 코드 구현시 필요한 것만 구현하면 되므로 구현 편의성이 향상됩니다.

**위반 사례**

동물에 관련된 `IAnimal` 인터페이스를 구현했다고 합시다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/10ee7355-4caf-4737-ae4c-22ab8c150697)

이제 `IAnimal`을 상속받은 강아지, 고양이, 물고기, 새를 구현하고자 합니다. 그런데 각 동물들의 특성에 따라 할 수 없는 것이 있습니다.

예를 들어 강아지를 구현할 때,`Dog`의 `Fly()` 를 어떻게 구현해야 할지 난감합니다.

``` cpp
class Dog : public IAnimal {
public:        
    virtual void Born() override { /*구현해야 함*/ }
    virtual void Dead() override { /*구현해야 함*/ }
    virtual void Eat() override { /*구현해야 함*/ }
    virtual void Walk() override { /*구현해야 함*/ }
    virtual void Swim() override { /*구현해야 함*/ }
    virtual void Fly() override { } // 뭘하지? 특별히 하는게 없더라도 구현해 두어야 함
};
```

이러한 경우 자식에서 쓸데없이 함수 구현을 억지로 해야 하므로, [인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/) 위반이며, 억지스런 구현을 하다보니 부모처럼 동작하기 힘들어져 [리스코프 치환 원칙](https://tango1202.github.io/principle/principle-liskov-substitution/)을 위반하게 됩니다. 또한, 자식 개체들도 부모처럼 뚱뚱해 지다가 결국 [블롭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B8%94%EB%A1%ADthe-blob)이 되어 많은 개체들에게 종속된 괴물로 변신하게 됩니다.

**준수 방법**

`IAnimal`에 동물들의 다양한 특성들을 넣어 뚱뚱한 인터페이스를 만들지 않고, 하기 그림처럼 각 특성별로 인터페이스를 분리하여 날씬하게 구성합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7bdd75ed-9c53-4ec4-91b6-78046788351e)

먼저 `IAnimal`인터페이스에서 단위 기능들을 분리합니다.

```cpp
// 단위 특성들을 분리하여 날씬해졌습니다.
class IAnimal {
public:
    virtual ~IAnimal() {} // 다형 소멸 하도록 public virtual

public:
    virtual void Born() = 0;
    virtual void Dead() = 0;
};
```

단위 기능들을 인터페이스로 구현합니다.

```cpp
// 인터페이스들이 분리되어 간소화 되었습니다.
class IEatable {
protected:
    ~IEatable() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.

public:
    virtual void Eat() = 0;
};
class IWalkable {
protected:
    ~IWalkable() {} // // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.

public:
    virtual void Walk() = 0;
};
class ISwimmable {
protected:
    ~ISwimmable() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.
public:
    virtual void Swim() = 0;
};
class IFlyable {
protected:
    ~IFlyable() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.

public:
    virtual void Fly() = 0;
};
```

`Dog`은 살고, 죽고, 먹을 수 있고, 걸을 수 있고, 수영할 수 있으므로 다음과 같이 구현할 수 있습니다.

```cpp
// 분리된 인터페이스들중 필요한 것만으로 구성합니다.
class Dog :
    public IAnimal,
    public IEatable,
    public IWalkable,
    public ISwimmable {
public:        
    virtual void Born() override { /*구현해야 함*/ }
    virtual void Dead() override { /*구현해야 함*/ }
    virtual void Eat() override { /*구현해야 함*/ }
    virtual void Walk() override { /*구현해야 함*/ }
    virtual void Swim() override { /*구현해야 함*/ }
};
```

이제 `Dog`에서 억지로 `Fly()`를 구현할 필요가 없어졌습니다.






