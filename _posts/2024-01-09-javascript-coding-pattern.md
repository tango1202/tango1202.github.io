---
layout: single
title: "#9. [Javascript] 코딩 패턴"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 코딩 패턴 - 즉시 실행 함수를 이용한 모듈화

연관있는 일련의 [함수](https://tango1202.github.io/javascript/javascript-function/)들을 모아 [모듈화](https://tango1202.github.io/javascript/javascript-module/)를 하면, 구조적이고 계층적으로 코드를 관리를 할 수 있어 가독성과 생산성이 좋아집니다. 기본적으로 [클로저와 정보 은닉](https://tango1202.github.io/javascript/javascript-function/#%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EC%A0%95%EB%B3%B4-%EC%9D%80%EB%8B%89)을 사용하므로 `private` 함수와 `public` 함수로 구분할 수도 할 수 있습니다.

다음에서 `Module`은 [즉시 실행 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%95%9C%EC%A0%95)입니다. 

즉시 실행되어 `publicFunc` 함수(*`privateFunc`은 빼고요*)를 메서드로 가진 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 리턴합니다. 

이렇게 하면 `privateFunc`은 외부에서 접근할 방법이 없어 사용할 수 없고, `publicFunc`만 리턴된 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 통해 사용할 수 있습니다. 또한 `publicFunc`이 사용되는 한 `Module`내의 실행 환경이 소멸되지 않고 유지되므로, `publicFunc`에서는 `privateFunc`을 안심하고 사용할 수 있습니다.(*[클로저와 정보 은닉](https://tango1202.github.io/javascript/javascript-function/#%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EC%A0%95%EB%B3%B4-%EC%9D%80%EB%8B%89) 참고*)

```javascript
const Module = (() => { // 즉시 실행 함수입니다.
    const privateFunc = (a, b) => { // 외부에서 접근할 방법이 없습니다.
        return a + b;
    };
    const publicFunc = (a, b) => { // 외부에서 접근할 수 있도록 리턴합니다.
        return privateFunc(a, b);
    }; 

    // public 함수 선언들로 구성된 개체를 리턴합니다.
    return {
        publicFunc // 함수가 리턴되므로 함수 실행 환경이 소멸되지 않고 유지됩니다.
    };
})();

console.log('모듈의 public 함수 호출', Module.publicFunc(1, 2)); // 3
Module.privateFunc(a, b); // (X) 오류 발생. 접근할 수 없습니다.
```

# 코딩 패턴 - 즉시 실행 함수를 이용한 개체 선언

[개체의 생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)와 [프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)의 메서드들을 [즉시 실행 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%95%9C%EC%A0%95)를 이용하여 응집하면 코드의 가독성이 좋아집니다.

다음에서 [즉시 실행 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%95%9C%EC%A0%95)인 `User`(*#1*)는 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)인 `User`(*#2*)를 #3과 같이 리턴하는데요, #4에서 #1의 리턴값인 #2 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 호출하고 있습니다.

```javascript
const User = (() => { // #1. 즉시 실행 함수입니다.
    function User(name) { // #2. 생성자 함수
        this.name = name;
    }
    User.prototype.getName = function() { // 메서드는 프로토타입에 선언합니다.
        return this.name;
    }; 
    
    return User; // #3. #2 생성자 함수를 리턴합니다.
})();

const user1 = new User('Kim'); // #4. #1 개체의 리턴값인 #3을 new () 형태로 실행합니다. 즉, #2 생성자 함수를 호출합니다.
const user2 = new User('Lee');

console.log('즉시 실행 함수를 이용한 개체 선언', user1.getName()); // Kim
console.log('즉시 실행 함수를 이용한 개체 선언', user2.getName()); // Lee     
```

# 코딩 패턴 - MixIn을 이용한 메서드 동적 추가

`mixIn` 헬퍼 [함수](https://tango1202.github.io/javascript/javascript-function/)를 이용하여 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 메서드를 동적으로 추가할 수 있습니다.

```javascript
const user1 = {name: 'Kim'};
const user2 = {name: 'Lee'};

const getNameMixIn = (obj) => {
    obj.getName = function() { // this를 사용하므로 화살표 함수를 사용하지 않습니다.
        return this.name;
    };
}; 

// user1, user2에 getName 메서드를 만들어 줍니다.
getNameMixIn(user1); 
getNameMixIn(user2);

console.log('MixIn으로 추가된 메서드', user1.getName()); // Kim
console.log('MixIn으로 추가된 메서드', user2.getName()); // Lee
```

`mixIn`들을 모아 [즉시 실행 함수를 이용한 모듈 개체](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%AA%A8%EB%93%88%ED%99%94)(`MixInModule`)로 리턴하면 주어진 `getNameMixIn`, `printNameMixIn`들 중에서 필요한 것만 선택해서 결합할 수 있습니다.

```javascript
const user1 = {name: 'Kim'};
const user2 = {name: 'Lee'};

const MixInModule = (() => {
    const getNameMixIn = (obj) => {
        obj.getName = function() { 
            return this.name;
        };
    };
    const printNameMixIn = (obj) => {
        obj.printName = function() {
            console.log(this.name);
        };
    };

    // MixIn 함수 선언들로 구성된 개체를 리턴합니다.
    return {
        getNameMixIn,
        printNameMixIn,
    };
})();       

// user1, user2에 추가할 메서드들을 MixInModule에서 선택적으로 결합합니다.
MixInModule.getNameMixIn(user1); 
MixInModule.printNameMixIn(user2);

console.log('MixInModule에서 추가된 메서드', user1.getName()); // Kim
user2.printName(); // Lee 출력
```

또다른 방법으로는 `Object.assign()`을 이용하는 방법이 있습니다. 상기처럼 선택적으로 추가할 수는 없지만, `mixIn`의 모든 메서드를 한번에 추가할 수 있습니다.

```javascript
const user1 = {name: 'Kim'};
const user2 = {name: 'Lee'};

const mixIn = {
    getName: function() {
        return this.name;
    },
    printName: function() {
        console.log(this.name);
    }
}; 

// user1, user2에 mixIn을 추가합니다.
Object.assign(user1, mixIn); // 기존 속성중 동일한게 있으면 덮어쓰고, 없다면 추가
Object.assign(user2, mixIn);

console.log('Object.assign()을 이용한 MixIn', user1.getName()); // Kim
user2.printName(); // Lee 출력    
```

# 코딩 패턴 - 정적 함수

[정적 함수](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A0%95%EC%A0%81-%ED%95%A8%EC%88%98)는 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)의 속성 기능을 이용하여 구현 할 수 있습니다.

```javascript
const User = (() => { 
    function User(name) { 
        this.name = name;
    }
    User.prototype.getName = function() { 
        return this.name;
    }; 

    User.staticFunc = (msg) => { // 생성자 함수의 속성 메서드 입니다.
        console.log(msg);
    };
    
    return User; 
})();

const user1 = new User('Kim');
const user2 = new User('Lee');

console.log('개체의 메서드', user1.getName()); // Kim
console.log('개체의 메서드', user2.getName()); // Lee
User.staticFunc('생성자 함수의 속성 메서드입니다. 정적 함수와 유사합니다.');
```

# 코딩 패턴 - 클로저를 이용한 캡슐화

[클로저](https://tango1202.github.io/javascript/javascript-function/#%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EC%A0%95%EB%B3%B4-%EC%9D%80%EB%8B%89)를 이용하면, C++에서 클래스의 멤버 변수를 `private`로 은닉하듯이 감출 수 있습니다.

다음 예에서 `count` 변수는 `inc()`, `dec()`등 `Counter` 함수 내부에서만 사용할 수 있고, 외부에서는 직접 접근할 수 없습니다. 

```javascript
function Counter() {
    let count = 0; // 외부에서 접근 할 수 없는 변수입니다. 마치 private와 유사합니다.

    this.inc = () => {
        return ++count;
    };
    this.dec = () => {
        return --count;
    };
};

const counter = new Counter();

console.log('counter', counter.inc()); // 1
console.log('counter', counter.dec()); // 0
```



