---
layout: single
title: "#3. [Javascript] 함수"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 함수

[함수](https://tango1202.github.io/javascript/javascript-function/)는 #1의 함수 선언 방식과, #2의 함수 표현식 방식이 있습니다. 함수 표현식일 때는 관례적으로 함수명을 생략해서 사용합니다.(*함수명도 변수처럼 관습적으로 카멜 표기법을 사용합니다.*)

어느 방식으로 사용하던 함수 호출 방식은 동일합니다. 가독성 측면에서는 #1이 좋지만, 저는 #2를 선호합니다. #2는 [함수 호이스팅](https://tango1202.github.io/javascript/javascript-function/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)이 되지 않고, [타입스크립트](https://tango1202.github.io/categories/typescript/)와 함께 사용할때 함수 [인터페이스](https://tango1202.github.io/typescript/typescript-basic/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)를 지정할 수 있거든요.

아무튼, #1이던 #2이던 함수를 선언하면, #3과 같이 변수에 대입하여 호출할 수 있습니다.

```javascript
// #1. 함수 선언
function add(a, b) {
    return a + b;
}

// #2. 함수 표현식
const addFunc = function(a, b) {
    return a + b;
};

console.log('add(1, 2)', add(1, 2)); // 3
console.log('addFunc(1, 2)', addFunc(1, 2)); // 3

// #3. 변수 대입후 호출
const addVar1 = add;
const addVar2 = addFunc;
console.log('addVar1(1, 2)', addVar1(1, 2)); // 3
console.log('addVar2(1, 2)', addVar2(1, 2)); // 3
```

# 즉시 실행 함수를 이용한 유효 범위 한정

`function add(a, b) {return a + b;}(1, 2);`와 같이 함수 선언후 `(1, 2)`처럼 `(arg1, arg2, ...)`을 붙이면, 선언과 동시에 호출하는 구문이 됩니다. 이때 표현식으로 파싱되지 않을 수도 있어 함수 선언부를 괄호로 감쌉니다.

```javascript
console.log('즉시 실행 함수', 
    ( // 함수 선언부를 괄호로 감쌉니다.
        function(a, b) {
            return a + b;
        }
    )(1, 2)
); // 3
```

[즉시 실행 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%95%9C%EC%A0%95)를 활용하면 [유효 범위](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84)를 한정하고 실행할 수 있어, 전역 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 최소화 할 뿐만 아니라 이름 충돌을 최소화할 수 있습니다.

```javascript
(function() {
    const myApp = {};
    console.log('전역 개체 최소화', myApp); // myApp은 즉시 실행되고 소멸됩니다.
})();
```

[화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)를 이용하면 다음처럼 작성할 수 있습니다.

```javascript
(() => {
    const myApp = {};
    console.log('화살표 함수를 이용한 즉시 실행 함수', myApp); 
})();
```

# 함수 호이스팅

[변수 호이스팅](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)처럼 함수도 호이스팅됩니다. 따라서 함수 선언 전에 사용할 수 있습니다.

```javascript
console.log("함수 호이스팅", myHoisting(1, 2)); // 3. 함수 선언 전에 사용할 수 있습니다.

function myHoisting(a, b) {
    return a + b;
}
```

하지만, 함수 표현식은 [변수 호이스팅](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)을 합니다. 따라서, 호출시 종속성 순서가 맞지 않으면 `undefined`이기 때문에 컴파일 오류가 발생합니다. 유연성은 떨어지지만, 함수 선언 순서와 종속성 순서가 맞아야 하기 때문에 코딩 계약이 단단해 지는 느낌이라 저는 이 방식을 더 선호합니다.

```javascript
console.log("함수 표현식 호이스팅", myHoisting(1, 2)); // (X) 오류 발생. 함수 표현식은 변수 호이스팅을 합니다. 따라서 undefined 입니다.

const myHoisting = function(a, b) {
    return a + b;
}
```

# 문자열로부터 함수 생성

외부에서 문자열을 전달받아 함수를 생성할 수 있습니다.

`new Function(param1, param2, ..., body)`

```javascript
const myAdd = new Function('a', 'b', 'return a + b'); // 임의의 문자열로 함수를 생성합니다.
console.log('문자열로부터 함수 생성', myAdd(1, 2)); // 3
```

# 함수 속성

함수도 사실은 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)이기 때문에 속성을 가질 수 있습니다.

