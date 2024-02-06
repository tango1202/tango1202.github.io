---
layout: single
title: "#3. [Javascript] prototype, this, 개체 지향"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 프로토타입

자바스크립트는 자신의 부모 역할을 하는 객체와 체인으로 연결되어 있으며, 부모로부터 속성과 메소드를 상속받아 사용할 수 있습니다. 이렇게 부모 역할을 하는 개체를 프로토타입이라고 합니다.

다음과 같이 `console.dir()`을 사용하면 브라우저 개발자 도구에서 개체의 프로토타입(*`[[Prototype]]`*)을 확인할 수 있습니다.

```javascript
var user = {
    name: 'Lee'
};
console.dir(user);
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4a9a87fd-05ba-48e6-b17b-93f9454cb3b1)

`[[Prototype]]`은 `__proto__` 속성으로 접근할 수 있으며, 초기에는 `Object.prototype`을 가리킵니다. 

```javascript
var user = {
    name: 'Lee'
};

console.log('프로토타입', user.__proto__ === Object.prototype); // true
```

# `[[Prototype]]`과 prototype과 constructor

* `[[Prototype]]` : 부모 역할을 하는 개체입니다. 
* `prototype` : 함수 개체에만 있으며, 생성자 함수가 생성한 개체의 `__proto__`를 가리킵니다.

```javascript
function User(name) {
    this.name = name;
}

var user = new User('Lee'); // 생성자 함수로 생성한 개체
console.log('user.__proto__', user.__proto__ === User.prototype); // 자신을 생성한 생성자 함수의 prototype과 같습니다.
console.log('User.[[Prototype]]', User.__proto__ === Function.prototype); // 일반 개체는 Object.prototype과 일치하지만, 함수는 Function.prototype과 동일합니다.
console.log('User.prototype', User.prototype === user.__proto__); // User.prototype은 User 함수가 생성한 개체의 `__proto__` 개체를 가리킵니다.
```

또한, 생성자 함수로 생성한 개체는 `constuctor` 속성을 가지며, 자신을 생성한 개체를 가리킵니다.

```javascript
function User(name) {
    this.name = name;
}

var user = new User('Lee'); // 생성자 함수로 생성한 개체

console.log(user.constructor === User); // 자신을 생성한 개체와 동일합니다.
console.log(user.__proto__.constructor == User); // user.__proto__는 User 함수에서 생성했습니다.
console.log(User.constructor == Function);
console.log(User.__proto__.constructor == Function);
console.log(User.prototype.constructor == User); // user.__proto__ === User.prototype입니다.
```
상기 참조 관계를 그림으로 그리면 다음과 같습니다. 복잡한데요, 요약하자면 생성자 함수인 `User`의 `prototype`과 `User`로 부터 생성된 `user`의 `__proto__`는 동일한 개체이며, 이 개체의 `constructor`는 생성자 함수인 `User`입니다.

이러한 참조 관계를 이용하여 개체들이 참조하는 공통 구현을 `prototype`에 작성할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cb7c3874-6381-4158-b308-9b99aa79c8b5)

# 프로토타입 체인

특정 개체의 속성에 접근하려 할때 해당 속성이 없으면, 자신의 부모 역할을 하는 객체인 `__proto__` 접근하여 검색하게 됩니다. 이를 프로토타입 체인이라 합니다.

다음 예에서는 생성자 함수인 `User`의 `prototype`에 `addr`속성을 추가하였습니다.

이에 따라 `User`로 생성한 개체에서 `addr`에 접근하면, `User.prototype.addr`가 사용됩니다. 

```javascript
function User(name) {
    this.name = name;
}
User.prototype.addr = 'Seoul';

var user1 = new User('Kim');
var user2 = new User('Lee');

console.log(user1.__proto__ === user2.__proto__); // 같은 생성자 함수여서 __proto__가 동일합니다.
console.log(user1.name === 'Kim' && user1.addr === 'Seoul'); // user1.__proto__.addr 속성입니다.
console.log(user2.name === 'Lee' && user2.addr === 'Seoul'); // user2.__proto__.addr 속성입니다.
```

상기를 그림으로 보면 다음과 같습니다.

![Image](https://github.com/tango1202/tango1202.github.io/assets/133472501/162ff771-58b3-40d4-b51e-00243eecabd2)


만약 `user1.__proto__.addr`을 수정하면, 이로부터 생성된 모든 개체에서 수정된 값을 사용하게 됩니다.

다음 예에서 `user1.__proto__.addr`를 수정했지만, `user1`과 `user2`는 같은 `__proto__`를 공유하므로 함께 수정되는 것을 확인할 수 있습니다.

```javascript
// __proto__의 addr을 수정합니다. 
// User.prototype.addr = 'Pusan'; 과 동일합니다.
user1.__proto__.addr = 'Pusan'; 

