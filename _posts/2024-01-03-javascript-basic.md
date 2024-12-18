---
layout: single
title: "#3. [Javascript] 기본 문법(변수, 타입, 리터럴, 연산자, 형변환, 제어문, nullish, 옵셔널 체이닝)"
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

자바스크립트는 동적 타입 언어입니다. 따라서 변수의 타입이 동적으로 결정되는데요, 숫자형 변수로 사용하다가 문자열형 변수로 바꿔서 사용할 수도 있습니다.

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

5. 아예 선언하지 않고 사용할 수 있습니다. 이 경우 전역 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 변수로 선언됩니다.

    ```javascript
    z = 4; // #5. 선언하지 않고 사용. 전역 개체의 변수로 선언됩니다.
    ```

엄청 유연합니다만, 코딩 계약이 엉망진창이 되버려 대규모 프로젝트에서는 오류를 찾아내기 힘들어 집니다. 따라서 이젠 사용하지 마시고요, [let](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-const)과 [const](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-const)나 [타입 스크립트](https://tango1202.github.io/categories/typescript/)를 사용하시길 추천드립니다.

# 변수 호이스팅

선언하지 않은 변수를 사용했을때, 변수 선언이 유효 범위의 시작점에 있는 것처럼 이동시켜 처리합니다. 이를 [변수 호이스팅](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)이라 하는데요, 초기값이 대입되지 않은 것처럼 선언되기 때문에 값이 할당되기 전까지는 `undefined`입니다.

```javascript
console.log('변수 호이스팅', myHoistingVar); // undefined. hoisting되어 상단에 선언되고 값을 지정하지 않아 undefined 입니다.
var myHoistingVar = 100; // 값이 할당되면, 할당된 값이 사용됩니다.
console.log('변수 호이스팅', myHoistingVar); // 100
```

# 변수 유효 범위

`var`는 함수 레벨에서 유효합니다. 다음에서 #2는 함수 유효 범위에 #1이 있으므로 `val` 변수를 새롭게 생성하지 않고 #1을 사용합니다. 

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

#2에서 생성했는데, #1의 것이 사용되다니 혼란스럽습니다. 따라서 함수내에서는 이름이 중복되지 않게 주의해야 하는데요, 아무리 주의해도 휴먼 에러를 피하기 힘듭니다. 코드의 규모가 커지면 커질수록 상당히 복잡하고 골치 아픈 문제가 됩니다. 

이러한 복잡함을 없애고자 ECMAScript6 부터 블록 레벨에서 유효한 [let](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-const)과 [const](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-const)를 제공합니다.

# let과 const(ECMAScript6)

`var`의 유연함과 [유효 범위](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84) 문제는 대규모 프로젝트를 진행할 때 찾기 힘든 오류가 됩니다. 이러한 문제를 보완하도록 EMAScript6 부터 [let](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6)과 [const](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6)가 보강되었습니다. 이제 `var`대신 [let](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6)과 [const](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6)를 쓰세요.

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

4. [변수 호이스팅](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)시 오류가 발생합니다.

    ```javascript
    console.log(val); // (X) 오류 발생
    let val = 0;
    ```

**const**

1. [let](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6)과 동일하나 변하지 않는 값을 선언합니다.
2. [let](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6)과 달리 선언과 동시에 초기화해야 합니다.

    ```javascript
    // const val; // (X) 오류 발생. 선언과 동시에 초기화해야 합니다.
    const val = 0; 
    ```

3. 상수는 예외 발생 확률이 낮으므로 최대한 [const](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6)로 사용하는게 좋습니다.
4. [const](https://tango1202.github.io/javascript/javascript-basic/#let%EA%B3%BC-constecmascript6) [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) 자체는 수정할 수 없지만, ***[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 속성은 수정할 수 있습니다.*** 즉, 하위 내용은 수정할 수 있어서 진정한 불변(*immutable*)은 아닙니다. 이러한 특징 때문에 리액트 [개체/배열 State](https://tango1202.github.io/react/react-basic/#%EA%B0%9C%EC%B2%B4%EB%B0%B0%EC%97%B4-state) 관리에 주의가 필요합니다. 

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

자바스크립트는 `number, boolean, string, null, undefined, bigint, symbol`의 기본 타입(*Primitive Type*)이 있으며, [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)와 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 사용할 수 있습니다.

1. `number` : 숫자형은 정수와 실수를 표현합니다.

    (-2<sup>53</sup> - 1) ~ (2<sup>53</sup> - 1)를 표현합니다.
    
    정수끼리 나눴을때 실수가 나올 수 있으며, 연산결과에 따라 다음 값을 가질 수 있습니다.
    
    |항목|내용|
    |--|--|
    |`infinity`|양의 무한대|
    |`-infinity`|음의 무한대|   
    |`NaN`|연산 불가|

2. `boolean` : `true`와 `false`로 참, 거짓을 표현합니다.

3. `string` : 큰따옴표, 작은 따옴표, 백틱(*ECMAScript6*)으로 문자열을 표현합니다. 특히 백틱은 **템플릿 리터럴**인데요, 문자열내에서 다른 변수를 사용하여 합성할 수 있으며, 여러줄의 문자열을 `\n`없이 직관적으로 표현할 수 있습니다.

    * 문자열은 [UTF-16](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)을 사용하며, 일반적으로 작은 따옴표를 사용합니다.

    * 문자열 내에 문자열 기호를 사용하고 싶은 경우에는 `'문자열 기호는 ""과 \'\' 입니다.'` 와 같이 `''`와 `""`쌍을 교차하여 사용하거나 `\`로 이스케이프 문자(*[https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String) 참고*)를 사용하면 됩니다. 

4. `null` : 값이 할당되지 않았다는 의미로 개발자가 의도적으로 설정한 값입니다.
5. `undefined` : 값이 할당되지 않아 자바스크립트에서 미리 초기화 한 값입니다. [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 아예 선언되지 않았다라는 의미로 사용합니다.
 `var a = undefined;`와 같이 `undefined`를 사용하는건 권장되지 않습니다. 왜냐면 이미 `a`를 선언했기 때문이죠. 이런 경우는 `null`을 사용하세요.
6. [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4) : `{}` 안에 속성명을 주어 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 선언합니다.
7. [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) : `[]` 안에 값을 나열하여 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 선언합니다. 이때 타입이 서로 다를 수도 있습니다.
8. #8 : `//`사이에 정규표현식을 사용할 수 있습니다.
9. #9 : 래퍼 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 이용하여 [기본 타입](https://tango1202.github.io/javascript/javascript-basic/#%ED%83%80%EC%9E%85%EA%B3%BC-%EB%A6%AC%ED%84%B0%EB%9F%B4)의 속성이나 메서드를 호출할 수 있습니다.

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

[number](https://tango1202.github.io/javascript/javascript-basic/#%ED%83%80%EC%9E%85%EA%B3%BC-%EB%A6%AC%ED%84%B0%EB%9F%B4)의 범위인 (-2<sup>53</sup> - 1) ~ (2<sup>53</sup> - 1)범위보다 큰 정수를 저장합니다. 리터럴 끝에 `n`을 붙이면 됩니다.

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

또한 `+` 부호 연산자는 특별히 문자열을 숫자로 바꿀 수 있습니다. `+'1'`은 숫자 `1`입니다.

```javascript
function add(a, b) {
    return a + b;
}

console.log('add(1, 2) 는 3', add(1, 2)); // 3
console.log("add('1', 2) 는 '12'", add('1', 2)); // '12'. 문자열을 더하다 보니 2도 문자열로 만들고 더함
console.log("add(+'1', 2) 는 3", add(+'1', 2)); // 3. '1'을 숫자로 바꾸고 더함
```

`==`비교는 값이 같은지 다른지를 검사할때 적극적으로 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98) 하기 때문에 의도치 않은 결과가 나올 수도 있습니다. 따라서 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98) 없이 비교하려면 `===`을 사용해야 합니다. 

```javascript
console.log("1 == '1' 는 적극적으로 형변환하여 true", 1 === '1'); // true
console.log("1 === '1' 는 false", 1 === '1'); // false
```

특히 `null` 검사시 뜻하지 않게 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98)되어 잘못 검사될 수 있으니 꼭 `===`을 사용하세요. 

