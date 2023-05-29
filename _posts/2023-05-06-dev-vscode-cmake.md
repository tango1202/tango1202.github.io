---
layout: single
title: "#6. [개발설정] Visual Studio Code 에서 CMake 환경 구성"
categories: "dev-setting"
tag: ["개발설정", "visual studio code", "CMake", "Windows"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
C++ 에서는 프로젝트를 구성하는 파일이 여러개 라면 Make 환경을 구축해야 해야 합니다. Makefile을 직접 작성하는 방법도 있지만, 손쉬운 유지관리를 위해 CMake를 사용하는 방법이 좋습니다. CMake를 사용하려면, CMake 설치와 Visual Studio Code Extensions에서 CMake, CMake Tools를 설치해야 합니다.

|항목|내용|
|--|--|
|CMake|Makefile 생성 지원|
|Visual Studio Extension의 CMake|에디터의 CMake intelliSence 지원, C/C++ Extension Pack 설치시 포함됨|
|Visual Studio Extension의 CMake Tools|빠른 시작, CMake 기반 빌드 환경, C/C++ Extension Pack 설치시 포함됨|

# 사전 설치

하기 항목이 사전에 설치되어야 합니다.

|항목|내용|
|--|--|
|Visual Studio Code|[Visual Studio Code 설치](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#visual-studio-code-%EC%84%A4%EC%B9%98)|
|Visual Studio Code Extension(C/C++ Extension Pack)|[Visual Studio Code Extension 설치(C/C++ Extension Pack)](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#visual-studio-code-extension-%EC%84%A4%EC%B9%98cc-extension-pack)|
|MinGW-w64|[MinGW-w64 설치(GNU C++ 컴파일러)](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#mingw-w64-%EC%84%A4%EC%B9%98gnu-c-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC)|
|Git|[Git 설치](https://tango1202.github.io/dev-setting/dev-git/#git-%EC%84%A4%EC%B9%98)|

# 테스트 환경 구축(Github 활용)

이전 따라하기를 참고하여 테스트 환경을 구축합니다. 

[[개발환경] Github를 원격 레포지토리로 사용하기](https://tango1202.github.io/dev-setting/dev-remote-github/)에서 `Github`에 소스코드를 push 해 두었으므로, 이를 pull 하여 수정하는 것으로 진행합니다.

1. 탐색기에서 `language_test` 폴더를 생성합니다.

2. Visual Studio Code 에서 `File/Open Folder` 를 실행하여 `language_test` 폴더를 엽니다.

3. [Git 초기화](https://tango1202.github.io/dev-setting/dev-git/#git-%EC%B4%88%EA%B8%B0%ED%99%94) 를 참고하여, `git config`로 이메일과 이름을 등록하고, `Side Bar(SOURCE CONTROL)/Initialize Repository` 를 실행합니다.

4. `Panel(TERMINAL)`에서 `git remote add origin [원격 레포지토리 주소]` 를 실행합니다.

5. `Panel(TERMINAL)`에서 `git pull origin master --allow-unrelated-histories` 를 실행합니다.

6. 그러면 `Github`에 있었던 `cpp/src/main.cpp`, `.gitignore`, `README.md` 파일이 로컬로 저장됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c2896b56-a7ee-459c-8e0b-61205788d64a)

7. `cpp/src/main.cpp` 파일을 하기와 같이 작성합니다.

    ```cpp
    #include <iostream>
    #include "Test.h"

    int main() {
        std::cout << "Hello World!!!" << std::endl;

        Test::Run(); // Test.cpp에 정의된 함수
        return 0;
    }
    ```

8. `cpp/src/Test.h` 파일을 생성하고, 하기와 같이 작성합니다.

    ```cpp
    #ifndef __Test_h
    #define __Test_h

    class Test {
    public:
        static void Run();
    };
    #endif // __Test_h
    ```

9. `cpp/src/Test.cpp` 파일을 생성하고, 하기와 같이 작성합니다.

    ```cpp
    #include "Test.h"
    #include <iostream>

    void Test::Run() {
        int a = 1; 
        int b = 2; 
        int c = a + b; 
        std::cout << "Call Run" << std::endl; 
        std::cout << c << std::endl; 
    }
    ```

10. `main.cpp` 를 활성화하고, `F5`를 눌러 디버깅을 하면, 하기와 같은 오류 메시지가 표시됩니다.(`.vscode/task.json` 파일도 만들어 집니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8bd2f61b-1ee0-4004-8245-f166ee5c52dc)

11. `Abort`를 누르고, `Panel(TERMINAL)`을 확인하면, 하기와 같이 `Test::Run()`이 정의되지 않았다는 메시지가 나옵니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/31afdfe8-6087-4367-9d60-fed0010bc4e8)

12. 이는 컴파일러가 `main.cpp` 만 컴파일하고, `Test.cpp`는 컴파일하지 않았기 때문입니다. CMake를 이용한 Make 환경을 구축해야 합니다.

# CMake 설치

1. 다음 링크에서 CMake를 다운로드 받습니다.
    [CMake(https://cmake.org/download/)](https://cmake.org/download/)

2. CMake `Path` 설정을 위해 `Install Options` 에서 `Add CMake to the system PATH for all users`를 선택합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/569c3e30-9287-4987-b870-3ec00f51ce19)

3. 설치 확인을 위해 `cmd` 에서 `cmake --version`를 입력하여 버전을 확인합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6e6a8cdf-d1e3-466f-9865-01035106afb2)

# Visual Studio Code Extension 설치(CMake, CMake Tools)

1. [Visual Studio Code Extension 설치(C/C++ Extension Pack)](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#visual-studio-code-extension-%EC%84%A4%EC%B9%98cc-extension-pack) 에서 `C/C++ Extension Pack`을 설치하면, `CMake`와 `CMake Tools` 가 함께 설치됩니다.

2. 설치되어 있지 않다면 `CMake`로 검색하여 설치하면 됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e5277471-2f38-4e72-8b85-e62f1624e0cc)

# CMake를 이용한 빌드 구성(Quick Start)

1. `View/Command Palette`(Ctrl+Shift+P) 를 실행하고, `CMake`로 검색합니다. 목록에서 `CMake:Quick Start`를 실행합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/73cf9be1-fafb-4768-8b2e-9415c2a4d266)

2. 하기 화면에서 컴파일러를 선택합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a854029a-5976-4c0f-b8df-2604d35ab850)

3. 혹은 `CMakeLists.txt`가 없다고 표시될 수도 있는데, 그냥 `Enter`하여 나타나는 `열기`대화상자를 그냥 닫고, 다시 `CMake:Quick Start` 를 실행합니다.(이러한 경우 아마도 `CMake:Reset` 이 필요할 수 있습니다. [CMake 초기화(Reset)](https://tango1202.github.io/dev-setting/dev-vscode-cmake/#cmake-%EC%B4%88%EA%B8%B0%ED%99%94reset))

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/61f39640-4001-48d4-bfef-a930662c832c)

4. 2의 단계나 3의 단계를 거치면 새 프로젝트의 이름을 요청합니다. `test_cmake`로 입력하고 `Enter` 합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/51ee2de0-9687-418e-9c6e-ad2b08d70cb0)

5. 실행 파일을 만들 것이므로 `Executable`을 선택합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a220d272-735b-472e-8d20-87ae08b18fbb)

6. 그러면 `build`폴더가 생성되고, `Source Control`에 `245`개의 수정이 있다고 표시되고, root 폴더에 `CMakeLists.txt`파일과 `main.cpp`가 생성됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/77798df4-f56d-48c7-8498-2483b98e0e89)

    |항목|내용|
    |--|--|
    |`build` 폴더|빌드 과정에 필요한 임의 생성 파일들|
    |245개의 수정항목|대부분 `build` 폴더 하위에 자동 생성된 파일들|
    |CMakeLists.txt|Makefile 생성을 위한 스크립트 파일|
    |main.cpp|CMake가 생성한 기본 cpp 파일|

7. 자동 생성된 `main.cpp`를 삭제합니다.(`cpp/src/main.cpp`를 삭제하면 안됩니다.)

8. `.gitignore`에 `build/`추가하여 Git 관리항목에서 제외합니다.

    ```ini
    .vscode/
    *.exe
    build/
    ```

9. 그러면 `245`개의 수정항목이 `5`개로 줄어듭니다. 

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cc12e571-fa5c-4b56-b2d9-e9ad190c2c49)

