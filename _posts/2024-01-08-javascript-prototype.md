---
layout: single
title: "#8. [Javascript] prototype, this"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 프로토타입

자바스크립트는 [프로토타입 개체와 체인으로 연결](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%86%8D%EC%84%B1-%EC%B0%B8%EC%A1%B0)되어 있으며, 자신에게 해당 속성/메서드가 없는 경우 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)의 속성/메서드를 이용합니다. 마치 디자인 패턴중 [Chain of Responsibility](https://tango1202.github.io/pattern/pattern-chain-of-responsibility/) 처럼요.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0a87cb27-0f62-41ec-8c49-49b795853e17)

다음과 같이 `console.dir()`을 사용하면 브라우저 개발자 도구에서 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)(*[[[Prototype]]](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)*)를 확인할 수 있습니다.

```javascript
const user = {
    name: 'Lee'
};
console.dir(user);
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4a9a87fd-05ba-48e6-b17b-93f9454cb3b1)

[[[Prototype]]](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)은 [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor) 속성으로 접근할 수 있으며, 초기에는 `Object.prototype`을 가리킵니다. 

```javascript
const user = {
    name: 'Lee'
};

console.log('프로토타입 user.__proto__ === Object.prototype', user.__proto__ === Object.prototype); // true
```

# [[Prototype]]과 __proto__와 prototype과 constructor

[생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)로 생성한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)와 [프로토타입 개체를 체인으로 연결](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%86%8D%EC%84%B1-%EC%B0%B8%EC%A1%B0)하기 위해 각 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)는 다음과 같이 구성됩니다.

* `prototype` : [함수 개체](https://tango1202.github.io/javascript/javascript-function/)에만 있습니다. [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)인 경우 자신이 생성한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)를 가리킵니다.
* [[[Prototype]]](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor) : 자신에게 해당 속성/메서드가 없는 경우 호출되는 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)입니다. [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)로 접근합니다.
* `constructor` : [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)에만 있습니다. 자신을 생성한 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 가리킵니다.

```javascript
function User(name) { // 생성자 함수
    this.name = name;
}

const user = new User('Lee'); // 생성자 함수로 생성한 개체

console.log('User.prototype과 user.__proto__ 는 동일한 프로토타입 개체를 가리킵니다.', User.prototype === user.__proto__); 
console.log('프로토타입 개체의 constructor는 생성자 함수입니다.', user.__proto__.constructor === User); 
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6800bf95-cb1d-4d3a-b980-8a278b3d4caf)

# 프로토타입 체인을 이용한 속성 참조

다음 예는 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 속성/메서드에 접근할때 해당 속성/메서드가 없으면 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)의 속성/메서드에 접근하는 것을 보여줍니다. 

`User`에 `addr` 속성이 없고 `User`의 `prototype`에 `addr`속성이 추가되었으므로, `user1.addr`이나 `user2.addr`시 `User.prototype.addr`에 접근하여 `'Seoul'`이 사용됩니다.

```javascript
function User(name) {
    this.name = name;
}
User.prototype.addr = 'Seoul'; // 프로토타입에 addr 속성을 추가합니다.

const user1 = new User('Kim');
const user2 = new User('Lee');

console.log('user1과 user2는 프로토타입이 같습니다.', user1.__proto__ === user2.__proto__); 

// user1.name, user2.name은 addr 속성이 없으므로 User.prorotype.addr을 사용합니다.
console.log('addr은 프로토타입의 속성입니다', user1.name === 'Kim' && user1.addr === 'Seoul'); 
console.log('addr은 프로토타입의 속성입니다', user2.name === 'Lee' && user2.addr === 'Seoul'); 
```

상기를 그림으로 보면 다음과 같습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/43c881e7-edca-4b10-9e0d-f358b2294f4c)


만약 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)의 속성값을 수정하면, 이를 참조하는 모든 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에서 수정된 값을 사용하게 됩니다.