다음 예에서 `undefinedVal`은 `undefined` 값인데, `undefinedVal == null`로 검사하면 `true`가 되버립니다.

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
|삼항 연산자|`조건식 ? 표현식1 : 표현식2`<br/>* 표현식1 : 조건식이 `true`인 경우 실행합니다.<br/>* 표현식2 : 조건식이 `false`인 경우 실행합니다.|
|쉼표 연산자|왼쪽부터 차례로 표현식을 실행하고, 마지막에 가장 오른쪽 표현식을 리턴합니다.|
|괄호|괄호안의 연산 우선순위를 최우선으로 높입니다.|
|`typeof`|피 연산자의 타입을 문자열(*`number, string, boolean, undefined, symbol, object, fuction`*)로 리턴합니다. `null`과 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)은 모두 `object`로 리턴됩니다.|
|`instanceof`|주어진 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 [프로토타입](https://tango1202.github.io/javascript/javascript-prototype/#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85) 체인중 일치하는 타입이 있는지 검사합니다.|
|`in`|`'x' in obj`와 같이 사용시 `obj`[개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)안에 `x`속성이 있는지 검사합니다.|

# 수치 처리 - Math, toFixed()

[Math](https://tango1202.github.io/javascript/javascript-basic/#%EC%88%98%EC%B9%98-%EC%B2%98%EB%A6%AC---math-tofixed)를 이용하면, `min(), max(), floor(), ceil(), round()`등의 기본적인 수치 처리와 `pow(), log(), sqrt(), sin()`등의 수학 함수를 사용할 수 있습니다.  

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
예를 들어 `0.1 + 0.2`는 `0.3`이 아니라  `0.30000000000000001`이 될 수 있습니다. 

이런 경우 `toFixed()`를 이용하여 고정된 자리수 만큼만 사용할 수 있습니다. 단, `toFixed()`는 문자열로 리턴하므로 문자열과 비교해야 합니다.

```javascript
console.log('toFixed() - 소수점 정밀도 오차 보정', 0.1 + 0.2, (0.1 + 0.2).toFixed(4) === '0.3000'); 
```

# 형변환

자바스크립트는 값을 비교할때 적극적으로 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98)하기 때문에 뜻하지 않은 곳에서 오동작을 할 수 있습니다. [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98)은 타입에 기반한 **코딩 계약** 을 위반하기 때문에 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98) 하는건 언제나 좋지 않습니다. 최선을 다해서 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98) 하지 마세요.

