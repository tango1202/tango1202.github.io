---
layout: single
title: "#2. [개발설정] Visual Studio Code 로 Cpp 개발환경 구축"
categories: "dev-setting"
tag: ["개발설정", "Visual Studio Code", "Cpp", "Windows"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
Visual Studio Code 는 에티터이기 때문에, C++ 을 이용한 개발을 하려면 MinGW(GNU C++)와 Visual Studio Extension의 C/C++ Extension Pack 설치가 필요합니다.

|항목|내용|
|--|--|
|MinGW|C++ 컴파일러, 디버거|
|Visual Studio Extension의 C/C++ Extension Pack|에디터의 C++ intelliSence 지원, 에디터의 C++ UI 테마 지원, CMake 지원|

# Visual Studio Code 설치
다음 링크에서 Visual Studio Code 를 다운로드 받고 설치합니다.

[Visual Studio Code(https://code.visualstudio.com/download)](https://code.visualstudio.com/download)

# 컴파일러 설치(GNU C++ 컴파일러)

1. 다음 링크에서 MinGW(Minimalist GNU for Windows)를 다운로드 받습니다.
[MinGW(https://sourceforge.net/projects/mingw/files/latest/download)](https://sourceforge.net/projects/mingw/files/latest/download)

2. `minggw-get-set`을 실행하여 설치합니다.
3. `MinGW installation Manager`에서 `mingw-developer-toolkit`, `minw32-base`, `minw32-gcc-g++`, `msys-base`를 선택하고 `Mark for Installation`을 선택합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/62e87041-cda9-4796-9969-6135742f3c4d)

4. `Installation/Apply Changes` 를 실행후, 대화상자에서 `Apply`를 실행합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/caad6646-53f5-4359-be66-5fd6cbed4454)<br/>
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/361fc1b8-4f72-487c-b2b0-8248b556a574)

5. `Path` 설정을 수정하기 위해, `제어판/시스템 속성`(Windows의 `작업표시줄/찾기`에서 `sysdm.cpl` 실행) 을 실행하여 , `고급`탭의 `환경 변수` 버튼 클릭후, `환경 변수` 대화상자에서 `Path`를 선택하여 `편집` 버튼을 실행합니다. `환경 변수 편집` 대화상자에 `MinGW/bin` 폴더를 추가합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8f9c464d-1aec-479c-b58c-81b298764fe0)<br/>
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/89385f08-04f2-4a21-9086-08a52d3cd5ad)<br/>
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e71b182c-3eaf-4cdf-a670-ecbafef3d64f)

6. 설치 확인을 위해 `cmd` 에서 `gcc -v`를 입력하여 버전을 확인합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a5dfb388-5d81-4219-af88-87d6e467ad5d)

# Visual Studio Code Extension 설치(C/C++ Extension Pack)

1. `Extensions`(Ctrl+Shift+X) 탭을 클릭하여 Extension을 실행합니다. 
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b8516461-6468-401d-95eb-b70d5c251c80)

2. `C++`로 검색하고 `C/C++ Extension Pack`의 `install` 버튼을 클릭하여 설치후, 정상적인 반영을 위해 Visual Studio Code를 재실행합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/19a9a392-405e-4d93-b305-831f9d3269fc)

3. `C/C++ Extension Pack` 을 설치하면 하기와 같이 에디터에 C++ 용 IntelliSence 가 표시됩니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/3e55881f-69c0-4ed3-9fd6-0e699d6fbd56)

# C++ 코드 작성과 빌드 구성

1. 탐색기에서 `language_test` 폴더를 생성합니다.
2. Visual Studio Code 에서 `File/Open Folder` 를 실행하여 `language_test` 폴더를 엽니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c86250d0-95ec-436f-90f6-bf0a620b711c)

3. 지정한 폴더가 `EXPLORER` 패널에 열리고, MouseOver나 폴더명을 선택하면 `New File`, `New Folder`, `Refresh Explorer`,  `Collapse Folders in Explorer` 버튼이 표시됩니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/fc48b1a3-b3a8-431b-b7c0-f198af367c36)

4. `New Folder`버튼을 이용하여 `cpp`폴더를 만들고, 그 하위에 `src`폴더를 만듭니다. 그리고, `New File` 버튼을 이용하여 `main.cpp`파일을 생성합니다. 그러면 오른쪽에 `main.cpp` 파일이 열립니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a06cbe3f-9635-4465-ad84-f6245984957a)

5. `main.cpp`에 하기 코드를 작성합니다.<br/><br/>
```cpp
#include <iostream>

int main() {
    std::cout << "Hello World!!!" << std::endl;
    return 0;
}
```

6. `Run/Start Debug`(F5)를 눌러 디버깅을 시작하면, 컴파일러 선택 화면이 나옵니다. `C++ (GDB/LLDB)`를 선택합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4d310420-ead2-4082-8ac7-292dca9b7867)

7. 다음으로는 환경설정 선택화면이 나옵니다. `C/C++:g++.exe build and debug active file`을 선택합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e03ccb4e-12b5-4c38-ae13-902212e06839)

8. 실행결과로 `EXPLORER`에 `.vscode`폴더가 추가되고, `task.json`파일이 추가되고, `main.exe`가 생성되고, `DEBUG CONSOLE`에 `Hello world!!!` 가 표시되었습니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2bae95da-8e7d-444a-bf49-ac96060642ef)

9. `task.json`파일에는, `g++.exe`로 컴파일 및 디버깅 하기 위한 정보들이 포함됩니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b2223b79-5f90-41dd-bd90-c82916c6e24d)

# C++ 속성 설정

1. `View/Command Palette`(Ctrl+Shift+P) 실행후  `C/C++ : Edit Configurations(UI)`을 실행합니다. 
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6a371f72-d098-4bc3-9eec-dc256fae803a)

2. `C++ standard`를 `C++17`으로 변경합니다.
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/40903eb2-6d47-4005-971c-2dbf1588e1a1)

3. 그러면 `.vscode` 에 `c_cpp_properties.json` 파일이 생성됩니다.(`cppStandard`에 `c++17`이 설정된 것을 확인할 수 있습니다.)
![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6be7a126-faee-4334-99de-3bf57670931b)








