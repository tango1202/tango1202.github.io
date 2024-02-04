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

# 변수

javascript는 동적 타입 언어 입니다. 

1. `var`로 정의할 수 있습니다.
2. 정의와 동시에 할당 할 수 있습니다.
3. 정의한 것을 다시 재정의 할 수 있습니다.
4. 동적 타입 언어 이기 때문에 정의한 타입과 다른 타입을 바꿔서 할당할 수 있습니다.
5. 아예 정의하지 않고 사용할 수 있습니다.

유연한 것 같지만, 코딩 계약이 엉망진창이 되버려 대규모 프로젝트에서는 오류를 찾아내기 힘들어 집니다. 따라서 [TypeScript](??)를 사용하시길 추천드립니다.

```javascript
var x; // #1. 정의
x = 1; // 정의 후 할당
var y = 2; // #2. 정의와 동시에 할당
var x = 3; // #3. 정의된 것을 다시 재정의
x = 'Hello'; // #4. 타입을 바꿔서 할당
z = 4; // #5. 정의하지 않고 사용

alert(x + (y + z)); // Hello6 (문자열과 2 + 4의 결과가 더해짐)
```

`var`는 함수 레벨에서 유효합니다. 다음에서 #2는 함수 유효 범위에 #1이 있으므로 val 변수를 새롭게 생성하지 않고 #1을 사용합니다.(*변수 정의가 유효 범위의 시작점에 있는 것처럼 이동시켜 처리하며, 이를 변수 호이스팅이라 합니다.*) 따라서 함수내에서는 이름이 중복되지 않게 주의해야 합니다. 이러한 복잡함을 없애고자 [ECMAScript6](??)에서부터 [let](??)과 [const](??)를 제공합니다.

```javascript
var val = 1;
function myFunction() {
    var val= 10; // #1.
    {
        var val = 20; // #2. 새롭게 생성되지 않고 함수 유효 범위의 val을 사용합니다.
    }

    return val; 
}
alert(myFunction() == 20); // true
alert(val); // 1
```

# 타입과 리터럴

`number, boolean, string, null, undefined, symbol`의 기본 타입이 있으며, 개체와 배열을 사용할 수 있습니다.

1. 숫자형은 정수와 실수를 표현합니다.

    (-2<sup>53</sup> - 1) ~ (2<sup>53</sup> - 1)를 표현합니다.
    
    정수끼리 나눴을때 실수가 나올 수 있으며, 연산결과에 따라 다음 값을 가질 수 있습니다.
    
    |항목|내용|
    |--|--|
    |`infinity`|양의 무한대|
    |`-infinity`|음의 무한대|   
    |`NaN`|연산 불가|

2. `true`와 `false`로 참, 거짓을 표현합니다.

3. 큰따옴표, 작은 따옴표, 백틱으로 문자열을 표현합니다. 특히 백틱을 사용하면, 문자열내에 변수를 사용할 수있습니다.

    문자열은 [UTF-16](??)을 사용하며, 일반적으로 작은 따옴표를 사용합니다.

4. `null`은 값이 할당되지 않았다는 의미로 개발자가 의도적으로 설정한 값입니다.
5. `undefined`는 값이 할당되지 않아 javascript에서 초기화 한 값입니다. `var a = undefined;`와 같이 사용하는건 권장되지 않습니다. `null`을 사용하세요.
6. `{}` 안에 속성명을 주어 개체를 정의합니다.
7. `[]` 안에 값을 나열하여 배열을 정의합니다. 이때 타입이 서로 다를 수도 있습니다.
8. `//`사이에 정규표현식을 사용할 수 있습니다.

```javascript
var num1 = 10.5; // #1. 실수
var num2 = 10; // #1. 정수
num2 = 0x0a; // 16진수
num2 = 0o10; // 8진수
num2 = 0b101; // 2진수
alert(3 / 2); // #1. 1.5. 정수끼리 나눴을때 실수가 나올 수 있습니다.

var b1 = true; // #2. bool
var b2 = false;

var str1 = "Hello"; // #3. 문자열
var str2 = 'Kim';
var str3 = `Hello ${str2}`; // ${variable}을 이용하여 변수값과 합성하여 출력 
alert(str3); // Hello Kim

var a = null; // #4. 값이 없음
var b; // #5. undefined. 값이 할당되지 않아 javascript에서 초기화 한 값

var obj = {name: 'Lee', number: '123-4567'}; // #6. 개체
alert('Name :' + obj.name + ', Number :' + obj.number);

var arr = [1, 'Kim', 3]; // #7. 배열
alert(arr); // 1,2,3 출력

var data = 'a,b,c~d';
var result = data.split(/,|~/); // #7. 정규 표현식을 이용하여 구분자 , ~ 로 분리
for (var i = 0; i < result.length; ++i) {
    alert(result[i]); // a b c d 출력
}
```

# 연산자

산술 연산자중 `+`는 숫자를 더할 뿐만아니라 문자열도 더할 수 있습니다.

```javascript
alert('1' + '2' == '12'); // true
```

또한 부호 연산자는 특별히 문자열을 숫자로 바꿀 수 있습니다.

