---
layout: single
title: "#4. [개체지향 원칙] 리스코프 치환 원칙(Liskov Substitution Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

리스코프 치환 원칙은 **자식 개체는 부모 개체를 완전하게 치환할 수 있어야 한다** 는 원칙입니다. 

조금 풀어 쓰면,

1. 다형적 동작을 위해 상속받은 자식은 부모와 **[is-a 관계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#is-a-%EA%B4%80%EA%B3%84)** 여야 하고,
2. 자식은 부모와 다른 행동을 하지 말고, 같은 행동을 하게 하라.

라는 뜻입니다. 더군다나 !!!완전하게!!!말이죠.

이 원칙을 준수하면,

1. 코드 분석시 불필요하게 하위 개체의 구현을 신경 쓰지 않아도 되므로 코드 분석 용이성이 향상됩니다.
2. **코딩 계약** 에 의한 코드 구현시 신뢰성이 확보되고, 이에 따른 시스템 안정성도 확보됩니다.

**위반 사례 : 부모와 다른 동작을 하는 자식**

우리는 개체지향의 다형성을 이용하여 부모 클래스를 상속하고 기능 확장이나 변형을 하게 됩니다.

왼쪽/상단 좌표와 크기를 입력받아 자신의 모양을 출력하는 `Shape` 개체를 생각해 봅시다. 부모 클래스인 `Shape`에서 `m_Left`, `m_Top`, `m_Width`, `m_Height` 값을 입력받고, `Draw()` 함수를 `virtual`로 작성하여 자식 클래스에서 재구현 하여 그릴 수 있게 했습니다.

```cpp
// 부모 클래스 입니다.
class Shape {
private:
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Shape(int l, int t, int w, int h) :
        m_Left(l),
        m_Top(t),
        m_Width(w),
        m_Height(h) {}
    virtual ~Shape() {} // 다형 소멸 하도록 public virtual
public:
    // Get/Set 함수
    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetLeft(int val) {m_Left = val;}
    void SetTop(int val) {m_Top = val;}
    void SetWidth(int val) { m_Width = val;}
    void SetHeight(int val) { m_Height = val;}

    // m_Left, m_Top, m_Width, m_Height로 자식 클래스들이 알아서 그려야 함
    virtual void Draw() const = 0; 
};
```

하기는 자식 클래스 입니다. `Draw()`함수를 각각 잘 구현했다 가정하고요.

```cpp
// 자식 클래스 입니다.
class Rectangle : public Shape {
public:    
    Rectangle(int l, int t, int w, int h) : 
        Shape(l, t, w, h) {}
    virtual ~Rectangle() override {}

    virtual void Draw() const override {
        // 사각형을 그립니다.
    }
};

// 자식 클래스 입니다.
class Ellipse : public Shape {
public:
    Ellipse(int l, int t, int w, int h) : 
        Shape(l, t, w, h) {}
    virtual ~Ellipse() override {}

    virtual void Draw() const override {
        // 타원을 그립니다.
    }
};
```

하기는 테스트 입니다. `Rectangle`과 `Ellipse`를 생성하고, Draw를 호출하여 자기 자신을 그립니다.

```cpp
TEST(TestPrinciple, LiskovSubstitution) {
    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);

    // 생성자에 입력한 값이 잘 전달됐는지 확인합니다.
    EXPECT_TRUE(rect.GetLeft() == 0 && rect.GetTop() == 0 && rect.GetWidth() == 10 && rect.GetHeight() == 20);   
    EXPECT_TRUE(ellipse.GetLeft() == 0 && ellipse.GetTop() == 0 && ellipse.GetWidth() == 10 && ellipse.GetHeight() == 20);  

    // 부모 클래스 포인터로 받아서 Draw를 호출해 봅니다.
    std::vector<Shape*> shapes;
    shapes.push_back(&rect);
    shapes.push_back(&ellipse);
    for(std::vector<Shape*>::iterator itr = shapes.begin(); itr != shapes.end(); ++itr) {
        (*itr)->Draw();
    }
}
```

아직은 좋습니다. `Draw()`를 잘 구현했다면, 사각형과 타원을 잘 그릴 것입니다. 이제 정사각형을 추가해 봅시다. 

정사각형은 `m_Width`와 `m_Height`가 동일해야 합니다. 그래서 부모 클래스인 `Shape`의 `SetWidth()`와 `SetHeight()`를 재구현하여 동기화할 필요가 있습니다. 

먼저 부모 클래스의 `SetWidth()`와 `SetHeight()`를 `virtual`로 만들어 재구현 할 수 있게 합니다.

```cpp
// 부모 클래스 입니다.
class Shape {
    ...
    // 자식이 재구현할 수 있도록 virtual로 변경합니다.
    virtual void SetWidth(int val) {m_Width = val;}
    virtual void SetHeight(int val) {m_Height = val;}
    ...
};
```

다음으로 정사각형을 그리는 `Square` 클래스를 구현합니다. 생성자에서 `h` 값은 무시하고 `w` 값을 사용하도록 조정하고, `SetWidth()` 와 `SeitHeight()` 에서도 `m_Width` 값을 사용하도록 재구현했습니다.

```cpp
// 자식 클래스입니다. 생성자를 손보고, SetWidth와 SetHeight를 손봤습니다.
class Square : public Shape {
public:
    // h 는 무시하고 w만 사용합니다.
    Square(int l, int t, int w, int /*h*/) : 
        Shape(l, t, w, w) {}
    virtual ~Square() override {}

    // m_Width를 바꾸면 m_Height도 바꿉니다.
    virtual void SetWidth(int val) override {
        Shape::SetWidth(val);
        Shape::SetHeight(val);
    }
    // m_Height를 바꾸면 m_Width를 바꿉니다. 어차피 SetWidth()랑 똑같이 동작하니 SetWidth를 불러줍니다.
    virtual void SetHeight(int val) override {
        SetWidth(val);
    }

    virtual void Draw() const override {
        // 정사각형을 그립니다.
    }
};
```

하기는 테스트 코드입니다. `Square`를 생성하고 `Draw()`합니다.

```cpp
TEST(TestPrinciple, LiskovSubstitution) {
    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);
    Square square(0, 0, 10, 20); // m_Height는 내부적으로 10으로 설정됩니다.
 
    // 생성자에 입력한 값이 잘 전달됐는지 확인합니다.
    EXPECT_TRUE(rect.GetLeft() == 0 && rect.GetTop() == 0 && rect.GetWidth() == 10 && rect.GetHeight() == 20);   
    EXPECT_TRUE(ellipse.GetLeft() == 0 && ellipse.GetTop() == 0 && ellipse.GetWidth() == 10 && ellipse.GetHeight() == 20);  
    // 정사각형은 m_Width와 m_Height가 같아야 합니다.
    EXPECT_TRUE(square.GetWidth() == square.GetHeight());  

    // 부모 클래스 포인터로 받아서 Draw를 호출해 봅니다.
    std::vector<Shape*> shapes;
    shapes.push_back(&rect);
    shapes.push_back(&ellipse);
    shapes.push_back(&square);
    for(std::vector<Shape*>::iterator itr = shapes.begin(); itr != shapes.end(); ++itr) {
        (*itr)->Draw();
    }
}
```

악취가 나실까요? 아직은 동작하는데 큰 이상이 없습니다만, `Square`의 구조를 모르는 다른 동료들이 테스트하다가 낭패를 경험할 수 있습니다.

```cpp
TEST(TestPrinciple, LiskovSubstitution) {
    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);
    Square square(0, 0, 10, 20); // m_Height는 내부적으로 10으로 설정됩니다.
 
    // 생성자에 입력한 값이 잘 전달됐는지 확인합니다.
    std::vector<Shape*> shapes;
    shapes.push_back(&rect);
    shapes.push_back(&ellipse);
    shapes.push_back(&square);
    for(std::vector<Shape*>::iterator itr = shapes.begin(); itr != shapes.end(); ++itr) {
        EXPECT_TRUE((*itr)->GetWidth() == 10);
        EXPECT_TRUE((*itr)->GetHeight() == 20); // Fail. Square의 m_Height는 m_Width인 10입니다.
    }
}
```

그렇죠. `Square`는 `m_Width` 값을 `m_Height`에 세팅하니 당연히 테스트를 실패하겠죠. 이제 `Square`의 특성을 알았으니 주의해서 사용하면 될까요? 이런 자식 개체가 또 뭐가 있을까요? 단순하게 Circle이 있겠고요, 또 더 있을 수 있을까요? 겁이 나서 모든 자식들의 소스코드와 테스트케이스들을 확인해 봐야 할까요? 이크!!! 라이브러리라 소스코드를 못보네요? 

이런 경우가 리스코프 치환 원칙을 위반한 것입니다. 자식이 부모와 다른 동작을 하게 되면 부모 개체를 안심하고 사용할 수 없게 됩니다. 자식 코드를 다 파악하고 있어야지만, 안심하고 사용할 수 있게 되죠.

이 경우엔 부모가 잘못한 것이라 생각합니다. 모든 `Shape`이 `m_Width`와 `m_Height`를 가진다는 잘못된 가정을 한겁니다. 그래서 자식은 어쩔수 없이 `SetWidth()`와 `SetHeight()`를 재구현할 수 밖에 없었고요.

사실 정사각형은 길이 하나만 가지고 있는 편이 좋고, 원은 지름을 가지고 있는 편이 좋습니다. 직선은 시작점과 끝점을 가지고 있는 편이 좋고, 호는 반지름과 각도를 가지고 있는 편이 좋고요. 이 정보로부터 나중에 너비와 높이를 계산해 낼 수 있죠.
즉, 부모가 쓸데없이 `m_Width`와 `m_Height`를 가진 거라 보면 됩니다. 자식들이 헷갈리게요.

**준수 방법 : 부모 클래스 간소화**

부모 클래스가 불필요한 인터페이스를 강제하므로, 부모를 수정하여 자식들이 억지로 불필요한 구현을 안하게 해야 합니다. 

먼저 `Shape`에서 `m_Width`와 `m_Height`를 뺍니다.

```cpp
// 부모 클래스 입니다. m_Width와 m_Height를 뺐습니다.
class Shape {
private:
    int m_Left;
    int m_Top;
public:
    Shape(int l, int t) :
        m_Left(l),
        m_Top(t) {}
    virtual ~Shape() {} // 다형 소멸 하도록 public virtual
public:
    // Get/Set 함수
    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}

    void SetLeft(int val) {m_Left = val;}
    void SetTop(int val) {m_Top = val;}

    // m_Left, m_Top으로 자식 클래스들이 알아서 그려야 함
    virtual void Draw() const = 0; 
};
```

다음으로 자식인 `Rectangle`과 `Ellipse`에 `m_Width`와 `m_Height`를 구현합니다.

```cpp
// 자식 클래스 입니다. m_Width와 m_Height를 제공합니다.
class Rectangle : public Shape {
private:
    int m_Width;
    int m_Height;
public:    
    Rectangle(int l, int t, int w, int h) : 
        Shape(l, t),
        m_Width(w),
        m_Weight(h) {}
    virtual ~Rectangle() override {}   
    
    // Get/Set 함수
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetWidth(int val) {m_Width = val;}
    void SetHeight(int val) {m_Height = val;}

    virtual void Draw() const override {
        // m_Left, m_Top, m_Width, m_Height로 사각형을 그립니다.
    }
};

// 자식 클래스 입니다. m_Width와 m_Height를 제공합니다.
class Ellipse : public Shape {
private:
    int m_Width;
    int m_Height;
public:
    Ellipse(int l, int t, int w, int h) : 
        Shape(l, t),
        m_Width(w),
        m_Height(h) {}
    virtual ~Ellipse() override {}

    // Get/Set 함수
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetWidth(int val) {m_Width = val;}
    void SetHeight(int val) {m_Height = val;}

    virtual void Draw() const override {
        // m_Left, m_Top, m_Width, m_Height로 타원을 그립니다.
    }
};
```

다음으로 `Square`를 구현합니다. 이제 억지로 `m_Width`, `m_Height`를 사용하지 않고, `m_Length`로 구현했습니다.

```cpp
// 자식 클래스입니다. m_Length를 제공합니다.
class Square : public Shape {
public:
    int m_Length;
public:
    // width, height없이 length 하나만 사용합니다.
    Square(int l, int t, int length) : 
        Shape(l, t),
        m_Length(length) {}
    virtual ~Square() override {}

    // Get/Set 함수
    int GetLength() const {return m_Length;}

    void SeLength(int val) {m_Length = val;}

    virtual void Draw() const override {
        // m_Left, m_Top, m_Length로 정사각형을 그립니다.
    }
};
```

이제 자식인 `Square`가 부모인 `Shape`과 다른 동작을 하지 않습니다. `Shape` 과 `Square` 모두 `GetWidth()`, `GetHeight()` 를 아예 제공하지 않으니까요.

다음과 같이 테스트 해야 합니다.

```cpp
TEST(TestPrinciple, LiskovSubstitution) {
    Rectangle rect(0, 0, 10, 20);
    Ellipse ellipse(0, 0, 10, 20);
    Square square(0, 0, 10); // length를 10으로 설정합니다.

    // 생성자에 입력한 값이 잘 전달됐는지 확인합니다.
    EXPECT_TRUE(rect.GetWidth() == 10 && rect.GetHeight() == 20);
    EXPECT_TRUE(ellipse.GetWidth() == 10 && ellipse.GetHeight() == 20);
    EXPECT_TRUE(square.GetLength() == 10);
}
```

한가지 아쉬운 점은 `Rectangle`과 `Ellipse`구현시 `m_Width`, `m_Height` 구현에 코드 중복이 있다는 것입니다. [스스로 반복하지 마라](https://tango1202.github.io/principle/principle-dont-repeat-yourself/)를 위배했습니다.

이는 인터페이스를 분리([인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/))한 뒤, 상속(**has-a 관계** )하여 개선할 수 있습니다.

먼저 `m_Width`와 `m_Height`를 제공하는 `IResizeable`인터페이스를 만듭니다.

```cpp
// 크기 변경이 가능한 개체입니다.
class IResizeable {
protected : 
    ~IResizeable() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.
public:
    // Get/Set을 구현해야 합니다.
    virtual int GetWidth() const = 0;
    virtual int GetHeight() const = 0;

    virtual void SetWidth(int val) = 0;
    virtual void SetHeight(int val) = 0;   
};
```

다음으로 `IResizeable`인터페이스를 구현한 `ResizeableImpl`을 만듭니다.

```cpp
// IResizeable 구현한 클래스입니다. 다른 클래스에서 상속받아 기능을 포함합니다.
class ResizeableImpl : public IResizeable {
private:
    int m_Width;
    int m_Height;    
protected:
    ResizeableImpl(int w, int h) :
        m_Width(w), 
        m_Height(h) {}
    ~ResizeableImpl() {} // has-a 관계로 사용되기 때문에, 단독으로 생성되지 않도록 protected입니다.
public:    
    // Get/Set 함수
    virtual int GetWidth() const override {return m_Width;}
    virtual int GetHeight() const override {return m_Height;}

    virtual void SetWidth(int val) override {m_Width = val;}
    virtual void SetHeight(int val) override {m_Height = val;}
};
```

마지막으로 `Rectangle`과 `Ellipse`의 `m_Width`, `m_Height`관련 코드를 지우고, `ResizeableImpl`을 상속받아 **has-a 관계** 를 만들어 줍니다. 그러면 `m_Width`, `m_Height` 코드 중복을 제거할 수 있습니다.

```cpp
// 자식 클래스 입니다. ResizeableImpl을 포함하여 m_Width와 m_Height를 제공합니다.
class Rectangle : 
    public Shape, 
    public ResizeableImpl {
public:    
    Rectangle(int l, int t, int w, int h) : 
        Shape(l, t), 
        ResizeableImpl(w, h) {}
    virtual ~Rectangle() override {}   
  
    virtual void Draw() const override {
        // m_Left, m_Top, IResizeable::GetWidth(), IResizeable::GetHeight()로 사각형을 그립니다.
    }
};

// 자식 클래스 입니다. ResizeableImpl을 포함하여 m_Width와 m_Height를 제공합니다.
class Ellipse : 
    public Shape, 
    public ResizeableImpl {
public:
    Ellipse(int l, int t, int w, int h) : 
        Shape(l, t), 
        ResizeableImpl(w, h) {}
    virtual ~Ellipse() override {}
 
    virtual void Draw() const override {
        // m_Left, m_Top, IResizeable::GetWidth(), IResizeable::GetHeight()로 타원을 그립니다.
    }
};
```


