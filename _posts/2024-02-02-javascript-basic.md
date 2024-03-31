---
layout: single
title: "#2. [Javascript] 기본 문법"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 문

구문은 `;`나 개행으로 끝납니다. 관습적으로 `;`을 선호합니다.

# 주석

* `//` : 한줄 주석입니다.
* `/* */` : 블록 영역 주석입니다.

# 변수

자바스크립트는 동적 타입 언어입니다. 따라서 변수의 타입은 동적으로 결정되는데요, 숫자형 변수로 사용하다가 문자열형 변수로 바꿔서 사용할 수도 있습니다.

변수명은 관습적으로 카멜 표기법을 따르며, 문자, 숫자, `$`, `_`로 구성할 수 있고, 숫자의 경우 첫글자로 사용할 수 없습니다.

1. `var`로 변수를 선언할 수 있습니다.

    ```javascript
    var x; // #1. 선언
    x = 1; // 선언 후 할당
    ```

2. 선언과 동시에 할당할 수 있습니다.

    ```javascript
    var y = 2; // #2. 선언과 동시에 할당
    ```

3. 선언한 것을 다시 재선언 할 수 있습니다.

    ```javascript
    var x; // #1. 선언
    var x = 3; // #3. 선언된 것을 다시 재선언
    ```

4. 동적 타입 언어 이기 때문에 선언한 타입과 다른 타입을 바꿔서 할당할 수 있습니다.

    ```javascript
    var x; 
    var x = 3; // 숫자 타입을 할당했습니다.
    x = 'Hello'; // #4. 타입을 바꿔서 할당할 수 있습니다.
    ```

5. 아예 선언하지 않고 사용할 수 있습니다. 이 경우 전역 개체의 변수로 선언됩니다.

    ```javascript
    z = 4; // #5. 선언하지 않고 사용. 전역 개체의 변수로 선언됩니다.
    ```

