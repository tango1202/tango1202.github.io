---
layout: single
title: "#5. [Javascript] 개체"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개체

[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)는 속성의 집합이며 `{}` 내부에 속성을 정의합니다. `.`을 사용하거나 속성명(*키*)으로 속성값에 접근할 수 있습니다.

```javascript
const empty = {}; // 빈 개체
const user = {
    name: 'Lee' // name 속성이 있는 개체
};
console.log('개체 속성 접근, . 사용', user.name === 'Lee');
console.log('개체 속성 접근, 속성명 사용', user['name'] === 'Lee');
```

자바스크립트의 모든 것들은 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)이며, 심지어 함수도 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)입니다.

1. [함수](https://tango1202.github.io/javascript/javascript-function/)를 속성으로 사용할 수도 있으며, 이러한 [함수](https://tango1202.github.io/javascript/javascript-function/)를 특별히 ***메서드***라고 합니다.
2. [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 사용하여 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 자신을 나타낼 수 있습니다.
3. [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 `alert()`으로 출력하면 `object Object`만 표시됩니다. 

    ![image](https://github.com/user-attachments/assets/e8e18d3a-dc32-438b-8824-744fd1a9a837)

    하지만, `console.log()`로 출력하면 트리 형태로 표시되는데요, 이를 확장하면 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 속성명과 속성값을 확인할 수 있습니다.

    ![image](https://github.com/user-attachments/assets/4dcaeebd-554f-4cd9-ab9f-dac252dde10d)

4. `new Object()`로 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 생성한뒤 뒤늦게 속성들을 설정하는 방식도 있으나, 코딩 계약에 좋지 않아 잘 사용하지 않습니다.
5. [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 내부에서만 사용하는 속성은 관례적으로 밑줄(*_*)을 접두어로 사용합니다. 설계자가 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 위해 조치한 것이니(*언어적 차원에서 막을 수 있다면 더 좋았겠지만, 방도가 마땅치 않아서 한 조치이니*), 밑줄(*_*) 접두어가 있다면 외부에서는 사용하지 마세요.

```javascript
// 리터럴 방식 개체 생성
const user1 = {
    name: 'Lee',
    number: '123-4567',
    getName: function() { // #1. 함수를 사용할 수 있으며 메서드라고 합니다.
        return this.name; // #2. this는 자기 자신을 나타냅니다.
    }
};
alert(user1); // #3. object Object
console.log(user1); // #3. Object. 트리를 확장하여 내부 속성값을 확인할 수 있습니다.{name: 'Lee', number: '123-4567', getName: f}

console.log('개체 메서드 호출 user1.getName()', user1.getName()); // Lee

// #4. new Object 방식 
const user2 = new Object();
user2.name = 'Lee';
user2.getName = function() {
    return this.name;
}; 
console.log('개체 메서드 호출 user2.getName()', user2.getName()); // Lee

// #5. 내부에서만 사용하는 속성은 관습적으로 _을 붙임
const user3 = {
    _name1: 'Bruce', // 외부에서 사용하지 마세요.
    _name2: 'Lee', // 외부에서 사용하지 마세요.
    getName: function() {
        return `${this._name1} ${this._name2}`
    }
} 
console.log('개체 메서드 호출 user3.getName()', user3.getName()); // Bruce Lee
```

# 개체 속성 접근

속성명은 변수명에서 사용할 수 없는 숫자나 `-`, 공백 문자 조차도 사용할 수 있습니다.

심지어, `for`, `let`과 같은 예약어를 사용할 수 있으며, [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)만 사용할 수 없습니다.

각 속성은 보통 마침표로 접근할 수 있는데요, 숫자나 `-`, 공백 문자를 사용한 경우에는 `[]`에 속성명(*키*)을 이용해야 접근할 수 있습니다. 

```javascript
const user = {
    name: 'Lee',
    'addr': 'Seoul', // 속성명을 문자열로 선언할 수 있습니다.
    1 : '1', // 속성명을 숫자로 선언할 수 있습니다.
    2 : '2',
    'my number': '123-4567' // 속성명에 공백 문자등 일반적으로 사용할 수 없는 문자가 있으면 문자열로만 선언할 수 있습니다.
};
console.log("개체 속성 접근 user.name === 'Lee'", user.name === 'Lee'); // 마침표로 속성값에 접근합니다.
console.log("개체 속성 접근 user.addr === 'Seoul'", user.addr === 'Seoul'); 
console.log("개체 속성 접근 user[1] === '1'", user[1] === '1'); // 속성명이 숫자이면 배열처럼 접근할 수 있습니다.
console.log("개체 속성 접근 user[2] === '2'", user[2] === '2'); 
console.log("개체 속성 접근 user['my number'] === '123-4567'", user['my number'] === '123-4567'); // 일반적으로 사용할 수 없는 속성명이면, [] 로 접근할 수 있습니다. 
```

# 개체 속성 추가/삭제

속성을 동적으로 추가/삭제 할 수도 있습니다.

```javascript
const user = {};
user.name = 'Lee'; // name 속성이 없으면 추가합니다.
console.log("속성 추가 user.name === 'Lee'", user.name === 'Lee'); // name 속성이 추가되었습니다.

delete user.name;
console.log("속성 삭제 후 user.name === undefined", user.name === undefined); // name 속성이 삭제되었습니다.
```

# 속성 나열 : for-in

[for-in](https://tango1202.github.io/javascript/javascript-object/#%EC%86%8D%EC%84%B1-%EB%82%98%EC%97%B4--for-in)으로 속성명을 나열할 수 있고, 이를 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 키로 사용하면 속성값을 확인할 수 있습니다.

```javascript
const user = {
    name: 'Lee',
    'addr': 'Seoul', 
    1 : '1',
    2 : '2',
    'my number': '123-4567' 
};

for (let prop in user) { // prop은 속성명, user[prop]은 속성값
    console.log('속성명 :' + prop , '속성값 :' + user[prop]);
}
```

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)에도 사용할 수 있지만, [배열 요소](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C-%EC%B6%94%EA%B0%80%EC%82%AD%EC%A0%9C)외에 다른 속성이 있다면 함께 나열되기 때문에, [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)에서는 잘 사용하지 않습니다.

```javascript
const arr = ['one', 'two', 'three'];
arr.extraData = "추가 정보 입니다."; // 속성을 추가합니다.
for (let prop in arr) { // 배열 요소와 추가 속성이 나열됩니다.
    console.log('속성명 :' + prop , '속성값 :' + arr[prop]);
}
```

상기 코드를 실행하면, 다음처럼 배열 요소와 `extraData`가 나열되는 것을 확인할 수 있습니다.

![image](https://github.com/user-attachments/assets/1102b3ef-24b1-424a-8b65-6df75c5c1cc3)

# defineProperty(), getOwnPropertyDescriptor()

[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 속성은 세부적으로 다음과 같은 플래그가 있습니다.

|항목|내용|
|--|--|
|`writable`|`true`이면 읽기/쓰기가 가능하고, `false`이면 읽기만 가능합니다.|
|`enumerable`|`true`이면 [for-in](https://tango1202.github.io/javascript/javascript-object/#%EC%86%8D%EC%84%B1-%EB%82%98%EC%97%B4--for-in)으로 나열이 가능합니다.
|`configurable`|`true` 이면 속성 삭제나 플래그 수정이 가능하고, `false`이면 불가능 합니다. `writable`, `enumerable`도 `false`로 만들며 다시 수정할 수 없게 만듭니다.|

다음은 `user`의 `name`속성이 열거되지 않도록 `defineProperty()`를 이용해서 `enumerable`을 설정한 예입니다.

```javascript
const user = {
    name: 'Lee',
    addr: 'Seoul'
};

for (let prop in user) {
    console.log('속성명 :' + prop , '속성값 :' + user[prop]); // name과 addr이 열거됩니다.
}

Object.defineProperty(user, 'name', {
    enumerable: false // name은 열거되지 않게 합니다.
});
for (let prop in user) {
    console.log('name은 열거되지 않습니다. 속성명 :' + prop , '속성값 :' + user[prop]);
}

const descriptor = Object.getOwnPropertyDescriptor(user, 'name');
console.log('name의 enumerable은 false 입니다.', descriptor.enumerable === false);
```

# 개체 비교와 대입

[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)나 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)은 `===`비교를 할때 동일한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)인지 검사합니다. 값이 동일한 지 검사하는게 아니라 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 자체가 동일한지를 검사합니다.

따라서, 값이 동일한지를 검사하려면, #1과 같이 하위 속성을 모두 뒤져서 [기본 타입](https://tango1202.github.io/javascript/javascript-basic/#%ED%83%80%EC%9E%85%EA%B3%BC-%EB%A6%AC%ED%84%B0%EB%9F%B4)끼리 검사해야 합니다.

#2와 같이 `=`로 대입하면 동일 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 됩니다. 한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 새로운 별칭은 만든 셈입니다.(*이를 얕은 복사라 합니다.*) 동일한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)이므로 `===` 비교시 `true`이며, 한쪽의 속성을 바꾸더라도, 다른쪽에 변경사항이 반영됩니다.

```javascript
const user1 = {name: 'Lee'};
const user2 = {name: 'Lee'};

console.log('값은 같지만 다른 개체입니다. user1 !== user2', user1 !== user2); 
console.log('개체의 하위 속성을 모두 뒤져서 기본 타입끼리  검사해야 합니다. user1.name === user2.name', user1.name === user2.name); // #1

const user3 = user1; // #2. 대입하면 동일 개체입니다.
console.log('개체를 대입하면 동일 개체입니다. user1 === user3', user1 === user3);
user1.name = 'Kim';
console.log('user1을 수정하면, 동일 개체인 user3도 반영됩니다.', user3.name === 'Kim');
```

# 개체 복제/동결

[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)는 기본적으로 얕은 복사를 하며, 동일 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 참조합니다. 따라서, `user2 = user1`은 사실 동일 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 참조하며, `user2`를 수정하면, `user1`도 수정됩니다.

```javascript
const user1 = {name: 'Lee'};
const user2 = user1;
user2.name = 'Kim';
console.log("동일 개체를 참조합니다 user1.name === 'Kim'", user1.name === 'Kim'); // true. user2를 수정했지만, user1도 수정되었습니다.
```

동일 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 아니라 값이 동일한 복제본을 만드려면, 내부 속성들을 일일이 복제해야 하며, `Object.assign()`(*ECMAScript6*)을 이용할 수 있습니다.(*또한 [Spread](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#spreadecmascript6)를 이용하여 개체 속성을 복제할 수도 있습니다.*) 하지만 하위 개체는 여전히 얕은 복사를 하니 주의해야 합니다. 다음 예에서 `name`은 복제되었지만, `addr`은 하위 개체이기 때문에 복제되지 않고 여전히 같은 개체를 참조합니다.

```javascript
const user1 = {
    name: 'Lee',
    detail: {
        addr: 'Seoul'
    }
};
const user2 = Object.assign({}, user1); // 복제합니다.
user2.name = 'Kim';
user2.detail.addr = 'Busan';

console.log("개체 복제 user1.name === 'Lee'", user1.name === 'Lee'); // true. user2를 수정했지만, user1은 수정되지 않습니다.
console.log("개체 복제 user2.name === 'Kim'", user2.name === 'Kim');

console.log("하위 개체는 여전히 참조 user1.detail.addr === 'Busan'", user1.detail.addr === 'Busan'); // true. 하위 개체는 얕은 복사됩니다.
console.log("하위 개체는 여전히 참조 user2.detail.addr === 'Busan'", user2.detail.addr === 'Busan'); 

const user3 = {...user1}; // spread를 이용하여 복제합니다.
user3.name = 'Park';
user3.detail.addr = 'Incheon';

console.log("개체 복제 user1.name === 'Lee'", user1.name === 'Lee'); // true. user3를 수정했지만, user1은 수정되지 않습니다.
console.log("개체 복제 user3.name === 'Park'", user2.name === 'Park');

console.log("하위 개체는 여전히 참조 user1.detail.addr === 'Incheon'", user1.detail.addr === 'Incheon'); // true. 하위 개체는 얕은 복사됩니다.
console.log("하위 개체는 여전히 참조 user2.detail.addr === 'Incheon'", user2.detail.addr === 'Incheon'); 

```

하위 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)까지 복제하고 싶으면, 하위 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)도 일일이 `Object.assign()`을 이용하거나, [Spread](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#spreadecmascript6)를 이용하여 일일이 복제해야 합니다. 좀 번거롭죠. 그래서 대안으로 속도 성능은 떨어지지만, [JSON](https://tango1202.github.io/javascript/javascript-object/#json)을 이용하여 간편하게 복제할 수 있습니다.

```javascript
const user1 = {
    name: 'Lee',
    detail: {
        addr: 'Seoul'
    }
};
const user2 = JSON.parse(JSON.stringify(user1)); // 하위 개체의 속성까지 복제합니다.
user2.name = 'Kim';
user2.detail.addr = 'Busan';

console.log("개체 복제 user1.name === 'Lee'", user1.name === 'Lee'); 
console.log("개체 복제 user2.name === 'Kim'", user2.name === 'Kim');

console.log("JSON으로 하위 개체도 복제 user1.detail.addr === 'Seoul'", user1.detail.addr === 'Seoul'); // 하위 개체도 복제됩니다.
console.log("JSON으로 하위 개체도 복제 user2.detail.addr === 'Busan'", user2.detail.addr === 'Busan');    
```

하위 속성까지 꼼꼼하게 복제하면 개체의 진정한 복제본이 될까요? 아쉽지만 아닙니다. [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)가 남아 있습니다.

[생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 이용하여 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 생성하면, 내부적으로 [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)가 생성되는데요, `assign()`이나 [Spread](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#spreadecmascript6)로 복제하면 #1처럼 [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)가 `Object`로 초기화 되어 버립니다. [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)도 동일하게 하려면, #2처럼 `Object.create()`를 함께 이용해야 합니다.(*[prototype](https://tango1202.github.io/javascript/javascript-prototype/) 참고*)

```javascript
function User(name) { // 생성자 함수.
    this.name = name;
}
const user1 = new User('Lee'); 
const user2 = {...user1}; 
const user3 = Object.assign(Object.create(User.prototype), user1); // #2. 동일한 prototype을 사용할 수 있도록 Object.create()를 이용합니다.

console.log('생성자 함수 User로 생성했습니다.', user1 instanceof User);
console.log('spread로 개체 속성을 복제한 개체입니다.', user2 instanceof Object); // #1. Object로 초기화 되어 있습니다.
console.log('create()와 assign()으로 복제했습니다.', user3 instanceof User); // #2. Object.create()를 이용하면 user개체로 복제됩니다.
```

`Object.freeze()`로 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 수정할 수 없게끔 동결시킬 수 있습니다. 하지만, 하위 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에는 적용되지 않습니다.

이외에도 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 수정과 관련하여 다음과 같은 메서드 들이 있습니다.

|항목|내용|
|--|--|
|`Object.preventExtensions()`|[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에 새로운 속성 추가를 막습니다.|
|`Object.seal()`|새로운 속성 추가나 기존 속성 삭제를 막습니다.|
|`Object.freeze()`|새로운 속성 추가나 기존 속성 삭제, 속성값 수정을 막습니다.|
|`Object.isExtensible()`|`preventExtensions()`인지 확인합니다.|
|`Object.isSealed()`|`seal()`인지 확인합니다.|
|`Object.isFrozen()`|`freeze()`인지 확인합니다.|

# getter, setter(ECMAScript5)

`getter` 와 `setter`를 이용하면 속성에 값을 저장하거나 불러올때 [함수](https://tango1202.github.io/javascript/javascript-function/)인 메서드를 사용할 수 있고, 속성에 대한 접근 통제나 유효성 검사를 좀 더 캡슐화할 수 있습니다.

```javascript
    const user = {
    _name: '', // 실제 name 값을 저장합니다.
    get name() {
        return this._name;
    }, 
    set name(name) {
        // 값을 설정하기 전에 유효성 검사를 할 수 있고, 수정할 수도 있습니다.
        this._name = 'name is ' + name;
    }
};

user.name = 'Lee';
console.log('setter로 이름을 수정했습니다.', user.name === 'name is Lee');
```

# JSON

[JSON](https://tango1202.github.io/javascript/javascript-object/#json)은 자바스크립트 객체 문법으로 구조화된 데이터를 표현하기 위한 문자 기반의 표준 포맷입니다.

[JSON](https://tango1202.github.io/javascript/javascript-object/#json)을 이용하면, `stringify()`함수로 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 문자열로 만들고, `parse()`함수로 문자열을 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)로 만들 수 있습니다.

```javascript
const obj = {
    x: 10,
    y: 20,
    value: '문자열',
    datas: [1, 2, 3]
};

const str = JSON.stringify(obj);
console.log('JSON으로 만든 문자열', str === '{"x":10,"y":20,"value":"문자열","datas":[1,2,3]}');  

const result = JSON.parse(str);
console.log('JSON 문자열로부터 개체 생성', result.x === 10 && result.y === 20 && result.value === '문자열' && result.datas[0] === 1 && result.datas[1] === 2 && result.datas[2] === 3);
```

개체에 `toJSON()` 함수가 구현되어 있다면, `stringify()` 호출시 `toJSON()`을 호출합니다.

```javascript
const obj = {
    name: 'Lee',
    toJSON: function() {
        return `name is ${this.name}`
    }
};
console.log('toJSON을 이용합니다.', JSON.stringify(obj) === '"name is Lee"');
```


# 개체의 생성자 함수

동일한 구조의 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 여러개 생성하고자 할때 매번 리터럴 방식으로 생성하면, 속성값 선언이나 메서드 선언 코드가 중복 될 수 있습니다. 

```javascript
const user1 = {
    name: 'Kim',
    number: '123-4567',
    getName: function() {return this.name;} // 메서드 선언 코드가 중복됩니다.
};
const user2 = {
    name: 'Lee',
    number: '111-2222',
    getName: function() {return this.name;} // 메서드 선언 코드가 중복됩니다.
};
```

이러한 경우에는 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 통해 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 생성하면 메서드 선언 코드 중복을 어느정도 해결할 수 있습니다.

1. [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)는 일반 [함수](https://tango1202.github.io/javascript/javascript-function/)와 구분하기 위해 관습적으로 Pascal 표기법을 사용합니다.
2. [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 생성해서 리턴되는 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 지칭합니다.
3. `new`로 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 호출합니다.
4. 암시적으로 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)를 생성하여 리턴하는 [함수](https://tango1202.github.io/javascript/javascript-function/)라고 이해하셔도 됩니다.

```javascript
function User(name, number) { // #1. 일반 함수와 구분하기 위해 Pascal 표기법을 사용합니다.
    this.name = name; // #2. this는 생성될 개체입니다.
    this.number = number;
    this.getName = function() {
        return this.name;
    };
    // 암시적으로 this 개체를 리턴합니다.
}

const user1 = new User('Kim', '123-4567'); // #3. new로 함수를 호출합니다.
const user2 = new User('Lee','111-2222');

console.log('user1.getName()', user1.getName()); // Kim
console.log('user2.getName()', user2.getName()); // Lee
```

[생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)로 메서드 선언 코드 중복을 어느정도 해결할 수 있습니다만, 메서드 자체가 메모리에 중복 생성되는 문제가 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/62b04234-9868-4a15-ac4d-6ef78230347d)

`name`이나 `number`같은 데이터를 저장한 속성은 당연히 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)마다 따로 존재해야 하지만, 메서드까지 따로 존재할 필요는 없죠. 

쓸데없이 메모리를 차지하게 되니 메서드는 [프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85)을 이용하는게 좋습니다.(*[즉시 실행 함수를 이용한 개체 선언](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EA%B0%9C%EC%B2%B4-%EC%84%A0%EC%96%B8) 참고*)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d3cdbd51-a101-45a3-aa1f-36db27feb44f)

또한, [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 호출할때 `new`를 사용하지 않는다면, 일반 [함수](https://tango1202.github.io/javascript/javascript-function/)처럼 호출되니 주의해야 합니다.

만약 `new`를 생략한다면,

1. [함수](https://tango1202.github.io/javascript/javascript-function/)내에서 직접적인 `return`이 없으므로 `user`는 `undefined`입니다.
2. [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 전역 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)이므로 전역 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에 `name`속성을 추가합니다.

```javascript
function User (name) { 
    this.name = name; 
}
const user = User('Kim'); 

console.log('리턴값이 없으므로 user는 undefined 입니다', user === undefined); // #1
console.log('this는 전역 개체이므로 전역 개체에 name을 저장합니다.', name === 'Kim'); // #2
```

[기본 타입](https://tango1202.github.io/javascript/javascript-basic/#%ED%83%80%EC%9E%85%EA%B3%BC-%EB%A6%AC%ED%84%B0%EB%9F%B4)도 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)로 생성할 수 있습니다.

하지만 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)이다 보니 [기본 타입](https://tango1202.github.io/javascript/javascript-basic/#%ED%83%80%EC%9E%85%EA%B3%BC-%EB%A6%AC%ED%84%B0%EB%9F%B4)과 직접 비교 할 수 없어 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98) 하여야 하며, 값이 같더라도 `===` 비교시 다른 값으로 평가됩니다.(*[개체 비교](??) 참고*)

```javascript
const num1 = new Number(1);
const num2 = new Number(1);

console.log('Number()로 기본타입 개체를 생성합니다. 기본 타입과 검사하려먼 형변환 해야 합니다. 개체이므로 실제값은 동일하지만 === 이진 않습니다.', num1 !== 1 && Number(num1) === 1, num1 !== num2);

const str1 = new String('Kim');
const str2 = new String('Kim');  

console.log('String()으로 기본타입 개체를 생성합니다. 기본타입과 검사하려면 형변환 해야 합니다. 개체이므로 실제값은 동일하지만 === 이진 않습니다.', str1 !== 'Kim', String(str2) === 'Kim', str1 !== str2);

```


# 속성 축약 표현(ECMAScript6)

[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에 사용하는 변수가 속성명과 같은 경우 축약하여 표현할 수 있습니다.

즉,

```javascript
const x = 10;
const y = 20;
const obj = {
    x: x, 
    y: y
};
```

을 다음과 같이 축약하여 변수만 나열하면 됩니다.

```javascript
const x = 10;
const y = 20;
const obj = {
    x, // x: x 와 동일합니다.
    y // y: y 와 동일합니다.
};
console.log('속성 축약 표현', obj.x === 10 && obj.y === 20);
```
# 속성명 동적 생성(ECMAScript6)

리터럴 방식으로 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 생성시 속성명(*키*)을 동적으로 생성할 수 있습니다. 이때 속성명 표현식은 `[]`로 묶습니다.

```javascript
const index = 10;
const obj = {
    [`myData-${index + 1}`]: 1 // 속성명을 myData-11로 만듭니다.
};
console.log('속성명 동적 생성', obj['myData-11'] === 1);
```

# 메서드 축약 표현(ECMAScript6)

[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 메서드 선언시 `function`을 생략할 수 있습니다. 

```javascript
const obj1 = {
    myMethod: function() {
    },
};
const obj2 = {
    myMethod() { // 축약해서 표현합니다.
    },
};
```

# Date

`Date 개체`를 사용하면 날짜를 처리할 수 있습니다. 

내부적으로는 UTC 1970년 1월 1일 0시 0분 0초 이후의 밀리초로 데이터를 관리하는데요, 이때 `month`는 0 base 여서 0이면 1월이고, 1이면 2월이고 그렇습니다.

```javascript
const now = new Date(); // 현재. 혹은 Date.now();
const milli = new Date(2 * 60 * 60 * 1000); // UTC 1970년 1월 1일 0시 0분 0초 후 2시간 뒤
const ymd = new Date('2024-05-06');
const date = new Date(2023, 4, 6, 10, 20, 30, 456); // monthIndex 0부터 시작함. 즉 4는 5월. 2023-05-06 10시20분30초 456밀리초
const parse = new Date('2024-05-06T10:20:30.456'); // T 시간 구분 기호

console.log('현재 시간', now);
console.log(`UTC 시분초 밀리초 ${now.getUTCHours()}:${now.getUTCMinutes()}:${now.getUTCSeconds()}.${now.getUTCMilliseconds()}`);
console.log('기준시에서 2시간뒤', milli);
console.log('지정 년월일', ymd);
console.log('지정 년월일시분초밀리초', date);
console.log('파싱한 시간', parse);

console.log(`로컬 년월일 ${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()}`);
console.log(`로컬 시분초 밀리초 ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}.${date.getMilliseconds()}`);
console.log(`로컬 요일 ${date.getDay()}`); // 0이 일요일
```

두 날짜의 차이는 `Number`로 변환한 뒤 계산할 수 있습니다. 계산 결과는 밀리초입니다.

```javascript
const day = new Date('2024-05-06');
day.setDate(day.getDate() + 2)
console.log('2일 뒤', day.getDate() === 8);

const day1 = new Date('2024-05-06');
const day2 = new Date('2024-05-08');

console.log('날짜 차이', (Number(day2) - Number(day1)) === 2 * 24 * 60 * 60 * 1000); // 2일에 해당 하는 밀리초
```

서식화된 출력을 위해선 약간의 트릭이 필요합니다. 예를 들어 5월은 1자리수인데, `05`와 같이 `0`을 붙여 표시하는게 좋죠. 이런 경우 앞에 `0`을 붙여서 문자열로 만든뒤, 뒤의 2자리만 취하여 원하는 결과를 얻을 수 있습니다.

```javascript
const date = new Date('2024-05-06');

const toYYYYMMDD = (date) => {
    const yyyy = date.getFullYear();
    const mm = ('0' + (date.getMonth() + 1)).slice(-2); // 앞에 0을 붙여서 문자열로 만들고, 뒤의 2자리만 취함
    const dd = ('0' + date.getDate()).slice(-2);
    return `${yyyy}-${mm}-${dd}`;
};

console.log('기본 형태', date); 
console.log('yyyy-mm-dd', toYYYYMMDD(date) === '2024-05-06'); 
```