```javascript
function add(a, b) {
    return a + b;
}
add.data = 'Lee'; // 개체처럼 함수도 속성을 갖습니다.
console.log("함수 속성", add.data);
```

기본적으로 다음의 속성을 갖고 있습니다.(*`console.dir(함수 개체)`을 이용하면 상호 작용이 가능한 트리 형태로 [함수 속성](https://tango1202.github.io/javascript/javascript-function/#%ED%95%A8%EC%88%98-%EC%86%8D%EC%84%B1)을 확인할 수 있습니다.*)

|항목|내용|
|--|--|
|`arguments`|전달된 인수를 나열할 수 있는 [유사 배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4)입니다. 함수 외부에서는 사용할 수 없습니다. 가변 인자인 경우에 활용할 수 있습니다. ECMAScript6부터는 [나머지 인자](https://tango1202.github.io/javascript/javascript-function/#%EB%82%98%EB%A8%B8%EC%A7%80-%EC%9D%B8%EC%9E%90ecmascript6)를 사용합니다.|
|`caller`|자신을 호출한 함수를 가리킵니다. 브라우저에서 실행하면 `null`을 리턴합니다.|
|`length`|선언시 사용한 인자의 갯수입니다. (*arguments.length는 함수 호출시 전달한 인수의 갯수입니다.*)|
|`name`|함수의 이름입니다.|
|[`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)|모든 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)는 [[[Prototype]]](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)이라는 내부 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 있으며 기본적으로 `Object.prototype`과 동일합니다. [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)는 이에 접근하기 위한 속성입니다. [[[Prototype]] 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)는 [상속](https://tango1202.github.io/javascript/javascript-inheritance-class/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%83%81%EC%86%8D)을 구현하기 위해 사용되며, 다른 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에 공유 속성을 제공합니다.|
|[prototype](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)|함수 개체만 있는 속성입니다.(*일반 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)에는 없습니다.*) [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 생성하는 [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)로 사용될때 함수가 생성한 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 [`__proto__` 개체](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)를 가리킵니다.|

# 중첩 함수

함수내에 다른 함수를 중첩해서 선언할 수 있습니다. 

* [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)는 상위 함수의 변수에 접근 가능합니다.(*반대로 상위 함수는 [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)의 변수에 접근할 수 없습니다.*)
* 외부에서는 [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)에 접근할 수 없습니다.

```javascript
function outer(a, b) {
    function nestFunc(param1, param2) {
        return param1 + param2; // 상위 함수의 변수에 접근 가능합니다.
    }

    return nestFunc(1, 2);
}
console.log('중첩 함수', outer(1, 2)); // 3
```

# 클로저와 정보 은닉

[클로저](https://tango1202.github.io/javascript/javascript-function/#%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EC%A0%95%EB%B3%B4-%EC%9D%80%EB%8B%89)는 함수와 그 함수가 호출되었을때의 환경과의 조합입니다. 이 환경은 함수가 사용중이라면 소멸되지 않고 사용할 수 있습니다.

이 특징을 이용하면, [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)를 이용하여 특정 정보를 은닉하는 캡슐화를 할 수 있습니다.(*[클로저를 이용한 캡슐화](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%ED%81%B4%EB%A1%9C%EC%A0%80%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%BA%A1%EC%8A%90%ED%99%94) 참고*) 또한 [모듈화](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%AA%A8%EB%93%88%ED%99%94)에서도 사용하는 개념이니 만큼 잘 알아 두셔야 합니다.

다음은 함수의 지역 변수인 `count`를 은닉하고, [중첩 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)를 이용하여 `count`에 접근하는 예입니다.

1. 외부 함수인 #1을 선언하고, [즉시 실행](https://tango1202.github.io/javascript/javascript-function/#%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%95%9C%EC%A0%95)하여 [중첩된 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)인 #3 함수를 리턴합니다.
2. `count`는 #1 함수의 지역 변수 입니다. 외부에서 접근할 수 없습니다.
3. [중첩된 함수](https://tango1202.github.io/javascript/javascript-function/#%EC%A4%91%EC%B2%A9-%ED%95%A8%EC%88%98)인 #3을 리턴합니다.
4. #3에서 #2를 사용하므로 #1 함수가 소멸되더라도 #2는 소멸되지 않습니다.
5. `count` 변수가 소멸되지 않고 사용됩니다.

```javascript
const counter = (function() { // #1. 함수를 즉시 실행합니다. 중첩된 함수인 #3함수를 리턴합니다.
    var count = 0; // #2. 함수의 지역 변수입니다. 외부에서 직접 접근할 수 없습니다.
    return function() { // #3. 중첩된 함수입니다.
        return ++count; // #4. count 변수는 #2입니다. #3 함수가 참조하므로 #1함수가 소멸되더라도 #2는 소멸되지 않습니다.
    };
})();

// #5. #1 함수는 소멸되었지만, #3 함수를 counter에 전달받아 사용합니다. 
// 이에 따라 #2 변수는 소멸되지 않고 사용됩니다. 
console.log('클로저와 정보 은닉 : ', counter()); // 1
console.log('클로저와 정보 은닉 : ', counter()); // 2
console.log('클로저와 정보 은닉 : ', counter()); // 3
```

# 화살표 함수(ECMAScript6)

[화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)는 함수 선언을 간략하게 표현합니다.

```javascript
const add = function (a, b) {
    return a + b;
};
console.log('일반 함수', add(1, 2)); // 3
```

를

```javascript
const add = (a, b) => {
    return a + b;
};
console.log('화살표 함수', add(1, 2)); // 3
```

와 같이 간소하게 표현할 수 있습니다.

1줄짜리라면 `return`과 중괄호를 생략하여 더 간략하게 표현할 수 있습니다.

```javascript
const add = (a, b) => a + b;
console.log('화살표 함수', add(1, 2)); // 3
```
인자가 1개라면, 인자를 감싸는 괄호도 생략할 수 있습니다.

```javascript
const inc = a => a + 1;
```

[화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)는 일반 함수의 [this](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)와 다릅니다. 자세한 내용은 [함수 호출 방식에 따른 this 변경](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)을 참고하시기 바랍니다.


# 기본값 인자(ECMAScript6)

함수 인자에 [기본값 인자](https://tango1202.github.io/javascript/javascript-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90ecmascript6)를 주면, 인수가 전달되지 않았을때 사용합니다.

```javascript
const add = (a = 1, b = 2) => {
    return a + b;
};
console.log('인수를 전달하면, 전달한 값을 사용합니다', add(10, 20) === 30);
console.log('인수가 없으면, 기본값을 사용합니다', add() === 3);
```

# 나머지 인자(ECMAScript6)

함수 인자에 `...`을 붙여 선언하면, 전달된 인수들을 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)로 전달받습니다. 비슷한 용도로 제공되는 `arguments`는 [유사 배열](https://tango1202.github.io/javascript/javascript-basic/#%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4)이며, [화살표 함수](https://tango1202.github.io/javascript/javascript-function/#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98ecmascript6)에서는 사용할 수 없다고 하네요. 또한 ECMAScript3부터 deprecate되었다고 합니다. 이제 [나머지 인자](https://tango1202.github.io/javascript/javascript-function/#%EB%82%98%EB%A8%B8%EC%A7%80-%EC%9D%B8%EC%9E%90ecmascript6)를 사용하세요.

```javascript
const f = (...params) => {
    console.log('...params는 배열입니다', Array.isArray(params));
    params.forEach((param) => console.log('param은 배열 요소입니다', param));
};
f(1, 2, 3);
```

일반적인 인자와 [나머지 인자](https://tango1202.github.io/javascript/javascript-function/#%EB%82%98%EB%A8%B8%EC%A7%80-%EC%9D%B8%EC%9E%90ecmascript6)를 섞어 쓰면, 일반적인 인자에 인수를 할당한뒤 남은 것만 [나머지 인자](https://tango1202.github.io/javascript/javascript-function/#%EB%82%98%EB%A8%B8%EC%A7%80-%EC%9D%B8%EC%9E%90ecmascript6)에 할당합니다.

다음은 전달된 인수들의 합을 구하는 예입니다. 첫번째 인수를 뺀 나머지들을 다시 재귀적으로 호출하여 값을 누적합니다. [나머지 인자](https://tango1202.github.io/javascript/javascript-function/#%EB%82%98%EB%A8%B8%EC%A7%80-%EC%9D%B8%EC%9E%90ecmascript6)의 갯수가 1이면, 재귀 호출을 하지 않고 그냥 더합니다. 

`sum()`함수를 재귀 호출할때 `sum(...rest)`와 같이 호출했는데요, 이는 [Spread](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#spreadecmascript6)로서 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 다시 분리하는 역할을 합니다. 즉, `sum(rest[0], rest[1], ... , rest[N - 1])`처럼 호출합니다. 


```javascript
const sum = (first, ...rest) => {
    return first + ((rest.length === 1) ? rest[0] : sum(...rest)); // ...rest는 배열을 다시 분리하여 나열합니다. spread 참고
};

console.log('나머지 인자를 재귀적으로 호출합니다', sum(1, 2, 3) === 1 + 2 + 3);
```

# 이벤트 핸들러

이벤트가 발생했을때 실행하는 함수를 특별히 [이벤트 핸들러](https://tango1202.github.io/javascript/javascript-function/#%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%95%B8%EB%93%A4%EB%9F%AC)라고 합니다. 어떤 개체가 자기 할일을 하기 전이나 하고 나서, 현 상태를 통지하는 용도로 사용합니다. 즉, 개체 입장에서 [이벤트 핸들러](https://tango1202.github.io/javascript/javascript-function/#%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%95%B8%EB%93%A4%EB%9F%AC)는 있어도 그만, 없어도 그만인 것들입니다. 만약, 단순 통지가 아니라 외부에서 어떤 실행을 꼭 해야하고 리턴 결과가 필요하다면, [이벤트 핸들러](https://tango1202.github.io/javascript/javascript-function/#%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%95%B8%EB%93%A4%EB%9F%AC)를 실행하여 리턴값을 사용하기 보다는 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)으로 의존성을 주입하는게 가독성이나 일관성 측면에서 좋습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/699f8321-763f-491e-b791-b27c0d2d0264)

다음은 HTML DOM 엘리먼트의 이벤트를 전달받는 예입니다.

1. #1 : 문서 로딩이 끝나면 이벤트를 연결할 수 있도록 `DOMContentLoaded`에 [이벤트 핸들러](https://tango1202.github.io/javascript/javascript-function/#%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%95%B8%EB%93%A4%EB%9F%AC)를 연결하여 실행합니다.

2. #2 : `querySelector()`를 이용하여 문서에서 `button`과 `div` 엘리먼트를 찾습니다.

3. #3 : `button`과 `div` 클릭시 `onButtonClick()`과 `onDivClick()`이 호출되도록 [이벤트 핸들러](https://tango1202.github.io/javascript/javascript-function/#%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%95%B8%EB%93%A4%EB%9F%AC)를 연결합니다.

```html
<!doctype html>
<html>
    <body>
        <script>
            // #4
            const onDivClick = (e) => {
                alert('div를 클릭했습니다.');
            }; 
            // #4          
            const onButtonClick = (e) => {
                alert('버튼을 클릭했습니다.');
            };
            // #1. HTML 문서 로딩이 끝나면 호출됩니다.
            document.addEventListener("DOMContentLoaded", () => {
                // #2. 문서에서 button과 div 엘리먼트를 찾습니다.
                const button = document.querySelector("button");
                const div = document.querySelector("div");

                // #3. 버튼 클릭시 onButtonClick을 호출합니다.
                button.addEventListener("click", onButtonClick);

                // #3. div 클릭시 onDivClick을 호출합니다.
                div.addEventListener("click", onDivClick);
            });
        </script>    
        <div style = 'padding:10px;background-color: red'>
            <button>버튼</button>
        </div>
    </body>
</html>
```

상기 예를 실행하면, `div`클릭시에는 `onDivClick()`이 잘 호출되지만, `button`클릭시에는 `onButtonClick()`과 `onDivClick()`이 같이 호출됩니다. 이는 `onButtonClick()`후 이벤트가 상위 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)인 `div`로 전파되기 때문입니다. 이를 ***이벤트 버블링***이라고 하는데요, 다음과 같이 `stopPropagation()`을 호출하면 ***이벤트 버블링***을 막기 때문에 `onButtonClick()`만 호출됩니다.

```javascript
const onButtonClick = (e) => {
    alert('버튼을 클릭했습니다.');
    e.stopPropagation();
};
```

# alert(), confirm(), prompt() 

브라우저 환경에서 사용자 상호작용을 위한 메시지 대화상자입니다.

```javascript
alert('안녕하세요.'); // 메시지를 표시합니다.

const result1 = confirm('제목입니다.', '메시지 입니다.');
alert(result1); // 확인시 true, 취소시 false 입니다.

const result2 = prompt('제목입니다.', '내용을 입력하세요.'); // input을 통해 내용을 입력받습니다.
alert(result2); // 확인 클릭시 입력받은 내용입니다. 취소시 null 입니다.
```

# setTimeout(), setInterval(), clearTimeout()

`setTimeout()`는 지정한 밀리초 뒤에 함수를 1회 실행하며, `setInterval()`은 지정한 밀리초 간격으로 함수를 실행합니다. 각 함수는 `timerId`를 리턴하는데요, 이를 `clearTimeout()`에 인수로 전달하여 타이머를 삭제할 수 있습니다.

```javascript
const f = () => {
    console.log('1초 뒤에 출력합니다.');
};
setTimeout(f, 1000);

const intervalFunc = () => {
    console.log('1초 간격으로 출력합니다.');
};

const timerId = setInterval(intervalFunc, 1000);
setTimeout(() => {
    clearTimeout(timerId);
    console.log('타이머를 멈춥니다.');
}, 5000); // 5초 뒤에 timer를 삭제합니다.
```

# 함수 바인딩

함수 호출 인터페이스를 맞추기 위해 인자를 조정해야 할 때가 있습니다. 

예를 들어 [커맨드 패턴](https://tango1202.github.io/pattern/pattern-command/)으로 호출되는 함수를 구현한다면, 모든 함수가 아마도 `run()`과 같이 인자 없이 호출되어야 할 것입니다. 이러한 경우 어디선가 인자에 해당하는 값을 주섬주섬 가져와서, 다음처럼 만들어야 겠죠.

```javascript
const run = () => {
    func(getArg1(), getArg2());
};
```

상기 처럼 함수 바인딩을 직접 만들 수도 있지만, `bind()`함수를 직접 이용할 수도 있습니다.

다음은 인자 2개짜리 `sum()` 함수를 1개짜리 함수로 변경한 예입니다.

```javascript
const sum = (a, b) => {
    return a + b;
}
const inc = sum.bind(null, 1); // a 인자로 항상 1이 전달되게 합니다. 

console.log('함수 바인딩하여 인자 1개 짜리 함수로 바꿉니다.', inc(10) === 1 + 10);
```

