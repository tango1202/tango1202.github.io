---
layout: single
title: "#5. [개발설정] Github를 원격 레포지토리로 사용하기"
categories: "dev-setting"
tag: ["개발설정", "Visual Studio Code", "Git", "Github", "Windows"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
Github는 Git으로 소스코드를 관리하는 원격 저장소 서비스 입니다. Git이 설치되어 있어야 하며, Github에 회원가입을 해야 사용할 수 있습니다.

# 사전 설치

하기 항목이 사전에 설치되어야 합니다.

|항목|내용|
|--|--|
|Git|[Git 설치](https://tango1202.github.io/dev-setting/dev-git/#git-%EC%84%A4%EC%B9%98)|

# 테스트 환경 구축

이전 따라하기의 하기 내용을 수행합니다.

1. [C++ 코드 작성과 빌드 구성](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#c-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%EA%B3%BC-%EB%B9%8C%EB%93%9C-%EA%B5%AC%EC%84%B1) 과 [C++ 속성 설정](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#c-%EC%86%8D%EC%84%B1-%EC%84%A4%EC%A0%95)에 따라 환경을 갖춥니다.

2. [Git 초기화](https://tango1202.github.io/dev-setting/dev-git/#git-%EC%B4%88%EA%B8%B0%ED%99%94)를 진행합니다.

3. [Git 기본 사용(add, commit)](https://tango1202.github.io/dev-setting/dev-git/#git-%EA%B8%B0%EB%B3%B8-%EC%82%AC%EC%9A%A9add-commit)를 진행하여 2개의 commit을 추가합니다.

# Github 환경 구축

1. [Github(https://github.com)](https://github.com)에서 회원가입을 합니다.

2. 기본 레포지토리를 생성합니다.(`Repository name`과 `Public` 설정 후 `Create Repository` 버튼을 클릭합니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ae429ad1-e0fe-4b91-b0c6-091dfa9b88de)

3. 그러면 원격 레포지토리 주소와 원격 레포지토리에 push 하는 방법이 소개됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/aad4968e-1822-4791-9ae8-9d416d8ded31)

4. 테스트를 위한 파일을 생성하기 위해 `creating a new file`을 클릭합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/97af9cf1-4b0f-460b-80a9-03b0b6eac8ff)

5. 파일명을 `README.md`로 입력하고 `Github 테스트 입니다.`라고 입력 후 `Commit changes`를 클릭합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e7da038a-1268-46b9-8b8d-0db318b3f91e)

6. 그러면 `Commit changes` 대화상자가 표시됩니다. 추가할 메시지를 입력하고 `Commit changes`를 클릭합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c8c830f3-db41-42c3-9228-0744125e8a35)

7. 레포지토리의 `Code` 탭에서 기존 소개 내용은 사라지고, 추가한 `README.md` 파일이 표시됩니다.
    
    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cee757ae-ff2d-4d82-9e5e-c0561901a134)

# Github 연결

1. Github에서 자동으로 생성하는 레포지토리는 `main`으로 설정되어 있기에 이를 `master`로 변경해 줍니다. `Settings` 탭의 `Default branch`를 수정하시면 됩니다.
    
    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d3dd972a-fa37-40c4-a509-6e0daf25ba65)

2. `Code`탭의 `Code` 버튼에서 원격 레포지토리 주소를 확인합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/828d9fec-76bf-4e67-8b6f-0cacdf3ccc1b)

3. 터미널에서 하기 코드를 입력하여 원격지 레포지토리를 `origin`으로 설정합니다.(일반적으로 원격지의 이름은 origin 으로 설정합니다.)

    ```ini
    git remote add origin [원격 레포지토리 주소]
    ```

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/eae6c1f6-c7f9-42fa-b5ba-82c48ee40bc9)

4. `git remote`로 원격지 이름을 확인합니다.
    
    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/02bdd833-a818-4d73-b1e8-3eacdb5c4068)

# 원격지 레포지토리에서 가져오기(pull)

1. 원격지와 연동하기 전에 원격지의 브랜치명과 다르다면 다음과 같이 원격지 브랜치명과 로컬 브랜치 명을 먼저 맞춰 주는게 좋습니다. 다음 예는 로컬 브랜치 명(main)을 원격지 브랜치명(master) 로 변경하는 예입니다.

    ```ini
    git branch -m main master
    ```

2. 일반적으로 원격지에 `push`하기 전에 원격지의 내용을 `pull`하는 것이 좋습니다.(Working Tree에 작업중인 것이 있다면 미리 commit하는 것이 좋습니다.) `--allow-unrelated-histories`은 서로 관련 기록이 없는 이질적인 프로젝트를 병합할때 사용합니다. 최초에만 1회 사용합니다.   

    ```ini
    git pull origin master --allow-unrelated-histories
    ```
3. 상기 `pull` 명령을 실행하면 다음과 같이 Github 로그인을 요청합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d998f293-3982-4920-a209-02735052ae07)

4. `Sign in with your browser` 를 선택하고 로그인을 진행합니다.(세션이 끊어지기 전까지 1회만 진행하면 됩니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/bdf1a08e-837e-4f44-8090-65c3804f459b)

5. `pull`이 완료되면 다음과 같이 메시지가 나타납니다. 가장 하단에 보면 Github에서 작성한 `README.md`가 생성되었다는 메시지가 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d984b81d-7283-40c8-a699-bb18f4806942)

# 원격지 레포지토리로 내보내기(push)

1. `push` 전에 Github의 레포지토리를 확인합니다. 테스트 환경 구축을 위해 생성한 `README.md` 파일만 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f8d5569a-e014-411b-b66c-50da80466e0f)

2. 터미널에서 하기 코드를 입력하여 로컬 내용을 원격지 master 에 `push`합니다.(`pull`의 경우에서처럼 Github 로그인을 요청합니다.)
  
    ```ini
    git push origin master
    ```

    혹은

    ```ini
    git push --set-upstream origin master
    ```

    을 합니다.(`-set-upstream` 을 사용하면 로컬에서 원격저장소를 tracking 하기 때문에, 이후로는 `git push`만 하면 됩니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5f93f824-817a-4f93-8c1c-f46c70ca2528)

3. 다시 Github의 레포지토리를 확인하면, `source_code` 폴더의 파일들과 `.gitignore`가 추가된 것을 확인할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/880a89d1-6fc2-49c7-8f1a-bac970e840ff)




