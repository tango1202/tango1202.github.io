---
layout: single
title: "#10. [Javascript] 상속, 클래스"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 자바스크립트에서 상속이 필요한가?

자바스크립트는 C++와 같은 상속을 지원하지 않습니다. 하지만, [프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)을 이용하여 상속한 것처럼 만들 수는 있는데요, 억지로 상속한 것처럼 만들다 보니 쓸데없이 부모 개체의 속성을 복제하기 때문에 좀 비효율적입니다.

공통된 기능은 상속보다는 [즉시 실행 함수를 이용한 모듈 개체](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%AA%A8%EB%93%88%ED%99%94)를 통해 위임을 하도록 논리를 만드는게 좋습니다. [Chain of Responsibility](https://tango1202.github.io/pattern/pattern-chain-of-responsibility/)처럼 [프로토타입 체인](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%86%8D%EC%84%B1-%EC%B0%B8%EC%A1%B0)을 이용하는 거죠.

C++에서는 `Base`로부터 상속한 `Derived` 클래스로 개체를 인스턴스화 하면, 속성과 메서드를 물려받게 됩니다.
따라서 각 개체는 각각 `baseProperty, derivedProperty`를 갖게 되고, `baseMethed(), derivedMethod()`를 제공합니다. 메서드 정의는 `Base` 클래스와 `Derived` 클래스에서 제공하고요.

다음은 C++의 상속과 자바스크립트의 [프로토타입 체인](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%86%8D%EC%84%B1-%EC%B0%B8%EC%A1%B0)을 비교한 그림입니다. C++은 `baseProperty`가 각각의 개체에 정의되는데, 자바스크립트는 `Obj1`과 `Obj2`가 동일한 `baseProperty`와 `baseMethod()`를 참조합니다. 그림을 보면 근본적인 구조부터 다른걸 알 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c222deb6-23e9-4250-bd7d-b1b1d8426a49)

우리가 상속을 하려는 경우는 다음과 같습니다.

