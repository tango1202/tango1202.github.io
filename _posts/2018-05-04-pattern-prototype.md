---
layout: single
title: "#4. [디자인 패턴-생성 패턴] Prototype"
categories: "pattern"
tag: ["디자인 패턴", "생성 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Prototype](https://tango1202.github.io/pattern/pattern-prototype/)은 미리 정의된 개체로부터 복제하여 개체를 생성할때 사용합니다.

# 설명

다음과 같이 편집 프로그램에서 사용하는 도형 팔레트 메뉴를 생각해 봅시다.

![Image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ad01b76d-74b3-454d-978b-ea18a6117e58)

각 도형은 지오메트리 정보와 배경 속성, 테두리 속성을 가지고 있는데요, 메뉴를 클릭할 때마다 해당 속성을 설정해서 개체를 생성해 주어야 합니다. 이 값을 일일이 설정하는 건 번거로운 일이죠.

이런 경우 [Prototype](https://tango1202.github.io/pattern/pattern-prototype/)을 이용하여 미리 정의된 개체를 메뉴에 연결해 두고, 메뉴 클릭시 개체를 복제해서 사용하면 손쉽게 구현할 수 있습니다.

다음 그림에서 `Client`는 `m_Prototype`의 `Clone()`을 호출하여 개체를 복제 생성합니다.

![Prototype](https://github.com/tango1202/tango1202.github.io/assets/133472501/4d59eae0-3b2d-4829-bea8-d81f3ead664f)

|항목|내용|
|--|--|
|`Prototype`|`Clone()` 을 제공하는 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)입니다. 자식 개체에서 `Clone()`을 구현해야 합니다.|
|`ConcretePrototype1, ConcretePrototype2`|`Clone()`을 구체화한 개체입니다.|
|`Client`|`Prototype`의 `Clone()`을 이용하여 새로운 개체를 만듭니다.|

# 특징

도형 팔레트와 같이 UI를 통해 미리 속성(*개체 크기, 색상 등*)을 설정한 개체를 생성할때 좋습니다. 또한, 사용자 정의한 개체를 UI에 추가하여 상호작용할 수도 있습니다.

# 예제

다음 예제는 `ShapePalette`에 다양한 `ShapeButton`을 두고, `ShapeButton`을 `Click()`하면 미리 정의된 `Shape`을 복제하여 사용합니다. 또한 #4와 같이 사용자 정의한 개체를 UI에 등록하여 사용할 수 있습니다.

1. #1 : `Shape`, `Rectangle`, `Circle`은 `Clone()`을 제공하며, 자기 자신을 복제합니다.
2. #2 : `ShapeButton` 생성시 사용할 `Shape`개체를 연결합니다.
3. #3 : `ShapeButton`을 `Click()`하면 복제본을 생성합니다.
4. #4 : 사용자가 정의한 `Shape`을 `ShapeButton`에 등록하여 사용할 수 있습니다.

```cpp
class Shape {
protected:
    Shape() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
    Shape(const Shape&) = default; 
private:
    Shape(Shape&&) = delete; 
    Shape& operator =(const Shape&) = delete; 
    Shape& operator =(Shape&&) = delete;   
public:
    virtual ~Shape() = default; // 다형 소멸 하도록 public virtual    

    // #1. 복제본을 생성합니다.
    virtual std::unique_ptr<Shape> Clone() const = 0;
};

class Rectangle : public Shape {
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : m_Left{l}, m_Top{t}, m_Width{w}, m_Height{h} {}
private:
    Rectangle(const Rectangle&) = default; // Clone 에서만 사용하기 때문에 private입니다.
public:
    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetLeft(int val) {m_Left = val;}
    void SetTop(int val) {m_Top = val;}
    void SetWidth(int val) {m_Width = val;}
    void SetHeight(int val) {m_Height = val;}   

    // #1. 복제본을 생성합니다.
    virtual std::unique_ptr<Shape> Clone() const override {
        return std::unique_ptr<Shape>{new Rectangle(*this)};
    }
};
class Circle : public Shape {
    int m_CenterX;
    int m_CenterY;
    int m_Diameter;
public:
    Circle(int centerX, int centerY, int diameter) : m_CenterX(centerX), m_CenterY(centerY), m_Diameter(diameter) {}
private:
    Circle(const Circle&) = default; // Clone 에서만 사용하기 때문에 private입니다.
public:
    int GetCenterX() const {return m_CenterX;}
    int GetCenterY() const {return m_CenterY;}
    int GetDiameter() const {return m_Diameter;}

    void SetCenterX(int val) {m_CenterX = val;}
    void SetCenterY(int val) {m_CenterY = val;}
    void SetDiameter(int val) {m_Diameter = val;}

    // #1. 복제본을 생성합니다.
    virtual std::unique_ptr<Shape> Clone() const override {
        return std::unique_ptr<Shape>{new Circle(*this)};
    }
};
// ----
// Client 입니다. 클릭하면 해당 Shape개체를 생성해줍니다.
// ----
class ShapeButton {
    std::unique_ptr<Shape> m_Shape; // #2. Prototype 개체입니다.
public:
    explicit ShapeButton(std::unique_ptr<Shape> shape) : m_Shape{std::move(shape)} {} // #2
private:
    ShapeButton(const ShapeButton&) = default; 
    ShapeButton(ShapeButton&&) = delete; 
    ShapeButton& operator =(const ShapeButton&) = delete; 
    ShapeButton& operator =(ShapeButton&&) = delete;   
public:

    // ----
    // #3. 관라하는 m_Shape의 복제본을 리턴합니다.
    // ----
    std::unique_ptr<Shape> Click() const {
        if(!m_Shape) {
            return std::unique_ptr<Shape>{};
        }

        return m_Shape->Clone();
    }
    // #4. 사용자의 개체를 등록합니다.
    void SetShape(std::unique_ptr<Shape> shape) {
        m_Shape = std::move(shape);
    }
};

// ShapeButton에 미리 정의된 도형들을 연결합니다.
class ShapePalette {
    ShapeButton m_RectangleButton;
    ShapeButton m_SquareButton;
    ShapeButton m_CircleButton;
    ShapeButton m_UserButton;

public:
    ShapePalette() :
        m_RectangleButton{std::unique_ptr<Shape>{new Rectangle{1, 2, 5, 10}}},
        m_SquareButton{std::unique_ptr<Shape>{new Rectangle{1, 2, 20, 20}}}, // 너비와 높이가 동일한 Rectangle입니다.
        m_CircleButton{std::unique_ptr<Shape>{new Circle{1, 2, 30}}},
        m_UserButton{std::unique_ptr<Shape>{}} {}
private:
    ShapePalette(const ShapePalette&) = delete; 
    ShapePalette(ShapePalette&&) = delete; 
    ShapePalette& operator =(const ShapePalette&) = delete; 
    ShapePalette& operator =(ShapePalette&&) = delete;       
public:
    const ShapeButton& GetRectangleButton() const {return m_RectangleButton;}
    const ShapeButton& GetSquareButton() const {return m_SquareButton;}
    const ShapeButton& GetCircleButton() const {return m_CircleButton;}
    const ShapeButton& GetUserButton() const {return m_UserButton;}

    void SetUserButton(std::unique_ptr<Shape> shape) { // #4
        m_UserButton.SetShape(std::move(shape));
    }
};

// ----
// 테스트 코드
// ----
ShapePalette shapePalette;

// 각 버튼에 연결된 shape을 복제해서 사용합니다.
std::unique_ptr<Shape> shape1{shapePalette.GetRectangleButton().Click()};
const Rectangle& rectangle{dynamic_cast<const Rectangle&>(*shape1)};
EXPECT_TRUE(rectangle.GetLeft() == 1 && rectangle.GetTop() == 2 && rectangle.GetWidth() == 5 && rectangle.GetHeight() == 10);

std::unique_ptr<Shape> shape2{shapePalette.GetSquareButton().Click()};
const Rectangle& square{dynamic_cast<const Rectangle&>(*shape2)};
EXPECT_TRUE(square.GetLeft() == 1 && square.GetTop() == 2 && square.GetWidth() == 20 && square.GetHeight() == 20);

std::unique_ptr<Shape> shape3{shapePalette.GetCircleButton().Click()};
Circle& circle{dynamic_cast<Circle&>(*shape3)};
EXPECT_TRUE(circle.GetCenterX() == 1 && circle.GetCenterY() == 2 && circle.GetDiameter() == 30);

// #4. circle의 지름을 수정하고 해당 shape을 사용자 버튼에 추가합니다.
circle.SetDiameter(100);
shapePalette.SetUserButton(std::move(shape3));

// 사용자가 추가한 개체를 복제해서 사용합니다.
std::unique_ptr<Shape> shape4{shapePalette.GetUserButton().Click()};
Circle& user{dynamic_cast<Circle&>(*shape4)};
EXPECT_TRUE(user.GetCenterX() == 1 && user.GetCenterY() == 2 && user.GetDiameter() == 100);
```

