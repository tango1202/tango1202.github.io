---
layout: single
title: "#23. [디자인 패턴-행위 패턴] Visitor"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Visitor](https://tango1202.github.io/pattern/pattern-visitor/)는 개체를 수정하지 않고도 새로운 기능을 추가할 수 있게 합니다.

# 설명

일반적으로 개체에 기능을 추가하고 싶은 경우 해당 개체에 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 작성하면 됩니다. 하지만, 개체가 거대해지는 것을 막기 위해 [Visitor 패턴](https://tango1202.github.io/pattern/pattern-visitor/)을 활용할 수 있습니다.

다음 그림에서 `Element`의 `Accept()`에 `Visitor`를 전달하면, 각 자식 개체에서 관련 함수들을 실행해 줍니다. 따라서 추가할 기능을 `ConcreteVisitor1`이나 `ConcreteVisitor2`에서 구현해 주면 됩니다.

![Visitor](https://github.com/tango1202/tango1202.github.io/assets/133472501/f93b9bab-9a45-412e-8151-8274480990e6)

|항목|내용|
|--|--|
|`Visitor`|방문할 개체에서 호출할 함수들을 정의합니다.|
|`Element`|`Visitor`가 방문할 수 있는 `Accept()`를 정의합니다.|
|`ConcreteVisitor1`,`ConcreteVisitor2`|`Visitor`를 구체화한 개체입니다.|
|`ConcreteElementA`,`ConcreteElementB`|`Element`를 구체화한 개체입니다.|

# 특징

개체는 `Visitor`가 동작할 수 있는 충분한 정보를 제공해야 합니다. 이에 따라 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 너무 많이 `public`이 되는 경향이 있습니다.

# 예제

다음은 `Rectangle`과 `Circle` 개체에 방문하는 `Visitor`의 구현 예입니다. `XmlWriter`와 `JsonWriter`로 방문하여 해당 개체의 내용을 저장할 수 있습니다.

1. #1 : `IVisitor`는 개체를 방문합니다. 방문한 개체가 호출할 수 있도록 `VisitRectangle()`과 `VisitCircle()`을 제공합니다.
2. #2 : `Shape`은 `IVisitor`가 방문할 수 있도록 `Accept()`를 제공합니다.
3. #3 : `Rectangle`과 `Circle`은 `IVisitor`가 방문하면, 현 개체에 해당하는 함수를 호출합니다.
4. #4 : 각 개체에서 `VisitRectangle()`이나 `VisitCircle()`을 호출하면 해당 기능을 실행합니다.

```cpp
class Shape; // 상호 참조가 되어 전방 선언이 필요합니다.
class Rectangle;
class Circle;

// ----
// #1. 개체를 방문합니다. 방문한 개체가 호출할 수 있도록 VisitRectangle()과 VisitCircle()을 제공합니다.
// ----
class IVisitor {
protected:
    IVisitor() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~IVisitor() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    IVisitor(const IVisitor&) = delete;
    IVisitor(IVisitor&&) = delete;
    IVisitor& operator =(const IVisitor&) = delete;
    IVisitor& operator =(IVisitor&&) = delete;
public:
    virtual void VisitRectangle(Rectangle& rectangle) = 0;
    virtual void VisitCircle(Circle& circle) = 0;
};
// ----
// #2. IVisitor가 방문할 수 있도록 Accept()를 제공합니다.
// ----
class Shape {
protected:
    Shape() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
    Shape(const Shape&) = default; 
public:
    virtual ~Shape() = default; // 다형 소멸 하도록 public virtual 
private:
    Shape(Shape&&) = delete; 
    Shape& operator =(const Shape&) = delete; 
    Shape& operator =(Shape&&) = delete;   
public:
    virtual void Accept(IVisitor& visitor) = 0;
};
// ----
// #3. IVisitor가 방문하면, 현 개체에 해당하는 함수를 실행합니다.
// ----
class Rectangle : public Shape {
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : m_Left{l}, m_Top{t}, m_Width{w}, m_Height{h} {}
public:
    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetLeft(int val) {m_Left = val;}
    void SetTop(int val) {m_Top = val;}
    void SetWidth(int val) {m_Width = val;}
    void SetHeight(int val) {m_Height = val;}   

    virtual void Accept(IVisitor& visitor) override {
        visitor.VisitRectangle(*this); // #3
    }
};
class Circle : public Shape {
    int m_CenterX;
    int m_CenterY;
    int m_Diameter;
public:
    Circle(int centerX, int centerY, int diameter) : m_CenterX(centerX), m_CenterY(centerY), m_Diameter(diameter) {}
public:
    int GetCenterX() const {return m_CenterX;}
    int GetCenterY() const {return m_CenterY;}
    int GetDiameter() const {return m_Diameter;}

    void SetCenterX(int val) {m_CenterX = val;}
    void SetCenterY(int val) {m_CenterY = val;}
    void SetDiameter(int val) {m_Diameter = val;}

    virtual void Accept(IVisitor& visitor) override {
        visitor.VisitCircle(*this); // #3
    }
};

// ----
// #4. 각 개체에서 호출하면, 해당 기능을 실행합니다.
// ----
class XmlWriter : public IVisitor {
public:
    XmlWriter() = default;

    virtual void VisitRectangle(Rectangle& rectangle) override {
        std::cout << "XmlWriter Rectangle" << std::endl; 
    };
    virtual void VisitCircle(Circle& rectangle) override {
        std::cout << "XmlWriter Circle" << std::endl; 
    }
};

class JsonWriter : public IVisitor {
public:
    JsonWriter() = default;

    virtual void VisitRectangle(Rectangle& rectangle) override {
        std::cout << "JsonWriter Rectangle" << std::endl; 
    };
    virtual void VisitCircle(Circle& rectangle) override {
        std::cout << "JsonWriter Circle" << std::endl; 
    }
};

// ----
// 테스트 코드
// ----
Rectangle rectangle{0, 0, 10, 20};
Circle circle{0, 0, 10};

XmlWriter xmlWriter;
rectangle.Accept(xmlWriter);
circle.Accept(xmlWriter);

JsonWriter jsonWriter;
rectangle.Accept(jsonWriter);
circle.Accept(jsonWriter);  
```

상기 `Rectangle`과 `Circle`의 크기를 2배로 크게하는 기능을 추가해 봅시다. 다음과 같이 `ScaleVisitor`를 만들어 방문해 주면 됩니다.

```cpp
class ScaleVisitor : public IVisitor {
public:
    virtual void VisitRectangle(Rectangle& rectangle) override {
        rectangle.SetWidth(rectangle.GetWidth() * 2); // 2배로 크게합니다.
    };
    virtual void VisitCircle(Circle& rectangle) override {
        rectangle.SetDiameter(rectangle.GetDiameter() * 2); // 2배로 크게 합니다.
    }
};

ScaleVisitor scaleVisitor; 
rectangle.Accept(scaleVisitor);
circle.Accept(scaleVisitor);

EXPECT_TRUE(rectangle.GetWidth() == 10 * 2 && rectangle.GetHeight() == 20 * 2);
EXPECT_TRUE(circle.GetDiameter() == 10 * 2);  
```