---
layout: single
title: "#6. [Javascript] 배열, 문자열, Spread, 맵, 셋, Symbol, 구조 분해"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 배열

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)은 여러개의 값을 순차적으로 표현합니다. [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)처럼 `=`로 대입시 얕은 복사를 합니다.

1. #1 : `[]`로 빈 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 생성합니다.
2. #2 : `[]`에 각 요소를 쉼표로 구분하여 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 생성합니다. 다른 언어와 달리 타입이 다르더라도 하나의 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)로 사용할 수 있습니다.
3. #3 : `length`로 길이를 구할 수 있습니다.
4. #4 : `[]`로 각 요소에 접근하여 값을 수정할 수 있습니다.
5. #5 : `[[], []]`로 다차원 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 생성하고 `[][]`로 접근할 수 있습니다.

```javascript
const empty = []; // #1. 빈 배열입니다.
const arr = [1, 'Kim', 2]; // #2. 타입이 다르더라도 배열로 사용할 수 있습니다.
for (let i = 0; i < arr.length; ++i) { // #3. length 로 길이를 구할 수 있습니다.
    console.log('배열 요소', arr[i]); // 1 Kim 2 출력
}

for (let i = 0; i < arr.length; ++i) { 
   arr[i] = i * 10; // #4
}
console.log('수정된 배열값', arr[0] === 0 && arr[1] === 10 && arr[2] === 20);

const multiArr = [[1, 2, 3], [10, 20, 30]]; // #5
console.log('다차원 배열', 
    multiArr[0][0] === 1 && multiArr[0][1] === 2 && multiArr[0][2] === 3,
    multiArr[1][0] === 10 && multiArr[1][1] === 20 && multiArr[1][2] === 30
); 
```

`Array()` [생성자 함수](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)로 생성할 수 있습니다.

```javascript
const arr = new Array(2); // 요소가 2개인 배열을 생성합니다.
console.log("Array()로 생성할 수 있습니다", arr.length === 2);
```

# 배열 요소 추가/삭제

C++언어등에서는 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)의 크기가 컴파일 타임에 정적으로 결정되지만, 자바스크립트에서는 크기가 동적으로 결정됩니다. 

또한 C++언어등에서는 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 크기가 확정된 후 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 크기 내에서 요소를 수정할 수 있지만, 자바스크립트에서는 이런 제약이 없습니다. [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 크기와 상관없이 아무곳에나 요소를 대입할 수 있고 그에 따라 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 크기가 결정됩니다.

1. #1 : 주어진 인덱스 요소가 없다면 추가합니다.(*C++ 언어등에서 이런 짓을 하면 컴파일 오류죠.*) 이때 #1-1과 같이 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 크기가 조정되며, #1-2와 같이 나머지 요소는 `undefined`로 추가됩니다. 되기는 합니다만, 이런 방법으로 사용하는 건 좋지 않습니다. [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 답게 여러개의 데이터를 순차적으로 저장하세요.

2. #2 : `delete`로 삭제할 수는 있으나, [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 크기는 조정되지 않으며, `undefined`로 만들어 줍니다.

```javascript
const arr = []; // 빈 배열입니다.
arr[3] = 30; // #1. 3번 인덱스 요소가 없다면 요소를 추가하고, 30을 대입합니다. 
console.log('배열의 3번 인덱스 요소만 추가했지만, 0, 1, 2도 없어서 추가되었습니다', arr.length === 4); // #1-1
console.log('값을 대입받지 않은 요소는 undefined입니다', arr[0] === undefined); // #1-2

delete arr[3]; // #2. 3번 인덱스 요소를 삭제합니다.
console.log('3번 인덱스를 delete 했지만 크기는 4입니다', arr.length === 4);
console.log('3번 인덱스는 delete 되어 undefined입니다', arr[3] === undefined);
```

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)의 끝에 요소를 추가하기 위해서 `push()`를 이용하거나 `length` 위치에 직접 대입하는 방법이 있습니다. `push()`는 여러개의 요소도 추가할 수 있어서, 1개만 추가할때에는 `length`를 사용하는게 성능상 좋습니다. 

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)의 마지막 요소는 `pop()`을 이용하여 삭제할 수 있습니다.

```javascript
const arr = []; 
const length = arr.push(100); 
console.log('배열의 끝에 추가합니다', arr.length === 1 && arr[0] === 100, arr.length === length);

arr.push(1, 2, 3);
console.log('여러개의 요소를 추가할 수 있습니다.', arr[1] === 1 && arr[2] === 2 && arr[3] === 3);

arr[arr.length] = 200; // 단일 요소를 추가할 때에는 push() 보다 성능이 좋습니다.
console.log('배열의 끝에 추가합니다', arr.length === 5 && arr[4] === 200);

const removed = arr.pop();
console.log('배열의 마지막 요소를 제거합니다.', arr.length === 4 && removed == 200);
```

