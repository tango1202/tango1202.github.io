---
layout: single
title: "#10. [Javascript] 예외 처리(try-catch-finally)"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 예외 탐지(try-catch())

런타임 실행중에 예외가 발생하면, [try](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch)로 예외를 탐지할 수 있으며, 탐지된 예외는 [catch()](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch)에서 적절한 작업을 할 수 있습니다. 

다음 코드에서 `func()`함수는 `print()`메서드가 없어 예외를 발생시키는데요,
예외가 발생하면 곧바로 [catch()](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch)문으로 분기하기 때문에 #1은 실행되지 않습니다.

```javascript
const func = (obj) => {
    obj.print(); // (x) 예외 발생. print 메서드가 없습니다.
    console.log('func()을 실행했습니다.'); // #1
};

try {
    const myData = {};
    func(myData); // print 메서드가 없어서 예외를 발생시킵니다.
}
catch(e) {
    console.log('예외를 처리합니다.', e);
}
```

# 에러 종류

[catch()](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch)는 발생된 에러가 전달되며 기본적으로 `Error`클래스를 상속한 다음 클래스들이 제공됩니다.

|항목|내용|
|--|--|
|`Error`|최상위 클래스|
|`EvalError`|문자열로된 코드를 직접 실행하는 `eval()`함수 오류|
|`RangeError`|수치 범위 오류|
|`ReferenceError`|존재하지 않은 개체 참조|
|`SyntaxError`|문법 오류. 코드 실행 전에 발생|
|`TypeError`|잘못된 타입|
|`URIError`|잘못된 URI|

# 에러 발생과 전파(throw)

예외 상황일때 직접 예외를 발생시키고 싶으면 [throw](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%97%90%EB%9F%AC-%EB%B0%9C%EC%83%9D%EA%B3%BC-%EC%A0%84%ED%8C%8Cthrow)를 사용하면 됩니다.

```javascript
const add = (a, b) => {
    if (a < 0 || b < 0) {
        throw new RangeError('a또는 b는 0 이상이어야 합니다.');
    }
    return a + b;
};

try {
    add(-1, -1);
}
catch(e) {
    console.log('예외를 처리합니다.', e);
}
```

만약 사용자가 정의한 예외를 발생시키고 싶다면, 예외 [catch()](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch)의 일관성을 위해 `Error`를 상속해서 재구현 하는게 좋으며, [catch()](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch)에서 처리할 수 없는 예외라면, 다른 곳에서 처리할 수 있도록 [throw](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%97%90%EB%9F%AC-%EB%B0%9C%EC%83%9D%EA%B3%BC-%EC%A0%84%ED%8C%8Cthrow)로 재전파 하는게 좋습니다.

```javascript
// 예외를 catch()할때 일관성있게 작성할 수 있으므로,
// Error를 상속해서 만드는게 좋습니다. 
class ResultError extends Error {}

const add = (a, b) => {
    if (a < 0 || b < 0) {
        throw new RangeError('a또는 b는 0 이상이어야 합니다.');
    }

    const result = a + b;

    if (result > 10) {
        throw new ResultError('결과값 오류 입니다.');
    }
    return a + b;
};

try {
    add(1, 100);
}
catch(e) {
    if (e instanceof RangeError) {
        console.log('입력값이 잘못되었습니다.');
    }
    else if (e instanceof ResultError) {
        console.log('결과값이 잘못되었습니다.');
    }
    else {
        console.log('처리할 수 없는 예외는 전파합니다.', e);
        throw e; // 예외 전파
    }
}
```

# finally

[try](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch) 나 [catch()](https://tango1202.github.io/javascript/javascript-try-catch-finally/#%EC%98%88%EC%99%B8-%ED%83%90%EC%A7%80try-catch)후 반드시 실행되어야 하는 코드가 있으면 [finally](https://tango1202.github.io/javascript/javascript-try-catch-finally/#finally)를 사용할 수 있으며, #1과 같이 `return`이 있더라도 `return`전에 [finally](https://tango1202.github.io/javascript/javascript-try-catch-finally/#finally)가 호출됩니다.

```javascript
const func = (error) => {
    if (error) {
        throw new Error();
    }
};

try {
    func(false); // 예외를 발생시키지 않으면, return 전에 finally가 호출됩니다.    
                 // 예외를 발생시키면 catch() 호출뒤 finally가 호출됩니다.
    return; // #1
}
catch(e) {
    console.log('catch하였습니다.', e);
}
finally {
    console.log('finally 입니다.');
}
```




