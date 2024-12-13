---
layout: single
title: "#11. [React] NginX로 웹서버 구축"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

`NginX`는 비동기 I/O를 사용함으로서 메모리 사용량이 적고, 빠른 응답을 보장하는 웹서버입니다. 아파치 보다 가벼워서 많이들 사용하는 듯 합니다.

# Windows 설치

1. [https://nginx.org/en/download.html](https://nginx.org/en/download.html) 에서 안정화된 버전을 다운로드 합니다.

    ![image](https://github.com/user-attachments/assets/3702834d-4a6e-405c-894a-b9d24377dc94)

2. 설치하려는 경로에 압축을 풉니다.

3. 해당 경로에서 `nginx.exe`를 실행합니다.

    ![image](https://github.com/user-attachments/assets/88a89425-63f4-4d1a-a904-b04c0a1c471d)

4. 뭔가 순식간에 팝업이 나타나고 사라집니다.(*실행된 것입니다. 작업 관리자의 프로세스를 확인하세요.*)

5. 브라우저의 주소창에 `127.0.0.1`에 접근하면, `nginx`의 기본 index.html이 웹 서비스 되는 것을 확인 할 수 있습니다.(*`nginx.exe`설치 폴더 하위의 `html/index.html`이 표시됩니다.*)

    ![image](https://github.com/user-attachments/assets/0053705b-22d4-448c-b433-f85531328a6f)

# 종료

작업 관리자에서 프로세스를 강제 종료하거나, 관리자 모드로 `cmd` 를 실행후 `nginx.exe` 가 설치된 폴더에서 다음을 실행합니다.

```
nginx.exe -s stop
```

# 웹 서비스 설정

1. 먼저 웹 서비스 할 내용을 준비하기 위해 [개발 환경 구축(vite, yarn)](https://tango1202.github.io/react/react-config-vite/) 의 sample 코드를 빌드합니다.

    ```
    yarn build
    ```

    그러면 다음과 같이 `dist`폴더가 생성되는데요,

    ![image](https://github.com/user-attachments/assets/3dba0e8f-dc07-4394-8ec0-41066c5716af)

    해당 폴더에는 루트 페이지인 `index.html`과 자바스크립트와 css가 번들링된 `assets` 폴더가 있습니다.

    ![image](https://github.com/user-attachments/assets/afbd47c9-b3b5-4096-ae60-074233a8af3e)


2. `nginx.exe` 가 설치된 폴더 하위의 `conf/nginx.conf` 파일을 엽니다.

3. 다음 server 부분을 

    ```python
    server {
        listen       80; # 사용하는 포트
        location / {
            root   html; # 웹 서비스 루트
            index  index.html index.htm;
        }
    }
    ```

    다음과 같이 `listen`의 포트와 `root`의 웹 서비스 루트 경로를 수정합니다.

    ```
    server {
        listen       4000; # 사용하는 포트
        location / {
            root   C:/Personal/react_vite_test/dist; # 웹 서비스 루트
            index  index.html index.htm;
        }
    }
    ```

4. 그러면 브라우저로 `127.0.0.1:4000`에서 웹 서비스가 되는 걸 확인할 수 있습니다.

    ![image](https://github.com/user-attachments/assets/fb8b9c7d-1c5c-483b-a9ec-50f1112bb676)

# Windows 서비스 설정

1. Windows 서비스 설정 유틸리티인 [wssm(https://nssm.cc/download)](https://nssm.cc/download)을 다운로드 받습니다.

    ![image](https://github.com/user-attachments/assets/189d81ac-516c-4ec8-8efd-81fe77f06029)

2. 설치하려는 경로에 압축을 풉니다.

3. 관리자 모드로 `cmd` 를 실행후 해당 경로 하위 폴더인 `win64`에서 `nssm install`를 실행합니다.

    ```
    C:\WebService\nssm-2.24\win64>nssm install
    ```

    그러면 다음과 같이 설정 화면이 나타나는데요, `nginx.exe`를 선택하고, `Service name`을 지정한뒤 `Install service`버튼을 클릭합니다.

    ![image](https://github.com/user-attachments/assets/1e1ada00-c6ab-4b74-a34f-56e1b9dec09c)

4. `services.msc`를 실행하여 Windows 서비스 상태를 확인합니다. 그러면 다음과 같이 `NginX`가 `자동`으로 등록되어 있는 것을 확인할 수 있습니다. 시작 유형이 `자동`이므로 시스템 재시작시 항상 `NginX`가 실행되어 웹 서비스가 시작됩니다.

    ![image](https://github.com/user-attachments/assets/fb273bbd-c680-4f62-a693-7b5ad50d67c9)