엄청 유연합니다만, 코딩 계약이 엉망진창이 되버려 대규모 프로젝트에서는 오류를 찾아내기 힘들어 집니다. 따라서 [타입스크립트](https://tango1202.github.io/categories/typescript/)를 사용하시길 추천드립니다.

# 변수 호이스팅

선언하지 않은 변수를 사용했을때, 변수 선언이 유효 범위의 시작점에 있는 것처럼 이동시켜 처리합니다. 이를 변수 호이스팅이라 하는데요, 초기값이 대입되지 않은 것처럼 선언되어 값이 할당되기 전까지는 `undefined`입니다.

```javascript
console.log('변수 호이스팅', myHoistingVar); // undefined. hoisting되어 상단에 선언되고 값을 지정하지 않아 undefined 입니다.
var myHoistingVar = 100;
console.log('변수 호이스팅', myHoistingVar); // 100
```

# 변수 유효 범위

`var`는 함수 레벨에서 유효합니다. 다음에서 #2는 함수 유효 범위에 #1이 있으므로 `val` 변수를 새롭게 생성하지 않고 #1을 사용합니다.

따라서 함수내에서는 이름이 중복되지 않게 주의해야 하는데요, 이러한 복잡함을 없애고자 ECMAScript6 부터 [let](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-const)과 [const](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-const)를 제공합니다.

```javascript
var val = 1;
function myFunc() {
    var val= 10; // #1.
    {
        var val = 20; // #2. 새롭게 생성되지 않고 함수 유효 범위의 val을 사용합니다.
    }

    return val; 
}
console.log('변수 유효 범위', myFunc() === 20); // true
console.log('변수 유효 범위', val); // 1
```

# let과 const(ECMAScript6)

`var`의 유연함과 유효 범위 문제는 대규모 프로젝트를 진행할 때 찾기 힘든 오류를 유발합니다. 이러한 문제를 보완하도록 EMAScript6 부터 `let`과 `const`가 보강되었습니다. 이제 `var`대신 `let`과 `const`를 쓰세요.

**let**

1. 변수를 선언합니다.
2. 함수 레벨이 아닌 블록 레벨에서 유효합니다.

    ```javascript
    function myFunc() {
        let val= 10; // #1.
        {
            let val = 20; // #2. // 블록에서만 유효합니다.
        }

        return val; // #1 을 사용합니다.
    }
    console.log('let', myFunc() === 10); // true
    ```

3. 중복 선언을 허용하지 않습니다.

    ```javascript
    let val = 10;
    let val = 20; // (X) 오류 발생
    ```

4. 변수 호이스팅시 오류가 발생합니다.

    ```javascript
    console.log(val); // (X) 오류 발생
    let val = 0;
    ```

**const**

1. `let`과 동일하나 변하지 않는 값을 선언합니다.
2. `let`과 달리 선언과 동시에 초기화해야 합니다.

    ```javascript
    // const val; // (X) 오류 발생. 선언과 동시에 초기화해야 합니다.
    const val = 0; 
    ```

3. 상수는 예외 발생 확률이 낮으므로 최대한 `const`로 사용하는게 좋습니다.
4. `const` 개체 자체는 수정할 수 없지만, 개체의 속성은 수정할 수 있습니다.

    ```javascript
    const obj1 = {x: 1, y: 2};
    const obj2 = {x: 10, y: 20};
    // obj1 = obj2; // (X) 오류 발생. const에 값을 할당할 수는 없습니다.
    obj1.x = obj2.x;
    obj1.y = obj2.y;
    console.log('const 개체의 속성은 수정할 수 있습니다.', obj1.x === obj2.x, obj1.y === obj2.y);
    ```

5. 특별한 상수값에 이름을 부여하여 사용할 수 있으며, 관례적으로 대문자로 작성합니다.

    ```javascript
    const PI = 3.14;
    ```

# 타입과 리터럴

`number, boolean, string, null, undefined, bigint, symbol`의 기본 타입(*Primitive Type*)이 있으며, 개체와 배열을 사용할 수 있습니다.

1. 숫자형은 정수와 실수를 표현합니다.

    (-2<sup>53</sup> - 1) ~ (2<sup>53</sup> - 1)를 표현합니다.
    
    정수끼리 나눴을때 실수가 나올 수 있으며, 연산결과에 따라 다음 값을 가질 수 있습니다.
    
    |항목|내용|
    |--|--|
    |`infinity`|양의 무한대|
    |`-infinity`|음의 무한대|   
    |`NaN`|연산 불가|

2. `true`와 `false`로 참, 거짓을 표현합니다.

3. 큰따옴표, 작은 따옴표, 백틱(*ECMAScript6*)으로 문자열을 표현합니다. 특히 백틱은 **템플릿 리터럴**인데요, 문자열내에서 다른 변수를 사용하여 합성할 수 있으며, 여러줄의 문자열을 `\n`없이 직관적으로 표현할 수 있습니다.

    * 문자열은 [UTF-16](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)을 사용하며, 일반적으로 작은 따옴표를 사용합니다.

    * 문자열 내에 문자열 기호를 사용하고 싶은 경우에는 `'문자열 기호는 ""과 \'\' 입니다.'` 와 같이 `''`와 `""`쌍을 교차하여 사용하거나 `\`로 이스케이프 문자(*[https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String) 참고*)를 사용하면 됩니다. 

4. `null`은 값이 할당되지 않았다는 의미로 개발자가 의도적으로 설정한 값입니다.
5. `undefined`는 값이 할당되지 않아 자바스크립트에서 미리 초기화 한 값입니다. 다른 언어에서는 쓰레기값 이라고도 하죠. `var a = undefined;`와 같이 `undefined`를 사용하는건 권장되지 않습니다. `null`을 사용하세요.
6. `{}` 안에 속성명을 주어 개체를 선언합니다.
7. `[]` 안에 값을 나열하여 배열을 선언합니다. 이때 타입이 서로 다를 수도 있습니다.
8. `//`사이에 정규표현식을 사용할 수 있습니다.
9. 래퍼 개체를 이용하여 기본 타입의 속성이나 메서드를 호출할 수 있습니다.

```javascript
const num1 = 10.5; // #1. 실수
let num2 = 10; // #1. 정수
num2 = 0x0a; // 16진수
num2 = 0o10; // 8진수
num2 = 0b101; // 2진수
num2 = 1e3; // 1과 3개의 0
console.log('1e3', 1e3 === 1000);
console.log('타입과 리터럴 테스트 : 3 / 2', 3 / 2); // #1. 1.5. 정수끼리 나눴을때 실수가 나올 수 있습니다.

const b1 = true; // #2. bool
const b2 = false;

const str1 = "Hello"; // #3. 문자열
const str2 = 'Kim';
const str3 = `Hello ${str2}`; // ${variable}을 이용하여 변수값과 합성하여 출력 
console.log('타입과 리터럴 테스트 : 백틱 합성', str3); //. Hello Kim

console.log('이스케이프 문자를 이용한 개행 : ', '첫번째 줄\n두번째줄');
console.log('백틱 문자열에서의 개행 : ', `첫번째 줄
두번째줄`);
console.log('문자열 기호는 ""과 \'\'입니다.');

const a = null; // #4. 값이 없음
let b; // #5. undefined. 값이 할당되지 않아 자바스크립트에서 초기화 한 값

const obj = {name: 'Lee', number: '123-4567'}; // #6. 개체
console.log('타입과 리터럴 테스트 : 개체 표시', obj); // Object. 트리를 확장하여 내부 속성값을 확인할 수 있음

const arr = [1, 'Kim', 3]; // #7. 배열
console.log('타입과 리터럴 테스트 : 배열 표시', arr); // Array(3). 트리를 확장하여 내부 요소를 확인할 수 있음

const data = 'a,b,c~d';
const result = data.split(/,|~/); // #8. 정규 표현식을 이용하여 구분자 , ~ 로 분리
for (let i = 0; i < result.length; ++i) {
    console.log('타입과 리터럴 테스트 : 배열 요소 표시', result[i]); // a b c d
}

const wrapper = 'Lee'; // #9. 래퍼 개체
console.log('기본 타입에 .을 찍고 wrapper 개체의 속성이나 메서드를 호출할 수 있습니다.', wrapper.length === 3);
```

# BigInt(ECMAScript11)

(-2<sup>53</sup> - 1) ~ (2<sup>53</sup> - 1)범위보다 큰 정수를 저장합니다. 리터럴 끝에 `n`을 붙이면 됩니다.

```javascript
const bigInt = 1234567890123456789012345678901234567890n;
bigInt += 1n;

console.log('bigInt : ', bigInt);
```

# 연산자

산술 연산자중 `+`는 숫자를 더할 뿐만 아니라 문자열도 더할 수 있습니다.

```javascript
console.log("'1' + '2'", '1' + '2' === '12'); // true
```

또한 `+` 부호 연산자는 특별히 문자열을 숫자로 바꿀 수 있습니다.

```javascript
function add(a, b) {
    return a + b;
}

console.log('add(1, 2) 는 3', add(1, 2)); // 3
console.log("add('1', 2) 는 '12'", add('1', 2)); // '12'. 문자열을 더하다 보니 2도 문자열로 만들고 더함
console.log("add(+'1', 2) 는 3", add(+'1', 2)); // 3. '1'을 숫자로 바꾸고 더함
```

`==`비교는 값이 같은지 다른지를 검사할때 적극적으로 형변환 하기 때문에 의도치 않은 결과가 나올 수도 있습니다. 따라서 형변환 없이 비교하려면 `===`을 사용해야 합니다. 

```javascript
console.log("1 == '1' 는 true", 1 === '1'); // true
console.log("1 === '1' 는 false", 1 === '1'); // false
```

특히 `null` 검사시 뜻하지 않게 형변환되어 잘못 검사될 수 있으니 꼭 `===`을 사용하세요. 다음 예에서 `undefinedVal`은 `undefined` 값인데, `undefinedVal == null`로 검사하면 `true`가 되버립니다.

```javascript
let undefinedVal;
console.log('undefinedVal === undefined는 true', undefinedVal === undefined); // true
console.log('undefinedVal === null은 false', undefinedVal === null); // false
console.log('undefinedVal == null은 true가 되버립니다.', undefinedVal == null); // true. undefined인데, true 입니다.
```

또한, 연산 불가를 뜻하는 `NaN`과 무한대를 뜻하는 `Infinity`의 경우는 `===` 로 검사할 수 없으며, `isNaN()`과 `isFinity()`로 검사해야 합니다.

```javascript
let nan = NaN;
console.log('nan === NaN', (nan === NaN) === false); // NaN 이지만, === 으로 비교하면 false 입니다.
console.log('isNaN(nan)', isNaN(nan) === true); // isNaN()으로 검사해야 합니다.

let infinity = Infinity;
console.log('infinity === Infinity', (infinity === Infinity) === false); // Infinity 이지만, === 으로 비교하면 false 입니다.
console.log('isFinite(infinity) === false', isFinite(infinity) === false); // isFinite()로 검사해야 합니다.
```

|항목|내용|
|--|--|
|산술 연산자|`+, -, *, /, %`, `**`(*지수*)|
|할당 연산자|`=, +=, -=, *=, /=, %=`|
|증감 연산자|`++, --`<br/>* 전위형 : 값을 증감시킨뒤 리턴합니다.<br/>* 후위형 : 값을 증감시기키 전의 값을 리턴합니다.|
|부호 연산자|`+, -`|
|비교 연산자|`==, !=, <, >, <=, >=`|
|일치 연산자|`===, !==`|
|논리 연산자|`||, &&, !`<br/>* 논리 연산자는 단축 평가 됩니다. 예를 들어 `A || B` 시 `A`가 `true`이면 어짜피 논리 연산의 결과는 `true`이기 때문에 `B`는 실행되지 않습니다.|
|삼항 연산자|`조건식 ? 표현식1 : 표현식2`<br/>* 표현식1 : 조건식이 `true`인 경우 실행합니다.<br/>* 표현식2 : 조건식이 `false`인 경우 실행|
|쉼표 연산자|왼쪽부터 차례로 표현식을 실행하고, 마지막에 가장 오른쪽 표현식을 리턴합니다.|
|괄호|괄호안의 연산 우선순위를 최우선으로 높입니다.|
|`typeof`|피 연산자의 타입을 문자열(*`number, string, boolean, undefined, symbol, object, fuction`*)로 리턴합니다. `null`과 배열은 모두 `object`로 리턴됩니다.|
|`instanceof`|주어진 개체가 프로토타입 체인중 일치하는 타입이 있는지 검사합니다.|
|`in`|`'x' in obj`와 같이 사용시 `obj`개체안에 `x`속성이 있는지 검사합니다.|

# 수치 처리 - Math, toFixed()

`Math` 개체를 이용하면, `min(), max(), floor(), ceil(), round()`등의 기본적인 수치 처리와 `pow(), log(), sqrt(), sin()`등의 수학 함수를 사용할 수 있습니다.  

```javascript
console.log('Math.min()', Math.min(1, 2, 3) === 1); // 인자로 전달한 값중 최소값
console.log('Math.max()', Math.max(1, 2, 3) === 3); // 인자로 전달한 값중 최대값

console.log('Math.floor() - 내림', Math.floor(3.14) === 3);
console.log('Math.ceil() - 올림', Math.ceil(3.14) === 4);
console.log('Math.round() - 반올림', Math.round(3.14) === 3, Math.round(3.5) === 4);
console.log('Math.trunc() - 버림', Math.trunc(3.14) === 3);
console.log('소수점 4째자리 반올림', Math.round(3.141592 * 1000) / 1000 === 3.142);
```

실수 연산은 정밀도에 따라 미세하게 다른 값이 될 수 있습니다. 
예를 들어 `0.1 + 0.2`는 `0.3`이 아니라  `0.30000000000000004`가 됩니다. 

이런 경우 `toFixed()`를 이용하여 고정된 자리수 만큼만 사용할 수 있습니다. 단, `toFixed()`는 문자열로 리턴하므로, 이를 다시 숫자로 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98)해주어야 합니다.

