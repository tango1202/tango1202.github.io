---
layout: single
title: "#6. [개체지향 원칙] 의존성 역전 원칙(Dependency Inversion Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/)은 ***상위 수준 모듈은 하위 수준 모듈에 의존하지 말아야 한다*** 는 원칙입니다.

조금 풀어 쓰면,

1. 상위 수준 모듈은 하위 수준 모듈을 직접 의존하지 말고,
2. 상위 수준과 하위 수준 모두 인터페이스에 의존하라.

라는 뜻입니다. !!!둘 다!!!말이죠.

이 원칙을 준수하면,

1. **의존성 주입** 을 통해 알고리즘 변경이 용이하여 유연성이 향상됩니다.(*[Strategy](https://tango1202.github.io/pattern/pattern-strategy/) 참고*)
2. 구체 모듈보다는 인터페이스에 의존하므로 모듈간의 결합도와 의존성이 낮아집니다.
3. **코딩 계약** 에 의한 코드 구현만 준수하면 되므로 구현 편의성이 향상됩니다.

**위반 사례**

저장 기능이 필요한 `Shape` 개체가 있다고 합시다. 

다음에서 `Save()`시 Xml로 저장하기 위해 멤버 변수로 `m_Writer`를 갖고, `Save()`함수 호출시 `m_Writer`를 이용합니다.

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
        m_Writer.WriteIntVal(L"x", m_X);
        m_Writer.WriteIntVal(L"y", m_Y);
    }
};
```

상기 코드에서 `Shape`은 `XmlWriter`에 의존하는데요, 하위 수준을 직접 참조하였으므로 [의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/)을 위반합니다. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f5bb3108-e8f8-44cc-98d9-8fab8430daba)

아직은 의존하는 하위 수준 개체가 한개 밖에 없지만, 이런 방식으로 점점 의존하는 개체가 많아지면, `Shape`은 결국 [블롭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B8%94%EB%A1%ADthe-blob)이 되어 많은 개체들에게 종속된 괴물로 변신하게 됩니다.

**준수 방법**

상위 수준과 하위 수준 모두 인터페이스를 의존하도록 `IWriter`를 만들고 `IWriter`에 의존하도록 수정합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/76242ec5-7ceb-4978-9c30-b2cf43cf1940)

구현하면 하기와 같습니다.

```cpp
class IWriter {
protected:
    ~IWriter() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.
public:
    virtual void WriteIntVal(const std::wstring& attrName, int val) const = 0;
};

class XmlWriter : public IWriter {
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

이제 상위 모듈인 `Shape`과 하위 모듈인 `XmlWriter`가 모두 인터페이스에 의존하였으므로 [의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/)을 준수하게 되었습니다.

**의존성 주입(Dependency Injection)**

의존성 주입은 외부에서 의존할 개체를 주입하는 기술입니다. 대표적으로 [Strategy](https://tango1202.github.io/pattern/pattern-strategy/)가 있습니다. 외부에서 의존할 개체를 선택해서 전달하므로, 개체의 확장성이 좋아지죠. 

특히  [테스트 주도 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9Ctest-driven-development)시 Fake나 Stub 개체(*[단위 테스트하라](https://tango1202.github.io/methodology/methodology-unittest/) 참고*)를 전달해서 임시 테스트를 한뒤, 차후에 실제 구현한 개체를 전달하는 방식으로 [점진적으로 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)할 수 있습니다.

[의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/)을 준수하여 인터페이스에만 의존하게 했다면, `Shape` 이 `Json`등 다른 포맷을 지원하려고 할 경우, **의존성 주입** 을 통해 손쉽게 확장할 수 있습니다. `IWriter` 인터페이스를 지원하는 `JsonWriter`만 개발하고 `Shape`에 전달해주면 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/3af31d5b-d97e-49c6-99de-372a0b4e27cd)

다음과 같이 `SetWriter()`함수를 만들어 주면, 런타임에 다양한 `IWriter`를 사용할 수 있습니다.

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

이제 `SetWriter()`로 다양한 포맷으로 저장할 수 있습니다.

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

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f953529b-6216-4f98-ae74-994ce6900526)






