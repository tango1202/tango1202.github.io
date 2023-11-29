---
layout: single
title: "#8. [개발설정] Visual Studio Code 에서 C++ 단위 테스트 구축(GoogleTest, TestMate)"
categories: "dev-setting"
tag: ["개발설정", "visual studio code", "C++", "GoogleTest", "TestMate", "CMake"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요
단위 테스트는 코드의 신뢰를 높여 사이드 이펙트를 최소화 해주고, 코드의 인터페이스도 깔끔하게 해주는 스킬입니다. 코드 작성시 기능 구현만 완료하면 반쪽 짜리입니다. 꼭 단위 테스트를 만들어 완성도를 올리시기 바랍니다.

특히 

1. 테스트를 먼저 작성후 실패하고(빨강) 
2. 테스트를 가까스로 통과시킨 뒤(초록) 
3. 코드를 간결하게 정리(리팩토링)하는 

테스트 주도 개발(Test-Driven Development, TDD)을 적용하여, **작동하는 깔끔한 코드(Clean Code That Works)** 를 개발하시기 바랍니다. **빨강-초록-리팩토링** 의 마법같은 습관으로 코딩 성취감을 맛볼 수 있고, 디버깅의 지옥에서 벗어날 수 있습니다.

Visual Studio Code + CMake + C++ 환경의 단위 테스트 구축시에는 GoogleTest가 적합하고, GoogleTest 유틸리티로 C++ TestMate 가 좋습니다.

|항목|내용|
|--|--|
|GoogleTest|C++ 단위 테스트 프레임워크|
|Visual Studio Extension의 C++ TestMate|Catch2, GoogleTest, doctest 탐색기|

# 사전 설치

하기 항목이 사전에 설치되어야 합니다.

|항목|내용|
|--|--|
|Visual Studio Code|[Visual Studio Code 설치](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#visual-studio-code-%EC%84%A4%EC%B9%98)|
|Visual Studio Code Extension(C/C++ Extension Pack)|[Visual Studio Code Extension 설치(C/C++ Extension Pack)](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#visual-studio-code-extension-%EC%84%A4%EC%B9%98cc-extension-pack)|
|MinGW-w64|[MinGW-w64 설치(GNU C++ 컴파일러)](https://tango1202.github.io/dev-setting/dev-vscode-cpp/#mingw-w64-%EC%84%A4%EC%B9%98gnu-c-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC)|
|CMake|[CMake 설치](https://tango1202.github.io/dev-setting/dev-vscode-cmake/#cmake-%EC%84%A4%EC%B9%98)|
|Visual Studio Code Extension(CMake, CMake Tools)|[Visual Studio Code Extension 설치(CMake, CMake Tools)](https://tango1202.github.io/dev-setting/dev-vscode-cmake/#visual-studio-code-extension-%EC%84%A4%EC%B9%98cmake-cmake-tools)|

# 테스트 환경 구축

1. 탐색기에서 `language_test` 폴더를 생성합니다.

2. `language_test` 하위에 `cpp` 폴더를 만들고, 그 하위에 `src` 폴더(소스코드가 작성될 곳)를 만듭니다.

3. Visual Studio Code 에서 `File/Open Folder` 를 실행하여 `language_test` 폴더를 엽니다.

4. `language_test/cpp/src` 폴더에 하기 4개의 파일을 작성합니다.

    **language_test/cpp/src/Plus.h**

    ```cpp
    #ifndef Plus_h
    #define Plus_h

    class Plus {
    public:
        static int Run(int a, int b);
    };

    #endif // Plus_h
    ```

    **language_test/cpp/src/Plus.cpp**

    ```cpp
    #include "Plus.h"

    int Plus::Run(int a, int b) {
        return a + b;
    }  
    ``` 

    **language_test/cpp/src/Minus.h**

    ```cpp
    #ifndef Minus_h
    #define Minus_h

    class Minus {
    public:
        static int Run(int a, int b);
    };

    #endif // Minus_h
    ``` 

    **language_test/cpp/src/Minus.cpp**

    ```cpp
    #include "Minus.h"

    int Minus::Run(int a, int b) {
        return a - b;
    }
    ```

5. `language_test` 하위에 `test` 폴더(테스트케이스가 작성될 곳)를 만듭니다. 전체적인 구조는 다음과 같습니다.

    ```cpp
    +--language_test
        +--cpp
            +--src
                +--Plus.h
                +--Plus.cpp
                +--Minus.h
                +--Minus.cpp
        +--test
    ```

# 테스트케이스 작성

`test` 폴더에 `Plus::Run()`과 `Minus::Run()`의 동작을 확인할 수 있도록 테스트케이스를 구성합니다.(아직은 프로젝트 구성이 되지 않아 `#include "gtest/gtest.h"` 에서 오류가 나옵니다.)

`EXPECT_TRUE()`로 `Plus::Run()`과 `Minus::Run()`함수의 호출 결과를 테스트합니다.

**language_test/test/TestPlus.cpp**

```cpp
#include "gtest/gtest.h"
#include "../cpp/src/Plus.h"

TEST(TestPlus, Test1) {
    EXPECT_TRUE(Plus::Run(1, 2) == 3);    
}

TEST(TestPlus, Test2) {
    EXPECT_TRUE(Plus::Run(1, -1) == 0);    
}
```

**language_test/test/TestMinus.cpp**

```cpp 
#include "gtest/gtest.h"
#include "../cpp/src/Minus.h"

TEST(TestMinus, Test1) {
    EXPECT_TRUE(Minus::Run(1, 2) == -1);    
}

TEST(TestMinus, Test2) {
    EXPECT_TRUE(Minus::Run(1, -1) == 2);    
}   
```

# GoogleTest 설치

GoogleTest는 

1. 자신의 프로젝트에 소스코드를 복제하여 사용하거나, 

2. GoogleTest의 Github 레포지토리에 연결에서 사용([GoogleTest Quickstart(http://google.github.io/googletest/quickstart-cmake.html)](http://google.github.io/googletest/quickstart-cmake.html) 참고)

하는 방법이 있습니다.

첫번째 방법인 소스코드를 복제하여 사용하는 방법으로 GoogleTest를 설치하도록 하겠습니다. 

1. 다음 링크에서 GoogleTest의 소스코드(zip파일)를 다운로드 받습니다.(*현재 v1.13.0 버전이 release 되었습니다. C++14 이상에서 빌드됩니다. C++11에서 사용하려면 [https://github.com/google/googletest/releases/tag/release-1.12.1](https://github.com/google/googletest/releases/tag/release-1.12.1)을 다운로드 하셔야 합니다.*)

     [GoogleTest Github(https://github.com/google/googletest)](https://github.com/google/googletest)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1dd8d879-8bff-4d11-af31-b690f162457d)


2. 소스코드(zip파일)를 복사하고 압축을 풉니다. `googletest-1.13.0` 폴더를 `language_test` 폴더에 복사합니다. 

3. 폴더 구조는 다음과 같습니다. 

    ```cpp
    +--language_test
        +--cpp
            +--src
                +--Plus.h
                +--Plus.cpp
                +--Minus.h
                +--Minus.cpp
        +--test
            +-- PlusTest.cpp
            +-- MinusTest.cpp
        +--googletest-1.13.0
            +--.github
            +--ci
            +--docs
            +--googlemock
            +--googletest
    ```

# CMake 빌드 구성

GoogleTest 소스코드가 포함된 프로젝트의 빌드는 다음과 같이 구성됩니다.

1. 소스코드(src 폴더)
2. GoogleTest 코드(googletest-xxx 폴더)
3. 테스트케이스 코드(test 폴더)
4. 1 ~ 3 링크

또한, 자체적으로 `main()` 을 제공하기 때문에 별도의 `main()` 정의가 필요하지 않습니다. 

1. `language_test` 폴더에 `CMakelists.txt` 파일을 다음과 같이 작성하고 저장합니다. (실행 파일은 `UnitTester.exe` 이고, `src`를 라이브러리로 빌드하고, 실행파일에 `GoogleTest`와 `src`라이브러리를 정적 링크합니다.)

    ```bash
    cmake_minimum_required(VERSION 3.0.0) # 최소요구버전
    project(test_cmake VERSION 0.1.0) # PROJECT_NAME 과 PROJECT_VERSION
    set(This UnitTester) # 빌드 타겟
    set(SrcLib SrcLib) # cpp/src 라이브러리

    # CTest 지원
    enable_testing() # CTest 지원

    # cpp/src 라이브러리 빌드
    set(SrcFiles 
        cpp/src/Plus.cpp
        cpp/src/Minus.cpp
    )
    add_library(${SrcLib} STATIC ${SrcFiles})

    # googletest 빌드
    add_subdirectory(googletest-1.13.0)

    # test 폴더 하위 테스트케이스 빌드
    set(TestFiles
        test/TestPlus.cpp
        test/TestMinus.cpp
    )

    # 실행파일 구성
    add_executable(${This} ${TestFiles}) 

    # googletest와 SrcLib 포함
    target_link_libraries(${This} 
        gtest_main 
        ${SrcLib}
    )
    ```

2. `View/Command Palette`(Ctrl+Shift+P) 에서 `CMake:Configure` 를 실행합니다. 그러면 `build` 폴더가 만들어 집니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6e2259d9-d8b8-44da-93d5-592ca422491a)

# 단위 테스트 결과 확인

1. `CMake:Build`(F7)를 하여 빌드합니다.

2. `CMake:Run without Debugging`(Shift+F5)를 하면, 다음과 같이 `Panel(TERMINAL)`에 단위 테스트 결과가 표시됩니다. 4개의 테스트가 성공한 것을 확인할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/84f78c64-c128-46c1-8f67-8be1f8735902)

3. 테스트 실패(빨강)을 확인하기 위해 `Plus::Run()`을 억지로 수정합니다.(더하기가 아니라 빼기를 하도록 했습니다.)

    ```cpp
    int Plus::Run(int a, int b) {
        // return a + b;
        return a - b; // 더하기가 아닌 빼기를 리턴합니다.
    }
    ```

4. `Plus::Run()`함수가 기대치를 리턴하지 않으므로, `TestPlus.Test1`과 `TestPlus.Test2`가 모두 실패한 것을 확인 할 수 있습니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/75caca0d-ac4f-43ef-99e6-afdb28439734)

# CTest 와 GoogleTest 연동하기

터미널을 통해 단위 테스트를 확인하면, 테스트케이스가 많은 경우 확인이 어렵습니다. 그래서 UI를 지원하는 유틸리티를 이용하는게 좋은데요, 약간 아쉽긴 하지만, CMake에서 기본으로 제공하는 CTest 에 GoogleTest를 연동하면, UI를 통해 테스트케이스를 확인할 수 있습니다.

1. `Status Bar`의 `Run CTest`를 클릭하거나, 왼편 `Activity Bar`의 `Testing` 을 클릭하면, `Side Bar(TESTING)` 이 표시됩니다. 

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0248b242-d570-4f52-ad99-5c1393c846df)

2. 목록에서 `Run Test`를 실행하면, 테스트 성공 여부가 아이콘으로 표시(성공 : 초록색, 실패 : 빨강색)됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ad984f76-c0ff-4637-89a5-1a206c58a789)

3. CTest가 GoogleTest의 테스트케이스를 좀더 세부적으로 찾을 수 있도록, `CMakelists.txt` 파일의 하단에 `gtest_discover_tests`를 추가하고 저장합니다.  

    ```bash
    cmake_minimum_required(VERSION 3.0.0) # 최소요구버전
    set(This UnitTester) # 빌드 타겟
    set(SrcLib SrcLib) # cpp/src 라이브러리
    enable_testing() # CTest 지원

    # cpp/src 라이브러리 빌드
    set(SrcFiles 
        cpp/src/Plus.cpp
        cpp/src/Minus.cpp
    )
    add_library(${SrcLib} STATIC ${SrcFiles})

    # googletest 빌드
    add_subdirectory(googletest-1.13.0)

    # test 폴더 하위 테스트케이스 빌드
    set(TestFiles
    test/TestPlus.cpp
    test/TestMinus.cpp
    )

    # 실행파일 구성
    add_executable(${This} ${TestFiles}) 

    # googletest와 SrcLib 포함
    target_link_libraries(${This} 
        gtest_main 
        ${SrcLib}
    )

    # CTest가 googletest의 테스트케이스 정보를 찾을 수 있게 함
    include(GoogleTest)
    gtest_discover_tests(${This}) 
    ```

4. 이제 `Refesh Tests` 를 클릭하면, 다음과 같이 GoogleTest의 테스트케이스들이 각각 표시됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/83c93526-39a6-4836-ae5d-a73f6b481197)
    
5. `Run Test`을 클릭하면(자동 빌드를 합니다.), 성공한 테스트(초록색)와 실패한 테스트(빨간색)가 아이콘을 통해 표시됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/cd4c2e44-e4a1-4f69-9eb7-a250b92e91bc)

# TestMate 사용

CTest는 테스트케이스를 조직화 하여 표시하지 않으므로, 테스트케이스가  많아지면 관리가 어렵습니다. 

Visual Studio Code 익스텐션중 C++ TestMate를 이용하면, 테스트케이스가 트리로 조직화되어 표시되고, 테스트케이스 코드 위치로 이동할 수 있게 합니다. 또한 특정 테스트케이스만 실행할 수 있어, 필요한 테스트만 빠르게 확인할 수 있습니다.

1. `Activity Bar`의 `Extensions`을 클릭하여 `C++ TestMate`를 설치합니다. 설치후 정상적인 반영을 위해 `View/Command Palette`(Ctrl+Shift+P) 에서 `Developer: Reload Window`를 합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8c32e297-6834-42ba-bb12-12534a6b3cc6)

2. `Activity Bar`의 `Testing` 을 클릭하면, 다음과 같이 `TestMate C++` 하위에 테스트케이스가 조직화되어 표시됩니다.(만약 보이지 않는다면 `Refresh Tests`를 실행합니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0c7067ef-793d-4e4a-a483-117d56fc411e)

3. `Run Test`를 클릭하면, 성공한 테스트(초록색)와 실패한 테스트(빨간색)가 아이콘을 통해 표시됩니다.(`CTest`의 경우 `Run Test`시 빌드를 하지만, TestMate는 빌드하지 않습니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/22d5d93d-5b65-46f2-be39-94b4d947e863)

4.  `Goto Test`를 클릭하면, 해당 테스트케이스로 이동합니다.  

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e144ef2d-a577-4472-bb71-6051da468f8c)

5. 테스트케이스에서 사용한 `Plus::Run()`을 수정하기 위해, `cpp/src/Plus.cpp`로 이동하여 다음과 같이 정상 동작하도록 수정합니다.

    ```cpp
    int Plus::Run(int a, int b) {
        return a + b;
    }
    ```

6. F7 하여 빌드합니다.(CTest는 `Run Test`시 자동 빌드하기 때문에 별도로 빌드할 필요가 없지만, TestMate는 코드 수정이 있으면 꼭 빌드해야 합니다.)

7. `TestPlus` 의 `Test1` 만 `Run Test`를 하면, 해당 테스트케이스만 녹색으로 바뀝니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0cec663d-e7ea-4707-82c9-49b5d54001ba)

8. 상위 트리아이템에서 `Run Test`를 하면, 하위 트리아이템의 테스트케이스를 모두 실행합니다.
    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/41ca9589-1bfc-4bf8-bc51-f825fc2be2db)
