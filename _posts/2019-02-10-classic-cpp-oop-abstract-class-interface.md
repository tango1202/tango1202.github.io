---
layout: single
title: "#10. [고전 C++ 개체 지향] 추상 클래스, 인터페이스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 기능 스펙을 정의하여 **코딩 계약**을 맺으려면 인터페이스로 정의하라.
> * 기능 스펙과 어느 정도의 공통 기능을 제공하려면 추상 클래스로 정의하라.

> **모던 C++**
> * `default`와 `delete`가 추가되어 암시적으로 생성되는 멤버 함수의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.([default와 delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete) 참고)

# 추상 클래스

인스턴스화 할 수 없는 부모 클래스를 추상 클래스라고 합니다. 추상 클래스는 구체 구현한 자식 클래스들의 공통적인 일반 개념(기능 스펙)을 정의하는데 사용합니다.

* [상속 강제](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C)를 해야 하므로 `protected` 생성자를 사용합니다.
* 추상 클래스는 복사 생성자를 사용하지 못하도록 `private`로 막던지, 상속해서만 사용할 수 있도록 `protected`로 만들고, 자식 개체에서 가상 복사 생성자를 구현합니다.([가상 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EA%B0%80%EC%83%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)
* 추상 클래스는 복사 대입 연산자를 `private`로 막습니다.([부모 개체의 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)
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
    Shape& operator =(const Shape& other) {return *this;} // 추상 클래스여서 외부에서 사용 못하게 복사 대입 연산자 막음    
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
        std::cout << "Rectangle::Draw()" << std::endl;
    }
};
// Shape을 구체화한 클래스 입니다. Draw()에서 타원을 그립니다.
class Ellipse : public Shape {
public:
    virtual void Draw() const {
        std::cout << "Ellipse::Draw()" << std::endl;
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
> *C++11 부터는 `default`와 `delete`가 추가되어 암시적으로 생성되는 복사 생성자와 복사 대입 연산자의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.([default와 delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete) 참고)*

**마땅한 순가상 함수가 없는 추상 클래스**

드문 경우지만, 순가상 함수로 정의할게 마땅히 없는데 추상 클래스로 만들어야 한다면, [순가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%88%9C%EA%B0%80%EC%83%81-%EC%86%8C%EB%A9%B8%EC%9E%90)를 이용하면 됩니다.

추상 클래스는 다형적으로 사용될 수도 있기 때문에 `public` Virtual 소멸자로 작성될 필요도 있는데요, 하지만 다음처럼, 순가상 함수가 하나도 없다면 추상 클래스가 인스턴스화 될 수 있습니다.

```cpp
class Abstract {
public:
    virtual ~Abstract() {} // 다형 소멸 하도록 public virtual
};

Abstract abstract; // (X) 오동작. 순가상 함수가 없어 인스턴스화 됩니다. 추상 클래스여서 인스턴스화 되면 안됩니다.
```

이런 경우 `protected` 생성자를 이용하여 [상속 강제](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C)할 수 있습니다만,

```cpp
class Abstract {
protected:
    Abstract() {} // 상속을 강제합니다.
public:
    virtual ~Abstract() {} // 다형 소멸 하도록 public virtual
};

Abstract abstract; // (X) 정상 코딩 계약. 추상 클래스를 인스턴스화 하지 못합니다.
```

이방법 보다는 순가상 함수가 있는 추상 클래스라는 의미가 강하도록, 소멸자를 순가상으로 만드는게 좋습니다.

```cpp
class Abstract {
public:
    virtual ~Abstract() = 0; // 다형 소멸 하도록 public virtual, 마땅히 할게 없어 순가상
};
Abstract::~Abstract() {} // 실제 구현 정의가 있어야 함

class Concrete : public Abstract {};

Abstract abstract; // (X) 정상 코딩 계약. 추상 클래스는 순가상 함수가 있어 인스턴스화 할 수 없습니다.
Concrete concrete; // (O) 상속하면 인스턴스화 가능
```

# 인터페이스

추상 클래스중 구체 구현없이 모두 추상화된 기능 스펙으로 구성된 클래스를 특별히 인터페이스라고 합니다.

* [상속 강제](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C)를 해야 하므로 `protected` 생성자를 사용합니다.
* 일반적으로 인터페이스는 구체 구현이 없으므로 복사 생성자, 복사 대입 연산자를 `private`로 막습니다. 특히 부모 개체의 복사 대입 연산자는 꼭 막아두는게 좋습니다.([부모 개체의 복사 대입 연산자](??) 참고)
* 일반적으로 인터페이스는 기능 스펙만 제공하고, 다형 소멸을 제공하지 않습니다. 다형 소멸을 제공하는건 그냥 추상 클래스라고 보시는게 좋습니다.
* 다형 소멸을 하지 않으므로 `protected` Non-Virtual 소멸자를 사용합니다.
* 인터페이스는 모두 순가상 함수로 구성됩니다. 단, 소멸자를 순가상 함수로 만들면, 정의를 따로 해야하므로, 그냥 `protected` Non-Virtual 소멸자로 정의합니다.

다음은 `IDrawable` 인터페이스로 `Shape` 클래스를 구현한 예입니다. 

1. `IDrawable` 은 * [상속 강제](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C)를 해야 하므로 `protected` 기본 생성자를 사용합니다.
2. `IDrawable` 은 인터페이스여서 복사 생성자, 복사 대입 연산자를 외부에서 사용하지 못하도록 `private`을 사용합니다.
3. `IDrawable` 은 인터페이스 여서 `protected` Non-Virtual 소멸자를 사용합니다.
4. `IDrawable`에 `Draw()` 순가상 함수를 선언합니다.
5. `Shape`은 `IDrawable`을 상속하고, 다형 소멸을 할 것이므로 `public` Virtual 소멸자를 사용합니다. (상속한 `IDrawable`의 `Draw()`함수가 구체화 되지 않았으므로 여전히 추상 클래스입니다.)

```cpp
// 인터페이스
class IDrawable {
private:
    IDrawable(const IDrawable& other) {} // 인터페이스여서 외부에서 사용 못하게 복사 생성자 막음
    IDrawable& operator =(const IDrawable& other) {return *this;} // 인터페이스여서 외부에서 사용 못하게 복사 대입 연산자 막음    
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
        std::cout << "Rectangle::Draw()" << std::endl;
    }
};
// Shape을 구체화한 클래스 입니다. Draw()에서 타원을 그립니다.
class Ellipse : public Shape { 
public:
    virtual void Draw() const {
        std::cout << "Ellipse::Draw()" << std::endl;
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

> *C++11 부터는 `default`와 `delete`가 추가되어 암시적으로 생성되는 복사 생성자와 복사 대입 연산자의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.([default와 delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete) 참고)*

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



