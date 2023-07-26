---
layout: single
title: "#5. [개체지향 원칙] 인터페이스 분리 원칙(Interface Segregation Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

인터페이스 분리 원칙은 **클라이언트는 자신이 사용하지 않는 것에 강제로 의존하지 말아야 한다** 는 원칙입니다.

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

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4d787743-47b6-4166-8ba1-4aef7e740bc9)

이제 `IAnimal`을 상속받은 강아지, 고양이, 물고기, 새를 구현하고자 합니다. 그런데 각 동물들의 특성에 따라 할 수 없는 것이 있습니다.

예를 들어 강아지를 구현할 때,`Dog`의 `Fly()` 를 어떻게 구현해야 할지 난감합니다.

``` cpp
class Dog :
    public IAnimal {
public:        
    virtual void Born() override { /*구현해야 함*/ }
    virtual void Dead() override { /*구현해야 함*/ }
    virtual void Eat() override { /*구현해야 함*/ }
    virtual void Walk() override { /*구현해야 함*/ }
    virtual void Swim() override { /*구현해야 함*/ }
    virtual void Fly() override { } // 뭘하지? 특별히 하는게 없더라도 구현해 두어야 함
};
```
자식에서 쓸데없이 함수 구현을 억지로 해야 하므로, 인터페이스 분리 원칙 위반입니다. 

**준수 방법**

`IAnimal`에 동물들의 다양한 특성들을 넣어 뚱뚱한 인터페이스를 만들지 않고, 하기 그림처럼 각 특성별로 인터페이스를 분리하여 날씬하게 구성합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8ec9296a-1ba4-4291-886e-2b02610e1558)

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
    ~IEatable() {} // 상속할 수 있지만, 다형적으로 사용하지 않아 non-virtual 입니다.

public:
    virtual void Eat() = 0;
};
class IWalkable {
protected:
    ~IWalkable() {} // 상속할 수 있지만, 다형적으로 사용하지 않아 non-virtual 입니다.

public:
    virtual void Walk() = 0;
};
class ISwimmable {
protected:
    ~ISwimmable() {} // 상속할 수 있지만, 다형적으로 사용하지 않아 non-virtual 입니다.

public:
    virtual void Swim() = 0;
};
class IFlyable {
protected:
    ~IFlyable() {} // 상속할 수 있지만, 다형적으로 사용하지 않아 non-virtual 입니다.

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






