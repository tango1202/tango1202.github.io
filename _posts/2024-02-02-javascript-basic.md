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

