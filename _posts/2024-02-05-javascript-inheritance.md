---
layout: single
title: "#5. [Javascript] 상속"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 자바스크립트에서 상속이 필요한가?

자바스크립트는 C++와 같은 상속을 지원하지 않습니다. 하지만, 프로토타입을 이용하여 상속한 것처럼 만들 수는 있는데요, 억지로 상속한 것처럼 만들다 보니 쓸데없이 부모 개체의 속성을 복제하는 등 비효율적입니다.

상속보다는 프로토타입 체인을 이용하여 실행을 위임하는 것으로 논리를 만드시는게 좋습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c222deb6-23e9-4250-bd7d-b1b1d8426a49)

C++에서는 `Base`로부터 상속한 `Derived` 클래스로 개체를 인스턴스화 하면, 속성과 메서드를 물려받게 됩니다.
따라서 각 개체는 `baseProperty, derivedProperty`를 갖게 되고, `baseMethed(), derivedMethod()`를 제공합니다. 메서드 정의는 `Base` 클래스와 `Derived` 클래스에서 제공하고요.

자바스크립트는 상속이 없으며, 재활용하고 싶은 개체가 있으면, 프로토타입 개체에 위임하여 처리할 수 있습니다.

우리가 상속을 하려는 경우는 다음과 같습니다.

