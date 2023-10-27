---
layout: single
title: "#3. [개체지향 원칙] 개방-폐쇄 원칙(Open-Closed Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

개방-폐쇄 원칙은 **확장에는 열려 있되, 수정에는 닫혀 있게 작성하라** 라는 원칙입니다. 

조금 풀어 쓰면,

1. 기존 코드의 수정없이 기능 추가나 변경이 가능하게 설계하여,
2. 요구사항 추가나 변경에 유연하게 대처하라.

라는 뜻입니다. !!!유연하게!!! 말이죠.

이 원칙을 준수하면,

1. 기능 수정 및 추가가 용이하도록 작성되어 유연성이 향상됩니다.
2. 모듈 사용에 실수가 적어져 **코딩 계약** 준수에 따른 사용성이 향상됩니다.

다만 가독성이 떨어지거나 사용성이 떨어지면, 누군가가 창문을 깨버리기 마련이니 창문을 깨지 않도록 함께 노력해야 합니다.

|개방|폐쇄|
|--|--|
|* 상속을 이용한 확장<br/>* 인터페이스를 이용한 확장<br/>* 가상 함수를 이용한 확장<br/>* 포함을 이용한 확장<br/>* 의존성 주입을 이용한 확장|* `private`를 이용한 가시성 폐쇄<br/>* 인터페이스를 이용한 코딩 계약으로 폐쇄<br/>* Non-Virtual로 상속 폐쇄<br/>* 단일 책임으로 변경 최소화<br/>* 문서화 안하기<br/>* 알기 힘들게 꼭꼭 숨기기<br/>* 고치기 무섭게 복잡하게 하기|

**준수 방법 : 유연한 함수**

`SaveFile()` 함수로 파일을 저장한다고 했을때, 함수 내부에서 경로명을 지정했다고 생각해 보시죠.

```cpp
void SaveFile() const {
    std::wstring pathName = L"c:/data/my_data.txt";
    ...
}
```

경로명이 함수내에 있기 때문에, 파일명이나 경로가 바뀐다면 `SaveFile()`함수를 수정해야 합니다.

하지만, `pathName`을 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 요청한다면, 파일명이나 경로가 바꼈을때 `SaveFile()`함수를 수정할 필요가 없습니다. [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달하면 되니까요.
경로명 변경 확장에는 열려있고, `SaveFile()`함수 수정에는 닫혀 있게 됩니다.

```cpp
void SaveFile(const std::wstring& pathName) const {}
```

**준수 방법 : 다형성을 이용한 유연한 개체**

다형성을 이용하여 부모 개체로부터 다양한 자식 개체를 추가할 수 있습니다.

하기의 구조에서 새로운 도형 개체가 필요하다면, `Shape`을 상속한 자식 개체를 추가하면 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/55d845d9-bcfb-4e92-9683-8a7b108d5cb5)

**준수 방법 : Visitor 패턴을 이용한 유연한 기능 추가**

만약 개체에 새로운 기능을 추가하고 싶다면, 일반적으로는 멤버 함수를 추가하면 됩니다. 하기는 개체를 확대하는 `Scale()` 을 추가한 예입니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ff2a24ff-2c54-4c5e-93fe-172eeb3a6947)

부모 클래스인 `Shape`에 가상 함수로 `Scale()`을 만들고 자식 클래스에서 이를 `override`하였습니다. 일반적인 방법입니다만, 부모 클래스가 뚱뚱해 질 수 있으므로(혹은, `Shape`이 외부 라이브러리라 인터페이스 수정을 못할 수 있으므로), 상황에 따라 [Visitor 패턴](https://tango1202.github.io/pattern/pattern-visitor/)으로 부모 클래스 인터페이스 수정없이 기능들을 추가할 수 있습니다.

`IVisitor`를 이용한 클래스 구성은 다음과 같습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6cc08759-629b-4be7-a617-02f373ad2b43)

`IVisitor`는 `Shape`의 자식 개체를 방문하면서 개체 종류에 따라 `VisitRectangle()` 이나 `VisitCircle()`을 호출할 수 있게 합니다.

개체에서 이 함수들을 호출하면, `ScaleVisitor`의 경우는 크기조정을 수행하는 코드를 구현하고, `RotateVisitor`는 회전을 수행하는 코드를 구현하면 됩니다. 