특히 `boolean`은 조건식에서 광범위하게 사용되기 때문에 변환 규칙을 잘 알아두셔야 합니다.(*저 같은 경우는 빈 문자열이 `false`로 변환되는걸 뒤늦게 알고 코드를 재점검하느라 고생한 적이 있습니다.*)

|항목|Number|Boolean|string|
|--|--|--|--|
|`null`|`0`|`false`|`'null'`|
|`undefined`|`NaN`|`false`|`'undefined'`|
|숫자 `0`|`0`|`false`|`'0'`|
|`NaN`|`NaN`|`false`|`'NaN'`|
|문자열 `'0'`|`0`|`true`|`'0'`|
|빈 문자열 `''`|`0`|`false`|`''`|
|빈 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) `[]`|`0`|`true`|`''`|

```javascript
console.log('null 형변환', Number(null) === 0, (!!null) === false, String(null) === 'null');
console.log('undefined 형변환', isNaN(Number(undefined)), (!!undefined) === false, String(undefined) === 'undefined');
console.log("숫자 0 형변환", Number(0) === 0, (!!0) === false, String(0) === '0');
console.log("NaN 형변환", isNaN(Number(NaN)), (!!NaN) === false, String(NaN) === 'NaN');
console.log("빈 문자열 형변환", Number('') === 0, (!!'') === false, String('') === '');
console.log("빈 배열 형변환", Number([]) === 0, (!![]) === true, String([]) === '');
```

