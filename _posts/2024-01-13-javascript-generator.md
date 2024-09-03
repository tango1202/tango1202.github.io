---
layout: single
title: "#13. [Javascript] Generator"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

`function` 오른쪽에 `*`을 붙이면 호출시마다 일시 정지하고 재개하는 `generator`함수를 만들 수 있습니다. C++ [코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)과 유사합니다.

다음 코드의 `getFunc()`을 호출하면 `generator`가 리턴되는데, `generator.next()` 함수를 호출할 때마다 다른 값이 반환되고 함수는 일시 정지 됩니다. 

리턴되는 값의 형식은 다음과 같습니다.

```javascript
{
    value, // yield나 return으로 반환한 값
    done, // true 또는 false로서 return으로 반환했는지의 여부  
}
```

1. #1 : `function` 오른쪽에 `*`을 붙여  `generator`함수를 만듭니다.
2. #2, #3, #4 : 함수 호출시마다 다른 값이 리턴되게 합니다.
3. #5 : 만약 `return`으로 반환한 후에 또다시 `generator.next()`를 호출한다면, `value`는 `undefined`가 됩니다. 

```javascript
(() => {
    const genFunc = function* () { // #1
        yield 1; // #2. 1을 반환하고 일시 정지
        yield 2; // #3. 2를 반환하고 일시 정지 
        return 3; // #4. 3을 반환하고 종료
    };

    const generator = genFunc();
    console.log('generator 1회', generator.next());
    console.log('generator 2회', generator.next());
    console.log('generator 3회', generator.next());
    console.log('generator 4회', generator.next()); // #5
})();
```

실행 결과는 다음과 같습니다.

![image](https://github.com/user-attachments/assets/5e4a25fd-5a68-42af-a983-ddb1c244b77c)

# generator 순회

[for-of](https://tango1202.github.io/javascript/javascript-array-string-spread-map-set/#iterable%EA%B3%BC-for-ofecmascript6) 로 순회하여 `value`를 나열할 수 있습니다. 단, `return`을 사용하면 마지막 것은 순회하지 않으므로, 모두 `yield`를 사용해야 합니다. 

```javascript
const genFunc = function* () { 
    yield 1; 
    yield 2; 
    yield 3; // 마지막 것도 순회하기 위해 yield 사용
};

const generator = genFunc();

for (let val of generator) {
    console.log('generator value : ', val);
}
```