---
layout: single
title: "#4. [개체지향 원칙] 리스코프 치환 원칙(Liskov Substitution Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

리스코프 대체 원칙은 **자식 개체는 부모 개체를 완전하게 치환할 수 있어야 한다** 는 원칙입니다. 

조금 풀어 쓰면,

1. 상속받은 자식은 부모와 is-a 관계여야 하고,
2. 부모 클래스에서와 동등한 행위를 보장하여,
3. 부모 클래스와 자식클래스 가 일관된 행동을 하라 

라는 뜻입니다.

우리는 개체지향의 다형성을 이용하여 부모 클래스를 상속하고 기능 확장이나 변형을 하게 됩니다.

왼쪽, 상단 좌표와 크기를 입력받아 자신의 모양을 출력하는 Shape 개체를 생각해 봅시다. 하기와 같이 부모 클래스인 Shape에서 left, top, width, height 값을 입력받고, Draw() 함수를 virtual로 작성하여 자식 클래스에서 재구현 하여 그릴 수 있게 했습니다.

```cpp
// 부모 클래스 입니다.
class Shape {
private:
    int left;
    int top;
    int width;
    int height;
public:
    Shape(int l, int t, int w, int h) :
        left(l),
        top(t),
        width(w),
        height(h) {
    }
    virtual ~Shape() {}; // 순가상. 상속해야만 사용 가능
public:

    // Get/Set 함수
    int GetLeft() const {return left;}
    int GetTop() const {return top;}
    int GetWidth() const {return width;}
    int GetHeight() const {return height;}

    void SetLeft(int val) {left = val;}
    void SetTop(int val) {top = val;}
    void SetWidth(int val) {width = val;}
    void SetHeight(int val) {height = val;}

    // left, top, width, height를 가지고, 자식 개체들이 알아서 그려야 함
    virtual void Draw() const = 0; 
};

// 자식 클래스 입니다.
class Rectangle : public Shape {
public:    
    Rectangle(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    ~Rectangle() override {}

    void Draw() const override {
        // 사각형을 그립니다.
    }
};

// 자식 클래스 입니다.
class Ellipse : public Shape {
public:
    Ellipse(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    ~Ellipse() override {}

    void Draw() const override {
        // 타원을 그립니다.
    }
};

TEST(TestShape, Draw) {
    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);

    std::vector<Shape*> shapes;
    shapes.push_back(&rect);
    shapes.push_back(&ellipse);
 
     EXPECT_TRUE(rect.GetLeft() == 0 && rect.GetTop() == 0 && rect.GetWidth() == 10 && rect.GetHeight() == 20);   
    EXPECT_TRUE(ellipse.GetLeft() == 0 && ellipse.GetTop() == 0 && ellipse.GetWidth() == 10 && ellipse.GetHeight() == 20);  
 
    for(std::vector<Shape*>::iterator itr = shapes.begin(); itr != shapes.end(); ++itr) {
        (*itr)->Draw();
    }
}
```

아직은 좋습니다. 이제 정사각형을 추가해 봅시다. 정사각형은 width와 height가 동일해야 합니다. 그래서 SetWidth()와 SetHeight()를 재구현하여 동기화할 필요가 있습니다. 부모클래스의 SetWidth()와 SetHeight()를 virtual로 만들고 재구현 합니다.

```cpp
// 부모 클래스 입니다.
class Shape {
private:
    int left;
    int top;
    int width;
    int height;
public:
    Shape(int l, int t, int w, int h) :
        left(l),
        top(t),
        width(w),
        height(h) {
    }
    virtual ~Shape() {}; // 순가상. 상속해야만 사용 가능
public:
    int GetLeft() const {return left;}
    int GetTop() const {return top;}
    int GetWidth() const {return width;}
    int GetHeight() const {return height;}

    void SetLeft(int val) {left = val;}
    void SetTop(int val) {top = val;}
    virtual void SetWidth(int val) {width = val;}
    virtual void SetHeight(int val) {height = val;}

    virtual void Draw() const = 0; // 자식 개체들이 알아서 그려야 함
};

// 자식 클래스 입니다.
class Rectangle : public Shape {
public:    
    Rectangle(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    ~Rectangle() override {}

    void Draw() const override {
        // 사각형을 그립니다.
    }
};

// 자식 클래스 입니다.
class Ellipse : public Shape {
public:
    Ellipse(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    ~Ellipse() override {}

    void Draw() const override {
        // 타원을 그립니다.
    }
};

// 자식 클래스입니다. 생성자를 손보고, SetWidth와 SetHeight를 손봤습니다.
class Square : public Shape {
public:
    Square(int l, int t, int w, int h) : Shape(l, t, w, w) {}
    ~Square() override {}

    //width를 바꾸면 height도 바꿉니다.
    virtual void SetWidth(int val) {
        Shape::SetWidth(val);
        Shape::SetHeight(val);
    }
    // Height를 바꾸면 Width를 바꿉니다. 어차피 SetWidth()랑 똑같이 동작하니 SetWidth를 불러줍니다.
    virtual void SetHeight(int val) {
        SetWidth(val);
    }

    void Draw() const override {
        // 타원을 그립니다.
    }
};

TEST(TestShape, Draw) {

    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);
    Square square(0, 0, 10, 20); // height는 내부적으로 10으로 설정됩니다.
 
    std::vector<Shape*> shapes;
    shapes.push_back(&rect);
    shapes.push_back(&ellipse);
    shapes.push_back(&square);

    EXPECT_TRUE(rect.GetLeft() == 0 && rect.GetTop() == 0 && rect.GetWidth() == 10 && rect.GetHeight() == 20);   
    EXPECT_TRUE(ellipse.GetLeft() == 0 && ellipse.GetTop() == 0 && ellipse.GetWidth() == 10 && ellipse.GetHeight() == 20);  
    EXPECT_TRUE(square.GetWidth() == square.GetHeight());  

    for(std::vector<Shape*>::iterator itr = shapes.begin(); itr != shapes.end(); ++itr) {
        (*itr)->Draw();
    }
}
```

악취가 보이실까요? 아직은 동작하는데 큰 이상이 없습니다만, Square의 구조를 모르는 다른 동료들이 하기와 같이 사용하다가 밤을 샐 수 있습니다.

```cpp
TEST(TestShape, Draw) {
    // 모든 개체의 넓이를 2배 크게 합니다.
    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);
    Square square(0, 0, 10, 20); // height는 내부적으로 10으로 설정됩니다.
 
    std::vector<Shape*> shapes;
    shapes.push_back(&rect);
    shapes.push_back(&ellipse);
    shapes.push_back(&square);

    for(std::vector<Shape*>::iterator itr = shapes.begin(); itr != shapes.end(); ++itr) {
        (*itr)->SetWidth((*itr)->GetWidth() * 2);
    }

    EXPECT_TRUE(square.GetWidth() == square.GetHeight());

}

```
