---
layout: single
title: "#1. [Typescript] 개발 환경 구축"
categories: "typescript"
tag: ["typescript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

자바스크립트는 동적 타입 언어이다보니 타입이 유연하다는 장점은 있지만 타입에 따른 코딩 계약을 맺기가 쉽지 않아 큰 프로젝트에서는 디버깅을 하기가 좀 어렵습니다. 

타입스크립트는 이런 단점을 보완하였습니다. 변수에 타입을 지정한뒤 컴파일 과정을 거쳐 자바스크립트 파일을 만들어 내므로 관련 오류를 사전에 감지할 수 있습니다.

타입스크립트로 작성된 코드는 `*.ts`파일로 저장되며, 트랜스파일러를 통해 자바스크립트인 `*.js`로 컴파일됩니다.(*ESScript5 기반입니다.*)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9f7ecb71-5a48-46e5-90b7-51ffe1e57415)

# node 프로젝트 생성

1. [개발 환경 구축](https://tango1202.github.io/javascript/javascript-config/)을 참고하여 다음을 설치합니다.
    * [Node.js(https://nodejs.org/)](https://nodejs.org/)
    * [Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)

2. 다음 명령을 이용하여 기본적인 `node.js`프로젝트를 생성합니다. 물어보는 것들은 기본적으로 `Enter`하여 지나갑니다.

    ```
    npm init
    ```

# 타입스크립트 설치

1. 타입스크립트를 자바스크립트로 변환하는 컴파일러를 설치합니다. 

    ```
    npm install typescript
    ```

2. 타입스크립트를 컴파일하지 않고 node에서 실행해주는 `ts-node`를 실행합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

    ```
    npm install -D ts-node
    ```

3. 프로젝트 루트에 `tsconfig.json`파일을 생성하고 다음을 입력합니다.(*`include`폴더에 파일이 없다며 오류가 나오면, VS Code를 재실행하세요.*) 

    ```json
    {
        "compilerOptions": {
            "strict": true,
            "module": "commonjs",
            "moduleResolution": "node",
            "lib": ["es2015","es2016","es2017","es2018","es2019","es2020"],
            "target": "ES5",
            "outDir": "./dist",
            "esModuleInterop": true
        },
        "exclude": ["node_modules"],
        "include": ["src/**/*"]
    }
    ```
# 타입스크립트 실행하기

1. `src`폴더를 만들고 다음 2개의 파일을 만듭니다.

    * `operator.ts` 파일을 작성합니다.

        ```typescript
        function plus(a: number, b: number) { // 타입스크립트는 인자에 타입명을 적습니다.
            return a + b;
        }
        function minus(a: number, b: number) {  // 타입스크립트는 인자에 타입명을 적습니다.
            return a - b;
        }

        export {plus, minus};  
        ```

    * `index.ts` 파일을 작성합니다.

        ```typescript
        import { plus, minus } from './operator'; 

        console.log('plus : ' + plus(1, 2));
        console.log('minus : ' + minus(1, 2)); 
        ```

2. 터미널에 다음을 입력하여 모든 `ts`파일을 `js`파일로 컴파일합니다.

    ```
    npx tsc
    ```

    그러면 `dist`폴더에 다음과 같이 `operator.js`와 `index.js`파일이 만들어 집니다.

    * dist/operator.js

        ```javascript
        "use strict";
        Object.defineProperty(exports, "__esModule", { value: true });
        exports.minus = exports.plus = void 0;
        function plus(a, b) {
            return a + b;
        }
        exports.plus = plus;
        function minus(a, b) {
            return a - b;
        }
        exports.minus = minus;
        ```
    * dist/index.js

        ```javascript
        "use strict";
        Object.defineProperty(exports, "__esModule", { value: true });
        var operator_1 = require("./operator");
        console.log('plus : ' + (0, operator_1.plus)(1, 2));
        console.log('minus : ' + (0, operator_1.minus)(1, 2));
        ```

3. 터미널에 다음을 입력하여 실행합니다.

    ```
    node dist/index.js
    ```

    그러면 다음과 같이 실행됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9a0b6888-e677-4351-9634-f3e8d91eb1ff)


4. `tsc`로 컴파일하지 않고 `*.ts`파일을 직접 실행할 수도 있습니다.

    ```
    npx ts-node src/index.ts
    ```

5. `package.json`의 `script`에 `"start": "tsc && node dist/index.js"`을 추가하여 `*.js` 컴파일과 실행을 한번에 할 수 있습니다.

    ```json
    "scripts": {
        "start": "tsc && node dist/index.js"
    }
    ```

    이제 다음과 같이 `npm start`를 실행하면, 컴파일과 실행을 한번에 할 수 있습니다.

    ```
    npm start
    ```

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6d9ed7f5-6dc2-43dc-8967-bffdaa3eb38e)

6. 옵션으로 `tsc -w`를 이용하면 `ts`파일 저장시마다 `js`파일을 컴파일합니다.

    ```
    npx tsc -w
    ```

    그러면 `ts`파일 저장시마다 컴파일하고 결과를 보여 줍니다.

    <img width="427" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/82a44402-9aeb-4b96-b2ff-820adbfa8824">

    다만 실행까지 시켜주지는 않습니다. 실행까지 하려면 `nodemon`을 이용하여야 합니다.

# nodemon 파일 저장시 실시간 실행

`nodemon`은 파일 저장을 실시간으로 감지하고 실행해주는 툴입니다. 이를 이용하면, 개발이 좀더 편리해 집니다.

1. `nodemon`을 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

    ```
    npm install -D nodemon
    ```

2. `package.json`의 `script`에 다음과 같이 `"dev": "nodemon src/index.ts"`을 추가하여 파일 저장시 실시간 실행이 되게 합니다.

    ```json
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "start": "tsc && node dist/index.js",
        "dev": "nodemon src/index.ts"
    },
    ```

    이제 다음과 같이 `npm run dev`를 하면, 파일 저장시마다 재실행 합니다.(*종료하려면 `Ctrl+C`를 합니다.*)

    ```
    npm run dev
    ```

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4e77f646-381a-4385-b2ec-cd3594d88927)

