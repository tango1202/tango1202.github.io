---
layout: single
title: "#2. [Javascript] VSCode 에서 디버깅"
categories: "javascript"
tag: ["javascript"]
author_profile: false
sidebar: 
    nav: "docs"
---

JavaScript Debugger (Nightly) 를 이용하면 VSCode 에서 직접 디버깅을 할 수 있습니다.

1. `Active Bar`의 `Extentions`을 클릭하여 `JavaScript Debugger (Nightly)`를 설치합니다.

    ![image](https://github.com/user-attachments/assets/addc7c89-c4e0-4c49-a548-a37bcbb8ed2b)

2. `Active Bar`의 `Run and Debug`를 클릭하고, `Side Bar`에서 `create a launch.json file`을 선택합니다.

    ![image](https://github.com/user-attachments/assets/edeec229-e4dd-473c-892a-df9b97ddb87d)

3.  개발 환경을 선택합니다. 

    ![image](https://github.com/user-attachments/assets/c056d780-fd0d-432a-899e-a2af88669ccc)

4. `Web app (Chrome)` 을 선택하면, 다음과 같이 기본적인 `launch.json`파일이 생성됩니다.

    ![image](https://github.com/user-attachments/assets/51ed7bb2-56e2-4cc9-9f42-c18b5a73117e)

5.  `launch.json`파일에서 `"url": "http://localhost:8080",`의 포트를 개발 환경에 맞게 수정합니다.(*제 경우는 `5500`입니다.*)

    `"url": "http://localhost:5500",`

6. `Side Bar`상단에서 `Launch Chrome against localhost` 를 클릭하여 디버깅을 시작합니다.

    ![image](https://github.com/user-attachments/assets/87938749-e061-46a6-9e0a-af0f9266e24b)
    
7. 중단점이 설정되어 있다면 VSCode 에디터 창에서 해당 위치가 표시되며, 상단 디버깅 툴바를 이용하여 `Step Over(F10)`, `Step Info(F11)` 등을 수행할 수 있습니다. 

    ![image](https://github.com/user-attachments/assets/1d643658-edfd-4bfc-9997-a0522d5ec3e2)