```javascript
console.log('toFixed() - 소수점 정밀도 오차 보정', 0.1 + 0.2, (0.1 + 0.2).toFixed(4) === '0.3000'); 
```

# 형변환

자바스크립트는 값을 비교할때 적극적으로 형변환하기 때문에 뜻하지 않은 곳에서 오동작을 할 수 있습니다. 형변환은 타입에 기반한 **코딩 계약** 을 위반하기 때문에 형변환 하는건 언제나 좋지 않습니다. 최선을 다해서 형변환 하지 마세요.

|항목|Number|Boolean|string|
|--|--|--|--|
|`null`|`0`|`false`|`'null'`|
|`undefined`|`NaN`|`false`|`'undefined'`|
|문자열 `'0'`|`0`|`true`|`'0'`|
|숫자 `0`|`0`|`false`|`'0'`|
|`NaN`|`NaN`|`false`|`'NaN'`|

명시적으로 형변환 하고 싶은 경우에는 `Number()`, `String()`, `toString()`, `parseInt()`, `parseFloat()`등을 사용합니다.

```javascript
console.log('null 형변환', Number(null) === 0, (!!null) === false, String(null) === 'null');
console.log('undefined 형변환', isNaN(Number(undefined)), (!!undefined) === false, String(undefined) === 'undefined');
console.log("문자열 '0' 형변환", Number('0') === 0, (!!'0') === true, String('0') === '0');
console.log("숫자 0 형변환", Number(0) === 0, (!!0) === false, String(0) === '0');
console.log("NaN 형변환", isNaN(Number(NaN)), (!!NaN) === false, String(NaN) === 'NaN');

console.log("Number('1') === 1", Number('1') === 1); // true. Number(), String()등을 이용하여 명시적으로 형변환 할 수 있습니다. 

const val = 10;
console.log('val === 10', val === 10); // true
console.log("val.toString() === '10'", val.toString() === '10', val.toString(16) === 'a'); // true. 문자열로 바꿉니다.
   
console.log("parseInt('1', 10) === 1", parseInt('1', 10) === 1); // true. 정수로 바꿉니다.
console.log("parseFloat('1.5') === 1.5", parseFloat('1.5') === 1.5); // true. 실수로 바꿉니다.
```