# ESLint 설치

타입스크립트에서는 예전에 `TSLint`를 사용했는데, 이제는 deprecate되어, `ESLint`를 사용합니다.

1. `ESLint`를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

    ```
    npm install -D eslint
    ```

2. `ESLint`의 환경을 설치합니다. 

    ```
    npm init @eslint/config
    ```

    이때 콘솔에서 대화형으로 물어볼때 꼭 타입스크립트를 선택해야 합니다. 그러면 `@typescript-eslint/eslint-plugin`, `@typescript-eslint/parser`, `eslint-plugin-import`등 타입스크립트 관련 모듈이 추가로 설치됩니다.

    |항목|내용|
    |--|--|
    |How would you like to use ESLint?|To check syntax, find problems, and enforce code style|
    |What type of modules does your project use?|JavaScript modules (import/export)|
    |Which framework does your project use?|None of these|
    |***Does your project use TypeScript?***|***Yes***|
    |Where does your code run?|Node|
    |How would you like to define a style for your project?|***Answer questions about your style***|
    |What format do you want your config file to be in?|JavaScript|
    |What style of indentation do you use?|Spaces|
    |What quotes do you use for strings?|Single|
    |What line endings do you use?|Windows|
    |Do you require semicolons?|Yes|
    |Would you like to install them now?|Yes|
    |Which package manager do you want to use?|npm|


3. 프로젝트 루트에 생성된 `.eslintrc`파일을 확인하면 다음과 같습니다.

    ```json
    module.exports = {
        'env': {
            'browser': true,
            'es2021': true
        },
        'extends': [
            'eslint:recommended',
            'plugin:@typescript-eslint/recommended'
        ],
        'overrides': [
            {
                'env': {
                    'node': true
                },
                'files': [
                    '.eslintrc.{js,cjs}'
                ],
                'parserOptions': {
                    'sourceType': 'script'
                }
            }
        ],
        'parser': '@typescript-eslint/parser',
        'parserOptions': {
            'ecmaVersion': 'latest',
            'sourceType': 'module'
        },
        'plugins': [
            '@typescript-eslint'
        ],
        'rules': {
            'indent': [
                'error',
                4
            ],
            'linebreak-style': [
                'error',
                'windows'
            ],
            'quotes': [
                'error',
                'single'
            ],
            'semi': [
                'error',
                'always'
            ]
        }
    };
    ```

4. Visual Studio Code 에서 `Activity Bar`의 `Extensions`을 클릭하여 `ESLint`를 설치합니다.

