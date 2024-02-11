---
layout: single
title: "#9. [Javascript] 자바스크립트 단위 테스트 구축(Mocha, Chai)"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# Mocha와 Chai 설치

`Mocha`는 자바스크립트 단위 테스트 프레임워크입니다. `assert`를 사용하기 위해 `Chai`와 같은 Assertion 라이브러리와 함께 사용합니다. 

1. [개발 환경 구축](https://tango1202.github.io/javascript/javascript-config/)을 참고하여 다음을 설치합니다.
    * [Node.js(https://nodejs.org/)](https://nodejs.org/)
    * [Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)

2. 다음 명령을 이용하여 기본적인 `node.js`프로젝트를 생성합니다. 물어보는 것들은 기본적으로 `Enter`하여 지나갑니다.

    ```
    npm init
    ```

3. 터미널에서 다음 명령을 입력하여 `Mocha`를 설치합니다. 

    ```
    npm install -D mocha
    ```
4. `package.json`에서 `scripts`의 `test`를 다음과 같이 `mocha`로 수정하고, `"type": "module"`을 추가합니다.

    ```json
    "scripts": {
        "test": "mocha"
    },
    "type": "module",
    ```    

5. 터미널에서 다음 명령을 입력하여 `Chai`를 설치합니다. 

    ```
    npm install -D chai
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

2. `test`폴더를 만들고, `test_operator.js`파일을 작성합니다.

    ```javascript
    import { plus, minus } from '../src/operator.js'; // 테스트할 모듈
    import { assert } from 'chai'; // chai 사용

    describe('테스트입니다', () => { // 테스트 범주
        it('plus() 테스트', () => { // 테스트케이스
            assert.equal(plus(1, 2), 3);
        })
        it('minus() 테스트', () => { // 테스트케이스
            assert.equal(minus(1, 2), -1);
        })
    });
    ```
3. 터미널에서 `npm test`를 입력하여 실행합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6fcfa1ce-d516-43e5-baa8-0d1c6eb8b574)

# Mocha Test Explorer(Visual Studio Code Extensions) 사용하기

1. `Activity Bar`의 `Extensions`을 클릭하여 `Mocha Test Explorer`를 설치합니다.

2. 프로젝트 루트 폴더에 `.vscode`폴더를 만든뒤 `settings.json`파일을 만들고,(*Live Server나 기타 다른 세팅에 따라 이미 있을 수 있습니다.*) 다음 내용을 작성합니다.

    ```javascript
    {
        "mochaExplorer.files": ["test/*.js"]
    }
    ```

3. `Mocha Test Explorer`를 실행하여 테스트 결과를 확인합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1774ae2f-eb62-46ff-8a35-c04347b663e4)