`unshift()`와 `shift()`를 이용하면 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)의 앞에 요소를 추가/삭제할 수 있습니다. 이때 전체 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 요소의 인덱스를 재지정해야 하기 때문에 속도 성능은 떨어집니다.

```javascript
const arr = [0, 1, 2, 3]; 
const length = arr.unshift(100); 
console.log('uhshift로 추가합니다.', arr.length === 5 && arr[0] === 100, arr.length === length);
const removed = arr.shift(); 
console.log('shift로 제거합니다.', arr.length === 4 && arr[0] === 0 && removed === 100);
```

`splice()`를 이용하면 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)의 중간에 요소를 추가/삭제할 수 있으며, 삭제된 요소들로 구성된 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 리턴합니다.

```javascript
const arr = [0, 1, 2, 3]; 
let removed = arr.splice(1, 2); // 1번 index부터 2개 요소를 지웁니다. 
console.log('splice로 제거합니다.', arr.length === 2 && arr[0] === 0 && arr[1] === 3, removed[0] === 1 && removed[1] === 2);
removed = arr.splice(1, 0, 10, 20); // 1번 index 부터 0개 요소를 지운뒤 10, 20을 추가한 배열을 리턴합니다.
console.log('splice로 추가합니다.', arr.length === 4 && arr[0] === 0 && arr[1] === 10 && arr[2] === 20 && arr[3] === 3, removed.length === 0);
```

`filter()`를 이용해서 주어진 조건에 맞는 요소로 구성된 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 구할 수 있습니다. 즉, 조건에 맞지 않은 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 요소는 삭제한 효과를 볼 수 있습니다.

```javascript
const arr = [0, 1, 2, 3]; 
const filtered = arr.filter((data) => data < 2); // 요소값이 2보다 작은 것만 필터링합니다. 즉, 2이상인 것은 삭제합니다.
console.log('filter로 2보다 작은 것만 필터링합니다.', filtered.length === 2 && filtered[0] === 0 && filtered[1] === 1);
```

# 배열 요소 나열

다음의 5가지 방법이 있습니다. 다른 것들은 다 괜찮은데, [for-in](https://tango1202.github.io/javascript/javascript-object/#%EC%86%8D%EC%84%B1-%EB%82%98%EC%97%B4--for-in)은 원래 속성명을 나열하는 용도로 사용하는 것이어서 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 요소 나열시 성능이 떨어집니다. 특별한 경우가 아니라면 사용하지 마세요. 

```javascript
const arr = [1, 2, 3];
for (let i = 0; i < arr.length; ++i) {
    console.log('for문으로 요소 나열', arr[i]);
}
for (const item of arr) {
    console.log('for of 로 요소 나열', item);    
}
for (let prop in arr) { // 배열 요소와 추가 속성이 나열됩니다. 성능이 떨어지니 사용하지 마세요.
    console.log('for in 으로 요소 나열', arr[prop]);
}
arr.forEach(
    (item) => console.log('forEach()로 요소 나열', item)
);
// 요소를 순회하고, 함수 결과를 다음번 함수 호출의 첫번째 인자로 전달합니다.
const result = arr.reduce(
    (total, item) => total + item, 
    0 // 최초 호출시 total의 초기값
);   
console.log('reduce()로 요소 합계 계산', result === 1 + 2 + 3);
```

# 배열 변형

`map()`을 이용하면, [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)의 각 요소에 [함수](https://tango1202.github.io/javascript/javascript-function/)를 적용하고, [함수](https://tango1202.github.io/javascript/javascript-function/)의 리턴값으로 구성된 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 얻을 수 있습니다.

```javascript
const arr = [1, 2, 3];
const result = arr.map((item) => item * 10);
console.log('map으로 각 요소에 10을 곱함', result[0] === 10 && result[1] === 20 && result[2] === 30);
```

`filter()`와 함께 결합하여 사용할 수도 있습니다. 다음은 `data`의 `value`가 `10`보다 큰 것들의 `id`들로 구성된 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 리턴한 예입니다.

```javascript
const datas = [
    {id: 10, value: 5}, 
    {id: 11, value: 15}, 
    {id: 12, value: 20}
];
const result = datas
    .filter((data) => data.value > 10) // value 가 10 보다 큰 요소만 추출
    .map((data) => data.id); // data.id로 구성된 배열을 구함 
console.log('filter후 map을 적용', result[0] === 11 && result[1] === 12);
```

# 배열 복제

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)도 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)처럼 얕은 복사를 합니다. 따라서, `const other1 = arr;`를 하면, `other1`과 `arr`은 같은 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)을 참조합니다. 

