---
layout: single
title: "#6. [Javascript] 모듈"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 모듈(ECMAScript6)

프로젝트의 규모가 커지면, 코드를 여러개의 파일로 구분해야 합니다. 

자바스크립트에서는 동일한 목적을 가진 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)들과 [함수](https://tango1202.github.io/javascript/javascript-function/)들을 각각의 파일로 묶어 라이브러리로 만들 수 있으며, 이를 [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)이라 합니다.

* `export` : [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)에서 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)나 [함수](https://tango1202.github.io/javascript/javascript-function/)를 내보냅니다.

    ```javascript
    export const plus = (a, b) => {
        return a + b;
    };
    export const minus = (a, b) => {
        return a - b;
    };
    ```

    혹은 하나의 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)로 구성해서 한번에 내보낼 수 있습니다.

    ```javascript
    const plus = (a, b) => {
        return a + b;
    };
    const minus = (a, b) => {
        return a - b;
    };
    
    export { plus, minus };
    ```

    1개만 `export`할 경우에는 `default`를 사용할 수 있습니다.

    ```javascript
    const plus = (a, b) => {
        return a + b;
    };
    const minus = (a, b) => {
        return a - b;
    };

    export default plus;
    ```

* `import` : [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)의 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)나 [함수](https://tango1202.github.io/javascript/javascript-function/)를 가져옵니다.

    ```javascript
    import { plus, minus } from 'myModule.js'; 
    import { plus } from 'myModule.js'; // 필요한 것만 가져옵니다.
    import {plus as PLUS, minus as MINUS } from 'myModule.js'; // 이름을 바꿔서 가져옵니다. 
    ```

    `* as 라이브러리명`으로 해당 [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)의 모든 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 주어진 라이브러리명으로 가져올 수 있습니다. 

    ```javascript
    import * as M from "./myModule.js"; // M이란 라이브러리명으로 모두 가져옵니다.
    M.plus(1, 2); // 모듈 실행시 라이브러리명을 붙입니다.
    M.minus(1, 2);
    ``` 

    `default`로 `export`된 모듈은 `{}`없이 가져와야 합니다.

    ```javascript
    import plus from 'myModule.js';
    ```

* [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)은 [strict mode](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#strict-mode)로 실행됩니다.

* [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)은 자신만의 [유효 범위](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84)를 사용합니다. `export`되지 않은 것은 외부에서 사용할 수 없습니다. 

* 여러곳에서 [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)을 사용하더라도 최초 호출시 단 한번만 실행됩니다. 즉, [Singleton](https://tango1202.github.io/pattern/pattern-singleton/)이 됩니다. 따라서 [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)을 사용하는 곳이 여러곳일때 한곳에서 수정하면, 다른 곳에서 수정된 내용을 참조할 수 있습니다. 

다음은 [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)을 테스트하는 코드입니다.
[모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)을 사용하기 위해 `<script>`에 `type=module` 속성을 주어야 합니다.

```javascript
<!DOCTYPE html>
<html>
    <body>
        <script type="module" src="/module_-_test.js"></script>
    </body>
</html>
```

다음은 [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)을 구현한 `myModule.js`입니다. `privateFunc`은 내보내지 않았습니다.

```javascript
const privateFunc = () => {
    console.log('모듈에서 사용하는 privateFunc입니다');
};

const publicFunc = () => {
    privateFunc(); // 모듈에서만 사용하는 privateFunc을 호출합니다.
}

const plus = (a, b) => {
    return a + b;
};
const minus = (a, b) => {
    return a - b;
};

export { publicFunc, plus, minus }; // privateFunc은 내보내지 않았습니다.
```

다음은 [모듈](https://tango1202.github.io/javascript/javascript-module/#%EB%AA%A8%EB%93%88ecmascript6)을 테스트하는 `module_test.js`입니다. `export`된 모든 [개체](https://tango1202.github.io/javascript/javascript-object/#%EA%B0%9C%EC%B2%B4)를 `M`라이브러리 명으로 가져와 사용합니다.

```javascript
import * as M from "./myModule.js";
// ----
// 모듈
// ----
(() => {
    M.publicFunc();
    console.log('myModule.js의 plus 함수 입니다', M.plus(1, 2) === 3);
    console.log('myModule.js의 minus 함수 입니다', M.minus(1, 2) === -1);
})();
```