5. 잘 설치되었는지 확인하기 위해 들여쓰기 등을 수정해봅니다. 만약 `Lint` 메시지가 나오지 않는다면 Visual Studio Code를 재실행 해보세요.(*혹은 Ctrl+Shift+P 후 Reload Window를 하셔도 됩니다.*)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/33a6b498-7257-4f24-a91f-f57ad361d876)


# Prettier 설치

1. `prettier`, `eslint-config-prettier`, `eslint-plugin-prettier`를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

    ```
    npm install -D prettier eslint-config-prettier eslint-plugin-prettier
    ```

2. 프로젝트 root에 `.prettierrc` 파일을 생성하고 다음과 같이 코드 레이아웃 규칙을 작성합니다.

    ```json
    {
        "singleQuote": true,
        "semi": true,
        "useTabs": false,
        "tabWidth": 4,
        "trailingComma": "all",
        "printWidth": 80,
        "bracketSpacing": true,
        "arrowParens": "always",
        "endOfLine": "auto"
    }
    ```

3. `Visual Studio Code` 에서 `Activity Bar`의 `Extensions`을 클릭하여 `Prettier - Code formatter`를 설치합니다.

4. 프로젝트 루트 폴더에 `.vscode`폴더를 만든뒤 `settings.json`파일을 만들고,(*Live Server나 기타 다른 세팅에 따라 이미 있을 수 있습니다.*) 다음 내용을 작성하여 파일 저장시 `prettier` 규칙으로 변경되게 합니다. (*`File/Preferences/Settings`에서 수정하면 모든 프로젝트에 반영될 수 있으니 현재 프로젝트에만 반영될 수 있도록 `.vscode`폴더에 작성하시는걸 추천합니다.*)

    ```javascript
    {
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.fixAll.eslint": "explicit"
        },
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
    ```
5. 들여쓰기등을 수정하고 저장했을때 잘 반영되는지 확인합니다.

# Jest 설치

1. 터미널에서 다음 명령을 입력하여 `Jest`를 설치합니다. `import`등 ECMAScript를 사용하려면 `@babel/core`, `@babel/preset-env`, `babel-jest`, `@babel/preset-typescript`도 함께 설치되어야 합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*)  

    ```
    npm install -D jest @babel/core @babel/preset-env babel-jest @babel/preset-typescript
    ```

2. `package.json`에서 `scripts`의 `test`를 다음과 같이 `jest`로 수정합니다.

    ```json
    "scripts": {
        "test": "Jest",
        "start": "tsc && node dist/index.js",
        "dev": "nodemon src/index.ts"
    },
    ```

3. 타입스크립트로 작성된 테스트를 자바스크립트로 변환할 수 있도록 프로젝트 루트에 `.babelrc`파일을 생성하고 다음을 입력합니다.

    ```json
    {
        "presets": [
            ["@babel/preset-env", {"targets": {"node": "current"}}],
            "@babel/preset-typescript",
        ]
    }
    ```

# 테스트 하기

1. `test`폴더를 만들고, `operator.test.ts`파일을 작성합니다. `Jest`는 타입스크립트의 경우 테스트 파일이 꼭 `test.ts`로 끝나야 합니다.(*자바스크립트는 `test.js`*) 또한 `@jest/globals`를 `import`해야 합니다.

    ```typescript
    import { describe, expect, it } from '@jest/globals'; // Jest 모듈
    import { plus, minus } from '../src/operator'; // 테스트할 모듈. 확장자를 안붙여도 됩니다.

    describe('테스트입니다', () => {
        // 테스트 범주
        it('plus() 테스트', () => {
            // 테스트케이스
            expect(plus(1, 3)).toBe(3);
        });
        it('minus() 테스트', () => {
            // 테스트케이스
            expect(minus(1, 2)).toBe(-1);
        });
    });
    ```

2. 터미널에서 `npm test`를 입력하여 실행합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6ec0699f-bf15-4edf-bf39-262c1287cadf)

# Jest(Visual Studio Code Extensions) 사용하기

1. `Activity Bar`의 `Extensions`을 클릭하여 `Jest`를 설치합니다.

2. `Activity Bar`의 `Testing`을 클릭하면 `Jest`가 표시됩니다. UI를 통해 각 테스트를 별개로 실행할 수 있습니다. 만약 보이지 않는다면 Visual Studio Code 를 재실행 해보세요.(*혹은 Ctrl+Shift+P 후 Reload Window를 하셔도 됩니다.*)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/40bdcda7-a6fa-4e13-b88c-beac8b4fd059)

