---
layout: single
title: "#10. [React] 개발 환경 구축(vite, yarn)"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

리액트 개발 환경 구축시 [create-react-app](https://tango1202.github.io/react/react-config/)은 이것 저것 설치되어 좀 무거울 수 있습니다. 이에 요즘은 `vite`와 `yarn`을 이용하는 것 같네요.

|항목|내용|
|--|--|
|`vite`|빌드 도구로서, 수정된 모듈만 교체하여 브라우저에 전달하기 때문에 소스 코드 갱신 속도가 빠릅니다.|
|`yarn`|`npm`와 같은 패키지 관리 도구로서, 병렬로 패키지를 설치하기 때문에 설치 속도가 좀더 빠릅니다.|

이 둘을 이용하면, 기본적인 리액트 개발 환경을 손쉽게 구축할 수 있습니다.

# vite 를 이용한 타입스크립트용 리액트 프로젝트 생성

1. [개발 환경 구축](https://tango1202.github.io/javascript/javascript-config/)을 참고하여 다음을 설치합니다.
    * [Node.js(https://nodejs.org/)](https://nodejs.org/)
    * [VSCode(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)

2. `yarn` 을 설치합니다.(*`yarn`은 패키지 관리 도구 이므로 모든 프로젝트에서 사용할 수 있도록 `-g`옵션을 주어 글로벌로 설치합니다.*) 

    ```
    npm install -g yarn
    ```

3. `yarn` 으로 `vite` 프로젝트를 생성합니다.(이때 프로젝트를 하위 폴더로 생성하므로, 상위 폴더에서 명령을 실행합니다.)

    ```
    yarn create vite
    ```
    
4. 프로젝트 폴더명을 입력합니다.(*`react_vite_test` 로 입력하는 것으로 가정합니다.*)
    ![image](https://github.com/user-attachments/assets/360da808-5694-46ac-8372-da3d000052a1)

5. 그러면 프레임워크를 선택하는 목록이 표시되는데, 방향키와 `Enter`로 `React`를 선택합니다.

    ![image](https://github.com/user-attachments/assets/2e2350af-3478-4ba7-8ab9-54031f7101cd)

6. 마지막으로 다음과 같이 언어를 선택하는 목록이 표시되는데, `TypeScript`를 선택합니다.

    ![image](https://github.com/user-attachments/assets/7b629ed0-56b0-4858-9fb8-db7e277f358f)

7. 그러면 프로젝트 구성이 끝났다는 메시지가 표시됩니다.

    ![image](https://github.com/user-attachments/assets/cda6e368-8a75-4f0c-9095-cd15377901bd)

# yarn 을 이용한 종속 패키지 설치

프로젝트 구성이 끝나면, `cd react_vite_test` 로 프로젝트 폴더로 이동한 후, 다음 명령을 실행하여 `yarn`으로 종속 패키지를 설치합니다.

```
yarn install
```

![image](https://github.com/user-attachments/assets/86e58082-085e-45b7-b67b-5141cd9dd810)

# 실행하기

1. 브라우저 실행을 위해서는 먼저 서버 정보를 설정 해야 합니다.

    면저 `vite.config.ts`파일을 엽니다.

    ```typescript
    import { defineConfig } from 'vite'
    import react from '@vitejs/plugin-react'

    // https://vitejs.dev/config/
    export default defineConfig({
    plugins: [react()],
    })    
    ```

    다음과 같이 서버 정보를 설정합니다.

    ```typescript
    import { defineConfig } from 'vite'
    import react from '@vitejs/plugin-react'

    // https://vitejs.dev/config/
    export default defineConfig({
    server: {
        port: 3000,
        open: true,
        host: true,
    },
    plugins: [react()],
    })
    ```
2. 콘솔에서 `yarn dev`를 실행하면 브라우저에 실행 결과가 표시됩니다.

    ![image](https://github.com/user-attachments/assets/c09c2626-80d7-4a80-a3e4-637ca6a47ae9)

    기존에는 `npm start`와 같이 `start`를 사용했었는데요, `package.json`을 열어보면, 다음과 같이 `start`대신 `dev`가 정의되어 있어서 `yarn dev`로 실행합니다.

    ```json
    "scripts": {
        "dev": "vite",
        "build": "tsc -b && vite build",
        "lint": "eslint .",
        "preview": "vite preview"
    },
    ```

# ESLint

`ESLint`는 별다는 설치가 필요하지 않습니다. [ESLint(VSCode Extensions) 사용하기](https://tango1202.github.io/react/react-config/#eslintvscode-extensions-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0) 를 참고하여 `VSCode Extension`만 설치하면 됩니다.

# Prettier 설치

1. `prettier-eslint`를 설치합니다.(*`yarn`에서는 `add` 명령으로 설치합니다.*)

    ```
    yarn add prettier-eslint
    ```

2. 프로젝트 root에 `.prettierrc` 파일을 생성하고 다음과 같이 코드 레이아웃 규칙을 작성합니다.

    ```json
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

그외 `VSCode`에 설정하는 방법은 [Prettier 설치](https://tango1202.github.io/react/react-config/#prettier-%EC%84%A4%EC%B9%98)를 참고하기 바랍니다.