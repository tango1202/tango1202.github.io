---
layout: single
title: "#10. [고전 C++ 가이드] 추상 클래스, 인터페이스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 기능 스펙을 정의하여 **코딩 계약**을 맺으려면 인터페이스로 정의하라.
> * 기능 스펙과 어느 정도의 공통 기능을 제공하려면 추상 클래스로 정의하라.

# 추상 클래스

인스턴스화 할 수 없는 부모 클래스를 추상 클래스라고 합니다. 추상 클래스는 구체 구현한 자식 클래스들의 공통적인 일반 개념(기능 스펙)을 정의하는데 사용합니다.

* 일반적으로 추상 클래스는 복사 생성자, 대입 연산자를 `private`로 막고, 다형적 동작을 하기 위해 가상 복사 생성자를 사용합니다.([가상 복사 생성자](??) 참고, [부모 개체의 대입 연산자](??) 참고)
* 다형 소멸을 하려면 `public` Virtual 소멸자를 사용합니다.
* 추상 클래스는 1개 이상의 순가상 함수가 있어야 합니다.

```cpp
class Shape {
    // 모든 도형은 왼쪽 상단 좌표와 크기를 가집니다.
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
private:
    Shape(const Shape& other) {} // 추상 클래스여서 외부에서 사용 못하게 복사 생성자 막음
    Shape& operator =(const Shape& other) {return *this;} // 추상 클래스여서 외부에서 사용 못하게 대입 연산자 막음    
protected:
    Shape() {} // 추상 클래스여서 상속한 개체에서만 생성할 수 있게함     
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

for(int i = 0; i < 2; ++i) {
    shapes[i]->Draw(); // 다형적으로 그립니다.
}
for(int i = 0; i < 2; ++i) {
    delete shapes[i]; // 다형 소멸 합니다. Shape*로 Rectangle, Ellipse을 소멸합니다.
} 
```
**마땅한 순가상 함수가 없는 추상 클래스**

순가상 함수로 정의할게 마땅히 없는데, 추상 클래스로 만들어야 한다면, 순가상 소멸자를 이용하면 됩니다.

```cpp
class Abstract {
public:
    virtual ~Abstract() = 0; // 다형 소멸 하도록 public virtual, 마땅히 할게 없어 순가상
};
Abstract::~Abstract() {} // 실제 구현 정의가 있어야 함

class Concrete : public Abstract {
};

Abstract abstract; // (X) 컴파일 오류. 추상 클래스는 순가상 함수가 있어 구체화 할 수 없습니다.

Concrete concrete; // (O)
```

# 인터페이스

추상 클래스중 구체 구현없이 모두 추상화된 기능 스펙으로 구성된 클래스를 특별히 인터페이스라고 합니다.

* 일반적으로 인터페이스는 구체 구현이 없으므로 복사 생성자, 대입 연산자를 `private`로 막습니다. 특히 부모 개체의 대입 연산자는 꼭 막아두는게 좋습니다.([부모 개체의 대입 연산자](??) 참고)
* 일반적으로 인터페이스는 기능 스펙만 제공하고, 다형 소멸을 제공하지 않습니다. 다형 소멸을 제공하는건 그냥 추상 클래스라고 보시는게 좋습니다.
* 다형 소멸을 하지 않으므로 `protected` Non-Virtual 소멸자를 사용합니다.
* 인터페이스는 모두 순가상 함수로 구성됩니다. 단, 소멸자를 순가상 함수로 만들면, 정의를 따로 해야하므로, 그냥 `protected` Non-Virtual 소멸자로 정의합니다.

다음은 `IDrawable` 인터페이스로 `Shape` 클래스를 구현한 예입니다. 

