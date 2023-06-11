---
layout: single
title: "#3. [개체지향 원칙] 개방-폐쇄 원칙(Open-Closed Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

개방-폐쇄 원칙은 **확장에는 열려 있되, 수정에는 닫혀 있게 작성하라** 라는 원칙입니다. 

조금 풀어 쓰면,

1. 코드의 수정없이 기능 추가나 변경이 가능하게 설계하고,

3. 요구사항 추가에 따라 새 클래스를 작성하면, 기존 클래스들이 수정 없이 유연하게 사용 가능해야 하라.

라는 뜻입니다. !!!유연하게!!! 말이죠.

**준수 방법 : 유연한 함수**

SaveFile() 함수로 파일을 저장한다고 했을때, 하기와 같이 함수 내부에서 경로명을 지정했다고 생각해 보시죠.

```cpp
void SaveFile() const {
    std::wstring pathName = L"c:/data/my_data.txt";
    
    ...
}
```

파일명이나 경로가 바뀐다면 "SaveFile"함수를 수정해야 합니다. 조금의 변화에도 일일이 코드를 뒤져 수정한다면, 유지보수가 어려워 집니다.

다음과 같이 `pathName`을 인자로 요청한다면, 좀더 확장성 있는 함수가 됩니다.
경로명 변경 확장에는 열려있고, SaveFile 함수 수정에는 닫혀 있게 됩니다.

```cpp

void SaveFile(const std::wstring& pathName) const {
}
```

**준수 방법 : 유연한 개체**

다형성을 이용하여 부모 개체로부터 다양한 자식 개체를 추가할 수 있습니다.

하기의 구조에서 새로운 도형 개체가 필요하다면, `Shape`을 상속하면 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/55d845d9-bcfb-4e92-9683-8a7b108d5cb5)

**준수 방법 : 유연한 기능 추가**

만약 개체에 새로운 기능을 추가하고 싶다면, 멤버 함수를 추가하면 됩니다. 하기는 개체를 확대하는 `Scale()` 을 추가한 예입니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ff2a24ff-2c54-4c5e-93fe-172eeb3a6947)

부모 클래스인 `Shape`에 가상 함수로 `Scale()`을 만들고 자식 클래스에서 이를 `override`하였습니다. 일반적인 방법입니다만, 부모 클래스가 뚱뚱해 질 수 있으므로, 상황에 따라 [비지터 패턴](https://tango1202.github.io/pattern/pattern-visitor/) 을 이용하여, 부모 클래스 인터페이스 수정없이 기능들을 추가할 수 있습니다.

`IVisitor`를 이용한 클래스 구성은 다음과 같습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/70aa06f2-a9aa-48c4-9943-7feadd2c1782)

`IVisitor`는 `Shape`의 자식 개체를 방문하면서 개체 종류에 따라 `VisitRectangle()` 이나 `VisitCircle()`을 호출하라고 강요합니다. `ScaleVisitor`의 경우는 해당 함수 호출시 크기조정을 할 것이고, `RotateVisitor`는 회전을 시킬 것입니다. 이와 같이 새로운 기능을 추가하고 싶을때 새로운 Visitor를 만들어 추가하면 됩니다.

```cpp
class Shape;

// 개체를 방문하여 개체별로 작업합니다.
class IVisitor {
protected:
    ~IVisitor() {}  // 상속받지만, 다형적으로 사용하지 않아 none virtual 입니다.

public:
    // object는 Rectangle입니다. 필요하면 dynamic_cast를 합니다.
    virtual void VisitRectangle(Shape* object) = 0;

    // object는 Circle입니다. 필요하면 dynamic_cast를 합니다.
    virtual void VisitCircle(Shape* object) = 0;
};

// 개체를 Scale합니다.
class ScaleVisitor : 
    public IVisitor {
public:
    virtual void VisitRectangle(Shape* object) override {
        std::cout<<"Scale Rectangle"<<std::endl; 

    };
    virtual void VisitCircle(Shape* object) override {
        std::cout<<"Scale Circle"<<std::endl; 
    }
};

// 개체를 Rotate합니다.
class RotateVisitor : 
    public IVisitor {
public:
    virtual void VisitRectangle(Shape* object) override {
        std::cout<<"Rotate Rectangle"<<std::endl; 

    };
    virtual void VisitCircle(Shape* object) override {
        std::cout<<"Rotate Circle"<<std::endl; 
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
    virtual ~Shape() {}; // 다형적 소멸
public:
    // m_Left, m_Top을 이용하여 자식 클래스들이 알아서 그려야 함
    virtual void Draw() const = 0; 

    // visitor 방문을 허용합니다.
    virtual void Accept(IVisitor& visitor) = 0;
};

class Rectangle : 
    public Shape {
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

class Circle : 
    public Shape {
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

하기와 같이 테스트할 수 있습니다.

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

**준수 방법 : 유연한 알고리즘 변경**

[Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하거나 **의존성 주입([의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고)** 을 활용하여, 원하는 알고리즘으로 손쉽게 변경할 수 있습니다.

하기 그림처럼 구성하고, `Shape`에 어떤 Writer(`XmlWriter` 혹은 `JsonWriter`)를 전달하는지에 따라 다른 결과를 출력하게 됩니다. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f84d4bda-f55f-4771-b241-e90eb02d38ae)

이 원칙을 준수하면,

1. 기능 수정 및 추가가 용이하도록 작성하므로 유연성이 향상됩니다.
2. 모듈 사용에 실수가 적어져 **코딩 계약** 준수에 따른 사용성이 향상됩니다.