다음 예에서 `user1.__proto__.addr`를 수정했지만, `user1`과 `user2`는 같은 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)를 공유하므로 함께 수정되어 `Pusan`이 출력됩니다.

```javascript
// __proto__의 addr을 수정합니다. 
// User.prototype.addr = 'Pusan'; 과 동일합니다.
user1.__proto__.addr = 'Pusan'; 

// user1, user2는 같은 __proto__를 공유하므로 Pusan 입니다.
console.log('user1.addr은 Pusan 입니다', user1.addr === 'Pusan'); 
console.log('user2.addr은 Pusan 입니다', user2.addr === 'Pusan'); 
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4cb08418-b216-4b78-9417-85e3aa56af3f)

만약 `user2.addr = 'Seoul'`와 같이 `user2` [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에 값을 설정한다면, `user2.__proto__.addr`을 수정하는게 아니라 `user2` [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에 `addr`속성을 추가하고 수정합니다.(*[개체 속성 추가/삭제](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4-%EC%86%8D%EC%84%B1-%EC%B6%94%EA%B0%80%EC%82%AD%EC%A0%9C) 참고*) 

따라서, 이후로 `user2.addr`은 자신의 속성값을 리턴하고, `user1.addr`은 [프로토타입 체인](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%86%8D%EC%84%B1-%EC%B0%B8%EC%A1%B0)을 통해 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)의 값을 리턴하기 때문에, 서로 다른 값을 출력하게 됩니다.

```javascript
user2.addr = 'Seoul'; // user2에 addr 속성을 추가합니다.
console.log('user1.addr은 Pusan 입니다', user1.addr === 'Pusan');
console.log('user2.addr은 Seoul 입니다', user2.addr === 'Seoul'); 
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ed1894bf-c9da-46f0-b655-cc2d4fa256b6)