명시적으로 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98) 하고 싶은 경우에는 `Number()`, `String()`, `toString()`, `parseInt()`, `parseFloat()`등을 사용합니다.

```javascript
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

# 삼항 연산자를 이용한 개체 할당 축약 표현

조건에 따라 값을 할당하는게 달라지면 다음과 같이 `if()`를 이용할 수 있습니다. 이때 #1과 같이 할당받을 변수를 미리 선언해야 합니다.

```javascript
let val = ''; // #1. 미리 선언합니다.
const a = 'a';
const b = 'b';

const condition = true;
if (condition) {
    val = a;
}
else {
    val = b;
}

console.log('조건이 참이므로 a 입니다.', val === a);
```

[삼항 연산자](https://tango1202.github.io/javascript/javascript-basic/#%EC%97%B0%EC%82%B0%EC%9E%90)를 이용하면, 할당받을 변수를 미리 선언할 필요없이 선언과 동시에 초기화 할 수 있습니다. 코드를 좀더 단순화 할 수 있죠.

```javascript
const a = 'a';
const b = 'b';

const condition = true;
const val = condition ? a : b; // 선언과 동시에 초기화할 수 있습니다.

console.log('조건이 참이므로 a 입니다.', val === a);
```

주어진 값이 유효할 때만 사용하고, 그렇지 않은 경우는 기본값을 사용하는 논리도 많이 사용되는데요, 이런 경우도 다음 코드의 #1, #2, #3, #4와 같이 삼항 연산자를 이용할 수 있습니다. 이때 #1, #2는 직접적으로 `null`인지만 검사하는데, #3과 #4는 `undefined`, 숫자 `0`, `NaN`, `빈 문자열`인 경우도 검사하니 주의하시거나 잘 활용하시고요(*[형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98) 참고*), 

개인적으론 #4(`a ? a : 'default';`)가 조건식이 단순하고 기본값이 뒤에 있는 형태여서 가독성이 좋아 선호하고 있습니다. 

```javascript
const a = null;

const val1 = a === null ? 'default' : a; // #1
console.log('a가 null 입니다.', val1 === 'default');

const val2 = a !== null ? a : 'default';  // #2
console.log('a가 null 입니다.', val2 === 'default');

const val3 = !a ? 'default' : a; // #3. null, undefined, 숫자 0, NaN, 빈 문자열도 검사합니다. 
console.log('a가 null 입니다.', val3 === 'default');

const val4 = a ? a : 'default'; // #4. null, undefined, 숫자 0, NaN, 빈 문자열도 검사합니다. 기본값이 뒤에 있어 가독성이 좋습니다. 
console.log('a가 null 입니다.', val4 === 'default');
```

# 논리 연산자를 이용한 개체 할당 축약 표현

기본값 설정을 [삼항 연산자](https://tango1202.github.io/javascript/javascript-basic/#%EC%97%B0%EC%82%B0%EC%9E%90)로 할 수도 있지만 [논리 연산자](https://tango1202.github.io/javascript/javascript-basic/#%EC%97%B0%EC%82%B0%EC%9E%90)를 이용하면 더 가독성 좋게 작성할 수 있습니다. 

자바스크립트에서는 아주 많은 부분에서 사용되니 꼭 이해하고 ***공식처럼 외워두는게 좋습니다***.

다음 코드의 #1에서 `a || 'default'` 는 `a`를 평가하여 `true`이면 더이상의 평가는 중단하고, `false`이면 `||` 다음을 평가하라는 의미입니다. 

즉, `a ? a : 'default'`와 결과가 같습니다. **주어진 값이 무효한 값이면 기본값을 사용**할 때 좋습니다.

한편 #2인 `a && 'default'`는 `a`를 평가하여 `true`이면 `&&` 다음을 평가하고, `false`이면 더이상의 평가는 중단하라는 의미입니다. 즉, `!a ? a : 'default'` 와 결과가 같습니다. 말이 좀 복잡한데요, 

```javascript
const a = null;

