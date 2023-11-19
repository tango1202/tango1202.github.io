---
layout: single
title: "#27. [모던 C++] (C++20~) module"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)이 추가되어 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/) 사용 방식을 개선하여 컴파일 속도를 향상시키고, [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제, [선언과 정의 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 구성의 불편함, 기호 충돌 문제를 해결했습니다.

# 개요

C++ 는 다음의 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/), 컴파일, 링크의 3가지 과정을 거쳐서 프로그램을 빌드합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b118642c-af31-42bb-be77-bb1888a41a16)


이중 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/) 과정은 다음의 문제점이 있습니다.


1. 용량 비대화에 따른 컴파일 속도 문제

    [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)를 하고 나면 소스 파일은 엄청 거대해집니다.

    ```cpp
    // helloworld.cpp
    #include <iostream>

    int main() {
        std::cout << "Hello World" << std::endl;
        return 0;
    }
    ```

    상기 코드에는 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/)가 필요한게 `#include <iostream>` 하나 있는데요, 

    다음과 같이 `-E` 옵션으로 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/) 결과를 출력할 수 있고, 해당 내용을 `-o` 옵션으로 파일에 저장할 수 있습니다.

    ```cpp
    F:\Data\language_test\test\module>g++ -E helloworld.cpp -o helloworld.out
    ```

    결과는 놀랍게도 943kbyte나 됩니다. 대략 1000배가 늘었습니다. 이를 다 일일이 기계어로 번역하다 보니 별것 아닌 코드이지만 컴파일 시간이 좀 걸리게 됩니다.

    아마도 `iostream`이 또다른 파일을 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하고, 또다른 곳에서 또 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하다 보니 벌어진 일이겠죠. [파일 구성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/)에서 말씀드린 것처럼, 헤더 파일에서 다른 헤더 파일을 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하는 것은 최소화 하더라도 한계가 있습니다.

2. [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제

     [#define](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)이나 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 는 해당 위치에 소스 코드를 대체하는 것이기 때문에, 어떤것을 먼저 대체했는지에 따라 다른 결과가 나올 수 있습니다. 또한 [정적 변수의 초기화 순서](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%B3%80%EC%88%98%EC%9D%98-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%88%9C%EC%84%9C)와 같은 문제가 있을 수도 있습니다.

3. [선언과 정의를 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC)하는 [파일 구성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/)의 불편함
    
    컴파일 속도 향상을 위해 [선언과 정의를 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC)하는데요, 소스 코드 관리가 상당히 번거롭습니다.

 4. 기호 충돌

    정의가 중복되면 충돌이 납니다. 따라서 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)를 `extern`으로 선언한다던지, [함수를 inline으로 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-inline/#%EC%97%AC%EB%9F%AC-cpp%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B8%EB%9D%BC%EC%9D%B8-%ED%95%A8%EC%88%98-%EC%A0%95%EC%9D%98)한다던지 해야 합니다. 


C++20 부터는 [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)이 추가되어 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/) 사용 방식을 개선하여 컴파일 속도를 향상시키고, [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제, [선언과 정의 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 구성의 불편함, 기호 충돌 문제를 해결했습니다.

[모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)을 사용하는 방식은 컴파일러마다 다릅니다. 자세한 내용은 컴파일러 도움말을 참조 하세요.

|항목|MSVC|CLang|GCC|
|--|--|--|--|
|모듈 파일 확장자|`ixx`|`cppm` 또는 `cpp`|특별한 확장자 없음|
|컴파일 옵션|`/interface`|`-emit-module-interface`|`-fmodules-ts`|

다음은 `MyMoudule.cpp`와 `main.cpp`을 분리한 예입니다. 

`MyMoudule.cpp`에서는 내부적으로 `cout`을 사용하기 위해 `#include <iostream>`를 사용하며, 외부에서 사용할 수 있도록 `Func_20()`함수를 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)로 내보내기 합니다.

```cpp
// ----
// MyModule.cpp 파일에서
// ----
module; // 전역 모듈 조각

#include <iostream> // 모듈에서 사용하는 헤더 파일

export module MyModule_20; // 내보내기할 모듈 선언

void Print() {
    std::cout << "MyModule_20 : Print()" << std::endl;
}
export void Func_20() { // 내보내기 선언
    Print();
}
```

`main.cpp`에서는 [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)을 이용하여 `MyModule_20` [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)을 가져오며, 내보내기된 `Func_20()`함수를 사용합니다. 이때 `Print()`함수는 내보내기 되지 않았으므로 사용할 수 없습니다.

```cpp
// ----
// main.cpp 파일에서
// ----
import MyModule_20; // 가져오기 선언

int main() {
    Func(); // 모듈에서 Export한 함수를 사용합니다.
    // Print(); // (X) 컴파일 오류. export 한 함수가 아닙니다.
    return 0;
}
```

GCC에서는 다음과 같이 빌드합니다.

```cpp
F:\Data\language_test\test\module>g++ -std=c++20 -fmodules-ts MyModule.cpp main.cpp -o MyModule  
```

# export(모듈 내보내기) 와 import(모듈 가져오기)

모듈 선언인 `export module Module_20;` 이하에 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)로 내보낼 항목을 작성합니다. 개별로 내보낼 수도 있고, `export {}`로 그룹지어 내보낼 수도 있으며, [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 내보낼 수도 있습니다.

```cpp
// ----
// Test_ExportImport.cpp 파일에서
// ----
export module MyModule_20; // 내보내기할 모듈 선언

// 개별 내보내기
export void Func1() {}

// 그룹 내보내기
export {
    void Func2() {}
    void Func3() {}
}

// 네임스페이스 내보내기
export namespace MyLib {
    void Func4() {}
    void Func5() {}
}

// 템플릿 함수 내보내기
export template<typename T>
void Func6(T val) {}

// 클래스 내보내기
export class T {};
```

가져오기시에는 [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)로 [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)만 가져오면, [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)에서 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)한 모든 항목들을 사용할 수 있습니다.

```cpp
// ----
// Test_ExportImport_main.cpp 파일에서
// ----
import MyModule_20; // 가져오기 선언

int main() {
    Func1(); 
    Func2(); 
    Func3(); 
    MyLib::Func4(); 
    MyLib::Func5(); 
    Func6<int>(10);
    Func6<double>(10.0);
    T obj;

    return 0;
}
```

# import 헤더 파일

특별히 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)를 [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)로 변경할 수 있습니다.

