---
layout: single
title: "#1. [React] 개발 환경 구축"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

리액트는 사용자 컴포넌트를 작성하고, 이를 조합하여 UI를 구성하는 UI 프레임워크입니다. `JSX`를 이용하여 `HTML`엘리먼트와 자바스크립트를 손쉽게 연동할 수 있고, 내부적으로 가상 돔을 사용하여 렌더링을 최소화해 줍니다. 

# create-react-app을 이용한 타입스크립트용 리액트 프로젝트 생성

 `create-react-app`은 기본적인 툴들을 설치하고 설정하는 툴 체인입니다. 이를 활용하면 손쉽게 리액트 프로젝트를 세팅할 수 있습니다.

1. [개발 환경 구축](https://tango1202.github.io/javascript/javascript-config/)을 참고하여 다음을 설치합니다.
    * [Node.js(https://nodejs.org/)](https://nodejs.org/)
    * [Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)

2. `create-react-app`을 실행합니다.

    다음과 같이 `create-react-app`을 실행하여 `react_test` 폴더를 만들고 타입스크립트용 프로젝트를 생성합니다. 하위 폴더에 만드므로, 상위 폴더에서 다음 명령을 실행합니다.

    ```
    npx create-react-app react_test --template typescript
    ```

    그러면 다음과 같이 샘플 프로젝트가 생성됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d0fba9ab-f0eb-41fd-8fd7-9ceefcd99c5b)

# 리액트 실행하기

1. `cd react_test`로 폴더를 이동한 후 `npm start`로 샘플 프로젝트를 실행합니다.(*종료하려면 콘솔에서 `Ctrl+C`를 합니다.*) 

    그러면 다음과 같이 `localhost:3000`을 이용하여 브라우저에 샘플 프로그램을 실행합니다. 

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/21c824a2-4edb-4295-995b-11b0bb8da42a)

2. 프로그램 진입점인 `public/index.html`를 엽니다. `<body>` 부분에 `<div id="root">`가 있는데, 이 부분에 리액트 컴포넌트를 표시하게 됩니다.

    ```html
    <!doctype html>
    <html lang="en">
        <head>
            <meta charset="utf-8" />
            <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
            <meta name="viewport" content="width=device-width, initial-scale=1" />
            <meta name="theme-color" content="#000000" />
            <meta
                name="description"
                content="Web site created using create-react-app"
            />
            <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
            <!--
            manifest.json provides metadata used when your web app is installed on a
            user's mobile device or desktop. See https://developers.google.com/web/fundamentals/web-app-manifest/
            -->
            <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
            <!--
            Notice the use of %PUBLIC_URL% in the tags above.
            It will be replaced with the URL of the `public` folder during the build.
            Only files inside the `public` folder can be referenced from the HTML.

            Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
            work correctly both with client-side routing and a non-root public URL.
            Learn how to configure a non-root public URL by running `npm run build`.
            -->
            <title>React App</title>
        </head>
        <body>
            <noscript>You need to enable JavaScript to run this app.</noscript>
            <div id="root"></div>
            <!--
            This HTML file is a template.
            If you open it directly in the browser, you will see an empty page.

            You can add webfonts, meta tags, or analytics to this file.
            The build step will place the bundled scripts into the <body> tag.

            To begin the development, run `npm start` or `yarn start`.
            To create a production bundle, use `npm run build` or `yarn build`.
            -->
            </body>
    </html>

    ```

3. `src/index.tsx`파일을 엽니다. `root`로 되어 있는 요소를 찾아 `<App>`컴포넌트를 표시하는 역할을 합니다.

    ```typescript
    import React from 'react';
    import ReactDOM from 'react-dom/client';
    import './index.css';
    import App from './App';
    import reportWebVitals from './reportWebVitals';

    const root = ReactDOM.createRoot(
        document.getElementById('root') as HTMLElement
    );
    root.render(
        <React.StrictMode>
            <App />
        </React.StrictMode>
    );

    // If you want to start measuring performance in your app, pass a function
    // to log results (for example: reportWebVitals(console.log))
    // or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
    reportWebVitals();
    ```
4. `App.tsx`파일을 엽니다. `logo`를 표시하고 간단한 메시지와 `Learn React`라는 링크를 표시하고 있습니다.

    ```typescript
    import React from 'react';
    import logo from './logo.svg';
    import './App.css';

    function App() {
        return (
            <div className="App">
                <header className="App-header">
                    <img src={logo} className="App-logo" alt="logo" />
                    <p>
                        Edit <code>src/App.tsx</code> and save to reload.
                    </p>
                    <a
                        className="App-link"
                        href="https://reactjs.org"
                        target="_blank"
                        rel="noopener noreferrer"
                    >
                        Learn React
                    </a>
                </header>
            </div>
        );
    }

    export default App;
    ```

5. `Edit <code>src/App.tsx</code> and save to reload.` 대신 아무 텍스트나 입력하고 저장하면, 바로 반영되는 것을 확인할 수 있습니다. 


# 리액트 테스트 환경

`create-react-app`을 이용하면 `React Testing Library`와 `Jest`가 함께 설치됩니다. 

1. `package.json`을 엽니다. 

    `@testing-library/jest-dom`, `@testing-library/react`, `@testing-library/user-event`이 설치되어 있는 것을 확인할 수 있으며 `script`에 `"test": "react-scripts test"`가 작성되어 있는 걸 확인할 수 있습니다.

    ```json
    {
        "name": "react_test",
        "version": "0.1.0",
        "private": true,
        "dependencies": {
            "@testing-library/jest-dom": "^5.17.0",
            "@testing-library/react": "^13.4.0",
            "@testing-library/user-event": "^13.5.0",
            "@types/jest": "^27.5.2",
            "@types/node": "^16.18.82",
            "@types/react": "^18.2.56",
            "@types/react-dom": "^18.2.19",
            "react": "^18.2.0",
            "react-dom": "^18.2.0",
            "react-scripts": "5.0.1",
            "typescript": "^4.9.5",
            "web-vitals": "^2.1.4"
        },
        "scripts": {
            "start": "react-scripts start",
            "build": "react-scripts build",
            "test": "react-scripts test",
            "eject": "react-scripts eject"
        },
        "eslintConfig": {
            "extends": [
                "react-app",
                "react-app/jest"
            ]
        },
        "browserslist": {
            "production": [
                ">0.2%",
                "not dead",
                "not op_mini all"
            ],
            "development": [
                "last 1 chrome version",
                "last 1 firefox version",
                "last 1 safari version"
            ]
        }
    }
    ```

2. `src`에서 `App.test.tsx`파일을 엽니다.

    1. `<App>`컴포넌트를 `render()`함수에 전달하고, 
    2. `screen.getByText()`함수로 `<App>`컴포넌트에서 대소문자 구분없이(*`//i`는 정규표현식입니다.*) `learn react`가 있는 요소를 찾습니다. 
    3. 그리고 찾아낸 요소가 문서내에 있는지 확인합니다. 

    ```typescript
    import React from 'react';
    import { render, screen } from '@testing-library/react';
    import App from './App';

    test('renders learn react link', () => {
        render(<App />);
        const linkElement = screen.getByText(/learn react/i);
        expect(linkElement).toBeInTheDocument();
    });
    ```

3. `setupTests.ts`파일을 엽니다.

    다음과 같이 `Jest`를 `import`하고 있습니다.

    ```typescript
    // jest-dom adds custom jest matchers for asserting on DOM nodes.
    // allows you to do things like:
    // expect(element).toHaveTextContent(/react/i)
    // learn more: https://github.com/testing-library/jest-dom
    import '@testing-library/jest-dom';
    ```

# 리액트 테스트 하기

1. 터미널에서 `npm test`를 입력하여 실행합니다.

    그러면 다음과 같이 변경된 테스트가 없다며 추가 실행 옵션 안내가 나옵니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/be1f9fdd-e08e-483f-a33f-68a80a44a855)

    `a`를 입력하여 모든 테스트를 다시 실행합니다. 그러면 `App.test.tsx`가 테스트 됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/14fff0c1-25f2-4531-b25f-e34765561758)