|항목|자바스크립트의 대처 방안|
|--|--|
|인터페이스를 만들어 단단한 코딩 계약을 만들고 싶습니다.|자바스크립트의 클래스는 너무도 유연합니다. 단단한 코딩 계약을 맺을 수 없으며, 대안으로 [타입스크립트](https://tango1202.github.io/categories/typescript/)를 이용할 수 있습니다.|
|추상 클래스를 만들어 일부 기능은 부모 클래스에서 제공하고, 순가상 함수를 이용하여 자식 클래스에서는 일부 기능을 강제로 구현하게 하고 싶습니다.|부모 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 속성으로 저장하고 기능을 위임합니다. 자식 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 기능을 강제하는 것은 [Strategy](https://tango1202.github.io/pattern/pattern-strategy/)로 넘겨줍니다.(*콜백 함수를 사용합니다.*) |
|[Template Method](https://tango1202.github.io/pattern/pattern-template-method/)로 부모 클래스에서 자식 클래스에게 기능을 요청하고 싶습니다.|부모 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 필요로 하는 기능을 [Strategy](https://tango1202.github.io/pattern/pattern-strategy/)로 넘겨줍니다.(*콜백 함수를 사용합니다.*)|
|`is-a`관계를 만들고, 부모 개체에서 호출시 자식 개체들이 다형적으로 동작하게 하고 싶습니다.|변수는 아무 타입이나 받을 수 있으므로, 이미 `is-a`관계라고 생각하셔도 됩니다. 메서드명만 동일하면 호출되므로, 동일한 메서드명이면 다형적으로 동작한다고 생각해도 됩니다.|
|기존의 코드를 재활용하고 싶습니다.|자바스크립트는 모든 것이 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다. [함수](https://tango1202.github.io/javascript/javascript-function/)까지도요. 따라서 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 재활용하고 위임하면 됩니다.|

즉, 상속이 필요한 항목들이 [Strategy](https://tango1202.github.io/pattern/pattern-strategy/)를 이용한 의존성 주입(*[의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고*)이나 위임으로 전환될 수 있습니다.

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

1. [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)에 아무 타입이나 들어가니 굳이 Shape으로 추상화할 필요가 없습니다.
2. 그냥 `draw()`를 호출하면 됩니다. 인터페이스로 만들 필요가 없습니다.

코딩 계약은 좀 느슨합니다만, 잘 동작합니다.

```javascript
const Rectangle = (() => {  
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
})();
const Ellipse = (() => {  
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
})();

// #1. 배열에 아무 타입이나 들어가니 굳이 Shape으로 추상화할 필요가 없습니다.
let shapes = [new Rectangle(1, 2, 3, 4), new Ellipse(10, 20, 30, 40)];
shapes.forEach(
    // #2. 그냥 draw를 호출하면 됩니다. 인터페이스로 만들 필요가 없습니다.
    (shape) => shape.draw()
);
```

# 프로토타입을 이용한 상속

그럼에도 불구하고, 상속이 필요하다면, 다음 그림처럼 기반이 되는 `Base`의 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)의 메서드들과 `baseObj`의 속성들을 복제해서 사용할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6866c646-778a-40f0-b4ac-76da85214fc2)

다음은 상기 그림을 코드로 구현한 예입니다.

1. #1: `Derived` [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)에서 `Base.call(this, baseProperty);`를 실행합니다. 

    이 코드는 내부적으로 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98) `Base`를 `Base(baseProperty);`와 같이 일반 [함수](https://tango1202.github.io/javascript/javascript-function/)처럼 호출(*[생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98) 참고*)해 줍니다. 이렇게 되면, [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 생성하는 것이 아니라 `this`에 속성을 추가하는 [함수](https://tango1202.github.io/javascript/javascript-function/)가 되죠.
    
    또한 `Base`함수의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 `Derived` [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)(*리턴하는 개체*)로 바인딩 합니다. 
    
    즉, `Derived`가 리턴하는 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에 `Base`의 속성을 추가하는 효과가 있습니다.

2. #2 : `Object.setPrototypeOf()`는 `Base.prototype`을 `Derived.prototype__proto__`에 복제하여, `Derived.prototype`에 속성/메서드가 없다면 `Base.prototype`에서 찾게 합니다.

실행 결과를 보면, `derived` 객체는 `baseProperty`를 복제하여 사용하고 있고, `Derived.prototype`은 `baseMethod()`를 복제하여 사용하고 있습니다.

```javascript
const Base = (() => {  
    function Base(baseProperty) { // #1
        this.baseProperty = baseProperty;   
    }
    Base.prototype.baseMethod = function() { 
        console.log('baseMethod 입니다', this.baseProperty);
    }; 
    
    return Base; 
})();

const Derived = (() => {  
    function Derived(baseProperty, derivedProperty)  {
        Base.call(this, baseProperty); // #1. Base 생성자 함수를 호출합니다. 리턴하려는 this개체를 Base()함수 내의 this로 바인딩합니다.(Base 속성을 this에 추가합니다.)
        this.derivedProperty = derivedProperty;   
    }

    Object.setPrototypeOf(Derived.prototype, Base.prototype); // #2. Base.prototype의 속성들을 복제합니다.

    Derived.prototype.derivedMethod = function() { 
        console.log('derivedMethod 입니다', this.derivedProperty);
    }; 
    return Derived;
})();

const base = new Base('base');
base.baseMethod(); // baseMethod 입니다 base

const derived = new Derived('base from derived', 'derived'); 

console.log('Base.prototype을 복제했지만, constructor는 Derived입니다', derived.__proto__.constructor === Derived);
console.log('Derived.prototype.__proto__ === Base.prototype입니다', Derived.prototype.__proto__ === Base.prototype);
console.log('derived는 Derived로부터 생성되었습니다', derived instanceof Derived);
console.log('derived는 Base로부터 생성되었습니다', derived instanceof Base);

derived.baseMethod(); // baseMethod 입니다 base from derived
derived.derivedMethod(); // derivedMethod 입니다 derived
```

`Derived`에서 `baseMethod`를 재정의하면 오버라이딩 됩니다.

```javascript
const OverridingDerived = (() => {  
    function OverridingDerived(baseProperty, derivedProperty) {
        Base.call(this, baseProperty); 
        this.derivedProperty = derivedProperty;   
    }
    Object.setPrototypeOf(Derived.prototype, Base.prototype);
    OverridingDerived.prototype.baseMethod = function() { // baseMethod를 오버라이딩 합니다.
        console.log('Overriding 입니다', this.baseProperty);
    }; 
    return OverridingDerived;
})();
const overridingDerived = new OverridingDerived('base from derived', 'derived'); 
overridingDerived.baseMethod(); // Overriding 입니다 base from derived
```

# 클래스를 이용한 상속(ECMAScript6)

ECMAScript6 부터는 `class`문법이 도입되어 [프로토타입을 이용한 상속](https://tango1202.github.io/javascript/javascript-inheritance-class/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%83%81%EC%86%8D)보다 간결하게 상속을 구현할 수 있습니다. 실제로 C++같은 클래스를 제공하는 것은 아니며, [프로토타입을 이용한 상속](https://tango1202.github.io/javascript/javascript-inheritance-class/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%83%81%EC%86%8D)의 좀더 쉬운 코딩법을 제공할 뿐입니다. 

1. `constructor`는 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)의 역할을 합니다. [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)처럼 속성들을 초기화합니다.
2. 메서드는 알아서 [프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)에 선언됩니다.
3. `extends`는 상속을 표현합니다. 아마도 내부적으로 `setPrototypeOf()`를 호출할 겁니다.
4. `super()`는 상위 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 호출합니다. 아마도, `Base.call()`을 호출할 겁니다.
5. `Derived`용 메서드를 추가할 수 있습니다. 또한 `Base`의 메서드와 동일한 이름으로 재정의하면 오버라이딩 됩니다.
6. [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)처럼 `new`로 생성합니다.

```javascript
class Base { // 클래스는 관습적으로 Pascal 표기를 사용합니다.
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
    derivedMethod() { // #5. Derived에 메서드를 추가합니다.
        console.log('derivedMethod 입니다', this.derivedProperty);   
    }
};

const base = new Base('base'); // #6. 생성자 함수처럼 new 로 생성합니다.
base.baseMethod(); // baseMethod 입니다 base

const derived = new Derived('base from derived', 'derived'); 

console.log('Base.prototype을 복제했지만, constructor는 Derived입니다', derived.__proto__.constructor === Derived);
console.log('derived는 Derived로부터 생성되었습니다', derived instanceof Derived);
console.log('derived는 Base로부터 생성되었습니다', derived instanceof Base);

derived.baseMethod(); // baseMethod 입니다 base from derived
derived.derivedMethod(); // derivedMethod 입니다 derived
```

# 클래스 getter/setter/static(ECMAScript6)

`get`과 `set`을 이용하여 속성처럼 사용할 수 있는 [getter](https://tango1202.github.io/javascript/javascript-inheritance-class/#%ED%81%B4%EB%9E%98%EC%8A%A4-gettersetterstaticecmascript6)와 [setter](https://tango1202.github.io/javascript/javascript-inheritance-class/#%ED%81%B4%EB%9E%98%EC%8A%A4-gettersetterstaticecmascript6)를 만들 수 있습니다.

또한 [static](https://tango1202.github.io/javascript/javascript-inheritance-class/#%ED%81%B4%EB%9E%98%EC%8A%A4-gettersetterstaticecmascript6)을 사용하면 [정적 함수](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A0%95%EC%A0%81-%ED%95%A8%EC%88%98)를 만들 수 있습니다. [정적 함수](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A0%95%EC%A0%81-%ED%95%A8%EC%88%98)는 클래스 명으로 호출합니다.

```javascript
class MyClass { 
    constructor(data) { 
        this.data = data;
    } 
    get xVal() { // getter
        return this.data.x;
    }
    set xVal(x) { // setter
        this.data.x = x;
    }

    get yVal() {
        return this.data.y;
    }
    set yVal(y) {
        this.data.y = y;
    }

    static staticMethod() {
        return '정적 함수 입니다';
    }
};
const data = { x: 1, y: 2 };
const myClass = new MyClass(data);
console.log('getter를 이용하여 속성처럼 데이터를 참조할 수 있습니다', myClass.xVal === 1 && myClass.yVal === 2);

myClass.xVal = 10;
myClass.yVal = 20;
console.log('setter를 이용하여 속성처럼 데이터를 수정할 수 있습니다', myClass.xVal === 10 && myClass.yVal === 20);

console.log(MyClass.staticMethod()); // 정적 함수 입니다
```

# protected, private(ECMAScript10)

자바스크립트에서는 [protected](https://tango1202.github.io/javascript/javascript-inheritance-class/#protected-privateecmascript10)를 지원하지 않습니다. 외부에 노출하지 않는 것은 관습적으로 속성앞에 `_`를 붙여 사용할 뿐입니다.

```javascript
class Base {
    _protectedVal = 0; // _는 관습적일 뿐, 실제 접근 통제를 하지 못합니다.
    get val() {
        return this._protectedVal;
    } 
    set val(val) {
        this._protectedVal = val;
    }
};
class Derived extends Base {
    inc() {
        this._protectedVal += 1;
    }
};

const data = new Derived();
data._protectedVal = 10; // 접근이 가능합니다.
data.inc();
console.log('protected는 지원하지 않습니다.', data.val === 11);
```

하지만 [private](https://tango1202.github.io/javascript/javascript-inheritance-class/#protected-privateecmascript10)는 지원합니다. 속성명 앞에 `#`을 붙이면 접근이 통제되어 자기 자신에서만 사용할 수 있습니다.

```javascript
class Base {
    #privateVal = 0; // #은 자기 자신 외에는 접근할 수 없습니다.
    get val() {
        return this.#privateVal;
    } 
    set val(val) {
        this.#privateVal = val;
    }
};
class Derived extends Base {
    inc() {
        // this.#privateVal += 1; // (X) 자식 클래스에서도 접근할 수 없습니다.
        this.val += 1;
    }
};

const data = new Derived();
// data.#privateVal = 10; // (X) 외부에서 접근할 수 없습니다.
data.inc();
console.log('getter, setter로만 접근할 수 있습니다.', data.val === 1);
```

# 클래스 MixIn

[코딩 패턴 - MixIn을 이용한 메서드 동적 추가](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---mixin%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%A9%94%EC%84%9C%EB%93%9C-%EB%8F%99%EC%A0%81-%EC%B6%94%EA%B0%80)에서 기존 개체에 다른 메서드를 추가하여 기능을 확장하는 패턴을 보여드렸는데요, 클래스에서도 이 기법을 활용하여 메서드를 추가할 수 있습니다. 

다음 예는 `Data`에 `BasicOperationMixIn` 개체의 메서드들을 추가하는 예입니다. `new Data()`로 생성된 `data1`에 MixIn 하면 `data1`개체에만 반영되지만, `Data.prototype`에 반영하면 모든 개체에 반영되는 걸 알 수 있습니다.

```javascript
class Data {
    constructor(a, b) { 
        this.a = a;
        this.b = b;
    } 
};

const BasicOperationMixIn = {
    plus: function() { // this를 사용하므로 화살표 함수를 사용하지 않습니다.
        return this.a + this.b;
    },
    minus: function() {
        return this.a - this.b;
    },
    multiply: function() {
        return this.a * this.b;
    },
    divide: function() {
        return this.a / this.b;
    } 
};

const data1 = new Data(1, 2);
// data1.plus(); // (X) plus 메서드가 없으므로 오류

Object.assign(data1, BasicOperationMixIn); // data1 개체에 사칙 연산 추가
console.log('개체에 MixIn', data1.plus() === 1 + 2);

const data2 = new Data(3, 4);
// data2.plus(); // (X) data1에만 사칙연산을 추가 했으므로 data2는 plus 메서드가 없으므로 오류
Object.assign(Data.prototype, BasicOperationMixIn); // 프로토타입 개체에 사칙 연산 추가
console.log('프로타입에 MixIn', data2.plus() === 3 + 4);

console.log('프로타입에 MixIn했으므로, 이후로 생성되는 모든 개체에 적용됨', (new Data(5, 6)).plus() === 5 + 6);  
```