```javascript
function add(a, b) {
    return a + b;
}
alert(add(1, 2) == 3); // true
alert(add('1', 2) == '12'); // true. 문자열을 더하다 보니 2도 문자열로 만들고 더함
alert(add(+'1', 2) == 3); // '1'을 숫자로 바꾸고 더함
```

`==`비교는 값이 같은지 다른지를 검사할때 적극적으로 형변환 하기 때문에 의도치 않은 결과가 나올 수도 있습니다. 따라서 형변환 없이 비교하려면 `===`을 사용해야 합니다. 

```javascript
alert(1 == '1'); // true
alert(1 === '1'); // false
```

특히 `null` 검사시 뜻하지 않게 형변환되어 잘못 검사될 수 있으니 꼭 `===`을 사용하세요.

|항목|내용|
|--|--|
|산술 연산자|`+, -, *, /, %`|
|할당 연산자|`=, +=, -=, *=, /=, %=`|
|증감 연산자|`++, --`<br/>* 전위형 : 값을 증감시킨뒤 리턴<br/>*후위형 : 값을 증감시기키 전의 값을 리턴|
|부호 연산자|`+, -`|
|비교 연산자|`==, !=, <, >, <=, >=`|
|일치 연산자|`===, !==`|
|논리 연산자|`||, &&, !`<br/>* 논리 연산자는 단축 평가 됩니다. 예를 들어 `A || B` 시 `A`가 `true`이면 어짜피 논리 연산의 결과는 `true`이기 때문에 `B`는 실행되지 않습니다.|
|삼항 연산자|`조건식 ? 표현식1 : 표현식2`<br/>* 표현식1 : 조건식이 `true`인 경우 실행<br/>*표현식2 : 조건식이 `false`인 경우 실행|
|쉼표 연산자|왼쪽부터 차례로 표현식을 실행하고, 마지막에 가장 오른쪽 표현식을 리턴함|
|괄호|괄호안의 연산 우선순위를 최우선으로 높임|
|typeof|피 연산자의 타입을 문자열(*`number, string, boolean,undefined, symbol, object, fuction`*)로 리턴함. `null`과 배열은 모두 `object`로 리턴됨|

# 형변환

javascript 는 값을 비교할때 적극적으로 형변환 합니다. 

* `null` : 0으로 변환됩니다.
* undefined : `NaN`으로 변환됩니다.
* 0, 빈 문자열, `null`, `undefined`, `NaN` : `false`로 변환됩니다.
* `'0'` : 빈 문자열이 아니므로 `true`로 변환됩니다.

명시적으로 형변환 하고 싶은 경우에는 `Number()`, `String()`, `toString()`, `parseInt()`, `parseFloat()`등을 사용합니다.

```javascript
alert(Number('1') === 1); // Number(), String()등을 이용하여 명시적으로 형변환 할 수 있습니다. 

var val = 10;
alert(val === 10); // true

var str = val.toString(); 
alert(str === '10'); // true

alert(parseInt('1') === 1); // true
alert(parseFloat('1.5') === 1.5); // true
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
    default: // 일치하는 항목이 없을 때 실행

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


# 함수

```javascript
function add(a, b) {
    return a + b;
}

add(1, 2); // 3
add('Hello', 'Lee'); // HelloLee
```

# 개체

개체는 속성의 집합이며,이름(*키*)으로 구분됩니다. 대입시 얕은 복사를 합니다.

1. 함수를 속성으로 사용할 수 있으며, 특별히 메서드라고 합니다.
2. `this`를 사용하여 개체 자신을 나타낼 수 있습니다.

```javascript
var empty = {}; // 빈 개체
var user = {
    name: 'Lee',
    number: '123-4567',
    getName: function() { // #1. 함수를 사용할 수 있으며 메서드라고 합니다.
        return this.name; // #2. this는 자기 자신을 나타냅니다.
    }
};

alert(user.getName() == 'Lee'); // true
```

# 배열

배열은 여러개의 값을 순차적으로 표현합니다. 대입시 얕은 복사를 합니다.

1. 다른 언어와 달리 타입이 다르더라도 하나의 배열로 사용할 수 있습니다.
2. `length`로 길이를 구할 수 있습니다.

```javascript
var arr = [1, 'Kim', 2]; // #1. 타입이 다르더라도 배열로 사용할 수 있습니다.
for (var i = 0; i < arr.length; ++i) { // #2. length 로 길이를 구할 수 있습니다.
    alert(arr[i]); // 1 Kim 2 출력
}
```

# 유사 배열

배열은 아니지만 배열처럼 사용할 수 있는 개체를 유사 배열이라고 합니다. 대표적으로는 문자열이 있습니다.

```javascript
var str = 'Kim';
for (var i = 0; i < str.length; ++i) { 
    alert(str[i]); // K i m 출력
}
```

# symbol(작성중)

ES6 에 추가된 값
변경 불가능한 원시 타입
이름 충돌이 없는 유일한 개체 키를 만들기 위해 사용

var key = Symbol('key');
console.log(typeof key); // symbol