이와 같이 새로운 기능 추가시, 기존 코드의 수정 없이 새로운 Visitor만 만들면 되므로, 코드가 좀더 유연해 집니다.(아쉬운 점은 `IVsitor`와 `Shape`이 상호 참조 되는 점인데, 기능 추가와 **의존성 부패** 간의 트레이드 오프가 필요해 집니다.)

```cpp
class Shape; // 상호 참조가 되어 전방 선언이 필요합니다.

// 개체를 방문하여 개체별로 작업합니다.
class IVisitor {
protected:
    ~IVisitor() {}  // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.

public:
    // object는 Rectangle입니다. 필요하면 dynamic_cast를 합니다.
    virtual void VisitRectangle(Shape* object) = 0;

    // object는 Circle입니다. 필요하면 dynamic_cast를 합니다.
    virtual void VisitCircle(Shape* object) = 0;
};

// 개체를 Scale합니다.
class ScaleVisitor : public IVisitor {
public:
    virtual void VisitRectangle(Shape* object) override {
        std::cout << "Scale Rectangle" << std::endl; 
    };
    virtual void VisitCircle(Shape* object) override {
        std::cout << "Scale Circle" << std::endl; 
    }
};

// 개체를 Rotate합니다.
class RotateVisitor : public IVisitor {
public:
    virtual void VisitRectangle(Shape* object) override {
        std::cout << "Rotate Rectangle" << std::endl; 
    };
    virtual void VisitCircle(Shape* object) override {
        std::cout << "Rotate Circle" << std::endl; 
    }
};
```

`Shape`은 `IVisitor`를 `Accept()`에서 전달받고 각 개체는 자신에 해당하는 함수를 호출합니다.(`Rectangle`은 `VisitRectangle()`을 호출하고, `Circle`은 `VisitCircle()` 을 호출합니다.)

```cpp
class Shape {
private:
    int m_Left;
    int m_Top;
public:
    Shape(int l, int t) :
        m_Left(l),
        m_Top(t) {
    }
    virtual ~Shape() {} // 다형 소멸 하도록 public virtual
public:
    // m_Left, m_Top으로 자식 클래스들이 알아서 그려야 함
    virtual void Draw() const = 0; 

    // visitor 방문을 허용합니다.
    virtual void Accept(IVisitor& visitor) = 0;
};

class Rectangle : public Shape {
private:
    int m_Width;
    int m_Height;
public:    
    Rectangle(int l, int t, int w, int h) : 
        Shape(l, t), 
        m_Width(w), m_Height(h) {}
    virtual ~Rectangle() override {}   

    virtual void Draw() const override {}

    // visitor 방문을 허용합니다.
    virtual void Accept(IVisitor& visitor) override {
        visitor.VisitRectangle(this);   
    }
};

class Circle : public Shape {
private:
    int m_Diameter;
public:    
    Circle(int l, int t, int diameter) : 
        Shape(l, t), 
        m_Diameter(diameter) {}
    virtual ~Circle() override {}   

    virtual void Draw() const override {}

    // visitor 방문을 허용합니다.
    virtual void Accept(IVisitor& visitor) override {
        visitor.VisitCircle(this);
    }
};    
```

다음과 같이 테스트할 수 있습니다.

```cpp
Rectangle rect(0, 0, 10, 20);
Circle circle(0, 0, 10);

ScaleVisitor scaleVisitor; // 방문하여 개체들을 Scale 합니다.
rect.Accept(scaleVisitor);
circle.Accept(scaleVisitor);

RotateVisitor rotateVisitor; // 방문하여 개체들을 Rotate 합니다.
rect.Accept(rotateVisitor);
circle.Accept(rotateVisitor);
```

**준수 방법 : 의존성 주입을 이용한 유연한 알고리즘 변경**

[Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하거나 **의존성 주입([의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고)** 을 활용하여, 원하는 알고리즘으로 손쉽게 변경할 수 있습니다.

하기 그림처럼 구성하고, `Shape`의 `SetWiter()`에 어떤 Writer(`XmlWriter` 혹은 `JsonWriter`)를 전달하는지에 따라 다른 알고리즘을 사용할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/fe211413-2247-44bf-99bf-603eb6cb7308)



