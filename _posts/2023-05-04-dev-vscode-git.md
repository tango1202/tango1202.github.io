---
layout: single
title: "#4. [개발설정] Visual Studio Code 로 Git 사용하기"
categories: "dev-setting"
tag: ["개발설정", "Visual Studio Code", "Git", "Windows"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
Git 을 설치했다면 Visual Studio Code에서 `Panel(TERMINAL)`과 UI를 통해 Git을 사용할 수 있습니다.

# 사전 설치

하기 항목이 사전에 설치되어야 합니다.

|항목|내용|
|--|--|
|Visual Studio Code|[Visual Studio Code 설치](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#visual-studio-code-%EC%84%A4%EC%B9%98)|
|Visual Studio Code Extension(C/C++ Extension Pack)|[Visual Studio Code Extension 설치(C/C++ Extension Pack)](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#visual-studio-code-extension-%EC%84%A4%EC%B9%98cc-extension-pack)|
|MinGW-w64|[MinGW-w64 설치(GNU C++ 컴파일러)](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#mingw-w64-%EC%84%A4%EC%B9%98gnu-c-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC)|
|Git|[Git 설치](https://tango1202.github.io/dev-setting/dev-git/#git-%EC%84%A4%EC%B9%98)|

# 테스트 환경 구축

이전 따라하기의 하기 내용을 수행합니다.

1. [C++ 코드 작성과 빌드 구성](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#c-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%EA%B3%BC-%EB%B9%8C%EB%93%9C-%EA%B5%AC%EC%84%B1) 과 [C++ 속성 설정](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#c-%EC%86%8D%EC%84%B1-%EC%84%A4%EC%A0%95)에 따라 환경을 갖춥니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4ec139bc-e6b3-4183-92e2-d4090303a4bb)

# Git 초기화

1. Git을 commit 하기 위해선 `user.email`과 `user.name`설정이 필요합니다. 설정되지 않았다면, `Panel(TERMINAL)`에 하기와 같이 입력합니다.(`xxx@xxx.com`, `myname` 대신 본인 정보를 입력하셔야 합니다.)

    ```ini
    git config --global user.email "xxx@xxx.com"
    git config --global user.name "myname"
    ```

2. `Activity Bar/Source Control/Initialize Repository`을 실행합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/70cc9568-2997-45c5-9f79-f9b1b8cec90e)

3. 그러면 `Side Bar(SOURCE CONTROL)`이 표시되고, 폴더내의 파일들이 `Changes`에 표시됩니다. 이때 파일정보에 `U`가 표시됩니다.(Untracked 되었다는 표시입니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1da0a107-1f2b-41cb-817a-3c78e03f6759)

4. .vscode폴더의 json 파일들과 exe 파일은 관리할 필요가 없으므로, `.gitignore`파일에 작성하여 제외시킵니다. 파일 본문에 하기 내용을 입력하고 저장합니다.(`.gitignore`파일은 꼭 Git이 관리하는 root 폴더에 작성해야 합니다.)

    ```ini
    .vscode/
    *.exe
    ```

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f4a5f3b1-d268-48e8-bf28-4b3ee46c25d0)

5. `Side Bar(SOURCE CONTROL)`을 확인하면, 불필요한 파일들은 관리대상에서 제외되고, `.gitignore` 와 `main.cpp`만 Untracked로 관리되는 걸 확인할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2ef7af83-affe-433c-9e37-1fb0ec5db951)

# Git 기본 사용(add, commit)

1. 파일을 `add`(`staged`상태로 전환) 하기 위해서는 `+` 버튼을 클릭합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6d8f5115-ffca-4b90-aeb0-29d661aff624)

2. 그러면 `Staged Changes` 로 옮겨지고, 이후 `-` 버튼을 클릭하여 취소할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9f5f1c0c-cc31-47c6-a8fa-f51f46749bd8)

3. 상단 메시지를 입력하고 `Commit` 버튼을 클릭합니다.(메시지를 입력하지 않으면 commit 메시지 에디터가 표시됩니다.) 

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/3dead880-0176-4534-99d4-1b6ed0bcaf74)

4. `main.cpp` 도 `+`버튼을 클릭하여 `Staged Changes` 로 전환하고, `Commit`하고 나면, 하기와 같이 `Source Control`의 관리 대상 파일들이 모두 사라집니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/716c0a14-a9d0-47dc-ab52-b507b3416067)

# 수정 파일 비교

1. `main.cpp` 파일을 하기와 같이 수정하고 저장합니다. 이때 파일정보에 `M`이 표시됩니다.(Modified 되었다는 표시입니다.) 또한 `Side Bar(SOURCE CONTROL)`에서 `main.cpp` 파일을 선택하면, 수정된 코드를 비교하여 확인할 수 있습니다.(왼쪽은 commit된 파일, 오른쪽은 현재 Working Tree에서 수정된 파일입니다.)

    ```cpp
    #include <iostream>

    int main() {
        std::cout << "Hello World!!!" << std::endl;
        std::cout << "코드 추가" << std::endl;
        return 0;
    }
    ```

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/686aeb29-5718-4d91-b427-653074625925)

2. `Panel(TERMINAL)`에서도 `git status`를 실행하여 상태를 확인할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/630c2699-2015-43cf-91d2-04f28811f06b)

# Git Commit 되돌리기

1. `Side Bar(SOURCE CONROL)/...메뉴/Commit/Undo Last Commit`을 사용하면 직전 commit을 취소할 수 있습니다. `revert`처럼 이전 commit 상태로 전환하는게 아니라, commit 명령을 취소한 상태로 전환(staged로 상태 전환)하는 것이어서, 비교적 안전하고 깔끔하게 commit 관리를 할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/46162d79-2339-41fe-93f1-d76a456d0a29)

# GitLens 사용

1. `Side Bar(EXTENSIONS)` 에서 `GitLens`를 검색하여 `install` 합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c75a61e2-26d4-4ad1-8e36-238c43838895)

2. 그러면 `Side Bar(SOURCE CONTROL)` 에 하기와 같이 다양한 메뉴가 표시됩니다. `COMMITS`에서 `Show Commit Graph`를 실행하여, commit 상황을 확인 할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/201aade6-1faf-40d4-98ea-fa9df780038e)


# Git History 사용

1. `Side Bar(EXTENSIONS)` 에서 `Git History`를 검색하여 `install` 합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4f63420c-e52e-4fd2-9bcd-59c48543a589)

2. `View/Command Palette`(Ctrl+Shift+P) 를 실행하고, `Git View History(git log)`를 실행합니다. 그러면, 하기와 같이 commit 목록이 표시됩니다. 목록에서 특정 commit을 선택하면, commit된 파일 목록이 나타납니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/55f7eca5-9c7e-4647-9b91-10d3a8782d16)

3. 하기 메뉴를 통해 파일 내용 보기(`View`), 작업공간 파일(Working Tree)과 비교하기(`Workspace`)등을 수행할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8dc303f0-7244-449b-9c3f-53d62c44696e)




