---
layout: single
title: "#11. [디자인 패턴-구조 패턴] Flyweight"
categories: "pattern"
tag: ["디자인 패턴", "구조 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Flyweight](https://tango1202.github.io/pattern/pattern-flyweight/)는 개체를 공유하여 효율적으로 메모리를 사용하게 하는 패턴입니다.

# 설명

보통 편집 프로그램에서 사용되는 개체는 글꼴, 테두리, 채우기 속성을 가지고 있습니다. 대부분 Look & Feel을 일관되게 유지하고자 동일한 글꼴과, 테두리와, 채우기 속성을 사용합니다. 모든 개체의 글꼴, 테두리, 채우기 속성을 다르게 하는 문서는 거의 없죠. 즉, 여러 개체에서 동일한 속성을 빈번하게 사용합니다. 

이때 일일이 속성을 생성해서 사용한다면, 불필요하게 중복 생성될 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/58fc91da-c1be-47b8-8b2a-901cb01dd832)


따라서, 중복되는 것은 공유해서 사용하여 메모리를 알뜰하게 사용하는게 좋습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6ab5aa06-b5e4-4a8e-86bf-1c2c6578aad2)


이러한 경우 [Flyweight 패턴](https://tango1202.github.io/pattern/pattern-flyweight/)을 이용하여 개체를 공유할 수 있습니다.

다음 그림에서 `Client`가 `FlyweightFactory::GetFlyweight()`로 개체를 요청하면, `m_FlyweightPool`에 기존 것이 없다면 새로 만들어 리턴하고, 있다면 기존 것을 리턴합니다. 

또한 `Client`는 `UnsharedConcreteFlyweight`에 직접 접근하여 공유되지 않는 개체를 사용할 수도 있습니다.

![Flyweight](https://github.com/tango1202/tango1202.github.io/assets/133472501/5a2b152b-5ffb-475a-b794-395ca81e9ab3)

|항목|내용|
|--|--|
|`FlyweightFactory`|`Pool`을 이용하여 개체를 관리하며, 기존 것이 없다면 새로 만들고, 있다면 기존 것을 리턴합니다.|
|`Flyweight`|`Client`에서 사용하는 개체의 인터페이스 입니다.|
|`ConcreteFlyweight`|`Flyweight`를 구체화한 개체입니다.|
|`UnsharedConcreteFlyweight`|공유되지 않는 개체를 사용하고 싶을때 사용합니다.|
|`Client`|`FlyweightFactory`나 `UnsharedConcreteFlyweight`를 이용하여 `Flyweight`를 사용합니다.|

# 특징

보통 글꼴, 테두리, 채우기 속성은 여러 개체에서 동일한 속성을 사용합니다. 이렇게 동일한 속성을 사용할 경우 [Flyweight](https://tango1202.github.io/pattern/pattern-flyweight/)를 이용한다면 메모리를 효율적으로 사용할 수 있습니다.

# 예제

다음은 도형의 `Brush`를 [Flyweight](https://tango1202.github.io/pattern/pattern-flyweight/)로 구현한 예입니다. `BrushFactory`로부터 `Brush`를 생성할때 동일한 `Brush`이면 기존 것을 사용합니다.

1. #1 : `Brush`는 `Flyweight`입니다. `Draw()` 인터페이스를 제공합니다.
2. #2 : `SolidBrush`, `GradientBrush`는 `Brush`를 구체화한 클래스입니다.
3. #3 : `BrushFactory`는 `FlyweightFactory`로서 `m_Pool`에서 `Brush`를 관리합니다.
4. #4 : `GetSolidBrush()`와 `GetGradientBrush()`에서 `m_Pool`에 있는 기존 `Brush`를 리턴합니다. 이때 기존 것이 없다면 새로 생성한 것을 등록후 리턴합니다.
5. #5 : `Brush`가 `set`에서 관리되므로 `<` 연산을 구현합니다.
6. #6 : `Rectangle`은 외부에서 전달된 `Brush`를 사용합니다. `BrushFactory`에서 관리되는 `Brush` 이거나 사용자가 임의로 생성한 `Brush` 일 수 있습니다.
7. #7 : `BrushFactory`로 부터 `Brush`를 사용합니다. 이전 것이 있으면 이전 것이 리턴됩니다.

또한 공유되지 않은 `Brush`를 사용하고 싶을 때에는 #8과 같이 `BrushFactory`를 이용하지 않고 직접 생성하면 됩니다.

```cpp
// ----
// #1. Flyweight입니다. Draw() 인터페이스를 제공합니다.
// ----
class Brush {
protected:
    Brush() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Brush() = default; // 다형 소멸 하도록 public virtual   
private:
    Brush(const Brush&) = delete;
    Brush(Brush&&) = delete;
    Brush& operator =(const Brush&) = delete;
    Brush& operator =(Brush&&) = delete;          
public:
    virtual void Draw(int x, int y, int w, int h) const = 0;    

    // #5. 연관 컨테이너에서 사용하기 위해 < 구현이 필요합니다.
    virtual bool operator <(const Brush& other) const = 0;
};
// ----
// #2. Flyweight를 구체화한 클래스입니다.
// ----
class SolidBrush : public Brush {
    const char* m_Color;
public:
    explicit SolidBrush(const char* color) : m_Color{color} {
        assert(m_Color);    
    } 
    
    virtual void Draw(int x, int y, int w, int h) const override {
        std::cout << "SolidBrush : " << m_Color << std::endl;
    }  

    // #5
    virtual bool operator <(const Brush& other) const override {
        const std::type_info& thisType{typeid(*this)};
        const std::type_info& otherType{typeid(other)};

        if (thisType.before(otherType)) return true;
        if (otherType.before(thisType)) return false;

        // 타입이 같은 경우 < 검사
        return *this < dynamic_cast<const SolidBrush&>(other);
    }      
private:
    // #5
    bool operator <(const SolidBrush& other) const {
        return m_Color < other.m_Color;
    }
};
class GradientBrush : public Brush {
    const char* m_StartColor;
    const char* m_LastColor;
public:
    GradientBrush(const char* startColor, const char * lastColor) :
        m_StartColor{startColor}, 
        m_LastColor{lastColor} {
        assert(m_StartColor && m_LastColor);
    }

    virtual void Draw(int x, int y, int w, int h) const override {
        std::cout << "GradientBrush : " << m_StartColor << ", " << m_LastColor << std::endl;
    }

    // #5
    virtual bool operator <(const Brush& other) const override {
        const std::type_info& thisType{typeid(*this)};
        const std::type_info& otherType{typeid(other)};

        if (thisType.before(otherType)) return true;
        if (otherType.before(thisType)) return false;

        // 타입이 같은 경우 < 검사
        return *this < dynamic_cast<const GradientBrush&>(other);
    }  
private:
    // #5
    bool operator <(const GradientBrush& other) const {
        if (m_StartColor < other.m_StartColor) return true;
        if (other.m_StartColor < m_StartColor) return false;

        return m_LastColor < other.m_LastColor;
    }           
};
// ----
// #3. FlyweightFactory 입니다.
// ----
class BrushFactory {
    private:
    using BrushComparer = bool (*)(std::shared_ptr<Brush>, std::shared_ptr<Brush>);
    std::set<std::shared_ptr<Brush>, BrushComparer> m_Pool; // #3
public:
    BrushFactory() : m_Pool{Compare} {}
    ~BrushFactory() = default; 
private:
    BrushFactory(const BrushFactory&) = delete;
    BrushFactory(BrushFactory&&) = delete;
    BrushFactory& operator =(const BrushFactory&) = delete;
    BrushFactory& operator =(BrushFactory&&) = delete;          
public:
    // #4. 주어진 브러쉬가 있으면 기존것을 사용하고, 그렇지 않으면 새롭게 추가합니다.
    std::shared_ptr<Brush> GetSolidBrush(const char* color) {
        assert(color);
        
        std::shared_ptr<Brush> item{new SolidBrush{color}};

        auto result = m_Pool.insert(item);
        
        return *result.first; // 삽입되지 않았다면 기존 것을 리턴하고, 삽입되었다면 삽입한 것을 리턴합니다.
    }
    // #4
    std::shared_ptr<Brush> GetGradientBrush(const char* startColor, const char* lastColor) {
        assert(startColor && lastColor);

        std::shared_ptr<Brush> item{new GradientBrush{startColor, lastColor}};

        auto result = m_Pool.insert(item);
        
        return *result.first; // 삽입되지 않았다면 기존 것을 리턴하고, 삽입되었다면 삽입한 것을 리턴합니다.        
    }

    size_t GetCount() const {
        return m_Pool.size();
    }
private:
    // Brush를 대소 비교합니다.
    static bool Compare(std::shared_ptr<Brush> left, std::shared_ptr<Brush> right) {
        assert(left && right);
        return *left < *right;
    }
};

class Rectangle {
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;

    std::shared_ptr<Brush> m_Brush; // #6. BrushFactory에서 관리되는 Brush 이거나 사용자가 임의로 생성한 Brush 입니다.
public:
    Rectangle(int l, int t, int w, int h) : m_Left{l}, m_Top{t}, m_Width{w}, m_Height{h} {}

    void SetBrush(std::shared_ptr<Brush> brush) {
        m_Brush = brush;
    }    
    void Draw() const { 
        if (!m_Brush) { // 브러쉬가 없다면 그리지 않습니다.
            return;    
        }

        m_Brush->Draw(m_Left, m_Top, m_Width, m_Height);
    }
};

// ----
// 테스트 코드
// ----
BrushFactory factory;

Rectangle rectangle{1, 1, 10, 20};

rectangle.SetBrush(factory.GetSolidBrush("red"));
rectangle.Draw();

rectangle.SetBrush(factory.GetSolidBrush("black"));
rectangle.Draw();

rectangle.SetBrush(factory.GetSolidBrush("red")); // #7. 이전에 "red"를 사용해서, 기존 것을 리턴합니다.
rectangle.Draw();

rectangle.SetBrush(factory.GetGradientBrush("red", "black"));
rectangle.Draw();

EXPECT_TRUE(factory.GetCount() == 3); // #7. 4개 Brush중 `red`는 기존 것을 사용하므로, 3개만 추가되었습니다.       

rectangle.SetBrush(std::shared_ptr<Brush>{new SolidBrush{"red"}}); // #8. 공유되지 않은 Brush를 사용하려면 BrushFactory를 이용하지 않고 직접 생성합니다. 
```

