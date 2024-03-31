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