(*GCC 12.3.0 에서 컴파일되지 않습니다. 뭐가 잘못됐는지 좀더 확인해 봐야 합니다. https://build2.org/blog/build2-cxx20-modules-gcc.xhtml#header-units 참고*)

```cpp
// #include <iostream>
import <iostream>;

int main() {
    std::cout << "Test Import header" << std::endl;

    return 0;
}
```

# 모듈 인터페이스와 구현 분리

[export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)로 외부로 내보낼때 인터페이스와 구현을 분리할 수 있습니다.

다음에서 `Test_Interface.cpp`에서 `Func()`함수의 선언만 내보내고, 

```cpp
// ----
// Test_Interface.cpp 파일에서
// ----
export module MyModule_20;
export void Func(); // 인터페이스 부분은 함수 선언만 합니다.
```

`Test_Implement.cpp`에서 `Func()`함수를 정의합니다. 이때 `MyModule_20`과 `Func()`에는 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)를 붙이지 않습니다.

```cpp
// ----
// Test_Implement.cpp 파일에서
// ----
module;
#include <iostream> // 모듈에서 사용하는 헤더 파일

module MyModule_20; // export를 붙이지 않았습니다.
void Func() {} // export를 붙이지 않습니다.
```

다음과 같이 `MyModule_20`을 [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)하여 사용할 수 있습니다.

```cpp
// ----
// Test_Interface_main.cpp 파일에서
// ----
import MyModule_20;

int main() {
   Func(); // 모듈에서 Export한 함수를 사용합니다.
   return 0;
}
```

# 전역 모듈 조각과 개인 모듈 조각