2. `Ctrl+C`를 눌러 종료합니다.

3. `src`하위에 `test`폴더를 만들고 `operator.test.ts`파일을 작성하고 저장합니다.(*`src`폴더 하위에 만들어야 테스트파일이 인식됩니다. 좀더 알아봐야 할것 같습니다.*)

    이때 타입스크립트에서 에러가 발생할 수 있으므로 파일 하단에 `export {};`를 추가합니다.

    ```typescript
    function plus(a: number, b: number) {
        return a + b;
    }
    function minus(a: number, b: number) {
        return a - b;
    }

    describe('테스트입니다', () => {
        it('plus() 테스트', () => {
            expect(plus(1, 2)).toBe(3);
        });
        it('minus() 테스트', () => {
            expect(minus(1, 2)).toBe(-1);
        });
    });

     // 타입스크립트의 cannot be compiled under '--isolatedModules' 에러 회피용
     export {};
    ```

3. 터미널에서 `npm test`를 입력하여 실행합니다.

    그러면 `operator.test.ts`를 테스트 합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/aadd5578-318d-4ada-80e7-525633f7bf6a)

    전체를 다시 테스트 하고 싶으면 `a`를 입력합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f50bb593-bb9f-42b5-9873-bce2991e48e1)

# Jest(Visual Studio Code Extensions) 사용하기

1. `Activity Bar`의 `Extensions`을 클릭하여 `Jest`를 설치합니다.

2. `Activity Bar`의 `Testing`을 클릭하면 `Jest`가 표시됩니다. UI를 통해 각 테스트를 별개로 실행할 수 있습니다. 만약 보이지 않는다면 Visual Studio Code 를 재실행 해보세요.(*혹은 Ctrl+Shift+P 후 Reload Window를 하셔도 됩니다.*)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5035e4f6-e573-40f0-ab58-9ae7b7cf6075)


# ESLint(Visual Studio Code Extensions) 사용하기

`create-react-app`을 이용하면 `ESLint`가 함께 설치됩니다.  

`package.json` 파일을 보면 다음과 같이 `ESLint`관련 설정이 있는 걸 확인할 수 있습니다.

```json
"eslintConfig": {
    "extends": [
        "react-app",
        "react-app/jest"
    ]
},
```

1. Visual Studio Code 에서 `Activity Bar`의 `Extensions`을 클릭하여 `ESLint`를 설치합니다.

2. 잘 설치되었는지 확인하기 위해 `const testVal = 0;`을 추가하고 밑줄 표시가 나오면 마우스로 hover해 봅니다. 만약 `Lint` 메시지가 나오지 않는다면 Visual Studio Code를 재실행 해보세요.(*혹은 Ctrl+Shift+P 후 Reload Window를 하셔도 됩니다.*)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/588af30d-989f-45ef-b6fa-b780a7282d99)

# Prettier 설치

1. `prettier-eslint`를 설치합니다.

```
npm install -D prettier-eslint
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

