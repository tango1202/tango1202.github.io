---
layout: single
title: "#10. [고전 C++ 가이드] 추상 클래스, 인터페이스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
https://en.cppreference.com/w/cpp/language/abstract_class
추상클래스 참고

# 추상 클래스

인스턴스화 할 수 없는 부모 클래스를 추상 클래스라고 합니다. 추상 클래스는 구체 구현한 자식 클래스들의 공통적인 일반 개념(기능 스펙)을 정의하는데 사용합니다.

* 다형 소멸을 하려면 `public` Virtual 소멸자를 사용합니다.
* 추상 클래스는 1개 이상의 순가상 함수가 있어야 합니다.

```cpp
class Shape {
    // 모든 도형은 왼쪽 상단 좌표와 크기를 가집니다.
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    virtual ~Shape() {} // 다형 소멸 하도록 public virtual
    virtual void Draw() const = 0; // 순가상 함수입니다. 자식 클래스에서 구체화 해야 합니다.
};

// Shape을 구체화한 클래스 입니다. Draw()에서 사각형을 그립니다.
class Rectangle : public Shape {
public:
    virtual void Draw() const {
        std::cout<<"Rectangle::Draw()"<<std::endl;
    }
};
// Shape을 구체화한 클래스 입니다. Draw()에서 타원을 그립니다.
class Ellipse : public Shape {
public:
    virtual void Draw() const {
        std::cout<<"Ellipse::Draw()"<<std::endl;
    }
};

Shape shape; // (X) 컴파일 오류. 추상 클래스는 순가상 함수가 있어 구체화 할 수 없습니다.
Shape* p = new Shape; // (X) 컴파일 오류. 추상 클래스는 순가상 함수가 있어 구체화 할 수 없습니다.

Shape* shapes[2] = { // 도형들을 Shape* 로 관리합니다.
    new Rectangle(), 
    new Ellipse()
};

for(int i = 0; i < 3; ++i) {
    shapes[i]->Draw(); // 다형적으로 그립니다.
}
for(int i = 0; i < 2; ++i) {
    delete shapes[i]; // 다형 소멸 합니다. Shape*로 Rectangle, Ellipse을 소멸합니다.
} 
```

순가상 함수로 정의할게 마땅히 없는데, 추상 클래스로 만들어야 한다면, 순가상 소멸자를 이용하면 됩니다.

```cpp
class Abstract {
public:
    virtual ~Abstract() = 0 // 다형 소멸 하도록 public virtual, 마땅히 할게 없어 순가상
};
Abstract::~Abstract() {} // 실제 구현 정의가 있어야 함

class Concrete : public Abstract {

};
```






마땅이 할게 없으면 다음과 같이 순가상 함
수로 만들어라. virtual void Draw() = 0; 순수 가상 함수가 1개 이상있으면 추상클래
스임. 추상 클래스는 인스턴스화 될 수 없음. Shape s; // (x) 컴파일 오류

# 인터페이스

추상 클래스는 다른 클래스의 기본 클래스
혹은 인터페이스로 사용될 수 있다. 파생 클래스의 기능을 명세화하여 서로간
의 계약에 의한 프로그래밍이 가능하다.

# 좋은 상속, 나쁜 상속

기본 클래스의 무의미한 구현