# 제어문

조건문, 반복문을 통해 제어의 흐름을 바꿉니다.

```javascript
if (조건식) { // 조건식이 참인 경우 표현식 실행
    표현식;
} else if (조건식) { // 생략 가능
    표현식;
} else { // 생략 가능
    표현식;
}

switch (표현식) {
    case Label1: // 표현식 === Label1이 일치할때 실행
    break;
    case Label2: // 표현식 === Label2가 일치할때 실행
    break;
    default: // 일치하는 항목이 없을 때 실행
    break;
}

for (초기식; 조건식; 증감식) { // 조건식이 참인 경우 표현식 실행후 증감식 실행 반복
    표현식
}

while(조건식) { // 조건식이 참인 경우 표현식 실행 반복
    표현식;
}

do { // 표현식을 먼저 실행 후 조건식이 참인 경우 표현식 실행 반복
    표현식;
} while (조건식);

break; // switch, for, while, do-while에서 코드 블럭 탈출 Label을 지정한 경우 해당 Label을 탈출

MY_Label for () {
    for () {
        break MY_Label; // 바깥 for문 탈출
    }
}

continue; // 현지점에서 진행을 중단하고, 다시 조건식 부터 재실행
```

# nullish(ECMAScript11)

주어진 값의 유효성을 확인할때 `if()`등을 이용하여 조건 검사를 합니다.

예를 들어 어떤 개체의 `color`값이 유효하지 않다면, `parant`의 `color`값을 이용한다고 해봅시다. 

대략, 다음과 같이 작성할 수 있는데요,

```javascript

function getColor1(obj) {
    if (obj.color !== null && obj.color !== undefined) {
        return obj.color;
    }
    else {
        return obj.parent.color;
    }
}
```

혹은 다음과 같이 삼중 연산자를 이용할 수도 있습니다.