[전역 모듈 조각](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#%EC%A0%84%EC%97%AD-%EB%AA%A8%EB%93%88-%EC%A1%B0%EA%B0%81%EA%B3%BC-%EA%B0%9C%EC%9D%B8-%EB%AA%A8%EB%93%88-%EC%A1%B0%EA%B0%81)은 [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)의 상단에 `module;`로 표시하며, [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)에서 사용하는 헤더 파일을 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)합니다.

[개인 모듈 조각](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#%EC%A0%84%EC%97%AD-%EB%AA%A8%EB%93%88-%EC%A1%B0%EA%B0%81%EA%B3%BC-%EA%B0%9C%EC%9D%B8-%EB%AA%A8%EB%93%88-%EC%A1%B0%EA%B0%81)은 [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)의 하단에 `module : private;`로 표시하며, [모듈 인터페이스와 구현](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#%EB%AA%A8%EB%93%88-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%99%80-%EA%B5%AC%ED%98%84-%EB%B6%84%EB%A6%AC)을 하나의 파일에 작성할 수 있게 해줍니다. 즉, 함수 선언에는 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)를 작성하고, `module : private;`에 실제 구현을 합니다.

(*GCC 12.3.0 에서 `module : private;` 은 아직 구현되지 않았다는 컴파일 오류가 발생합니다.*)

```cpp
// ----
// Test_Fragment.cpp 파일에서
// ----
module; // 전역 모듈 조각(Global module fragment)

#include<iostream> // 모듈에서 사용하는 헤더 파일

export module MyModule_20; // 모듈 선언
export void Func(); // 인터페이스만 작성합니다.

module : private; // 구현을 작성합니다.
void Print() {
    std::cout << "MyModule_20 : Print()" << std::endl; 
}
void Func() {
   Print();
} 
```

다음과 같이 `MyModule_20`을 [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)하여 사용할 수 있습니다.

```cpp
// ----
// Test_Fragment_main.cpp 파일에서
// ----
import MyModule_20; 

int main() {
    Func(); // 모듈에서 Export한 함수를 사용합니다.
    return 0;
}
```

# 모듈 분할

하나의 [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)을 여러개로 나누어 관리할 수 있습니다.

다음예는 `MyModule_20`을 `Part1`과 `Part2`, `Part3`으로 나누어 관리하는 예입니다. `Part1`과 `Part2`는 외부에서도 사용하고, `Part3`은 `MyModule_20` 내부에서만 사용합니다.

`Test_Part.cpp`는 모듈의 인터페이스로서 `Part1`과 `Part2`를 [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)한뒤 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)합니다. 다른 파트를 표현할때 `:Part1;`와 같이 모듈명 없이 작성합니다.

```cpp
// ----
// Test_Part.cpp 파일에서
// ----
 export module MyModule_20;

 export import :Part1; // 각 파트를 import한뒤 export 합니다.
 export import :Part2;
```

`Test_Part1.cpp`, `Test_Part2.cpp`는 각 파트를 정의하고 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)합니다. 

이때 `Part1`에서 `Part3`을 사용하는데요, `import :Part3;`하여 가져오기를 합니다.

```cpp
// ----
// Test_Part1.cpp 파일에서
// ----
export module MyModule_20:Part1;
import :Part3;
export void Part1() {
    Part3(); 
}

// ----
// Test_Part2.cpp 파일에서
// ----
export module MyModule_20:Part2;
export void Part2() {}
```

모듈의 각 파트간에는 서로 사용할 수 있기 때문에 굳이 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)를 하지 않아도 됩니다. 따라서, `Part3`은 외부에 내보내지 않고 `MyModule_20`내애서만 사용하는 것이므로, `module MyModule_20:Part3;`와 같이 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)없이 작성합니다.

```cpp
// ----
// Test_Part3.cpp 파일에서
// ----
module MyModule_20:Part3; // MyModule_20에서만 사용하므로 export 하지 않습니다.
void Part3() {} // MyModule_20에서만 사용하므로 export 하지 않습니다.
```

다음과 같이 `MyModule_20`을 [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)하여 사용할 수 있습니다.

```cpp
// ----
// Test_Part_main.cpp 파일에서
// ----
import MyModule_20; // MyModule_20.Part1, MyModule_20.Part2를 사용할 수 있습니다.

int main() {
    Part1(); 
    Part2(); 
    return 0;
}
```

