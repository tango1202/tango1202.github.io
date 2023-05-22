---
layout: single
title: "#6. [개발설정] Visual Studio Code 에서 CMake 환경 구성"
categories: "dev-setting"
tag: ["개발설정", "visual studio code", "cmake", "Windows"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
C++ 를 구성하는 파일이 1개 밖에 없으면 상관없지만, 여러개의 파일로 구성되었다면 Make 환경을 구축해야 해야 합니다. Makefile을 직접 작성하는 방법도 있지만, 손쉬운 유지관리를 위해 CMake를 사용하는 방법이 좋습니다. CMake를 사용하려면, CMake 설치와 Visual Studio Code Extensions에서 CMake, CMake Tools를 설치해야 합니다.

|항목|내용|
|--|--|
|CMake|Makefile 생성 지원|
|Visual Studio Extension의 CMake|에디터의 CMake intelliSence 지원, C/C++ Extension Pack 설치시 포함됨|
|Visual Studio Extension의 CMake Tools|빠른 시작, CMake 기반 빌드 환경, C/C++ Extension Pack 설치시 포함됨|

# 테스트 환경 구축(Github 활용)
이전 따라하기를 참고하여 테스트 환경을 구축합니다. 

[[개발환경] Github를 원격 레포지토리로 사용하기](https://tango1202.github.io/dev-setting/dev-remote-github/)에서 `Github`에 소스코드를 `push`해 두었으므로, 이를 `pull`하여 수정하는 것으로 진행합니다.

1. 탐색기에서 `language_test` 폴더를 생성합니다.
2. Visual Studio Code 에서 `File/Open Folder` 를 실행하여 `language_test` 폴더를 엽니다.
3. [Git 초기화](https://tango1202.github.io/dev-setting/dev-git/#git-%EC%B4%88%EA%B8%B0%ED%99%94) 를 참고하여, `git config`로 이메일과 이름을 등록하고, `Source Control/Initialize Repository` 를 실행합니다.
4. `TERMINAL`에서 `git remote add origin [원격 레포지토리 주소]` 를 실행합니다.
5. `TERMINAL`에서 `git pull origin master --allow-unrelated-histories` 를 실행합니다.
6. 그러면 `Github`에 있었던 `cpp/src/main.cpp`, `.gitignore`, `README.md` 파일이 로컬로 저장됩니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c2896b56-a7ee-459c-8e0b-61205788d64a)

7. cpp/src/main.cpp 파일을 하기와 같이 작성합니다.
```cpp
#include <iostream>
#include "Test.h"

int main() {
    std::cout << "Hello World!!!" << std::endl;

    Test::Run(); // Test.cpp에 정의된 함수
    return 0;
}
```
8. cpp/src/Test.h 파일을 생성하고, 하기와 같이 작성합니다.
```cpp
class Test {
public:
    static void Run();
};
```
9. cpp/src/Test.cpp 파일을 생성하고, 하기와 같이 작성합니다.
```cpp
#include "Test.h"
#include <iostream>

void Test::Run() {
#include "Test.h"
#include <iostream>

void Test::Run() {
    int a = 1;
    int b = 2;
    int c = a + b;
    std::cout << "Call Run" << std::endl;
    std::cout << c << std::endl;
}
}
```
10. main.cpp 를 활성화하고, `F5`를 눌러 디버깅을 하면, 하기와 같은 오류 메시지가 표시됩니다.(.vscode/task.json 파일도 만들어 집니다.)
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/761ab3db-c70d-46cd-897c-eadb74914af4)

11. `Abort`를 누르고, `TERMINAL`을 확인하면, 하기와 같이 `Test::Run()`이 정의되지 않았다는 메시지가 나옵니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f047b28b-36da-41a8-b117-27b876fe6615)

12. 이는 컴파일러가 main.cpp 만 컴파일하고, Test.cpp는 컴파일하지 않았기 때문입니다. CMake를 이용한 Make 환경을 구축해야 합니다.

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

2. 그러면 하기 화면에서 컴파일러를 선택합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e41c42e8-9cbe-496c-8e9d-23fca6021313)

3. 혹은 `CMakeLists.txt`가 없다고 표시될 수도 있는데, 그냥 `Enter`하여 나타나는 `열기`대화상자를 그냥 닫고, 다시 `CMake:Quick Start` 를 실행합니다.(이러한 경우 아마도 하기 7번의 문제 해결이 필요할 수 있습니다.)
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/61f39640-4001-48d4-bfef-a930662c832c)