```javascript
function getColor2(obj) {
    return (obj.color !== null && obj.color !== undefined) ? obj.color : obj.parent.color;
}
```

`null`과 `undefined`가 `boolean`으로 형변환하면 `false`이니 다음과 같이 간소화시킬 수도 있는데요,

```javascript
function getColor3(obj) {
    return obj.color || obj.parent.color; // (X) 오동작. obj.color가 true로 형변환 되면, obj.color를 사용하고, 그렇지 않으면, obj.parent.color를 사용합니다. obj.color === 0 이면, obj.parent.color를 사용합니다.
}
```

하지만 `getColor3()`은 `obj.color === 0`인 경우 검은색 그대로 사용해야 하는데, `false`로 형변환되어 오동작을 하게 됩니다. 

```javascript
const parent = {
    color: 0xFF0000,
}
const nullObj = {
    parent: parent,
    color: null // null 입니다. parent 색상을 사용해야 합니다.
};
const blackObj = {
    parent: parent,
    color: 0x000000 // 색상값이 있습니다. 자신의 색상을 이용해야 합니다.
};

console.log('parent의 color를 사용합니다.', getColor1(nullObj) === 0xFF0000, getColor2(nullObj) === 0xFF0000, getColor3(nullObj) === 0xFF0000);
console.log('자기 자신의 color를 사용합니다.', 
    getColor1(blackObj) === 0x000000,
    getColor2(blackObj) === 0x000000, 
    getColor3(blackObj) === 0xFF0000 // getColor3()가 parent.color를 사용합니다.
); 
```

이러한 경우 `??` 연산자를 사용할 수 있습니다.

`a ?? b`는 `a`가 `null` 도 아니고, `undefined`도 아니면, `a`를 사용하고, 그렇지 않으면, `b`를 사용합니다.

```javascript
function getColor4(obj) {
    return obj.color ?? obj.parent.color; 
}  
console.log('자기 자신의 color를 사용합니다.', 
    getColor1(blackObj) === 0x000000, 
    getColor2(blackObj) === 0x000000, 
    getColor4(blackObj) === 0x000000
); 
```

# 옵셔널 체이닝(ECMAScript11)

주어진 개체가 유효한 경우 특정 속성을 사용하거나 메서드를 호출하고 싶은 경우가 있습니다. 이런 경우에도 `if()`를 이용하여 조건을 검사하는데요, depth가 많아질수록 코드가 좀 지저분해 집니다.

```javascript

// 어떤 개체의 user 속성의 name속성을 리턴합니다.
function getName(obj) {
    if (obj) { // obj가 유효하고,
        if (obj.user) { // user 속성이 유효하면
            return obj.user.name; // name 속성을 리턴합니다.
        }
        return undefined;
    }
    return undefined;
} 

console.log('user 속성에서 이름을 구합니다.', getName({user: {name: 'Lee'}}) === 'Lee');
console.log('person 속성은 지원하지 않습니다.', getName({person: 'lee'}) === undefined);
console.log('null로부터 이름을 구합니다.', getName(null) === undefined);
```

이런 경우 [옵셔널 체이닝](??)을 이용하면 코드를 좀더 간결하게 작성할 수 있습니다. `?.`는 평가 대상이 `null` 도 아니고 `undefined`도 아니면 다음 속성을 평가하고, 그렇지 않으면 `undefined`를 반환합니다.

```javascript
function getName(obj) {
    return obj?.user?.name;
} 

console.log('user 속성에서 이름을 구합니다.', getName({user: {name: 'Lee'}}) === 'Lee');
console.log('person 속성은 지원하지 않습니다.', getName({person: 'lee'}) === undefined);
console.log('null로부터 이름을 구합니다.', getName(null) === undefined);
```

또한, `?.()`이나 `?.[]`와 함께 사용하여 주어진 `method()`나 배열이 있을때에만 사용 할 수 있습니다.

```javascript
obj.method?.(); // obj에 method가 선언되어 있으면 호출합니다.
obj.users?.[0]; // obj에 users 배열이 있는 경우 0번 요소에 접근합니다.
```

# 함수

함수는 #1의 함수 선언 방식과, #2의 함수 표현식 방식이 있습니다. 함수 표현식일 때는 관례적으로 함수명을 생략해서 사용합니다.(*함수명도 변수처럼 관습적으로 카멜 표기법을 사용합니다.*)

