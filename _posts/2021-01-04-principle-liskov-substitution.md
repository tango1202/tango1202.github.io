---
layout: single
title: "#4. [개체지향 원칙] 리스코프 치환 원칙(Liskov Substitution Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

리스코프 치환 원칙은 **자식 개체는 부모 개체를 완전하게 치환할 수 있어야 한다** 는 원칙입니다. 

조금 풀어 쓰면,

1. 상속받은 자식은 부모와 is-a 관계여야 하고,

2.자식이 부모와 다른 행동을 하지 말고, 같은 행동을 하게 하라 

라는 뜻입니다.

**위반사례**

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
```

하기는 자식 클래스 입니다. Draw()함수를 각각 잘 구현했다 가정하고요.

```cpp
// 자식 클래스 입니다.
class Rectangle : public Shape {
public:    
    Rectangle(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    ~Rectangle() override {}

    virtual void Draw() const override {
        // 사각형을 그립니다.
    }
};

// 자식 클래스 입니다.
class Ellipse : public Shape {
public:
    Ellipse(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    ~Ellipse() override {}

    virtual void Draw() const override {
        // 타원을 그립니다.
    }
};
```

하기는 테스트 입니다. Rectangle과 Ellipse를 생성하고, Draw를 호출하여 자기 자신을 그립니다.

```cpp
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

아직은 좋습니다. Draw를 잘 구현했다면, 사각형과 타원을 잘 그릴 것입니다. 이제 정사각형을 추가해 봅시다. 

정사각형은 width와 height가 동일해야 합니다. 그래서 SetWidth()와 SetHeight()를 재구현하여 동기화할 필요가 있습니다. 

먼저 부모 클래스의 SetWidth()와 SetHeight()를 virtual로 만들어 재구현 할 수 있게 합니다.

```cpp
// 부모 클래스 입니다.
class Shape {
    ...
    // 자식이 재구현할 수 있도록 virtual로 변경합니다.
    virtual void SetWidth(int val) {width = val;}
    virtual void SetHeight(int val) {height = val;}

    ...
};
```
다음으로 Square 클래스를 구현합니다. 생성자에서 height 값은 무시하고 width 값을 사용하도록 조정하고, SetWidth() 와 SeitHeight() 에서 width 값으로 height 값을 세팅하도록 재구현했습니다.

```cpp
// 자식 클래스입니다. 생성자를 손보고, SetWidth와 SetHeight를 손봤습니다.
class Square : public Shape {
public:
    // h 는 무시하고 w만 사용합니다.
    Square(int l, int t, int w, int /*h*/) : Shape(l, t, w, w) {}
    ~Square() override {}

    //width를 바꾸면 height도 바꿉니다.
    virtual void SetWidth(int val) override {
        Shape::SetWidth(val);
        Shape::SetHeight(val);
    }
    // height를 바꾸면 width를 바꿉니다. 어차피 SetWidth()랑 똑같이 동작하니 SetWidth를 불러줍니다.
    virtual void SetHeight(int val) override {
        SetWidth(val);
    }

    virtual void Draw() const override {
        // 정사각형을 그립니다.
    }
};
```

하기는 테스트 코드입니다. Square를 생성하고 Draw합니다.

```cpp
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

악취가 보이실까요? 아직은 동작하는데 큰 이상이 없습니다만, Square의 구조를 모르는 다른 동료들이 하기와 같이 테스트하다가 밤을 샐 수 있습니다.

```cpp
TEST(TestShape, Draw) {
   
    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);
    Square square(0, 0, 10, 20); // height는 내부적으로 10으로 설정됩니다.
 
    std::vector<Shape*> shapes;
    shapes.push_back(&rect);
    shapes.push_back(&ellipse);
    shapes.push_back(&square);
  
 for(std::vector<Shape*>::iterator itr = shapes.begin(); itr != shapes.end(); ++itr) {
        EXPECT_TRUE(*itr)->GetWidth() ==10);
        EXPECT_TRUE(*itr)->GetHeight() ==20); // Fail
        
    }

```

그렇죠. Square는 width 값을 height에 세팅하니 당연히 테스트를 실패하겠죠. 이제 Square의 동작 특성을 알았으니 주의해서 사용하면 될까요? 이런 자식 개체가 또 뭐가 있을까요? 단순하게 Circle이 있겠고요, 또 더 있을 수 있을까요? 겁이 나서 모든 자식들의 소스코드와 테스트케이스들을 확인해 봐야 할까요? 이크. 라이브러리라 소스코드를 못보내요? 이런경우가 리스코프 치환 원칙에 위배된 것입니다.

자식인 Square의 SetHeight()가 부모인인 Shape의 SetHeight 와는 다른 행동을 했습니다.

자식이 잘못한 걸까요? 부모가 잘못한 걸까요. 자식은 자신의 역할을 충실히 하기 위해 width와 height를 충실히 수행했을 뿐이었습니다. 제 생각엔 이 경우엔 부모가 잘못한 겁니다. 모든 Shape이 width와 height를 가진다는 잘못된 가정을 한겁니다.
정사각형은 길이 하나만 가지고 있는 편이 좋고, 직선은 시작점과 끝점을 가지고 있는 편이 좋고, 호는 반지름과 각도를 가지고 있는 편이 좋고, 원은 지름을 가지고 있는 편이 좋습니다. 이 정보로 부터 width와 height를 계산해 낼 수 있죠.
즉 부모가 쓸데없이 width와 height를 가진 셈이 됩니다. 자식들이 헷갈리게요.

**수정**

하기와 같이 부모를 수정하여 자식들이 억지로 불필요한 구현을 안하게 해야 합니다.