4. 2의 단계나 3의 단계를 거치면 새 프로젝트의 이름을 요청합니다. `test_cmake`로 입력하고 `Enter` 합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/51ee2de0-9687-418e-9c6e-ad2b08d70cb0)

5. 실행 파일을 만들 것이므로 `Executable`을 선택합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a220d272-735b-472e-8d20-87ae08b18fbb)

6. 그러면 `build`폴더가 생성되고, `Source Control`에 `245`개의 수정이 있고, root 폴더에 `CMakeLists.txt`파일과 `main.cpp`가 생성됩니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/34a20ab7-27c6-4dc8-a8da-9ca0198db5a4)

|항목|내용|
|--|--|
|`build` 폴더|빌드 과정에 필요한 임의 생성 파일들|
|245개의 수정항목|대부분 `build` 폴더 하위에 자동 생성된 파일들|
|CMakeLists.txt|Makefile 생성을 위한 스크립트 파일|
|main.cpp|CMake가 생성한 테스트용 cpp 파일|

7. 가끔 하기 오류가 표시되며 `build` 폴더가 생성되지 않을 때가 있습니다. 이럴 경우  `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Reset CMake Tools Extension State(For troubleshooting)` 을 실행하고, `CMake:Quick Start` 를 재시도 합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cb176898-ed24-4e74-9192-d9e1965d72be)

8. 자동 생성된 `main.cpp`를 삭제합니다.(`cpp/src/main.cpp`를 삭제하면 안됩니다.)
9. `.gitignore`에 `build/`추가하여 Git 관리항목에서 제외합니다.
```ini
.vscode/
*.exe
build/
```
10. 그러면 `245`개의 수정사항이 `5`개로 줄어듭니다. 
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/678a5c76-9488-4eb3-9df7-4bac544dada7)

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

`CTest` 는 사용하지 않고, 빌드대상은 `cpp/src/main.cpp`, `cpp/src/Test.cpp` 이므로 하기와 같이 코드를 수정합니다.

```CMake
cmake_minimum_required(VERSION 3.0.0) # 최소요구버전
project(test_cmake VERSION 0.1.0) # PROJECT_NAME 과 PROJECT_VERSION

add_executable(test_cmake cpp/src/main.cpp cpp/src/Test.cpp) # 빌드 대상 추가. [실행파일명] [소스파일명] [소스파일명]

set(CPACK_PROJECT_NAME ${PROJECT_NAME}) # 실행파일명
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION}) # 실행파일버전
include(CPack) # 기본적으로 build 경로에 실행파일을 생성함
```

# CMake를 이용한 빌드와 디버깅

1. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Build`(F7)를 하면, 빌드되고 빌드 결과물인 `test_cmake.exe` 가 `build`폴더에 생성됩니다. `OUTPUT`에는 빌드를 마쳤다는 메시지가 표시됩니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a5cf7e97-1c9b-4d4a-bab3-11bae08e5fd3)

2. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Run Without Debugging`(Shift+F5)를 하면, `test_cmake.exe`가 실행됩니다. `TERMINAL`에 실행 메시지가 표시된걸 확인할 수 있습니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a45b1912-e866-48c8-990b-f406583c4507)

3. `Run/Toggle breakpoint`(F9) 를 하면, 중단점이 설정됩니다. `main.cpp`의 `Test::Run()` 에 중단점을 설정합니다. 중단점을 설정하면 라인 옆에 붉은색 점이 표시됩니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8c02303c-dd0f-46bc-b325-186908cffd29)

4. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Debug`(Ctrl+F5) 를 하면, 중단점 위치까지 실행되며, 좌측 `RUN AND DEBUG`에서 변수값을 확인하고, 상단 디버깅 툴바에서 `Continue`(F5), `Step Over`(F10), `Step Into`(F11), `Step Out`(Shift+F11) 등을 이용하여 다음 지점의 중단점이나 다음 단계로 진입할 수 있습니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/adaa780e-dca2-4d81-9a07-9f0f5edae189)

5. `Step Into`(F11) 을 하여 `Test::Run()`함수내에 진입하여, `VARIALBLES` 에서 현 함수의 변수 값을 확인하고, `WATCH`에서 보고싶은 변수나 수식을 입력하여 확인할 수 있습니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/44da2cf5-f086-4b99-bf6a-ddcedfdac502)

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

# CMake 파일 구성 변경

빌드 대상인 파일의 구성이 변경되면, `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Configure` 를 다시 수행합니다. -> 확인 필요