const val1 = a || 'default'; // #1 
console.log('a가 null 이면 다음식을 평가합니다.', val1 === 'default');

const val2 = a && 'default'; // #2 
console.log('a가 null 이면 다음식을 평가하지 않습니다.', val2 === a);
```

`a && 'default'`를 좀더 쉽게 풀어쓰면, **주어진 값이 유효할때에만 기본동작을 수행**하라라는 의미로 해석하셔도 됩니다. `a && doSomething();` 처럼요.  

```javascript
const a = 'defalut';
function doSomething() {
    return 'something';
}
console.log('a가 true인 경우만 다음식을 평가합니다.', (a && doSomething()) === 'something');
```

# nullish(ECMAScript11)

[개체 할당 축약 표현](??)에서 '`||`'을 사용하는 것이 간결하긴 하지만, 오동작을 할때가 있습니다.

예를 들어 어떤 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 `color`값이 유효하지 않다면, `parant`의 `color`값을 이용한다고 해봅시다. 

대략, `if()`로는 다음과 같이 작성할 수 있고,

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

[삼항 연산자](https://tango1202.github.io/javascript/javascript-basic/#%EC%97%B0%EC%82%B0%EC%9E%90)로는 다음과 같이 작성할 수 있고,

```javascript
function getColor2(obj) {
    return (obj.color !== null && obj.color !== undefined) ? obj.color : obj.parent.color;
}
```

`null`과 `undefined`가 `boolean`으로 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98)하면 `false`이니 다음과 같이 [논리 연산자](https://tango1202.github.io/javascript/javascript-basic/#%EC%97%B0%EC%82%B0%EC%9E%90)로 간소화시킬 수도 있는데요,

```javascript
function getColor3(obj) {
    return obj.color || obj.parent.color; // (X) 오동작. obj.color가 true로 형변환 되면, obj.color를 사용하고, 그렇지 않으면, obj.parent.color를 사용합니다. obj.color === 0 이면, obj.parent.color를 사용합니다.
}
```

하지만 `getColor3()`은 `obj.color === 0`인 경우 검은색 그대로 사용해야 하는데, `false`로 [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98)되어 오동작을 하게 됩니다. 

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

이러한 경우 [?? 연산자](https://tango1202.github.io/javascript/javascript-basic/#nullishecmascript11)를 사용할 수 있습니다.

`a ?? b`는 `a`의 `null`과 `undefined`여부만 검사합니다. 값이 `0`인지는 검사하지 않습니다. 따라서 `a`가 `null` 도 아니고, `undefined`도 아니면, `a`를 사용하고, 그렇지 않으면, `b`를 사용합니다. 

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

주어진 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)가 유효한 경우 특정 속성을 사용하거나 메서드를 호출하고 싶은 경우가 있습니다. 이런 경우에도 `if()`를 이용하여 조건을 검사하는데요, depth가 많아질수록 코드가 좀 지저분해 집니다.

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

이런 경우 [옵셔널 체이닝](https://tango1202.github.io/javascript/javascript-basic/#%EC%98%B5%EC%85%94%EB%84%90-%EC%B2%B4%EC%9D%B4%EB%8B%9Decmascript11)을 이용하면 코드를 좀더 간결하게 작성할 수 있습니다. `?.`는 평가 대상이 `null` 도 아니고 `undefined`도 아니면 다음 속성을 평가하고, 그렇지 않으면 `undefined`를 반환합니다.

```javascript
function getName(obj) {
    return obj?.user?.name;
} 

console.log('user 속성에서 이름을 구합니다.', getName({user: {name: 'Lee'}}) === 'Lee');
console.log('person 속성은 지원하지 않습니다.', getName({person: 'lee'}) === undefined);
console.log('null로부터 이름을 구합니다.', getName(null) === undefined);
```

또한, `?.()`이나 `?.[]`와 함께 사용하여 주어진 `method()`나 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)이 있을때에만 사용 할 수 있습니다.

```javascript
obj.method?.(); // obj에 method가 선언되어 있으면 호출합니다.
obj.users?.[0]; // obj에 users 배열이 있는 경우 0번 요소에 접근합니다.
```