|항목|자바스크립트의 대처 방안|
|--|--|
|인터페이스를 만들어 단단한 코딩 계약을 만들고 싶습니다.|자바클래스는 너무도 유연합니다. 단단한 코딩 계약을 맺을 수 없으며, 대안으로 [타입스크립트](??)를 이용할 수 있습니다.|
|추상 클래스를 만들어 일부 기능은 부모 클래스에서 제공하고, 순가상 함수를 이용하여 자식 클래스에서는 일부 기능을 강제로 구현하게 하고 싶습니다.|부모 개체를 속성으로 저장하고 기능을 위임합니다. 자식 개체의 기능을 강제하는 것은 [Strategy](https://tango1202.github.io/pattern/pattern-strategy/)로 넘겨줍니다.(*콜백 함수를 사용합니다.*) |
|[Template Method](https://tango1202.github.io/pattern/pattern-template-method/)로 부모 클래스에서 자식 클래스에게 기능을 요청하고 싶습니다.|부모 개체가 필요로 하는 기능을 [Strategy](https://tango1202.github.io/pattern/pattern-strategy/)로 넘겨줍니다.(*콜백 함수를 사용합니다.*)|
|`is-a`관계를 만들고, 부모 개체에서 호출시 자식 개체들이 다형적으로 동작하게 하고 싶습니다.|변수는 아무 타입이나 받을 수 있으므로, 이미 `is-a`관계라고 생각하셔도 됩니다. 메서드명만 동일하면 호출되므로, 동일한 메서드명이면 다형적으로 동작한다고 생각해도 됩니다.|
|기존의 코드를 재활용하고 싶습니다.|자바스크립트는 모든 것이 개체입니다. 함수까지도요. 따라서 개체를 재활용하고 위임하면 됩니다.|

즉, 상속이 필요한 항목이 아에 불필요하거나 위임으로 전환될 수 있습니다.

다음은 `IDrawable` 인터페이스를 만들고, 이를 상속한 `Shape`클래스를 만들어 기본적인 속성인 `m_Left, m_Top, m_Width, m_Height`속성을 구현하고, `Shape`을 상속한 `Rectangle`과 `Ellipse`에서 `Draw`시 다형적으로 그려주는 C++의 예입니다.

```cpp
// 인터페이스
class IDrawable {
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
    Shape(int l, int t, int w, int h) : m_Left(l), m_Top(t), m_Width(w), m_Height(h) {}
    virtual ~Shape() {} // 다형 소멸 하도록 public virtual
};

// Shape을 구체화한 클래스 입니다. Draw()에서 사각형을 그립니다.
class Rectangle : public Shape {
public:
    Rectangle(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    virtual void Draw() const {
        std::cout << "Rectangle::Draw()" << std::endl;
    }
};
// Shape을 구체화한 클래스 입니다. Draw()에서 타원을 그립니다.
class Ellipse : public Shape { 
public:
    Ellipse(int l, int t, int w, int h) : Shape(l, t, w, h) {}
    virtual void Draw() const {
        std::cout << "Ellipse::Draw()" << std::endl;
    }
};

Shape* shapes[2] = { // 도형들을 Shape* 로 관리합니다.
    new Rectangle(1, 2, 3, 4), 
    new Ellipse(10, 20, 30, 40)
};

// (O) Shape이 IDrawable을 상속했으므로 Draw() 할 수 있습니다.
for(int i = 0; i < 2; ++i) {
    shapes[i]->Draw(); // 다형적으로 그립니다.
}

for(int i = 0; i < 2; ++i) {
    delete shapes[i]; // 다형 소멸 합니다. Shape*로 Rectangle, Ellipse을 소멸합니다.
} 
```

자바스크립트에서는 다음과 같이 할 수 있습니다.

1. 배열에 아무 타입이나 들어가니 굳이 Shape으로 추상화할 필요가 없습니다.
2. 그냥 `draw()`를 호출하면 됩니다. 인터페이스로 만들 필요가 없습니다.

```javascript
const Rectangle = (function() {  
    function Rectangle(l, t, w, h) { 
        this.left = l;
        this.top = t;
        this.width = w;
        this.height = h;   
    }
    Rectangle.prototype.draw = function() { // 메서드는 프로토타입에 선언합니다.
        console.log('Rectangle을 그립니다.', this.left, this.top, this.width, this.height);
    }; 
    
    return Rectangle; 
}());
const Ellipse = (function() {  
    function Ellipse(l, t, w, h) { 
        this.left = l;
        this.top = t;
        this.width = w;
        this.height = h;   
    }
    Ellipse.prototype.draw = function() { // 메서드는 프로토타입에 선언합니다.
        console.log('Ellipse을 그립니다.', this.left, this.top, this.width, this.height);
    }; 
    
    return Ellipse; 
}());

// #1. 배열에 아무 타입이나 들어가니 굳이 Shape으로 추상화할 필요가 없습니다.
let shapes = [new Rectangle(1, 2, 3, 4), new Ellipse(10, 20, 30, 40)];
shapes.forEach(
    // #2. 그냥 draw를 호출하면 됩니다. 인터페이스로 만들 필요가 없습니다.
    (shape) => shape.draw()
);
```

# 프로토타입을 이용한 상속

그럼에도 불구하고, 굳이 상속이 필요하다면, 다음과 같이 기반이 되는 `Base`의 프로토타입 개체의 메서드들과 `baseObj`의 속성들을 복제해서 사용할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/bac7d6cb-8d38-4c1f-af60-318e6cd00c12)

1. `Derived` 생성자 함수에서 `Base.call(this, baseProperty);`를 실행합니다. 

    이 코드는 생성자 함수 `Base`를 `Base(baseProperty);`와 같이 일반 함수처럼 호출(*[생성자 함수] 참고*)합니다. 또한 `Base`함수의 `this`를 `Derived` 생성자 함수의 `this`(*리턴하는 개체*)로 바인딩 합니다. 즉, `Derived`가 리턴하는 개체에 `Base`의 속성을 추가하는 효과가 있습니다.

2. `Object.setPrototypeOf`는 `Base.prototype`을 `Derived.prototype`에 복제합니다. 다만 `constructor`는 변경하지 않습니다.

실행 결과를 보면, `derived` 객체는 `baseProperty`, 복제하여 사용하고 있고, `Derived.prototype`은 `baseMethod()`를 복제하여 사용하고 있습니다.

```javascript
const Base = (function() {  
    function Base(baseProperty) { // #1
        this.baseProperty = baseProperty;   
    }
    Base.prototype.baseMethod = function() { 
        console.log('baseMethod 입니다', this.baseProperty);
    }; 
    
    return Base; 
}());

const Derived = (function() {  
    function Derived(baseProperty, derivedProperty) {
        Base.call(this, baseProperty); // #1. Base 생성자 함수를 호출합니다. 리턴하려는 this개체를 Base()함수 내의 this로 바인딩합니다.(Base 속성을 this에 추가합니다.)
        this.derivedProperty = derivedProperty;   
    }

    Object.setPrototypeOf(Derived.prototype, Base.prototype); // #2. Base.prototype의 속성들을 복제합니다.

    Derived.prototype.derivedMethod = function() { 
        console.log('derivedMethod 입니다', this.derivedProperty);
    }; 
    return Derived;
}());

const base = new Base('base');
base.baseMethod(); // baseMethod 입니다

const derived = new Derived('base from derived', 'derived'); 

console.log('Base.prototype을 복제했지만, constructor는 Derived입니다', derived.__proto__.constructor === Derived);
console.log('derived는 Derived로부터 생성되었습니다', derived instanceof Derived);
console.log('derived는 Base로부터 생성되었습니다', derived instanceof Base);

derived.baseMethod(); // baseMethod 입니다 base from derived
derived.derivedMethod(); // derivedMethod 입니다 derived
```

`Derived`에서 `baseMethod`를 재정의하면 오버라이딩 됩니다.

```javascript
const OverridingDerived = (function() {  
    function OverridingDerived(baseProperty, derivedProperty) {
        Base.call(this, baseProperty); 
        this.derivedProperty = derivedProperty;   
    }
    Object.setPrototypeOf(Derived.prototype, Base.prototype);
    OverridingDerived.prototype.baseMethod = function() { // baseMethod를 오버라이딩 합니다.
        console.log('Overriding 입니다', this.baseProperty);
    }; 
    return OverridingDerived;
}());
const overridingDerived = new OverridingDerived('base from derived', 'derived'); 
overridingDerived.baseMethod(); // Overriding 입니다 base from derived
```

# 클래스를 이용한 상속

ECMAScript6 부터는 `class`문법이 도입되어 [프로토타입을 이용한 상속](??)보다 간결하게 코드를 작성할 수 있습니다. 실제로 C++같은 클래스를 제공하는 것은 아니며, [프로토타입을 이용한 상속](??)의 좀더 쉬운 코딩법을 제공할 뿐입니다. 

1. 생성자 함수의 역할을 합니다.
2. 메서드는 알아서 프로토타입에 선언됩니다.
3. `extends`는 상속을 표현합니다. 아마도 내부적으로 `setPrototypeOf()`를 호출할 겁니다.
4. `super()`는 상위 생성자 함수를 호출합니다. 아마도, `Base.call()`을 호출할 겁니다.

```javascript
class Base {
    constructor(baseProperty) { // #1. 생성자 함수
        this.baseProperty = baseProperty;
    } 
    baseMethod() { // #2. 메서드는 알아서 프로토타입에 선언됩니다.
        console.log('baseMethod 입니다', this.baseProperty);
    } 
};
class Derived extends Base { // #3. 상속입니다. 아마도 내부적으로 setPrototypeOf()를 하겠죠.
    constructor(baseProperty, derivedProperty) { 
        super(baseProperty); // #4. 상위 생성자 함수를 호출합니다. 아마도, Base.call()을 호출하겠죠.
        this.derivedProperty = derivedProperty;
    }
    derivedMethod() {
        console.log('derivedMethod 입니다', this.derivedProperty);   
    }
};

const base = new Base('base');
base.baseMethod(); // baseMethod 입니다

const derived = new Derived('base from derived', 'derived'); 

console.log('Base.prototype을 복제했지만, constructor는 Derived입니다', derived.__proto__.constructor === Derived);
console.log('derived는 Derived로부터 생성되었습니다', derived instanceof Derived);
console.log('derived는 Base로부터 생성되었습니다', derived instanceof Base);

derived.baseMethod(); // baseMethod 입니다 base from derived
derived.derivedMethod(); // derivedMethod 입니다 derived
```

