---
layout: single
title: "#2. [개발설정] VSCode 로 Cpp 개발환경 구축"
categories: "dev-setting"
tag: ["개발설정", "VSCode", "C++", "Windows"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
VSCode 는 에디터이기 때문에, C++ 을 이용한 개발을 하려면 MinGW-w64(Minimalist GNU for Windows)와 VSCode Extension의 C/C++ Extension Pack 설치가 필요합니다.

|항목|내용|
|--|--|
|MinGW-w64|C++ 컴파일러, 디버거|
|VSCode Extension의 C/C++ Extension Pack|에디터의 C++ intelliSence 지원, 에디터의 C++ UI 테마 지원, CMake 지원|

# VSCode 설치

다음 링크에서 VSCode 를 다운로드 받고 설치합니다.

[VSCode(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)

# VSCode 사용자 인터페이스

VSCode는 다음과 같은 인터페이스를 제공합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/73f52133-ffa0-46cf-b4c7-072ae90c0533)

|항목|내용|
|--|--|
|Activity Bar|Side Bar의 보기를 전환합니다.|
|Side Bar|Activity Bar 선택에 따라 파일 탐색기등 다양한 보기가 제공됩니다.|
|Editor Groups|파일을 편집하는 영역입니다.|
|Panel|기본적으로 출력, 디버그등의 정보와 터미널이 제공됩니다.|
|Status Bar|열려있는 파일에 대한 다양한 상태가 표시됩니다.|


자세한 사용자 인터페이스는 하기 링크를 참고하시기 바랍니다.

[VSCode 사용자 인터페이스(https://code.visualstudio.com/docs/getstarted/userinterface)](https://code.visualstudio.com/docs/getstarted/userinterface)

# MinGW-w64 설치(GNU C++ 컴파일러)

1. 다음 MinGW-w64(Minimalist GNU for Windows) 다운로드 페이지로 이동합니다.([MinGW](https://sourceforge.net/projects/mingw/files/latest/download) 도 있습니다만, C++ 상위 버전 지원이 약해서 MinGW-w64를 추천합니다.)

    [MinGW-w64(https://sourceforge.net/projects/mingw-w64/files/)](https://sourceforge.net/projects/mingw-w64/files/)(혹은, [https://winlibs.com/](https://winlibs.com/)에서 2023.10월 현재 13.2.0 버전이 제공됩니다.)

2. 스크롤하여 중간 즈음에 있는 `x86_64-posix-seh`을 다운로드 받습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/63fa79fc-b4b9-4730-942a-911296afdabf)

3. 다운로드 받은 파일의 압축을 풀고, `mingw64` 폴더를 설치할 경로에 이동시킵니다.

4. `Path` 설정을 수정하기 위해, `제어판/시스템 속성`(Windows의 `작업표시줄/찾기`에서 `sysdm.cpl` 실행)을 실행하여 , `고급`탭의 `환경 변수` 버튼 클릭후, `환경 변수` 대화상자에서 `Path`를 선택하여 `편집` 버튼을 실행합니다. `환경 변수 편집` 대화상자에 `mingw64/bin` 폴더를 추가합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8f9c464d-1aec-479c-b58c-81b298764fe0)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/89385f08-04f2-4a21-9086-08a52d3cd5ad)
    
    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d7bae65a-8cc0-4315-8e45-4c8a7bcf319c)

5. 설치 확인을 위해 `cmd` 에서 `gcc -v`를 입력하여 버전을 확인합니다.(GCC의 C++ 버전 지원 여부는 [gcc.gnu.org](https://gcc.gnu.org/projects/cxx-status.html)을 참고하시기 바랍니다. 특정 버전의 C++ 을 사용하려면 `g++ main.cpp -std=c++11` 와 같이 버전을 명시해야 합니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1893d24b-2cc0-40e9-8213-5fb095543b49)

# VSCode Extension 설치(C/C++ Extension Pack)

1. `Activity Bar`에서 `Extensions`(Ctrl+Shift+X)을 클릭하여 Extension을 실행합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b8516461-6468-401d-95eb-b70d5c251c80)

2. `Side Bar(EXTENSIONS)`에서 `C++`로 검색하고 `C/C++ Extension Pack`의 `install` 버튼을 클릭하여 설치합니다. 그뒤 정상적인 반영을 위해 
`Command Palette`(Ctrl+Shift+P)에서 `Developer: Reload Window`를 합니다.(혹은 VSCode 를 재실행 합니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/19a9a392-405e-4d93-b305-831f9d3269fc)

3. `C/C++ Extension Pack` 을 설치하면 다음과 같이 에디터에 C++ 용 IntelliSence 가 표시됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/3e55881f-69c0-4ed3-9fd6-0e699d6fbd56)

# C++ 코드 작성과 빌드 구성

1. 탐색기에서 `language_test` 폴더를 생성합니다.

2. VSCode 에서 `File/Open Folder` 를 실행하여 `language_test` 폴더를 엽니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c86250d0-95ec-436f-90f6-bf0a620b711c)

3. 지정한 폴더가 `Side Bar(EXPLORER)`에 열리고, MouseOver나 폴더명을 선택하면 `New File`, `New Folder`, `Refresh Explorer`,  `Collapse Folders in Explorer` 버튼이 표시됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/fc48b1a3-b3a8-431b-b7c0-f198af367c36)

4. `New Folder`버튼으로 `cpp`폴더를 만들고, 그 하위에 `src`폴더를 만듭니다. 그리고, `New File` 버튼으로 `main.cpp`파일을 생성합니다. 그러면 `Editor Group`에 `main.cpp` 파일이 열립니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a06cbe3f-9635-4465-ad84-f6245984957a)

5. `main.cpp`에 하기 코드를 작성합니다.

    ```cpp  
    #include <iostream>

    int main() {
        std::cout << "Hello World!!!" << std::endl;
        return 0;
    }
    ```
6. 폴더 구조는 다음과 같습니다. 

    ```cpp
    +--language_test
        +--cpp
            +--src
                +--main.cpp
    ```

7. `Run/Start Debug`(F5)를 눌러 디버깅을 시작하면, 컴파일러 환경설정 선택 화면이 나옵니다. `C/C++:g++.exe build and debug active file`을 선택합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a5ecc208-5f0f-4ac1-aaed-c60ad2cf654a)

8. 실행결과로 `Side Bar(EXPLORER)`에 `.vscode`폴더가 추가되고, `task.json`파일이 추가되고, `main.exe`가 생성되고, `Panel(TERMINAL)`에 `Hello world!!!` 가 표시되었습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/24ecac76-958a-4c02-afef-fe6c5290e149)

9. `task.json`파일에는, `g++.exe`로 컴파일 및 디버깅 하기 위한 정보들이 포함됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5817ffa3-bb2d-46a9-b6cd-5952173f5a7e)

# C++ 속성 설정

1. `View/Command Palette`(Ctrl+Shift+P) 실행후  `C/C++ : Edit Configurations(UI)`을 실행합니다. 

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6a371f72-d098-4bc3-9eec-dc256fae803a)

2. `C++ standard`에서 intellisence 환경을 `C++17`으로 변경합니다.(Edit 화면에서 intellisence 버전입니다. 실제 컴파일은 g++ filname -std=c++11 와 같이 지정해 줘야 합니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8c524277-bbd3-467b-b4d9-da4c7bdd280e)

3. 그러면 `.vscode` 에 `c_cpp_properties.json` 파일이 생성됩니다.(`cppStandard`에 `c++17`이 설정된 것을 확인할 수 있습니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/09d051e4-b3f8-4627-b027-b4e0471d5b04)








