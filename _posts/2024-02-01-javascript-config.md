---
layout: single
title: "#1. [Javascript] 개발 환경 구축"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 브라우저 개발자 도구 이용하기

크롬에서 `F12`키를 눌러 개발자 도구를 표시합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b2ca48e6-b0e2-4d89-b1c6-62455ee0a8cd)

**요소**

`html`요소가 표시됩니다.

**콘솔**

간단한 자바스크립트의 결과를 확인할 수 있습니다. `console.log()` 로 출력한 정보가 표시될 뿐만 아니라, 다음처럼 `1 + 2`를 입력하여 간단한 명령문을 실행하고 결과를 확인할 수 있습니다. 여러줄 입력을 원할땐 `Shift + Enter`키를 입력하면 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/721ea82c-a871-4e3c-abe4-783df53d4eca)

**소스**

현 URL의 사이트 폴더 구조와 소스코드를 표시합니다.

# Node.js 이용하기

Node.js는 Chrome의 V8 자바스크립트 엔진을 이용한 런타임 환경입니다. 이를 이용하면 브라우저외 환경에서 자바스크립트를 실행할 수 있으며, 서버 애플리케이션등에 많이 사용됩니다.

1. [Node.js(https://nodejs.org/)](https://nodejs.org/)에서 다운로드 받고 설치합니다.

2. 다음처럼 잘 설치되었는지 버전을 확인합니다. `npm`은 `Node.js`를 설치할때 자동으로 설치되며, 웹페이지에서 패키지를 다운로드 받아 설치하는 역할을 합니다. 

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/27012aea-a2b0-4b46-873a-b47ab0cbdeb4)

3. `node`를 실행하면 가상환경(*REPL(Read Eval Point Loop)*)으로 코드를 직접 실행할 수 있습니다. 종료하려면 `Ctrl+C`를 2번 누릅니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7c1064ae-89f9-4a62-8d81-69cb6d860d78)

# Visual Studio Code 이용하기

1. [Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download) 에서 다운로드 받고 설치합니다.

2. `test.js` 새파일을 만들고 테스트 코드를 입력합니다.

    ```javascript
    for (var i = 0; i < 3; ++i) {
        console.log("i값 : ", i);
    }
    ```

3. 터미널에서 `node test`를 실행하여 실행 결과를 확인합니다.(*`node test.js`로 실행해도 됩니다.*)

<img width="690" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/f0e489f4-0818-49a9-aaf6-63d839b6208d">

# Visual Studio Code 익스텐션

**Code Runner**

`Code Runner`를 이용하면 `Ctrl+Alt+N`을 눌러 빠르게 실행 결과를 확인할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/72616616-780e-4846-b625-ce877698647d)

**Live Server**

`Live Server`를 이용하면 브라우저 실행을 좀더 편하게 할 수 있습니다.

1. `Activity Bar`의 `Extensions`을 클릭하여 `Live Server`를 설치합니다.

2. 그러면 상태 표시줄에 `Go Live` 버튼이 생깁니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4d8b20b0-471f-4ef7-9706-a35edb7b47ae)

3. `index.html`과 `test.js`를 작성합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1f172fb1-449f-480a-b15c-c0c2e8d1bae7)

4. `Go Live` 버튼을 클릭하면, 버튼 캡션이 `Port:XXXX`와 같이 변경되며, `index.html`이 서버로 로딩되어 브라우저에 표시됩니다.

5. 코드 수정후 저장하면 변경 내용이 즉시 반영되어 표시됩니다.

6. 서버를 종료하려면 `Port:XXXX`을 클릭합니다.