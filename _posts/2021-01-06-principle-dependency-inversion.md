---
layout: single
title: "#6. [개체지향 원칙] 의존성 역전 원칙(Dependency Inversion Principle)"
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

저장 기능이 필요한 Shape 개체가 있다고 합시다. Save()함수를 구현하고, 저장시에는 Xml을 이용한다고 봅시다. 다음과 같이 구현할 수 있습니다.

```cpp
class XmlWriter {
public:
    void SaveIntVal(std::wstring attrName, int val) const { /* xml로 저장합니다 */ }
};

class Shape {
private:
    int x;
    int y;
    XmlWriter writer; // 하위 수준 모듈에 의존합니다.
    
public:
    void Save() const {
        writer.SaveIntVal(L"x", x);
        writer.SaveIntVal(L"y", y);
    }
};
```

상기는 다음과 같이 `Shape`과 `XmlWriter` 간의 의존관계가 성립됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/abc29f45-8257-49be-903a-dba549a86124)

하위 수준을 직접 참조하였으므로 의존성 역전 원칙 위반입니다.

**준수 방법**

상위 수준과 하위 수준 모두 인터페이스를 의존하도록 하기와 같이 변경합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1adacbcb-d504-4c8b-a0f1-6d597374f72e)

구현하면 하기와 같습니다.

```cpp
class IWriter {
protected:
    ~IWriter() {} // 상속받지만, 다형적으로 사용하지 않아 none virtual 입니다.
public:
    virtual void SaveIntVal(std::wstring attrName, int val) const = 0;
};

class XmlWriter : 
    public IWriter {
public:
    virtual void SaveIntVal(std::wstring attrName, int val) const override { /* xml로 저장합니다 */ }
};

class Shape {
private:
    int x;
    int y;
    const IWriter* writer; // 인터페이스에 의존합니다.
public:
    explicit Shape(const IWriter* writer) {
        assert(writer != nullptr);
        this->writer = writer;
    }
public:
    void Save() const {
        assert(writer != nullptr);
        writer->SaveIntVal(L"x", x);
        writer->SaveIntVal(L"y", y);
    }
};
```

이제 상위 모듈인 `Shape`과 하위 모듈인 `XmlWriter`가 모두 인터페이스에 의존하였으므로 의존성 역전 원칙을 준수하게 되었습니다.

**의존성 주입**

`Shape` 이 `Json`등 다른 포맷을 지원하려고 할 경우, 의존성 주입을 통해 손쉽게 확장할 수 있습니다. `IWriter` 인터페이스를 지원하는 `JsonWriter`만 개발하고 `Shape`에 전달해주면 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/25fb6fc8-f26a-4894-9285-b7ecab598c01)

하기와 같이 `SetWriter()`함수를 만들어 주면, 런타임에 다양한 `Writer`를 사용할 수 있습니다.

```cpp
// Shape에 SetWriter() 함수를 만듭니다.
class Shape {
...
public: 
    Shape() {} // 이제 writer를 생성자에서 설정하지 않습니다.
public:
    void SetWriter(const IWriter* writer) {
        assert(writer != nullptr);
        this->writer = writer;  
    }
...
};
```

이제 하기와 같이 사용하여 다양한 포맷으로 저장할 수 있습니다.

```cpp
TEST(TestPrinciple, DependencyInversion) {
    XmlWriter xmlWriter;
    JsonWriter jsonWriter;

    Shape shape;
    shape.SetWriter(&xmlWriter);
    shape.Save(); // xml 으로 저장
    
    shape.SetWriter(&jsonWriter);
    shape.Save(); // json 으로 저장
}
```

**역전의 의미**

하위 수준 모듈 관점에서 의존의 방향이 기존과 달리 인터페이스 방향으로 역전되어, 의존성 역전이라고 불립니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0c4eef17-144d-43ce-b2c2-b9d89804325c)

이 원칙을 준수하면,

1. 의존성 주입을 통해 기능 수정 및 추가가 용이하여 유연성이 향상됩니다.
2. 구체 모듈보다는 인터페이스에 의존하므로 모듈간의 결합도와 의존성이 낮아집니다.
3. 계약에 의한 코드 구현만 준수하면 되므로 구현 편의성이 향상됩니다.