요소를 복제하려면 다음과 같이 `from()`이나 `slice()`를 이용하거나, [Spread](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#spreadecmascript6)를 이용합니다.

```javascript
const arr = [1, 2, 3];
const other1 = arr;
console.log('other1 = arr은 같은 배열 개체를 참조합니다', other1 === arr && other1 === arr);

const other2 = Array.from(arr);
console.log('other2 = Array.from(arr)은 값은 같지만 다른 배열 개체입니다', other2, other2 !== arr);

const other3 = arr.slice(); // 인수를 전달하지 않으면 배열의 전체 영역을 복사합니다.
console.log('other2 = arr.slice()은 배열의 특정 부분을 잘라 복사합니다. 값은 같지만 다른 배열 개체입니다', other3, other3 !== arr);

const other4 = [...arr];
console.log('[...arr]은 arr 요소들로 새로운 배열을 만듭니다. 값은 같지만 다른 배열 개체입니다', other4, other4 !== arr);
```

# 배열 속성과 메서드

|항목|내용|
|--|--|
|`arr[]`|배열 요소에 접근합니다. 요소가 할당되지 않았다면 `undefined`를 리턴합니다.|
|`arr.length`|`arr`의 요소 갯수입니다. 실제 요소보다 작은 값을 대입하여 배열을 자를 수 있습니다.|
|`Array.isArray(arr)`|`arr`이 배열인지 검사합니다.|
|`Array.from(arr)`|`arr`로부터 새로운 배열을 복제합니다.|
|`Array.of(a, b, c)`|`a`, `b`, `c` 요소로 구성된 배열을 생성합니다.|
|`arr.indexOf(a)`|값이 `a`인 요소가 처음으로 나타난 인덱스 입니다. 못찾으면 -1을 리턴합니다.|
|`arr.includes(a)`|값이 `a`인 요소가 있는지 검사합니다.|
|`arr1.concat(arr2)`|`arr1`과 `arr2`를 합친 배열을 리턴합니다.|
|`arr.join(delimeter = ',')`|배열 요소를 `delimeter`로 구분하여 나열한 문자열을 리턴합니다.|
|`arr.toString()`|배열 요소를 `,`로 구분하여 나열한 문자열을 리턴합니다. `join(',')`와 동일합니다.|
|`arr.push(...items)`|`arr` 뒤에 요소를 추가하고 배열 갯수를 리턴합니다.<br/>`push()`보다는 `arr[arr.lenth] = val;`로 추가하는게 성능이 좋습니다.|
|`arr.pop()`|배열의 마지막 요소를 제거하고, 제거한 요소를 리턴합니다. 빈 배열이면 `undefined`를 리턴합니다.|
|`arr.unshift()`|배열의 첫요소를 추가하고, 배열 갯수를 리턴합니다.|
|`arr.shift()`|배열의 첫요소를 제거하고, 제거한 요소를 리턴합니다. 빈 배열이면 `undefined`를 리턴합니다.|
|`arr.reverse()`|배열의 순서를 반대로 정렬합니다.|
|`arr.slice(start = 0, end = this.length)`|`arr`에서 `[start ~ end)`인 요소를 배열로 만들어 리턴합니다.<br/>`start`가 음수이면 뒤에서부터의 인덱스입니다.<br/>[기본값](https://tango1202.github.io/javascript/javascript-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90ecmascript6)을 사용하면 원본 배열을 복제합니다.|
|`arr.splice(start, deleteCount = this.length - start, ...items)`|`arr`에서 `start`부터 `deleteCount` 만큼 삭제하고, 삭제한 요소들로 구성된 배열을 리턴합니다. 이때 `items`를 전달하면, 삭제한 위치에 추가합니다.|
|`forEach(func)`|배열 요소를 순회하여 `func(value[, index[, array]])`을 호출합니다. `func()`함수는 배열 요소, 배열 인덱스, 원본 배열이 전달되며, 주로 `func(value)`와 같이 배열 요소만 사용합니다.|
|`filter(func)`|배열 요소가 `func(value[, index[, array]]) === true`인 항목으로 구성된 배열을 리턴합니다. `func()`함수는 배열 요소, 배열 인덱스, 원본 배열이 전달되며, 주로 `func(value)`와 같이 배열 요소만 사용합니다.|
|`map(func)`|배열 요소에 `func(value[, index[, array]])`을 실행한 결과로 구성된 배열을 리턴합니다. `func()`함수는 배열 요소, 배열 인덱스, 원본 배열이 전달되며, 주로 `func(value)`와 같이 배열 요소만 사용합니다.|
|`sort(func)`|배열 요소를 정렬합니다.|
|`reduce(func)`|배열의 요소를 순회하고, `func(accumulator, item, index, array)`를 호출합니다. 이때, `func()`의 결과는 다음 호출시 `accumulator`에 전달됩니다.|

# 개체와 배열간 변환

`Object.entries()`와 `Object.fromEntries()`를 이용하면 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)와 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)간 변환을 손쉽게 할 수 있습니다.

