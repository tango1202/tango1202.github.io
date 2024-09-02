---
layout: single
title: "#12. [Javascript] Promise"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# Promise

서버와의 통신이라던가, 아주 많은 시간이 필요로 하는 기능은 비동기 방식으로 호출하는게 좋습니다. 비동기로 호출해 두고 다른 작업을 하다가, 호출 결과가 생성되면 그때 이를 이용하는 거죠. 멀티 쓰레드 프로그래밍과 비슷하다고 볼 수도 있겠습니다.

`Promise`는 자바스크립트에서 비동기 호출을 구현해 줍니다. `then()`함수에는 `resolve()`함수와 `reject()`함수를 전달해야 하는데요, `resolve()`함수는 작업을 성공했을때 호출하는 함수이고, `reject()`함수는 작업을 거절할때 호출하는 함수입니다.

다음은 `new Promise()`의 사용 예입니다.

1. #1 : `new Promise`로 비동기 작업을 생성합니다.
2. #5 : `then()`의 첫번째 인자로 `resolve()`호출시 실행될 함수를 전달하고, 두번째 인자로 `reject()`호출시 실행될 함수를 전달합니다.
3. #6 : `flnally()`는 생략 가능합니다. `then()`실행후 호출해야 할 것들이 있다면 정의합니다.
4. 호출순서는 #2, #3, #4, #5-1, #6-1의 순서로 호출됩니다.

    ![image](https://github.com/user-attachments/assets/ca4a2464-71d3-4ac0-8cb0-a112ac7c0325)

다음에서 `resolve('오래 걸리는 작업의 결과값입니다.');` 와 같이 `resolve()`함수의 인자에 문자열(개체)을 전달했는데요, 이러면 이 문자열(개체)이 작업 결과로 리턴되며, 리턴된 값은 #5의 인자 `result`에 전달됩니다.

```javascript
new Promise((resolve, reject) => { // #1
    console.log('#2. 비동기 작업을 시작합니다.'); // #2
    setTimeout(() => {
        console.log('#4. 1초뒤에 작업을 시작합니다.'); // #4
        resolve('오래 걸리는 작업의 결과값입니다.'); 
    }, 1000);
}).then( // #5
    (result) => console.log('#5. Promise가 실행된 결과 입니다.', result), // #5-1
    (result) => console.log('#5. Promise가 실행 거절되었습니다.', result)
).finally( // #6
   () => console.log('#6. Promise finally가 호출 되었습니다.') // #6-1
);
console.log('#3. 1초뒤에 Promise가 호출됩니다.'); // #3
```

만약 다음의 #1처럼 `reject()`함수를 호출한다면, `then()`의 두번째 인자인 #5-2가 호출됩니다.

```javascript
new Promise((resolve, reject) => { 
    setTimeout(() => {
        reject('오래 걸리는 작업을 거절했습니다.'); // #1
    }, 1000);
}).then(
    (result) => console.log('#5. Promise가 실행된 결과 입니다.', result), 
    (result) => console.log('#5. Promise가 실행 거절되었습니다.', result) // #5-2
).finally(
    () => console.log('Promise finally가 호출 되었습니다.') 
);
console.log('1초뒤에 Promise가 호출됩니다.'); 
```

# Promise 체이닝

`then()`내에 또다른 `Promise()`를 생성해서 여러개의 `Promise`를 연달아 호출할 수 있습니다. 이때 각각 `then()`내에 있으므로 순차적으로 실행됨을 보장합니다.

다음은 #1, #2, #3을 순차적으로 실행하고, 실행 결과를 #4에서 출력하는 예입니다.

```javascript
new Promise((resolve, reject) => { // #1
    setTimeout(() => {
        resolve('Promise 1 '); 
    }, 1000);
}).then((result) => {
    return new Promise((resolve, reject)=> { // #2
        setTimeout(() => {
            resolve(result + 'Promise 2 '); 
        }, 1000);  
    });
}).then((result) => {
    return new Promise((resolve, reject)=> { // #3
        setTimeout(() => {
            resolve(result + 'Promise 3 '); 
        }, 1000);  
    });
}).then((result) => { // #4
    console.log('1, 2, 3의 결과가 합쳐져서 출력됩니다.', result === 'Promise 1 Promise 2 Promise 3 '); 
}).finally(
    () => console.log('Promise 체이닝의 finally가 호출 되었습니다.') // #5
)
console.log('1초뒤에 Promise 체이닝이 호출됩니다.'); 
```

# Promise 예외 처리

`Promise` 본문에서 예외가 발생한다면, `catch()`에서 이를 탐지할 수 있습니다. 하지만 만약 `then()`에 `reject()`를 설정했다면 `reject()`가 호출됩니다.

다음은 `reject()`가 설정된 경우의 예입니다. #1에서 예외를 발생하면 #2가 호출됩니다.

```javascript
new Promise((resolve, reject) => {
    throw new Error('Promise 에서 예외를 발생했습니다.'); // #1
}).then(
    (result) => console.log('Promise가 실행된 결과 입니다.', result),
    (e) => console.log('Promise가 실행 거절되었습니다.', e) // #2
).catch(
    (e) => console.log('catch가 호출되지 않고 reject가 호출됩니다.', e) // #3
).finally(
    () => console.log('예외를 발생하는 Promise finally가 호출 되었습니다.') 
);
```

하지만 다음과 같이 `reject()`를 설정하지 않으면 `catch()`가 호출됩니다.

```javascript
new Promise((resolve, reject) => {
    throw new Error('Promise 에서 예외를 발생했습니다.'); // #1
}).then(
    (result) => console.log('Promise가 실행된 결과 입니다.', result),
).catch(
    (e) => console.log('reject가 없으므로 catch가 호출됩니다.', e) // #3
).finally(
    () => console.log('예외를 발생하는 Promise finally가 호출 되었습니다.') 
);
```

다음 #1 처럼 명시적으로 `reject()`를 호출했더라도 `reject()`가 없다면 `catch()`가 호출됩니다.

```javascript
new Promise((resolve, reject) => {
    reject(new Error('Promise 에서 예외를 발생시키고 거절했습니다.')); // #1
}).then(
    (result) => console.log('Promise가 실행된 결과 입니다.', str),
).catch(
    (e) => console.log('명시적으로 reject()를 호출했더라도 reject가 없으므로 catch가 호출됩니다.', e) // #2
).finally(
    () => console.log('예외를 발생하는 Promise finally가 호출 되었습니다.')
);
```

# Promise 체이닝시 예외 처리

Promise 체이닝 되었을 때, 각 `Promise` 본문에서 `catch()`한 후 이를 다시 전파하여 전체적인 `catch()`를 한곳에서 처리할 수 있습니다.

다음은 Promise 체이닝의 예외 처리 예입니다.

#3에서 발생한 예외를 #4에서 `catch()`후 재전파하고, 이를 다시 #6에서 `catch()`합니다. 이에 따라 여러 `Promise`에서 내부적으로 예외를 발생시키더라도, 외부에서는 #6에서 일괄 `catch()`를 할 수 있게 됩니다.

```javascript
new Promise((resolve, reject) => {
    resolve('Promise 1 '); // #1
}).then((result) => {
    return new Promise((resolve, reject)=> {
        resolve(result + 'Promise 2 '); // #2
    });
}).then((result) => {
    return new Promise((resolve, reject)=> {
        throw new Error(result); // #3. #1 + #2 의 결과값을 예외로 전파합니다.
    }).catch((e) => { 
        throw e; // #4. #3의 예외를 전파합니다.
    });
}).then((result) => {
    console.log(result);  // #5. #3에서 예외가 발생해서 호출되지 않습니다.
}).catch((e)=> { // #6. #4를 캐치합니다.
    console.log('#4를 catch합니다.', e) 
}).finally(
    () => console.log('#7. Promise 체이닝의 finally가 호출 되었습니다.') // #8
)
```

# async

`async`는 특정 함수를 `Promise` 개체로 만들어 줍니다.

즉, 다음과 같은 `Promise` 표현을

```javascript
new Promise((resolve, reject) => {
    resolve('Promise 결과값입니다.'); 
}).then(
    (result) => console.log('Promise가 실행된 결과 입니다.', result)
);
```

다음과 같이 `async` 로 변경할 수 있습니다.

```javascript
const func = async ()=> {
    return 'Promise 결과값입니다.'; 
};

func().then(
    (result) => console.log('Promise가 실행된 결과 입니다.', result)
);
```

직접 실행 함수를 사용한다면 다음과 같이 표현할 수 있습니다.

```javascript
(async ()=> {
    return 'Promise 결과값입니다.'; 
})().then(
    (result) => console.log('Promise가 실행된 결과 입니다.', result)
);
```

# await

`await`는 `async`함수 내에서만 사용할 수 있으며, `Promise`호출이 완료될때까지 대기합니다. 즉 `then()`과 유사하다고 볼 수 있습니다.

다음 예에서 #3은 비동기 함수이므로 #1과 #2가 먼저 실행되며, #4가 종료되어야 #5와 #6이 실행됩니다.

```javascript
console.log('async 호출전입니다.'); // #1
(async ()=> { // #3. await는 aync 함수 내에서만 사용 가능합니다.
    const promise = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('Promise 결과값입니다.'); // #4
        }, 1000);
    });
    const result = await promise; // #5. promise가 실행될때 까지 기다립니다.
    console.log('Promise가 실행된 결과 입니다.', result); // #6. 이전에는 then()에서 작성했습니다.

})();
console.log('async 호출후입니다.'); // #2
```