어느 방식으로 사용하던 함수 호출 방식은 동일합니다. 가독성 측면에서는 #1이 좋지만, #2는 [함수 호이스팅](https://tango1202.github.io/javascript/javascript-basic/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)이 되지 않고, [타입스크립트](https://tango1202.github.io/categories/typescript/)와 함께 사용할때 리턴값에 
함수 [인터페이스](https://tango1202.github.io/typescript/typescript-basic/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)를 지정할 수 있는 장점이 있어, 저는 #2를 선호합니다.

#3과 같이 변수에 대입하여 호출할 수도 있습니다. 

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

`function add(a, b) {return a + b;}(1, 2);`와 같이 함수 선언후 `(arg...)`을 붙이면, 선언과 동시에 호출하는 구문이 됩니다. 이때 표현식으로 파싱되지 않을 수도 있어 함수 선언부를 괄호로 감쌉니다.

```javascript
console.log('즉시 실행 함수', 
    ( // 함수 선언부를 괄호로 감쌉니다.
        function(a, b) {
            return a + b;
        }
    )(1, 2)
); // 3
```

즉시 실행 함수를 활용하면 유효 범위를 한정하고 실행할 수 있어 전역 개체를 최소화 할 뿐만 아니라, 이름 충돌을 최소화할 수 있습니다.

```javascript
(function() {
    const myApp = {};
    console.log('전역 개체 최소화', myApp); // myApp은 즉시 실행되고 소멸됩니다.
})();
```

# 함수 호이스팅

변수 호이스팅처럼 함수도 호이스팅됩니다. 따라서 함수 선언 전에 사용할 수 있습니다.

```javascript
console.log("함수 호이스팅", myHoisting(1, 2)); // 3. 함수 선언 전에 사용할 수 있습니다.

function myHoisting(a, b) {
    return a + b;
}
```

함수 표현식은 변수 호이스팅을 합니다. 따라서, 호이스팅되었어도 `undefined`이기 때문에 사용할 수 없습니다.

```javascript
console.log("함수 표현식 호이스팅", myHoisting(1, 2)); // (X) 오류 발생. 함수 표현식은 변수 호이스팅을 합니다. 따라서 undefined 입니다.

const myHoisting = function(a, b) {
    return a + b;
}
```

# 문자열로부터 함수 생성

함수 선언을 외부에서 문자열로 전달받아 함수로 생성할 수 있습니다.

`new Function(param1, param2, ..., body)`

```javascript
const myAdd = new Function('a', 'b','return a + b'); // 임의의 문자열로 함수를 생성합니다.
console.log('문자열로부터 함수 생성', myAdd(1, 2)); // 3
```

# 함수 속성

함수도 사실은 개체이기 때문에 속성을 가질 수 있습니다.

```javascript
function add(a, b) {
    return a + b;
}
add.data = 'Lee'; // 개체처럼 함수도 속성을 갖습니다.
console.log("함수 속성", add.data);
```

기본적으로 다음의 속성을 갖고 있습니다.(*`console.dir(함수 개체)`을 이용하면 상호 작용이 가능한 트리 형태로 함수 속성을 확인할 수 있습니다.*)

|항목|내용|
|--|--|
|`arguments`|전달된 인수를 나열할 수 있는 [유사 배열](https://tango1202.github.io/javascript/javascript-basic/#%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4)입니다. 함수 외부에서는 사용할 수 없습니다. 가변 인자인 경우에 활용할 수 있습니다. ECMAScript6부터는 [나머지 인자](https://tango1202.github.io/javascript/javascript-basic/#%EB%82%98%EB%A8%B8%EC%A7%80-%EC%9D%B8%EC%9E%90ecmascript6)를 사용합니다.|
|`caller`|자신을 호출한 함수를 가리킵니다. 브라우저에서 실행하면 `null`을 리턴합니다.|
|`length`|선언시 사용한 인자의 갯수입니다. (*arguments.length는 함수 호출시 전달한 인수의 갯수입니다.*)|
|`name`|함수의 이름입니다.|
|`__proto__`|모든 개체는 `[[Prototype]]`이라는 내부 개체가 있으며 기본적으로 `Object.prototype`과 동일합니다. `__proto__`는 이에 접근하기 위한 속성입니다. `[[Prototype]]` 개체는 상속을 구현하기 위해 사용되며, 다른 개체에 공유 속성을 제공합니다.|
|`prototype`|함수 개체만 있는 속성입니다.(*일반 개체에는 없습니다.*) 개체를 생성하는 [생성자 함수](https://tango1202.github.io/javascript/javascript-basic/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)로 사용될때 함수가 생성한 개체의 `__proto__` 개체를 가리킵니다.|

# 중첩 함수

함수내에 다른 함수를 중첩해서 선언할 수 있습니다. 

* 중첩 함수는 상위 함수의 변수에 접근 가능합니다.(*상위 함수는 중첩 함수의 변수에 접근할 수 없습니다.*)
* 외부에서는 중첩 함수에 접근할 수 없습니다.

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

클로저는 함수와 그 함수가 호출되었을때의 환경과의 조합입니다. 이 환경은 함수가 사용중이라면 소멸되지 않고 사용할 수 있습니다.

이 특징을 이용하면, 중첩 함수를 이용하여 특정 정보를 은닉하는 캡슐화를 할 수 있습니다.(*[클로저를 이용한 캡슐화](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%ED%81%B4%EB%A1%9C%EC%A0%80%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%BA%A1%EC%8A%90%ED%99%94) 참고*) 또한 [모듈화](https://tango1202.github.io/javascript/javascript-coding-pattern/#%EC%BD%94%EB%94%A9-%ED%8C%A8%ED%84%B4---%EC%A6%89%EC%8B%9C-%EC%8B%A4%ED%96%89-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%AA%A8%EB%93%88%ED%99%94)에서도 사용하는 개념이니 만큼 잘 알아 두셔야 합니다.

다음 예에서 함수의 지역 변수인 `count`를 은닉하고, 중첩 함수를 이용하여 `count`에 접근합니다.

1. 외부 함수인 #1을 선언하고 즉시 실행하여 중첩된 함수인 #3함수를 리턴합니다.
2. `count`는 #1 함수의 지역 변수 입니다. 외부에서 접근할 수 없습니다.
3. 중첩된 함수인 #3을 리턴합니다.
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

화살표 함수는 함수 선언을 간략하게 표현합니다.

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

화살표 함수의 `this`는 일반 함수의 `this`와 다릅니다. 자세한 내용은 [함수 호출 방식에 따른 this 변경](https://tango1202.github.io/javascript/javascript-prototype/#%ED%95%A8%EC%88%98-%ED%98%B8%EC%B6%9C-%EB%B0%A9%EC%8B%9D%EC%97%90-%EB%94%B0%EB%A5%B8-this-%EB%B3%80%EA%B2%BD)을 참고하시기 바랍니다.


# 기본값 인자(ECMAScript6)

함수 인자에 기본값을 주면, 인수가 전달되지 않았을때 사용합니다.

```javascript
function add(a = 1, b = 2) {
    return a + b;
}
console.log('인수를 전달하면, 전달한 값을 사용합니다', add(10, 20) === 30);
console.log('인수가 없으면, 기본값을 사용합니다', add() === 3);
```

# 나머지 인자(ECMAScript6)

함수 인자에 `...`을 붙여 선언하면, 전달된 인수들을 배열로 전달받습니다. 비슷한 용도로 제공되는 `arguments`는 [유사 배열](https://tango1202.github.io/javascript/javascript-basic/#%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4)이며, 화살표 함수에서는 사용할 수 없다고 하네요. 또한 ECMAScript3부터 deprecate되었다고 합니다. 이제 나머지 인자를 사용하세요.

```javascript
function f(...params) {
    console.log('...params는 배열입니다', Array.isArray(params));
    params.forEach((param) => console.log('param은 배열 요소입니다', param));
}
f(1, 2, 3);
```

일반적인 인자와 나머지 인자를 섞어 쓰면, 일반적인 인자에 인수를 할당한뒤 남은 것만 나머지 인자에 할당합니다.

다음은 전달된 인수들의 합을 구하는 예입니다. 첫번째 인수를 뺀 나머지들을 다시 재귀적으로 호출하여 값을 누적합니다. 나머지 인자의 갯수가 1이면, 재귀 호출을 하지 않고 그냥 더합니다. `sum()`함수를 재귀 호출할때 `sum(...rest)`와 같이 호출했는데요, 이는 [Spread](https://tango1202.github.io/javascript/javascript-basic/#spreadecmascript6)로서 배열을 다시 분리하는 역할을 합니다. 즉, `sum(rest[0], rest[1], ... , rest[N - 1])`처럼 호출합니다. 


```javascript
function sum(first, ...rest) {
    return first + ((rest.length === 1) ? rest[0] : sum(...rest)); // ...rest는 배열을 다시 분리하여 나열합니다. spread 참고
}

console.log('나머지 인자를 재귀적으로 호출합니다', sum(1, 2, 3) === 1 + 2 + 3);
```

# 이벤트 핸들러

HTML DOM 엘리먼트의 이벤트가 발생했을때, [이벤트 핸들러](??)를 통해 이벤트를 전달받을 수 있습니다.

다음은 HTML DOM 엘리먼트의 이벤트를 전달받는 예입니다.

1. #1 : 문서 로딩이 끝나면 이벤트를 연결할 수 있도록 `DOMContentLoaded`에 이벤트 핸들러를 연결하여 실행합니다.

2. #2 : `querySelector()`를 이용하여 문서에서 `button`과 `div` 엘리먼트를 찾습니다.

3. #3 : `button`과 `div` 클릭시 `onButtonClick()`과 `onDivClick()`이 호출되도록 이벤트 핸들러를 연결합니다.

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

상기 예를 실행하면, `div`클릭시에는 `onDivClick()`이 잘 호출되지만, `button`클릭시에는 `onButtonClick()`과 `onDivClick()`이 같이 호출됩니다. 이는 `onButtonClick()`후 이벤트가 상위 개체인 `div`로 전파되기 때문입니다. 이를 ***이벤트 버블링***이라고 하는데요, 다음과 같이 `stopPropagation()`을 호출하면 ***이벤트 버블링***을 막기 때문에 `onButtonClick`만 호출됩니다.

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


# 개체

개체는 속성의 집합이며, 속성명(*키*)으로 속성값에 접근할 수 있습니다. 자바스크립트의 모든 것들은 개체입니다. 심지어 함수도 개체입니다. 개체는 대입시 얕은 복사를 합니다.

1. 함수를 속성으로 사용할 수 있으며, 개체의 속성으로 사용되는 함수를 특별히 메서드라고 합니다.
2. `this`를 사용하여 개체 자신을 나타낼 수 있습니다.
3. `alert()`를 사용하면 `object Object`로 출력되고, `console.log()`를 사용하면 트리를 확장하여 개체의 속성명과 속성값을 확인할 수 있습니다.
4. `new Object()`를 이용하여 생성한뒤 뒤늦게 개체의 속성들을 설정하는 방식도 있으나 코딩 계약에 좋지 않아 잘 사용하지 않습니다.

```javascript
const empty = {}; // 빈 개체

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
```

# 개체 속성 접근

속성명은 변수명에서 사용할 수 없는 숫자나 `-`, 공백 문자를 사용할 수 있으며, 마침표나 `[]`로 접근할 수 있습니다. 

심지어, `for`, `let`과 같은 예약어를 사용할 수 있으며, `__proto__`만 사용할 수 없습니다.

```javascript
const user = {
    name: 'Lee',
    'addr': 'Seoul', // 속성명을 문자열로 선언할 수 있습니다.
    1 : '1',
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

동적으로 속성을 추가/삭제 할 수 있습니다.

```javascript
const user = {};
user.name = 'Lee'; // name 속성이 없으면 추가합니다.
console.log("속성 추가 user.name === 'Lee'", user.name === 'Lee'); // name 속성이 추가되었습니다.

delete user.name;
console.log("속성 삭제 후 user.name === undefined", user.name === undefined); // name 속성이 삭제되었습니다.
```

# 속성 나열 : for-in

`for-in`으로 속성명을 나열할 수 있고, 이를 개체의 키로 사용하면 속성값을 확인할 수 있습니다.

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

배열에도 사용할 수 있지만 배열 요소외에 다른 속성이 있다면 함께 나열되기 때문에, 배열에서는 잘 사용하지 않습니다.

```javascript
const arr = ['one', 'two', 'three'];
arr.extraData = "추가 정보 입니다."; // 속성을 추가합니다.
for (let prop in arr) { // 배열 요소와 추가 속성이 나열됩니다.
    console.log('속성명 :' + prop , '속성값 :' + arr[prop]);
}
```

# 개체 복제/동결

개체는 기본적으로 얕은 복사를 하며, 동일 개체를 참조합니다. 따라서, `user2 = user1`은 사실 동일 개체를 참조하게 되며, `user2`를 수정하면, `user1`도 수정됩니다.

```javascript
const user1 = {name: 'Lee'};
const user2 = user1;
user2.name = 'Kim';
console.log("동일 개체를 참조합니다 user1.name === 'Kim'", user1.name === 'Kim'); // true. user2를 수정했지만, user1도 수정되었습니다.
```

이러한 경우 복제를 해야하며 `Object.assign()`(*ECMAScript6*)을 이용하면 됩니다.(*또한 [Spread](https://tango1202.github.io/javascript/javascript-basic/#spreadecmascript6)를 이용하여 개체 속성을 복제할 수도 있습니다.*) 하지만 하위 개체는 여전히 얕은 복사를 하니 주의해야 합니다. 다음 예에서 `name`은 복제되었지만, `addr`은 하위 개체에 있기 때문에 복제되지 않고 같은 개체를 참조합니다.

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

하위 개체까지 복제하고 싶으면, 하위 개체도 일일이 `Object.assign()`을 이용하거나, [Spread](https://tango1202.github.io/javascript/javascript-basic/#spreadecmascript6)를 이용하여 일일이 복제해야 합니다. 좀 번거롭죠. 대안으로 속도 성능은 떨어지지만, [JSON](https://tango1202.github.io/javascript/javascript-basic/#json)을 이용하여 다음처럼 간편하게 복제할 수 있습니다.

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

[생성자 함수](https://tango1202.github.io/javascript/javascript-basic/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)를 이용하여 개체를 생성하면, 내부적으로 [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)를 사용하는데요, `assign()`이나 [Spread](https://tango1202.github.io/javascript/javascript-basic/#spreadecmascript6)로 복제하면 [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)가 `Object`로 초기화 되어 버립니다. 만약 [`__proto__`](https://tango1202.github.io/javascript/javascript-prototype/#prototype%EA%B3%BC-__proto__%EC%99%80-prototype%EA%B3%BC-constructor)도 동일하게 하려면, `Object.create()`를 함께 이용해야 합니다.(*[prototype](https://tango1202.github.io/javascript/javascript-prototype/) 참고*)

```javascript
function User(name) { // 생성자 함수
    this.name = name;
}
const user1 = new User('Lee'); 
const user2 = {...user1}; 
const user3 = Object.assign(Object.create(User.prototype), user1); // 동일한 prototype을 사용할 수 있도록 Object.create()를 이용합니다.

console.log('생성자 함수 User로 생성했습니다.', user1 instanceof User);
console.log('spread로 개체 속성을 복제한 개체입니다.', user2 instanceof Object); // Object로 초기화 되어 있습니다.
console.log('create()와 assign()으로 복제했습니다.', user3 instanceof User);
```

`Object.freeze()`로 개체를 수정할 수 없게끔 동결시킬 수 있습니다. 하지만, 하위 개체에는 적용되지 않습니다.