사용자가 만든 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 뿐만 아니라 자바스크립트에서 기본으로 제공하는 [기본 타입](https://tango1202.github.io/javascript/javascript-basic/#%ED%83%80%EC%9E%85%EA%B3%BC-%EB%A6%AC%ED%84%B0%EB%9F%B4)의 [프로토타입 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)도 수정할 수 있습니다. 다음은 [기본 타입](https://tango1202.github.io/javascript/javascript-basic/#%ED%83%80%EC%9E%85%EA%B3%BC-%EB%A6%AC%ED%84%B0%EB%9F%B4)인 `String`의 `prototype`에 `add` 메서드를 추가한 예입니다.

```javascript
String.prototype.add = (a, b) => {
    return a + b;
};
console.log('기본타입인 String에 add 함수를 추가했습니다. String.add(1, 2)', 'test'.add(1, 2)); // 3
```

[프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)은 여러 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 공통된 메서드를 사용할때 메서드의 중복 선언을 최소화 할 수 있어 좋긴 합니다만(*[생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98) 참고*), 메서드 호출 과정에 있어 해당 메서드가 있는지 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에서 싹 뒤진뒤 없다면 호출하는 것이기 때문에, 메서드 호출 부하가 있을 수 있습니다. 

따라서 어지간하면(*개체 지향의 [다형성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-polymorphism/)을 배제한다면*), `obj.func()`형태의 메서드 호출보다는 `func(obj)`형태의 [함수](https://tango1202.github.io/javascript/javascript-function/) 호출 구문을 사용하시는게 좋습니다.

# 프로토타입 변경

[프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)을 다른 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)로 변경할 수도 있습니다. 이때 `constructor`가 덮어써지므로, 다음 예의 #2와 같이 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)로 설정해 줘야 합니다.

```javascript
function User(name) { // #1. 생성자 함수입니다.
    this.name = name;
}
User.prototype = { // 프로토타입을 다른 개체로 변경할 수도 있습니다.
    constructor: User, // #2. #1의 생성자 함수로 설정합니다.
    addr: 'Seoul'
};

const user1 = new User('Kim');
const user2 = new User('Lee');

// user1.name, user2.name은 addr 속성이 없으므로 User.prototype.addr을 사용합니다.
console.log('addr은 프로토타입의 속성입니다', user1.name === 'Kim' && user1.addr === 'Seoul'); 
console.log('addr은 프로토타입의 속성입니다', user2.name === 'Lee' && user2.addr === 'Seoul'); 

// prototype 값을 수정하면, 생성한 모든 개체에 반영됩니다.
User.prototype.addr = 'Busan'; 
console.log(user1.name === 'Kim' && user1.addr === 'Busan');
console.log(user2.name === 'Lee' && user2.addr === 'Busan'); 
```

# 프로토타입을 이용한 메서드 구현

[생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 이용하면 메서드가 중복 생성된다고 언급했었는데요(*[개체의 생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98) 참고*),

```javascript
fuction User(name) {
    this.name = name; 
    this.getName = function() { // 생성하는 개체마다 메서드 선언이 중복됩니다.
        return this.name; 
    };
}

const user1 = new User('Kim'); 
const user2 = new User('Lee');
```

[프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)을 이용하면, 메서드가 한개만 선언됩니다. 다만, 호출 부하가 생길 수 있죠.(*[프로토타입 체인을 이용한 속성 참조](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%86%8D%EC%84%B1-%EC%B0%B8%EC%A1%B0) 참고*)

메서드 중복 생성을 할 것이냐, 호출 부하가 큰 것을 사용할 것이냐 고민되실텐데, 그냥 가독성 좋은 것 쓰시고, 메모리가 부족해 지거나 속도가 느려진다면 개선을 고민하시기 바랍니다.

```javascript
function User(name) {
    this.name = name;
}
User.prototype.getName = function() { // 프로토타입 개체에 메서드를 한번만 선언합니다.
    return this.name;
};

const user1 = new User('Kim');
const user2 = new User('Lee');

console.log('프로토타입 메서드 호출', user1.getName()); // Kim
console.log('프로토타입 메서드 호출', user2.getName()); // Lee
```

# 함수 호출 방식에 따른 this 변경

[this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 사용하여 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 자신을 나타낼 수 있습니다만, [함수](https://tango1202.github.io/javascript/javascript-function/) 호출 방식에 따라 달라집니다. [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)가 달라지니 참 어처구니 없는 일(*설계상의 오류라고 하네요*)인데요, 주의해서 잘 사용해야 합니다.

1. 일반 [함수](https://tango1202.github.io/javascript/javascript-function/) : [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 전역 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다.
2. `call(), apply(), bind()` : [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 지정한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다.
3. [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 메서드 : [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 호출한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다.
4. [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98) : [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 리턴하는 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다.
5. [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98) : [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 전역 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다. 바깥 함수의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 사용하려면, #5-2와 같이 바깥 함수의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 저장하고 사용해야 합니다.
6. [화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6) : [화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)는 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)가 없습니다. 상위 환경의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)가 있다면 이를 따릅니다. 

    * 메서드의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 호출한 개체이므로 #3-1에서 `user1`이었지만, #6-1에서는 메서드를 [화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)로 선언했으므로 상위 환경의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 따릅니다. 즉 전역 개체 입니다.

    * [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 전역 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)이므로, #5-1에서 전역 개체 였지만, #6-2에서는 [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98) `arrow()`를 [화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)로 선언했습니다. `arrow()`의 상위 환경이 `function`으로 선언된 `getArrowNestName()` 메서드이고, #3에서처럼 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 메서드의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 호출한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)이므로 `getArrowNestName()`의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 `user4`입니다. 따라서, #6-2 [화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)인 `arrow()`의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 상위 환경  `getArrowNestName()`의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)인 `user4`입니다.
    
    ***function으로 선언했는지, 화살표 함수로 선언했는지에 따라 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)가 다릅니다. 혼란스럽죠. 따라서, [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 사용한다면, 메서드와 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)는 아예 [화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)로 선언하지 마세요.***

7. `prototype` : [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 호출한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다. [프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor) 개체가 아니고요.

```javascript
var name = 'Global'; // 전역 변수

// #1. 일반 함수에서의 this
function getName() {
    return this.name; // this는 전역 개체입니다. 
}
console.log('일반 함수에서 this는 전역 개체입니다', getName() === 'Global');  

// #2. this를 다른 개체에 연결
const obj = {
    name: 'Kim'
};
console.log('this를 다른 개체에 바인딩하여 사용할 수 있습니다', getName.call(obj) === 'Kim'); // getName 함수의 this를 obj에 바인딩합니다. 
console.log('this를 다른 개체에 바인딩하여 사용할 수 있습니다', getName.apply(obj) === 'Kim'); // call()과 유사하며, 추가 인수로 배열을 사용합니다.
const bindFunc = getName.bind(obj);
console.log('this를 다른 개체에 바인딩하여 사용할 수 있습니다', bindFunc() === 'Kim');

// #3. 개체 메서드에서의 this
const user1 = {
    name: 'Lee',
    getName: function() {
        return this.name; // 3-1. this는 user1입니다.
    }
};
console.log('개체 메서드에서 this는 user1입니다', user1.getName() === 'Lee'); 

// #4. 생성자 함수의 this
function User(name) {
    this.name = name;
    this.getName = function() {
        return this.name; // this는 생성자 함수가 리턴하는 개체입니다.
    };
}
const user2 = new User('Kim');
console.log('생성자 함수의 this는 리턴하는 개체입니다', user2.getName() === 'Kim'); 

// #5. 중첩 함수에서의 this
const user3 = {
    name: 'Park',
    getNestName: function() {
        function f() {
            return this.name; // #5-1. this는 전역 개체입니다. 
        }
        return f();
    },
    getName: function() {
        var that = this; // #5-2. 클로저를 활용하여 that으로 저장해 둡니다.
        function f() {
            return that.name; // that은 바깥 함수의 this입니다.
        }
        return f();
    }
};

console.log('중첩 함수에서 this는 전역 개체입니다', user3.getNestName() === 'Global'); 
console.log('that을 사용할 수 있습니다', user3.getName() === 'Park'); 

// #6 화살표 함수의 this
const user4 = {
    name: 'Park',
    getArrowName: () => { // 메서드를 화살표 함수로 선언했습니다.
        return this.name; // #6-1. this는 전역 개체입니다. 
    },        
    getNestName: function() {
        function f() {
            return this.name; // #5-1. this는 전역 개체입니다. 
        }
        return f();
    },
    getArrowNestName: function() {
        const arrow = () => {
            // #6-2. 화살표 함수에서는 this가 없어 상위 환경에서 찾습니다.
            // 상위 환경인 getArrowNestName()은 function으로 선언되었기 때문에 #3에서 처럼 this가 있고,
            // user4입니다.
            return this.name; 
        }
        return arrow();
    },
}
console.log('메서드를 화살표 함수로 선언했습니다. this는 상위 환경인 전역 개체입니다', user4.getArrowName() === 'Global');
console.log('중첩 함수에서의 this는 전역 개체입니다', user4.getNestName() === 'Global'); 
console.log('화살표 함수에서의 this는 상위 환경에서 찾습니다', user4.getArrowNestName() === 'Park');

// #7. prototype에서의 this
function PrototypeUser(name) {
    this.name = name;
}
PrototypeUser.prototype.getName = function() {
    return this.name; // 프로토타입 개체가 아닙니다. 해당 메서드를 호출한 개체입니다.
};
var user5 = new PrototypeUser('Kim');
var user6 = new PrototypeUser('Lee');

console.log('프로토타입 메서드에서 this는 user5입니다', user5.getName() === 'Kim'); // this는 user5입니다.
console.log('프로토타입 메서드에서 this는 user6입니다', user6.getName() === 'Lee'); // this는 user6입니다.
```

