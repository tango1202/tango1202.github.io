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
2. 상위 수준과 하위 수준 모두 인터페이스에 의존하라.

라는 뜻입니다. !!!둘 다!!!말이죠.

이 원칙을 준수하면,

1. **의존성 주입** 을 통해 알고리즘 변경이 용이하여 유연성이 향상됩니다.
2. 구체 모듈보다는 인터페이스에 의존하므로 모듈간의 결합도와 의존성이 낮아집니다.
3. **코딩 계약** 에 의한 코드 구현만 준수하면 되므로 구현 편의성이 향상됩니다.

**위반 사례**

저장 기능이 필요한 `Shape` 개체가 있다고 합시다. `Save()`함수를 구현하고, 저장시에는 Xml을 이용한다고 봅시다. 다음과 같이 구현할 수 있습니다.

```cpp
class XmlWriter {
public:
    void WriteIntVal(std::wstring attrName, int val) const { /* xml로 저장합니다 */ }
};

class Shape {
private:
    int m_X;
    int m_Y;
    XmlWriter m_Writer; // 하위 수준 모듈에 의존합니다.
    
public:
    void Save() const {
        writer.WriteIntVal(L"x", m_X);
        writer.WriteIntVal(L"y", m_Y);
    }
};
```

상기는 다음과 같이 `Shape`과 `XmlWriter` 간의 의존관계가 성립됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c539828b-23c3-418e-85a5-1cb0a73b2eca)

하위 수준을 직접 참조하였으므로 의존성 역전 원칙 위반입니다.

**준수 방법**

상위 수준과 하위 수준 모두 인터페이스를 의존하도록 하기와 같이 변경합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/59be2919-7af3-43e8-bf49-53da663d6495)

구현하면 하기와 같습니다.

```cpp
class IWriter {
protected:
    ~IWriter() {} // 상속받지만, 다형적으로 사용하지 않아 none virtual 입니다.
public:
    virtual void WriteIntVal(const std::wstring& attrName, int val) const = 0;
};

class XmlWriter : 
    public IWriter {
public:
    virtual void WriteIntVal(const std::wstring& attrName, int val) const override { /* xml로 저장합니다 */ }
};

class Shape {
private:
    int m_X;
    int m_Y;
    const IWriter* m_Writer; // 인터페이스에 의존합니다.
public:
    explicit Shape(const IWriter* writer) {
        m_Writer = writer;
    }
public:
    void Save() const {
        if (m_Writer != nullptr) {
            m_Writer->WriteIntVal(L"x", m_X);
            m_Writer->WriteIntVal(L"y", m_Y);
        }
    }
};
```

이제 상위 모듈인 `Shape`과 하위 모듈인 `XmlWriter`가 모두 인터페이스에 의존하였으므로 의존성 역전 원칙을 준수하게 되었습니다.

**의존성 주입**

의존성 역전 원칙을 준수하여 인터페이스에만 의존하게 했다면, `Shape` 이 `Json`등 다른 포맷을 지원하려고 할 경우, **의존성 주입** 을 통해 손쉽게 확장할 수 있습니다. `IWriter` 인터페이스를 지원하는 `JsonWriter`만 개발하고 `Shape`에 전달해주면 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/44a48e9b-044a-46fd-9b86-10528b9488c1)

하기와 같이 `SetWriter()`함수를 만들어 주면, 런타임에 다양한 `Writer`를 사용할 수 있습니다.

```cpp
// Shape에 SetWriter() 함수를 만듭니다.
class Shape {
...
public: 
    Shape() {} // 이제 writer를 생성자에서 설정하지 않습니다.
public:
    // 런타임에 writer를 변경합니다.
    void SetWriter(const IWriter* writer) {
        assert(writer != nullptr);
        m_Writer = writer;  
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

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/3ef80573-48b5-47d3-8110-573895b4d51f)