컴파일할 때 순서에 주의해야 합니다. 종속성 있는 파트들이 먼저 컴파일되어야 합니다. 따라서 `Test_Part3.cpp`는 `Test_Part1.cpp`보다 먼저 컴파일되어야 하고, `Test_Part.cpp`는 `Test_Part3.cpp Test_Part1.cpp Test_Part2.cpp`보다 나중에 컴파일 되어야 합니다.

```cpp
F:\Data\language_test\test\module>g++ -std=c++20 -fmodules-ts Test_Part3.cpp Test_Part1.cpp Test_Part2.cpp Test_Part.cpp Test_Part_main.cpp -o Test_Part_main  
```

# 하위 모듈

[모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)의 이름에는 `.`을 사용할 수 있는데요, 이를 이용해서 관용적으로 [하위 모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#%ED%95%98%EC%9C%84-%EB%AA%A8%EB%93%88)처럼 관리할 수 있습니다. `MyModule_20.Sub1`, `MyModule_20.Sub2`, `MyModule_20.Sub3`처럼요. 

다음은 [모듈 분할](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#%EB%AA%A8%EB%93%88-%EB%B6%84%ED%95%A0)에서의 예제를 [하위 모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#%ED%95%98%EC%9C%84-%EB%AA%A8%EB%93%88)로 변경한 예입니다. 서로간에 사용하려면 `export`되어야 하기 때문에, `MyModule_20.Sub3`도 [export](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0) 되었으며, 이에 따라 `main()`에서도 `MyModule_20.Sub3` [import](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#export%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B8%B0-%EC%99%80-import%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0) 하여 사용할 수 있습니다. 은닉성을 위해 [모듈 분할](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/#%EB%AA%A8%EB%93%88-%EB%B6%84%ED%95%A0)을 사용하는게 더 좋습니다.

```cpp
// ----
// Test_Sub.cpp 파일에서
// ----
 export module MyModule_20;

 export import MyModule_20.Sub1;
 export import MyModule_20.Sub2;

// ----
// Test_Sub1.cpp 파일에서
// ----
export module MyModule_20.Sub1;
import MyModule_20.Sub3; 
export void Sub1() {
    Sub3();
}

// ----
// Test_Sub2.cpp 파일에서
// ----
export module MyModule_20.Sub2;
export void Sub2() {}

// ----
// Test_Sub3.cpp 파일에서
// ----
export module MyModule_20.Sub3; // (△) 비권장. Sub1에서 사용하기 위해 export를 합니다.
export void Sub3() {} // (△) 비권장. Sub1에서 사용하기 위해 export를 합니다.
```

```cpp
// ----
// Test_Sub_main.cpp 파일에서
// ----
import MyModule_20; // MyModule_20.Sub1, MyModule_20.Sub2를 사용할 수 있습니다.
import MyModule_20.Sub3; // (△) 비권장. export되었기에 main에서도 사용할 수 있습니다.

int main() {
    Sub1();
    Sub2(); 
    Sub3(); // (△) 비권장. export되었기에 main에서도 사용할 수 있습니다.
    return 0;
}
```

# 모듈간 충돌

모듈에서 내보내는 개체가 동일하면 무얼 사용해야 할지 모호하기 때문에 컴파일 오류가 발생합니다. 

```cpp
// ----
// Test_My.cpp 파일에서
// ----
export module MyModule_20;
export void Func() {}

// ----
// Test_Your.cpp 파일에서
// ----
export module YourModule_20;
export void Func() {} // MyModule_20의 Func() 과 동일합니다.
```
```cpp
// ----
// Test_MyYour_main.cpp 파일에서
// ----
import MyModule_20; 
import YourModule_20; 

int main() {
    Func(); // (X) 컴파일 오류. MyModule_20 과 YourModule_20 모듈에 모두 있습니다.
    return 0;
}
```

따라서, [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 사용하시는게 좋습니다.

```cpp
// ----
// Test_My.cpp 파일에서
// ----
export module MyModule_20;
export namespace MyLib {
    void Func() {}
}

// ----
// Test_Your.cpp 파일에서
// ----
export module YourModule_20;
export namespace YourLib {
    void Func() {} 
}

// ----
// Test_MyYour_main.cpp 파일에서
// ----
import MyModule_20; 
import YourModule_20; 

int main() {
    MyLib::Func();
    YourLib::Func();
    return 0;
}
```