# CMakeLists.txt 작성

`CMakeLists.txt`파일은 `Makelist`파일로 변환된 후 `make`를 통해 빌드됩니다. `Quick Start`를 통해 자동 생성된 내용은 하기와 같습니다.(`#` 주석은 제가 붙인 것입니다.)

```CMake
cmake_minimum_required(VERSION 3.0.0) # 최소요구버전
project(test_cmake VERSION 0.1.0) # PROJECT_NAME 과 PROJECT_VERSION

include(CTest) # 단위 테스트인 CTest 포함
enable_testing() # 단위 테스트 활성화 시킴

add_executable(test_cmake main.cpp) # 빌드 대상 추가. <실행파일명> <소스파일명> <소스파일명>

set(CPACK_PROJECT_NAME ${PROJECT_NAME}) # 실행파일명
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION}) # 실행파일버전
include(CPack) # 기본적으로 build 경로에 실행파일을 생성함
```

CTest 는 사용하지 않고, 빌드대상은 `cpp/src/main.cpp`, `cpp/src/Test.cpp` 이므로 하기와 같이 코드를 수정합니다.

```CMake
cmake_minimum_required(VERSION 3.0.0) # 최소요구버전
project(test_cmake VERSION 0.1.0) # PROJECT_NAME 과 PROJECT_VERSION

add_executable(test_cmake cpp/src/main.cpp cpp/src/Test.cpp) # 빌드 대상 추가. [실행파일명] [소스파일명] [소스파일명]

set(CPACK_PROJECT_NAME ${PROJECT_NAME}) # 실행파일명
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION}) # 실행파일버전
include(CPack) # 기본적으로 build 경로에 실행파일을 생성함
```