|항목|내용|
|--|--|
|`Object.keys()`|개체의 속성명을 배열로 만듭니다.|
|`Object.values()`|개체의 값을 배열로 만듭니다.|
|`Object.entries()`|개체의 속성명-값 쌍을 배열로 만듭니다.|
|`Object.fromEntries()`|개체의 속성명-값 쌍 배열로 부터 개체를 만듭니다.|

```javascript
const obj = {
    name: 'Kim',
    addr: 'Seoul'
};
console.log('속성명으로 구성된 배열입니다.', Object.keys(obj)); // ['name', 'addr']
console.log('값으로 구성된 배열입니다.', Object.values(obj)); // ['Kim', 'Seoul']
console.log('속성명-값 쌍으로 구성된 배열입니다.', Object.entries(obj)); // [['name', 'Kim'], ['addr', 'Seoul']]
console.log('fromEntries()로 개체를 만듭니다.', Object.fromEntries([['name', 'Kim'], ['addr', 'Seoul']])); // {name: 'Kim', addr: 'Seoul'}     
```

# 유사 배열

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)은 아니지만 배열처럼 `length`와 `[]`을 사용할 수 있는 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 [유사 배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4)이라고 합니다. 대표적으로는 [문자열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%AC%B8%EC%9E%90%EC%97%B4)이 있습니다.

```javascript
const str = 'Kim';
console.log('유사 배열', Array.isArray(str) === false); // true. 배열이 아닙니다.
for (let i = 0; i < str.length; ++i) { 
    console.log('유사 배열 요소', str[i]); // K i m 출력
}
```

# 문자열