console.log(user1.name === 'Kim' && user1.addr === 'Pusan'); // user1, user2는 같은 __proto__를 공유하므로 값이 함께 수정됩니다.
console.log(user2.name === 'Lee' && user2.addr === 'Pusan'); 
```

하지만, 만약 `user1.addr = 'Seoul'`와 같이 값을 설정한다면, `user1.__proto__.addr`을 수정하는게 아니라 `user1`개체에 `addr`속성을 추가합니다. 따라서 `user1.addr`은 자신의 속성값을 리턴하고, `user2.addr`은 프로토타입 체인을 통해 `__proto__`의 값을 리턴하기 때문에, 서로 다른 값을 갖게 됩니다.

![Image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f7c28811-7abd-451b-bcd9-c94ba3e9fe9a)

```javascript
user1.addr = 'Seoul'; // user1에 addr 속성을 추가합니다.
console.log(user1.name === 'Kim' && user1.addr === 'Seoul'); // user1.addr 속성입니다.
console.log(user2.name === 'Lee' && user2.addr === 'Pusan'); // user2.__proto__.addr 속성입니다.
```

사용자 개체 외에 기본 타입의 프로토타입도 수정할 수 있습니다.

다음은 기본 타입인 `String`의 `prototype`에 `add` 메서드를 추가한 예입니다.

```javascript
String.prototype.add = function(a, b) {
    return a + b;
}
console.log('String.add(1, 2)', 'test'.add(1, 2)); // 3
```

# 프로토타입 변경

프로토타입을 다른 개체로 변경할 수도 있습니다.

```javascript
function User(name) {
    this.name = name;
}
User.prototype = { // 프로토타입을 다른 개체로 변경할 수도 있습니다.
    addr: 'Seoul'
};

var user1 = new User('Kim');
var user2 = new User('Lee');

console.log(user1.name === 'Kim' && user1.addr === 'Seoul'); // user1.__proto__.addr 속성입니다.
console.log(user2.name === 'Lee' && user2.addr === 'Seoul'); // user2.__proto__.addr 속성입니다.

User.prototype.addr = 'Busan'; // prototype 값을 수정하면, 생성한 모든 개체에 반영됩니다.
console.log(user1.name === 'Kim' && user1.addr === 'Busan');
console.log(user2.name === 'Lee' && user2.addr === 'Busan'); 
```

# 함수 호출 방식에 따른 this 변경

`this`를 사용하여 개체 자신을 나타낼 수 있습니다만, 함수 호출 방식에 따라 약간씩 다르므로 주의해야 합니다. 

1. 일반 함수 : `this`는 전역 개체입니다.
2. `apply(), call(), bind()` : `this`는 지정한 개체입니다.
3. 개체 메서드 : `this`는 호출한 개체입니다.
4. 생성자 함수 : `this`는 리턴하는 개체입니다.
5. 중첩 함수 : `this`는 전역 개체입니다.
6. `prototype` : `this`는 호출한 개체입니다.


```javascript
var myName = 'Global';

// #1. 일반 함수에서의 this
function getName() {
    return this.myName; // this는 전역 개체입니다. 
}
console.log(getName() == 'Global');  

// #2. this를 다른 개체에 연결
var obj = {
    myName: 'Kim'
};
console.log(getName.apply(obj) == 'Kim'); // getName 함수의 this를 obj에 바인딩합니다.
console.log(getName.call(obj) == 'Kim');
var bindFunc = getName.bind(obj);
console.log(bindFunc() == 'Kim');

// #3. 개체 메서드에서의 this
var myUser1 = {
    myName: 'Lee',
    getName: function() {
        return this.myName; // this는 myUser1입니다.
    }
};
console.log(myUser1.getName() == 'Lee'); 

// #4. 생성자 함수의 this
function MyUser(name) {
    this.myName = name;
    this.getName = function() {
        return this.myName; // this는 생성자 함수가 리턴하는 개체입니다.
    };
}
var myUser2 = new MyUser('Kim');
console.log(myUser2.getName() === 'Kim'); 

// #5. 중첩 함수에서의 this
var myUser3 = {
    myName: 'Park',
    getNestName: function() {
        function f() {
            return this.myName; // this는 전역 개체입니다. 
        }
        return f();
    },
    getName: function() {
        var that = this; // 클로저를 활용하여 that으로 저장해 둡니다.
        function f() {
            return that.myName; // that은 바깥 함수의 this입니다.
        }
        return f();
    }
};

console.log(myUser3.getNestName() === 'Global'); 
console.log(myUser3.getName() === 'Park'); 

// #6. prototype에서의 this
function MyPrototypeUser(name) {
    this.myName = name;
}
MyPrototypeUser.prototype.getName = function() {
    return this.myName; // 해당 메서드를 호출한 개체입니다.
}
var myUser4 = new MyPrototypeUser('Kim');
var myUser5 = new MyPrototypeUser('Lee');

console.log(myUser4.getName() === 'Kim'); // this는 myUser4입니다.
console.log(myUser5.getName() === 'Lee'); // this는 myUser5입니다.
```

# 클로저를 이용한 캡슐화

클로저를 이용하면, 클래스의 멤버 변수를 `private`로 은닉하듯이 감출 수 있습니다.

다음 예에서 `count` 변수는 함수 내부에서만 사용할 수 있는 변수이지만, 중첩 함수를 통해 내부에서만 사용할 수 있습니다.

```javascript
function Counter() {
    var count = 0; // 외부에서 접근 할 수 없는 변수입니다. 마치 private와 유사합니다.

    this.inc = function () {
        return ++count;
    };
    this.dec = function() {
        return --count;
    }
}

var counter = new Counter();

console.log('counter', counter.inc()); // 1
console.log('counter', counter.dec()); // 0
```