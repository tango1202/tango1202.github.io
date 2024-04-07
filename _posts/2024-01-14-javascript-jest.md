---
layout: single
title: "#14. [Javascript] 자바스크립트 단위 테스트 구축(Jest)"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

`Jest`는 자바스크립트 단위 테스트 프레임워크입니다. [Mocha](https://tango1202.github.io/javascript/javascript-mocha-chai/)같은 경우는 [Chai](https://tango1202.github.io/javascript/javascript-mocha-chai/)와 같은 Assertion 라이브러리가 필요한데, `Jest`는 단독으로 사용할 수 있어서 최근에는 더 많이 사용되는 것 같습니다.

# Jest 설치

1. [개발 환경 구축](https://tango1202.github.io/javascript/javascript-config/)을 참고하여 다음을 설치합니다.
    * [Node.js(https://nodejs.org/)](https://nodejs.org/)
    * [Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)

2. 다음 명령을 이용하여 기본적인 `node.js`프로젝트를 생성합니다. 물어보는 것들은 기본적으로 `Enter`하여 지나갑니다.

    ```
    npm init
    ```

3. 터미널에서 다음 명령을 입력하여 `Jest`를 설치합니다. `import`등 ECMAScript를 사용하려면 `@babel/core`, `@babel/preset-env`, `babel-jest`도 함께 설치되어야 합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*)  

    ```
    npm install -D jest @babel/core @babel/preset-env babel-jest 
    ```

4. `package.json`에서 `scripts`의 `test`를 다음과 같이 `jest`로 수정합니다.

    ```json
    "scripts": {
        "test": "Jest"
    },
    ```

5. 프로젝트 루트에 `.babelrc`파일을 생성하고 다음을 입력합니다.

    ```json
    {
        "presets": ["@babel/preset-env"]
    }
    ```

# 테스트 하기

1. `src`폴더를 만들고, `operator.js`파일을 작성합니다.

    ```javascript
    function plus(a, b) {
        return a + b;
    }
    function minus(a, b) {
        return a - b;
    }

    export {plus, minus};
    ```

2. `test`폴더를 만들고, `operator.test.js`파일을 작성합니다. `Jest`는 테스트 파일이 꼭 `test.js`로 끝나야 합니다.

    그리고 `import`시에 `.js`을 붙이지 않아도 됩니다.(*`Babel`에서 `CommonJ`S로 변환하기 때문인듯 한데, 좀더 알아봐야 겠습니다.*)

    ```javascript
    import { plus, minus } from '../src/operator'; // 테스트할 모듈. 확장자를 안붙여도 됩니다.

    describe('테스트입니다', () => { // 테스트 범주
        it('plus() 테스트', () => { // 테스트케이스
            expect(plus(1, 2)).toBe(3);
        });
        it('minus() 테스트', () => { // 테스트케이스
            expect(minus(1, 2)).toBe(-1);
        });
    });
    ```

3. 터미널에서 `npm test`를 입력하여 실행합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6ec0699f-bf15-4edf-bf39-262c1287cadf)

# Jest(Visual Studio Code Extensions) 사용하기

1. `Activity Bar`의 `Extensions`을 클릭하여 `Jest`를 설치합니다.

2. `Activity Bar`의 `Testing`을 클릭하면 `Jest`가 표시됩니다. UI를 통해 각 테스트를 별개로 실행할 수 있습니다. 만약 보이지 않는다면 Visual Studio Code 를 재실행 해보세요.(*혹은 Ctrl+Shift+P 후 Reload Window를 하셔도 됩니다.*)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/074da04b-24a0-4d76-92a1-6f88b360df9b)

# Jest(Visual Studio Code Extensions) 사용하기

파일을 저장하면 모든 테스트가 실행되어 오히려 번거로울 수 있습니다.

이런 경우 저장하는 파일만 테스트할 수 있도록 수정할 수 있습니다.

1. `Test Explorer`에서 `Configure Test Profiles`를 선택합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/369398b6-ee38-42c9-a736-30f9ddd281ab)


2. `run Jest Test Provider`를 선택합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ea32a954-d8dc-4c5a-b3e3-6faa230a6577)

3. 혹은 다음과 같이 테스트 루트에 마우스 OVer한 뒤 `Change Run Mode`를 실행해도 됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ee041f7e-ab9c-45b8-bc70-c4493ae36c02)

4. `on-save`의 편집 아이콘을 클릭합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/df91546e-0a49-4b63-bcd8-d43d6094f5f4)

    그러면 다음과 같이 `runMode.json` 파일이 작성됩니다.

    ```json
    // close without saving to cancel the change.
    // RunMode reference: https://github.com/jest-community/vscode-jest#runmode
    {
        "$schema": "file:///c%3A/Users/USER/.vscode/extensions/orta.vscode-jest-6.2.1/syntaxes/ExtSettingsSchema.json",
        "jest.runMode": {
            "type": "on-save"
        }
    }
    ```

5. 그뒤 `accept`버튼을 클릭합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cd31038f-91d8-46d0-80b8-9d418cc00f49)

6. 이제 저장하는 테스트 파일만 자동 테스트를 수행합니다.




