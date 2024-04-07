---
layout: single
title: "#12. [Javascript] strict mode, ESLint, Prettier"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# strict mode

[변수](https://tango1202.github.io/javascript/javascript-basic/#%EB%B3%80%EC%88%98)에서 언급했듯, 자바스크립트는 동적 타입 언어이다 보니 변수 선언이나 사용에 있어서 자유도는 높지만, 코딩 계약이 엉망진창이 되버려 오류를 찾아내기 힘들어 집니다.

이러한 문제를 해결하기위해 ECMAScript5 부터 [strict mode](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#strict-mode)를 제공하며, 좀더 엄격하게 코드를 점검합니다.

```javascript
'use strict';
x = 3.14; // Uncaught ReferenceEffor : x is not defined
```

1. 선언되지 않는 변수에 값을 할당하면 원래는 전역 변수로 자동 생성되지만, 이를 차단합니다.

2. 중복된 인수를 차단합니다.

3. 변수, 함수, 인자의 `delete`를 차단합니다.

4. `with`문을 차단합니다.

5. 일반 함수의 `this`를 `undefined`로 바인딩합니다.

# ESLint

[ESLint](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#eslint)는 소스 코드 품질 개선 요소와 코드 스타일 개선 요소를 제안하는 정적 분석 도구입니다. [strict mode](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#strict-mode)보다 더 많은 부분을 제안하기 때문에 필수적으로 사용하시는게 좋습니다.

1. [개발 환경 구축](https://tango1202.github.io/javascript/javascript-config/)을 참고하여 다음을 설치합니다.
    * [Node.js(https://nodejs.org/)](https://nodejs.org/)
    * [Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)
    * [Visual Studio Code 익스텐션](https://tango1202.github.io/javascript/javascript-config/#visual-studio-code-%EC%9D%B5%EC%8A%A4%ED%85%90%EC%85%98)(*Code Runner, Live Server*)

2. 터미널에서 다음 명령을 입력하여 `ESlint`를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

    ```
    npm install -D eslint
    ```

    그러면 다음 파일들이 생성됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ff4d1596-668b-40dc-b0be-9255a98281e5)

3. `ESlint`의 환경을 설정합니다.

    ```
    npm init @eslint/config
    ```
    
    그러면 콘솔에서 다음과 같이 대화형으로 물어봅니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/281ae776-2dbb-4a91-a192-63e7ebb87290)

    위아래 화살표로 이동하며 원하는 항목에서 `Enter`하면 됩니다. 프로젝트 성격에 맞게 선택하시면 되며, 예제는 다음 설정으로 진행하겠습니다.

    |항목|내용|
    |--|--|
    |How would you like to use ESLint?|To check syntax, find problems, and enforce code style|
    |What type of modules does your project use?|JavaScript modules (import/export)|
    |Which framework does your project use?|None of these|
    |Does your project use TypeScript?|No|
    |Where does your code run?|Node|
    |How would you like to define a style for your project?|Use a popular style guide|
    |Which style guide do you want to follow?|Airbnb: https://github.com/airbnb/javascript|
    |What format do you want your config file to be in?|JavaScript|
    |Would you like to install them now?|Yes|
    |Which package manager do you want to use?|npm|

    그러면 다음과 같이 `.eslintrc.js`파일이 생성됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1df2b64a-517a-4cac-bfbe-ae8aaf0f2ea5)

4. Visual Studio Code 에서 `Activity Bar`의 `Extensions`을 클릭하여 `ESLint`를 설치합니다.

5. `test.js` 새파일을 만들고 테스트 코드를 입력합니다.

    ```javascript
    for (var i = 0; i < 3; ++i) {
        console.log("i값 : ", i);
    }
    ```

6. 이전과 다르게 소스코드에 붉은 밑줄과 노란 밑줄이 표시됩니다.    

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cc10c63d-a3d6-4f9c-9155-c6a5954e9a52)

    `PROBLEMS` 탭의 내용을 보고 수정하면 되며, 연관된 링크를 클릭하여 내용을 확인 할 수 있습니다.
    
    |항목|내용|
    |--|--|
    |All 'var' declarations must be at the top of the function scope.|`var`를 `let`으로 바꿉니다.|
    |Unary operator '++' used.|`i += 1`을 권장하는데요, 저같은 C++ 기반 개발자는 용납할 수 없죠. `.eslintrc.js`의 `rules`에 `'no-plusplus': ['error', { allowForLoopAfterthoughts: true }]`를 추가합니다.|
    |Expected linebreaks to be 'LF' but found 'CRLF'.|OS에 따라 편집기 설정이 다를 수 있는데요, Windows는 CRLF를 사용하니 설정을 변경합니다. `.eslintrc.js`의 `rules`에 `'linebreak-style': 0`를 추가합니다.|
    |Expected indentation of 2 spaces but found 4.|4칸 띄기를 했는데, 2칸 띄기로 바꿉니다.|
    |Strings must use singlequote.|문자열을 `'`로 바꿉니다.|
    |Unexpected console statement.|`.eslintrc.js`의 `rules`에 `''no-console': 'off'`를 추가합니다.|

    최종적으로 상기 예제에서의 `.eslintrc.js`는 다음과 같습니다.

    ```javascript
    module.exports = {
        env: {
            browser: true,
            es2021: true,
        },
        extends: 'airbnb-base',
        overrides: [
            {
            env: {
                node: true,
            },
            files: [
                '.eslintrc.{js,cjs}',
            ],
            parserOptions: {
                sourceType: 'script',
            },
            },
        ],
        parserOptions: {
            ecmaVersion: 'latest',
            sourceType: 'module',
        },
        rules: {
            'no-plusplus': ['error', { allowForLoopAfterthoughts: true }],
            'linebreak-style': 0,
            'no-console': 'off',
        },
    };
    ```

# Prettier

[Prettier](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#prettier)는 코드 레이아웃을 일관되게 작성할 수 있도록 도와주는 도구입니다. [ESLint](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#eslint)와 함께 사용하며, 파일 저장시에 자동 포맷팅을 지원합니다.

다만, [ESLint](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#eslint)의 스타일 설정과 [Prettier](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#prettier)의 설정이 충돌이 날 수 있기 때문에, `eslint-config-prettier`를 함께 설치하고, `Visual Studio Code`에서 복잡한 환경 설정을 해주어야 합니다.

1. [개발 환경 구축](https://tango1202.github.io/javascript/javascript-config/)을 참고하여 다음을 설치합니다.

    * [Node.js(https://nodejs.org/)](https://nodejs.org/)
    * [Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)
    * [Visual Studio Code 익스텐션](https://tango1202.github.io/javascript/javascript-config/#visual-studio-code-%EC%9D%B5%EC%8A%A4%ED%85%90%EC%85%98)(*Code Runner, Live Server*)

2. [ESLint](https://tango1202.github.io/javascript/javascript-strict-eslint-prettier/#eslint)를 참고하여 설치합니다.

3. 터미널에서 다음 명령을 입력하여 `prettier`와 `eslint-config-prettier`를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

    ```
    npm install -D prettier eslint-config-prettier
    ```

4. 프로젝트 root에 `.prettierrc` 파일을 생성하고 다음과 같이 코드 레이아웃 규칙을 작성합니다.

    ```javascript
    {
        "singleQuote": true,
        "semi": true,
        "useTabs": false,
        "tabWidth": 2,
        "trailingComma": "all",
        "printWidth": 80,
        "bracketSpacing": true,
        "arrowParens": "always",
        "endOfLine": "auto"
    }
    ```

5. `Visual Studio Code` 에서 `Activity Bar`의 `Extensions`을 클릭하여 `Prettier - Code formatter`를 설치합니다.

6. 프로젝트 루트 폴더에 `.vscode`폴더를 만든뒤 `settings.json`파일을 만들고,(*Live Server나 기타 다른 세팅에 따라 이미 있을 수 있습니다.*) 다음 내용을 작성하여 파일 저장시 `prettier` 규칙으로 변경되게 합니다. (*`File/Preferences/Settings`에서 수정하면 모든 프로젝트에 반영될 수 있으니 현재 프로젝트에만 반영될 수 있도록 `.vscode`폴더에 작성하시는걸 추천합니다.*)

    ```javascript
    {
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.fixAll.eslint": "explicit"
        },
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
    ```

7. 이제 `javascript`코드를 작성하고 저장하면 자동으로 `prettier` 규칙이 적용됩니다.

