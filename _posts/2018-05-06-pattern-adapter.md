---
layout: single
title: "#6. [디자인 패턴-구조 패턴] Adapter"
categories: "pattern"
tag: ["디자인 패턴", "구조 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Adapter](https://tango1202.github.io/pattern/pattern-adapter/)는 기존 인터페이스와 서로 맞지 않은 개체(`Adpatee`)를 사용하기 위해 인터페이스를 맞춘 감싼 개체(*Wrapper*)입니다.

# 설명

프로젝트를 진행하다 보면, 다른 프로젝트에서 사용했던 클래스를 재활용할 수 있고, 외부 라이브러리에서 제공한 소스코드를 사용할 수도 있습니다.

이런 소스코드들이 현재 프로젝트와 인터페이스가 맞지 않다고 임의로 수정한다면, 다른 프로젝트와 충돌이 있을 수 있습니다. 그렇다고 복제해서 수정한다면, 코드가 중복될 우려도 있고, 다른쪽에서 버전업 되었을때 복제된 코드에도 일일이 반영해야 하는 문제가 있습니다.

이런 경우 [Adapter](https://tango1202.github.io/pattern/pattern-adapter/)로 감싸서 인터페이스를 맞춥니다.

# 클래스 Adpater와 개체 Adapter

[Adapter](https://tango1202.github.io/pattern/pattern-adapter/)는 구현 방법에 따라 [클래스 Adpater](https://tango1202.github.io/pattern/pattern-adapter/#%ED%81%B4%EB%9E%98%EC%8A%A4-adpater%EC%99%80-%EA%B0%9C%EC%B2%B4-adapter)와 [개체 Adapter](https://tango1202.github.io/pattern/pattern-adapter/#%ED%81%B4%EB%9E%98%EC%8A%A4-adpater%EC%99%80-%EA%B0%9C%EC%B2%B4-adapter)로 구분할 수 있습니다.

**클래스 Adpater**

[클래스 Adpater](https://tango1202.github.io/pattern/pattern-adapter/#%ED%81%B4%EB%9E%98%EC%8A%A4-adpater%EC%99%80-%EA%B0%9C%EC%B2%B4-adapter)는 `private` [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)을 통해 `Adaptee`를 포함합니다. [다중 상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%8B%A4%EC%A4%91-%EC%83%81%EC%86%8D)을 하며 `Request()`호출시 `Adaptee::SpecificRequest()`를 호출합니다.

![Adapter](https://github.com/tango1202/tango1202.github.io/assets/133472501/8f206ca3-28a7-4458-9cef-87aa241da0ee)

**개체 Adapter**

[개체 Adpater](https://tango1202.github.io/pattern/pattern-adapter/#%ED%81%B4%EB%9E%98%EC%8A%A4-adpater%EC%99%80-%EA%B0%9C%EC%B2%B4-adapter)는 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)로 `Adaptee`를 포함합니다. `Request()`호출시 `m_Adaptee.SpecificRequest()`를 호출합니다.

[개체 Adapter](https://github.com/tango1202/tango1202.github.io/assets/133472501/49143535-836d-43c3-a4af-c5c7ef44d6a9)의 경우 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 런타임에 변경할 수 있어 다른 개체로 교체 가능하며, `Adaptee`를 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)한 개체도 사용할 수 있어 확장성이 좋습니다.

![Adapter](https://github.com/tango1202/tango1202.github.io/assets/133472501/49143535-836d-43c3-a4af-c5c7ef44d6a9)

|항목|내용|
|--|--|
|`Target`|현재 시스템에서 사용중인 인터페이스입니다.|
|`Adaptee`|`Adapter`로 감싼 기존 개체입니다.|
|`Adapter`|`Target`인터페이스로 `Adaptee`를 사용할 수 있게 하는 개체입니다.|
|`Client`|`Target`인터페이스를 필요로하는 개체입니다.|

# 특징

`Adaptee`가 다른 프로젝트에서도 사용되는 코드여서 함부로 수정하면 안되거나, 소스코드 없이 제공되는 외부 라이브러리인 경우 유용합니다.

# 예제

다음 예는 `Shape` 을 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)한 개체들을 출력하는 예입니다. `Shape`의 `Draw()`함수를 이용하는데요, `Circle` 개체는 `Shape`을 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)하지 않은 클래스여서 #5와 같이 사용할 수 없습니다. 

`Circle`이 `Shape`을 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)하도록 리팩토링하면 될일입니다만, 만약 다른 프로젝트에서도 사용되는 코드라면 함부로 수정해선 안됩니다. 다른 프로젝트에 영향을 주니까요. 혹은 소스코드 없이 라이브러리로 제공되는 것이라면, 아예 수정할 수 없죠. 

이럴때 [Apdater](https://tango1202.github.io/pattern/pattern-adapter/)를 이용하여 감싸면 `Shape`처럼 사용할 수 있습니다.

1. #1 : `Shape`은 `Draw()`를 제공하는 부모 클래스입니다.
2. #2 : `Rectangle`과 `Ellipse`는 `Shape`을 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)하여 `Draw()`를 제공합니다.
3. #3 : `Circle`은 `Shape`을 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)하지 않아 `Draw()`를 제공하지는 않지만, 동일한 기능인 `Render()`를 제공합니다.
4. #4 : [클래스 Adapter](https://tango1202.github.io/pattern/pattern-adapter/#%ED%81%B4%EB%9E%98%EC%8A%A4-adpater%EC%99%80-%EA%B0%9C%EC%B2%B4-adapter)와 [개체 Adapter](https://tango1202.github.io/pattern/pattern-adapter/#%ED%81%B4%EB%9E%98%EC%8A%A4-adpater%EC%99%80-%EA%B0%9C%EC%B2%B4-adapter)방식으로 `Circle`개체에서 `Shape`인터페이스를 제공합니다.
5. #5 : `Circle`은 `Shape`이 아니므로 `std::vector<std::unique_ptr<Shape>>`에 추가할 수 없습니다.
6. #6 : [Adapter](https://tango1202.github.io/pattern/pattern-adapter/)를 이용하여 `Circle`을 `std::vector<std::unique_ptr<Shape>>`에 추가하여 사용할 수 있습니다.

```cpp
class Shape {
protected:
    Shape() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
private:
    Shape(const Shape&) = delete; 
    Shape(Shape&&) = delete; 
    Shape& operator =(const Shape&) = delete; 
    Shape& operator =(Shape&&) = delete;   
public:
    virtual ~Shape() = default; // 다형 소멸 하도록 public virtual    

    virtual void Draw() const = 0; // #1
};
class Rectangle : public Shape {
public:
    Rectangle() = default;

    virtual void Draw() const override { // #2
        std::cout << "Rectangle::Draw()" << std::endl;
    }
};
class Ellipse : public Shape {
public:
    Ellipse() = default;

    virtual void Draw() const override { // #2
        std::cout << "Ellipse::Draw()" << std::endl;
    }
};

class Circle { // Shape을 상속하지 않았습니다.
public:
    Circle() = default;

    void Render() const { // #3
        std::cout << "Circle::Render()" << std::endl;            
    }
};  

// ----
// 클래스 Adapter. Circle 클래스를 Shape 처럼 사용할 수 있게 해줍니다.
// ----
class CircleClassAdapter : public Shape, private Circle {
public:
    CircleClassAdapter() = default;

    virtual void Draw() const override { // #4
        Render();
    }   
};

// ----
// 개체 Adapter. Circle 개체를 Shape처럼 사용할 수 있게 해줍니다.
// ----
class CircleObjectAdapter : public Shape {
private:
    std::unique_ptr<Circle> m_Circle;
public:
    explicit CircleObjectAdapter(std::unique_ptr<Circle> circle) : m_Circle(std::move(circle)) {}
    virtual void Draw() const override {  // #4
        m_Circle->Render();
    } 
};

// ----
// 테스트 코드
// ----
std::vector<std::unique_ptr<Shape>> v;
v.emplace_back(new Rectangle{}); 
v.emplace_back(new Ellipse{});
// v.emplace_back(new Circle{}); // (X) #5. 컴파일 오류. Circle은 Shape이 아닙니다.
v.emplace_back(new CircleClassAdapter{}); // #6
v.emplace_back(new CircleObjectAdapter{std::unique_ptr<Circle>{new Circle{}}}); // #6

for (auto& shape : v) {
    shape->Draw();
}    
```