# CMake 초기화(Reset)

`CMake:QuickStart` 시 가끔 하기 오류가 표시되며 `build` 폴더가 생성되지 않을 때가 있습니다. 이럴 경우  `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Reset CMake Tools Extension State(For troubleshooting)` 을 실행하고, `CMake:Quick Start` 를 재시도 합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cb176898-ed24-4e74-9192-d9e1965d72be)

# CMake 빌드 재구성(Configure)

일반적으로 `CMakefiles.txt`를 수정 후 저장하면 `Makefile`과 `build`폴더가 생성됩니다. 하지만, `build` 폴더를 깔끔하게 정돈하고 싶을때도 있고, `Makefile` 변환 과정에서 이전 잔재가 남아 빌드 오류가 발생할 수도 있습니다.

이럴때는 물리적으로 `build`폴더를 삭제하고, `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Configure` 를 실행하여 `Makefile`과 `build`폴더를 다시 만들 수 있습니다.

# CMake를 이용한 빌드, 디버깅

1. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Build`(F7)를 하면, 빌드되고 빌드 결과물인 `test_cmake.exe` 가 `build`폴더에 생성됩니다. `Panel(OUTPUT)`에는 빌드를 마쳤다는 메시지가 표시됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2e8f326d-1f1a-4df5-820f-23fee358304e)

2. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Run Without Debugging`(Shift+F5)를 하면, `test_cmake.exe`가 실행됩니다. `Panel(TERMINAL)`에 실행 메시지가 표시된걸 확인할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/504723ac-6760-41f6-9f00-6c728415b932)

3. `Run/Toggle breakpoint`(F9) 를 하면, 중단점이 설정됩니다.(혹은 코드에서 라인넘버 왼쪽의 중단점 표시영역을 클릭합니다.) `main.cpp`의 `Test::Run()` 에 중단점을 설정합니다. 중단점을 설정하면 라인 옆에 붉은색 점이 표시됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8c02303c-dd0f-46bc-b325-186908cffd29)

4. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Debug`(Ctrl+F5) 를 하면, 중단점 위치까지 실행되며, 좌측 `Side Bar(RUN AND DEBUG)`에서 변수값을 확인하고, 상단 디버깅 툴바에서 `Continue`(F5), `Step Over`(F10), `Step Into`(F11), `Step Out`(Shift+F11) 등을 이용하여 다음 지점의 중단점이나 다음 단계로 진입할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/170627ca-357e-49ca-a4b5-36239871fda4)

5. `Step Into`(F11) 을 하여 `Test::Run()`함수내에 진입하여, `VARIALBLES` 에서 현 함수의 변수 값을 확인하고, `WATCH`에서 보고싶은 변수나 수식을 입력하여 확인할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a1ba2bbc-ce2a-41fd-93b7-aa9b110cce88)

6. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Clean`을 하면, 빌드시에 생성 파일들을 모두 지우고 정리 합니다.

7. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Variant`를 하면, 빌드 방법 중 `Debug`, `Release`, `MinSizeRel`, `RelWithDebInfo` 를 선택할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e5b38dd1-f478-41cc-ad26-9409ed1b349b)

    |항목|내용|
    |--|--|
    |Debug|최적화 불포함. 디버그 정보 포함|
    |Release|최적화 포함. 디버그 정보 불포함|
    |MinSizeRel|크기에 맞게 최적화. 디버그 정보 불포함|
    |RelWithDebInfo|최적화 포함. 디버그 정보 포함|

# CMake 상태 표시줄

Visual Studio Code 하단의 `Status Bar`를 보면 CMake 관련 명령들이 제공됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c9b366bf-cd1b-4fec-a679-fcc94e579d6c)

1. ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/08926487-8753-49f7-ad64-95768136d77e) : `CMake:Variant` 와 동일합니다.

2. ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/19384004-ec0d-4fea-8fc5-3e4a7cc91e8c) : 컴파일러를 선택합니다.

3. ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/da42a03b-b0f4-4e23-95f6-963479bdd4e9) : `CMake:Build` 와 동일합니다.

4. ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f20c0cde-f741-4cfc-9e83-a617cf05bfeb) : `CMake:Debug` 와 동일합니다.

5. ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/05a89274-edfb-4e68-90a0-7ceec5b299a4) : `CMake:CMake:Run Without Debugging` 와 동일합니다.

# CMake 파일 구성 변경

1. 빌드 대상인 파일의 구성이 변경되면, `CMakelists.txt`파일의 `add_excutable()`에 파일 구성을 변경합니다.

    ```CMake
    add_executable(test_cmake 
        cpp/src/main.cpp 
        cpp/src/Test.cpp
        cpp/src/Test2.cpp
        cpp/src/Test3.cpp
    ) # 빌드 대상 추가. [실행파일명] [소스파일명] [소스파일명]
    ```
2. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Build`(F7)를 하여 다시 빌드합니다.