1. `IDrawable` 은 인터페이스여서 기본 생성자를 상속한 개체에서만 사용할 수 있도록 `protected` 를 사용합니다.
2. `IDrawable` 은 인터페이스여서 복사 생성자, 대입 연산자를 외부에서 사용하지 못하도록 `private`을 사용합니다.
3. `IDrawable` 은 인터페이스 여서 `protected` Non-Virtual 소멸자를 사용합니다.
4. `IDrawable`에 `Draw()` 순가상 함수를 선언합니다.
5. `Shape`은 `IDrawable`을 상속하고, 다형 소멸을 할 것이므로 `public` Virtual 소멸자를 사용합니다. (상속한 `IDrawable`의 `Draw()`함수가 구체화 되지 않았으므로 여전히 추상 클래스입니다.)

```cpp
// 인터페이스
class IDrawable {
private:
    IDrawable(const IDrawable& other) {} // 인터페이스여서 외부에서 사용 못하게 복사 생성자 막음
    IDrawable& operator =(const IDrawable& other) {return *this;} // 인터페이스여서 외부에서 사용 못하게 대입 연산자 막음    
protected:
    IDrawable() {} // 인터페이스여서 상속한 개체에서만 생성할 수 있게함 
    ~IDrawable() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다. 
public:
    virtual void Draw() const = 0; // 순가상 함수입니다. 자식 클래스에서 구체화 해야 합니다.
};

// 추상 클래스
class Shape : 
    public IDrawable { // Shape은 IDrawable 인터페이스를 제공합니다.
    // 모든 도형은 왼쪽 상단 좌표와 크기를 가집니다.
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    virtual ~Shape() {} // 다형 소멸 하도록 public virtual
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

IDrawable drawable; // (X) 컴파일 오류. 인터페이스는 순가상 함수가 있어 구체화 할 수 없습니다.
IDrawable* p = new IDrawable; // (X) 컴파일 오류. 인터페이스는 순가상 함수가 있어 구체화 할 수 없습니다. 

// (O) Shape으로 다형 소멸 합니다.
Shape* shapes[2] = { // 도형들을 Shape* 로 관리합니다.
    new Rectangle(), 
    new Ellipse()
};

// (O) Shape이 IDrawable을 상속했으므로 Draw() 할 수 있습니다.
for(int i = 0; i < 2; ++i) {
    shapes[i]->Draw(); // 다형적으로 그립니다.
}

for(int i = 0; i < 2; ++i) {
    delete shapes[i]; // 다형 소멸 합니다. Shape*로 Rectangle, Ellipse을 소멸합니다.
} 
```

`Draw()` 함수는 `IDrawable`의 함수 이므로 다음과 같이 `IDrawble` 인터페이스로 실행할 수 있습니다.

```cpp
class DrawUtil {
public:
    static void Draw(const IDrawable& drawable) {
        drawable.Draw();
    }    
};

// (O) IDrawable 인터페이스로 Draw() 할 수 있습니다.
Shape* shapes[2] = { // 도형들을 Shape* 로 관리합니다.
    new Rectangle(), 
    new Ellipse()
};

// (O) IDrawable 인터페이스로 다형적으로 그립니다. 
for(int i = 0; i < 2; ++i) {
    DrawUtil::Draw(*shapes[i]); 
}
for(int i = 0; i < 2; ++i) {
    delete shapes[i]; // 다형 소멸 합니다. Shape*로 Rectangle, Ellipse을 소멸합니다.
} 
```

인터페이스는 다형 소멸을 지원하지 않으므로, 다음과 같이 `IDrawable` 포인터로 `delete`할 수 없습니다.

```cpp
// (X) 컴파일 오류. protected 소멸자 이므로, IDrawable로 다형 소멸 되지 않습니다.
IDrawable* drawables[2] = {
    new Rectangle(), 
    new Ellipse()
};

for(int i = 0; i < 3; ++i) {
    drawables[i]->Draw(); // 다형적으로 그립니다.
}

for(int i = 0; i < 2; ++i) {
    delete drawables[i]; // (X) 컴파일 오류. 인터페이스는 다형 소멸을 제공하지 않습니다.
}     
```