[문자열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%AC%B8%EC%9E%90%EC%97%B4)은 `length`와 `[]`을 제공하는 [유사 배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4)이어서 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)과 관련한 [함수](https://tango1202.github.io/javascript/javascript-function/#%ED%95%A8%EC%88%98)들을 이용할 수 있으며 [for of](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C-%EB%82%98%EC%97%B4)를 이용하여 나열할 수 있습니다.

주요 [함수](https://tango1202.github.io/javascript/javascript-function/#%ED%95%A8%EC%88%98)들은 다음과 같습니다.

|항목|내용|
|--|--|
|`str[]`|각 문자에 접근합니다. 문자열 범위 바깥이라면 `undefined`를 리턴합니다.|
|`str.length`|`str`의 요소 갯수입니다.|
|`str.indexOf(subStr)`|`subStr`인 요소가 처음으로 나타난 인덱스 입니다. 못찾으면 -1을 리턴합니다.|
|`str.startsWith(searchStr)`|`searchStr`이 접두사로 사용된다면 `true`를 리턴합니다.|
|`str.endsWith(searchStr)`|`searchStr`이 접미사로 사용된다면 `true`를 리턴합니다.|
|`str.includes(subStr)`|값이 `subStr`인 요소가 있는지 검사합니다.|
|`str1.concat(str2)`|`str1`과 `str2`를 합친 문자열 리턴합니다. `str1 + str2`와 동일합니다.|
|`str.slice(start = 0, end = this.length)`|`str`에서 `[start ~ end)`인 문자열을 리턴합니다.|
|`str.substring(start = 0, end = this.length)`|`slice()`와 동일하며, `start > end`이면, 반대로 적용해서 리턴합니다.<br/>유사하게 `substr()`은 문자열 갯수로 추출하는 함수인데요, 브라우저 호환성 문제로 deprecate 되었습니다.|
|`str.replace(searchValue, replaceValue)`|`searchValue`인 문자열을 `replaceValue`로 변경한 문자열을 리턴합니다.|
|`str.toUpperCase(), str.toLowerCase()`|대문자, 소문자로 변경합니다.|
|`str.split(delimeter)`|주어진 `delimeter`로 문자열을 분해합니다. 이때 `//`사이에 정규표현식을 사용할 수 있습니다.|

```javascript
const str = 'abcde';
console.log('문자열 길이', str.length === 5);
for (const ch of str) {
    console.log('for of 로 요소 나열', ch);    
}
console.log('하위 문자열 탐색', str.indexOf('cde') === 2);
console.log('하위 문자열 유무', str.includes('cde') === true);
console.log('문자열 합성', str.concat('fg') === 'abcdefg', str === 'abcde');
console.log('하위 문자열 지정한 위치까지 추출', str.slice(2, 5) === 'cde', str === 'abcde');
console.log('하위 문자열 마지막까지 추출', str.slice(2) === 'cde', str === 'abcde');  
console.log('하위 문자열 변경', str.replace('cde', 'CDE') === 'abCDE', str === 'abcde');  

str.toUpperCase();
console.log('대문자로 변경', str.toUpperCase() === 'ABCDE');
str.toLowerCase();
console.log('소문자로 변경', str.toLowerCase() === 'abcde');
```

다음은 `split()` 사용 예입니다. 각 데이터를 `,`로 구분했는데요, 테스트를 위해 의도적으로 공백 문자를 넣어 봤습니다.

1. #1 : 단순히 `,` 로만 구분하면, 데이터에 공백 문자가 남아 있습니다.
2. #2 : 정규표현식으로 `,`와 ` `로 구분하면 불필요한 빈 문자열이 데이터 요소로 추가됩니다.
3. #3 : `filter()`함수로 불필요한 요소를 제거하면, 필요한 데이터로만 구성할 수 있습니다.

```javascript
const datas = '홍길동, 성춘향, 이몽룡'; // 콤마 뒤에 공백 문자를 넣어 봤습니다.
const result1 = datas.split(','); // 쉼표로 구분합니다.
console.log(', 로 파싱', result1[0] === '홍길동' && result1[1] === ' 성춘향' && result1[2] === ' 이몽룡'); // #1. 데이터에 공백 문자가 남아 있습니다.

const result2 = datas.split(/,| /); // 쉼표 또는 공백 문자로 구분합니다.
console.log(', 또는 공백 문자로 파싱', result2[0] === '홍길동' && result2[1] === '' && result2[2] === '성춘향' && result2[3] === '' && result2[4] === '이몽룡'); // #2. 빈문자열이 추가되어 있습니다.

const result3 = datas.split(/,| /).filter((data) => data != '');
console.log('파싱 후 필터링', result3[0] === '홍길동' && result3[1] === '성춘향' && result3[2] === '이몽룡'); // #3
```

[문자열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%AC%B8%EC%9E%90%EC%97%B4)에서 특정 위치의 문자를 `[]`로 접근하여 수정할 수 없습니다. 다음처럼 [Spread](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#spreadecmascript6)를 이용하여 문자 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)로 만든 후 수정하고, 이를 다시 `join()`을 이용하여 [문자열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%AC%B8%EC%9E%90%EC%97%B4)로 만들어야 합니다.

```javascript
const str = 'abc';
str[0] = 'A'; // (X) 컴파일 오류도 없이 무시됩니다.
console.log('문자열 상수를 참조하는 문자열의 각 요소는 수정할 수 없습니다.', str === 'abc');

const arr = [...str]; // 배열로 만들어 수정합니다.
arr[0] = 'A';
arr[1] = 'B';
arr[2] = 'C';

console.log('배열을 join() 함수로 문자열로 만듭니다.', arr.join('') === 'ABC');
```

# Spread(ECMAScript6) 

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)등 [이터러블](https://tango1202.github.io/javascript/javascript-basic/#%EC%9D%B4%ED%84%B0%EB%9F%AC%EB%B8%94%EA%B3%BC-for-ofecmascript6) 개체에 `...`을 붙이면, 대상을 개별 요소로 분리하여 나열합니다.

```javascript
const f = (a, b, c) => {
    return a + b + c;
};

console.log('배열 [1, 2, 3]을 나열해서 전달합니다', f(...[1, 2, 3]) === 1 + 2 + 3); 
```

[Spread](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#spreadecmascript6)를 이용하면 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4) 처리를 좀더 단순하게 할 수 있습니다. 

```javascript
const arr = [1, 2, 3];

console.log('Spread를 이용한 배열 복제(slice)', [...arr]); // [1, 2, 3]
arr.push(...[4, 5]);
console.log('기존 배열과 다른 배열 합성한 새로운 배열 리턴(concat)', arr); // [1, 2, 3, 4, 5]
console.log('기존 배열에 새로운 값들을 추가한 새로운 배열 리턴', [...arr, 6, 7]); // [1, 2, 3, 4, 5, 6, 7]
console.log('기존 배열에 새로운 배열을 추가한 새로운 배열 리턴', [...arr, [6, 7]]); // [1, 2, 3, 4, 5, [6, 7]]
```

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)뿐만 아니라 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)도 분해해서 나열할 수 있습니다.

```javascript
console.log('개체를 복제하지만, prototype은 복제되지 않습니다', {...{ x: 1, y: 2 } }); // { x: 1, y: 2 } 
console.log('개체를 합성하여 새 개체를 생성합니다', { x: 1, y: 2, ...{ a: 3, b: 4 } }); // {x: 1, y: 2, a: 3, b: 4 }
console.log('기존 개체의 특정 속성값을 변경한 새 개체를 생성합니다.', { ...{x: 1, y: 2}, y: 3 }); // {x: 1, y: 3 }

console.log('두 개체를 합성하여 새로운 개체를 생성합니다. 중복된 속성값은 덮어씁니다', { ...{ x: 1, y: 2 }, ...{ y: 100, z: 3 } }); // { x: 1, y: 100, z: 3 }
console.log('속성을 추가한 새 개체를 생성합니다', { ...{ x: 1, y: 2 }, z: 3 }); // { x: 1, y: 2, z: 3 }
```

# Symbol(ECMAScript6)

[Symbol](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#symbolecmascript6)은 ECMAScript6에 추가된 타입으로서 유일한 Id로 사용될 값을 생성할때 사용합니다. 서로 다른 값임을 보장하며, [문자열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%AC%B8%EC%9E%90%EC%97%B4)로 변환시에는 그냥 `Symbol()`로 변환되기 때문에 서로 다른 값인지 확인할 수 없습니다. 또한 `Symbol('my')`와 같이 특정 설명을 줄 수 있으며, 주어진 설명에 대해 유일한 값을 생성할 수 있습니다.

```javascript
const val1 = Symbol();
const val2 = Symbol();
console.log('Symbol입니다.', val1 !== val2); // Symbol로 생성한 개체는 서로 다름을 보장합니다.
console.log('문자열로 변환하면 Symbol()로 변환됩니다.', val1.toString() === 'Symbol()')

const val3 = Symbol('my'); // 특정 설명을 줄수 있습니다.
const val4 = Symbol('my');
console.log('my로 구분한 Symbol입니다.', val3 != val4); 
```

`Symbol.for()`를 이용하면 전역적인 [Symbol](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#symbolecmascript6)을 만들며, 주어진 `key`에 대해 동일한 [Symbol](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#symbolecmascript6)을 구합니다. 이를 이용하면, 전역적인 데이터를 관리할 수 있습니다. 

다음은 `myData`를 키로 하여 `globalObj`의 속성에 값을 저장하고 불러오는 예입니다.

```javascript
const globalObj = {};

const myDataId = Symbol.for('myData');
globalObj[myDataId] = 'Lee'; 

console.log('유일키로 데이터에 접근합니다.', globalObj[myDataId] === 'Lee'); 
```

개체에 `Symbol.toPrimitive`속성으로 메서드를 추가하면, [형변환](https://tango1202.github.io/javascript/javascript-basic/#%ED%98%95%EB%B3%80%ED%99%98)에 따른 변환값을 제어할 수 있습니다. `hint`는 `string`, `number`, `default`(*형변환할 자료형이 확실하지 않은 경우입니다.*)중 하나로서 어떤식으로 형변환할지의 정보가 전달됩니다. 

```javascript
const user = {
    name: 'Lee',
    age: 20,

    [Symbol.toPrimitive] : function (hint) {
        switch(hint) {
            case 'string':
                return this.name;
                break;
            case 'number':
                return this.age;
                break;
            case 'default':
                return 'default는 그냥 문자열로 합니다.';
                break;
        } 
    }
};

console.log('hint로 string을 사용하여 변환합니다.', String(user)); // Lee
console.log('hint로 number를 사용하여 변환합니다.', +user); // 20
console.log('hint로 default 사용하여 변환합니다.', user + 100); // default는 그냥 문자열로 합니다.100
```

# iterable과 for-of(ECMAScript6)

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)이나 [문자열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%AC%B8%EC%9E%90%EC%97%B4)은 [for of](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C-%EB%82%98%EC%97%B4)를 이용하여 각 요소를 나열할 수 있었는데요, 이렇게 나열할 수 있는 개체를 [iterable](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6)이라고 합니다. [iterable](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6)이 되려면 특별히 `Symbol.iterator` 속성인 메서드가 제공되어야 합니다.

다음은 `myData`에 [iterable](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6)을 구현한 예입니다.

1. #1 : `Symbol.iterator`인 메서드를 만듭니다.
2. #2 : `next()`가 있는 개체를 리턴합니다.
3. #3 : `next()`는 [for of](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C-%EB%82%98%EC%97%B4)시 순회하며 호출됩니다. 리턴시 `done`과 `value`로 구성된 개체를 리턴하는데요, `done`은 순회가 끝났는지의 여부를 나타내고, `value`는 순회중인 항목의 값을 나타냅니다.

```javascript
const myData = {
    start: 0,
    last: 5,

    // #1. Symbol.iterator인 메서드가 있어야 합니다.
    [Symbol.iterator]: function() {
        // #2. next() 메서드가 있는 개체를 리턴해야 합니다.
        return {
            cur: this.start, // 최초에는 begin입니다.
            end: this.last,
            // #3. done과 value로 구성된 개체를 리턴해야 합니다.
            next() {
                return {
                    done: (this.cur >= this.end) ? true : false,
                    value: this.cur++ // cur값을 리턴하고, 이후에는 1 증가된 값을 사용합니다.
                };    
            }
        };
    }
};
for (const item of myData) {
    console.log('이터레이터로 나열합니다.', item); // 0, 1, 2, 3, 4
}
```

[iterable 개체](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6)는 `Array.from()`을 이용하여 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)로 만들 수 있습니다.

```javascript
const arr = Array.from(myData);
console.log('Array.from()으로 배열로 만들 수 있습니다.', Array.isArray(arr), arr.toString());
```

# Map(ECMAScript6)

[Map](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#mapecmascript6)은 키와 값을 쌍으로 관리하는 자료구조 입니다. `set()`으로 값을 저장하고, `get()`으로 값을 구합니다. [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 키로 사용할 수도 있으며, `forEach()`로 나열할 수 있습니다.

```javascript
const map = new Map();
map.set(1, 'Kim');
console.log('set()으로 값을 설정하고, get으로 구합니다.', map.get(1) === 'Kim');

map.set(1, 'Lee');
console.log('동일 키값을 값을 수정합니다.', map.get(1) === 'Lee');

const key = {data: 'myKey'};
map.set(key, 'Park');
console.log('개체를 키로 사용할 수 있습니다.', map.get(key) === 'Park');
console.log('값이 동일한 다른 개체로는 참조할 수 없습니다. undefined입니다.', map.get({data: 'myKey'}) === undefined);  

map.forEach((value, key) => console.log('Map을 forEach로 나열합니다.', key, value));
```

`Object.entries()`를 이용하면, [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 [Map](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#mapecmascript6)으로 바꿀 수 있으며, `Object.fromEntries()`를 이용하여 [Map](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#mapecmascript6)을 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)로 변경할 수 있습니다.

```javascript
const obj = {
    name: 'Kim',
    addr: 'Seoul'
};

const map = new Map(Object.entries(obj));
console.log('entries()로 개체를 Map으로 변환합니다.', map.get('name') === 'Kim' &&  map.get('addr') === 'Seoul');

const obj2 = Object.fromEntries(map);
console.log('fromEntries()로 Map을 개체로 변환합니다.', obj2.name === 'Kim' && obj2.addr === 'Seoul');
```

|항목|내용|
|--|--|
|`size`|요소 갯수를 리턴합니다.|
|`set(key, value)`|`key`인 `value`를 저장하고, Map 자신을 리턴합니다.|
|`get(key)`|`key`인 값을 리턴합니다. 없으면 `undefined`를 리턴합니다.|
|`has(key)`|주어진 `key`가 있는지 검사합니다.|
|`delete(key)`|`key`이 값을 삭제합니다.|
|`clear()`|모든 요소를 삭제합니다.|
|`forEach()`|Map을 나열합니다.|
|`keys()`|`key`로 구성된 이터러블 개체를 리턴합니다.|
|`values()`|`value`로 구성된 이터러블 개체를 리턴합니다.|
|`entries()`|`[key, value]`쌍으로 구성된 이터러블 개체를 리턴합니다.|

# Set(ECMAScript6)

[Set](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#setecmascript6)은 중복되지 않는 값을 관리하는 자료구조 입니다. `add()`으로 값을 추가하고, `delete()`으로 값을 삭제합니다. `forEach(), keys(), values(), entries()`가 제공되며, [Map](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#mapecmascript6)과의 호환성을 위해, `key`대신 `value`가 전달됩니다.

|항목|내용|
|--|--|
|`size`|요소 갯수를 리턴합니다.|
|`add(value)`|`value`를 저장하고, Set 자신을 리턴합니다.|
|`has(value)`|주어진 `value`가 있는지 검사합니다.|
|`delete(value)`|`value`를 삭제합니다.|
|`clear()`|모든 요소를 삭제합니다.|
|`forEach()`|Set을 나열합니다.|
|`keys()`|`value`로 구성된 [iterable 개체](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6)를 리턴합니다.|
|`values()`|`keys()`와 동일합니다. `value`로 구성된  [iterable 개체](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6)를 리턴합니다.|
|`entries()`|`[value, value]`쌍으로 구성된  [iterable 개체](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6)를 리턴합니다.|

# 구조 분해(ECMAScript6)

[배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)과 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 각 요소를 `[]`과 `{}`로 분해하여 처리할 수 있습니다. 이때 필요한 부분만 추출할 수도 있습니다.

```javascript
const arr = [1, 2, 3];
const [a, b, c] = arr;
console.log('배열 구조 분해', a === 1 && b === 2 && c === 3);

const [i, , k] = arr;
console.log('배열은 순서대로 구조 분해됩니다. i, , k는 1번 인덱스를 건너뜁니다', i === 1 && k === 3);

const obj = {
    x: 1,
    y: 2,
    z: 3
};
const {x, y} = obj; // obj에서 x, y만 추출합니다.
console.log('개체 구조 분해', obj.x === 1 && obj.y === 2);

const user = {
    name: 'Kim',
    info: {
        addr: 'Seoul',
        number: '123-4567'
    }
};
const {name, info: {addr}} = user; // 중첩 개체도 분해합니다.
console.log('중첩 개체도 분해해서 읽습니다', name === 'Kim' && addr === 'Seoul');
```

**기본값**

[구조 분해](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)시 [기본값](https://tango1202.github.io/javascript/javascript-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90ecmascript6)을 설정할 수 있습니다.

```javascript
const obj = {
    x: 1,
};
const {x, y = 2} = obj;
console.log('obj에 y가 없으므로 기본값 0이 할당됩니다.', x === 1 && y === 2);
```

**이름 변경**

[구조 분해](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)한 속성의 이름을 변경할 수 있습니다.

```javascript
const obj = {
    x: 1,
    y: 2
};
const {x: left, y: top} = obj;
console.log('obj.x를 left에, obj.y를 top에 저장합니다.', left === 1 && top === 2);
```

**함수 인자**

[함수](https://tango1202.github.io/javascript/javascript-function/)의 인자에 [구조 분해](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)를 사용할 수 있습니다. 이럴 경우 [기본값 인자](https://tango1202.github.io/javascript/javascript-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90ecmascript6) 설정의 자유도가 높아지고, [함수](https://tango1202.github.io/javascript/javascript-function/) 호출 부에서는 인수의 순서가 아닌 속성명으로 인자를 매칭하기 때문에 가독성이 높아집니다.

```javascript
// 함수에서는 기본값을 설정하면, 다음에 선언된 모든 인자에 기본값을 주어야 하지만,
// 구조 분해를 이용하면, 어떠한 위치에 있던 원하는 것에 기본값을 줄 수 있습니다.
const func = ({x = 0, y, z = 2}) => { 
    return x + y + z;
};

console.log('x, z는 전달하지 않았으므로 기본값이 사용됩니다.', func({ y:1 }) === 0 + 1 + 2); 
console.log('인수 전달시 이름으로 매칭되기 때문에 순서를 준수할 필요는 없습니다.', func({ y: 20, x: 10}) === 10 + 20 + 2); 
```

**나머지 인자**

[나머지 인자](https://tango1202.github.io/javascript/javascript-function/#%EB%82%98%EB%A8%B8%EC%A7%80-%EC%9D%B8%EC%9E%90ecmascript6)를 사용하면, 일부 요소만 [구조 분해](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)하고 나머지는 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)로 전달받을 수 있습니다.

```javascript
const obj = {
    name: 'Kim',
    addr: 'Seoul',
    age: 10
};

const {name, ...etc} = obj;
console.log('...을 이용하면 나머지를 개체로 전달받을 수 있습니다.', name === 'Kim', etc.addr === 'Seoul', etc.age === 10);
```

**swap**

임시로 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)로 만든뒤 순서를 바꿔 [구조 분해](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)하면, `swap`한 효과를 얻을 수 있습니다.

```javascript
let x = 10;
let y = 20;
[y, x] = [x, y]; // 임시로 배열로 만들고 순서를 바꿔 구조 분해 합니다.

console.log('임시로 배열로 만들고 순서를 바꿔 구조 분해하면, swap한 효과를 얻을 수 있습니다.', x === 20, y === 10);
```
**entries()를 이용한 속성명, 값 나열**

`Object.entries()`는 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 속성명-값 쌍을 [배열](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EB%B0%B0%EC%97%B4)로 변환하므로, [구조 분해](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)와 혼합하면, [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)의 각 속성의 `key`와 `value`를 손쉽게 분해해서 사용할 수 있습니다.

```javascript
const obj = {
    name: 'Kim',
    addr: 'Seoul',
    age: 10
};

for (let [key, value] of Object.entries(obj)) {
    console.log('개체의 각 속성을 key, value로 분해했습니다.', key, value); 
}
```







