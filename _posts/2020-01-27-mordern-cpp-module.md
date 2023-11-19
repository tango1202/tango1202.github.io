---
layout: single
title: "#27. [모던 C++] (C++20~) module"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

전처리 -> 컴파일 -> 링크

#include 를 이용한 전처리 과정은 생각외로 비효율적임. 파일 크기 엄청나고 컴파일하는데 시간도 많이 걸림.

g++ -E hello.cpp | vc -c // 파일 용량

모듈을 이용하면 좀더 효율 적임

```cpp
// ----
// MyModule.cpp 파일에서
// ----
module; // global module fragment

#include <iostream> // 모듈에서 사용하는 헤더 파일

export module MyModule_20; // 내보내기할 모듈 선언

void Print() {
    std::cout << "Module : Print()" << std::endl;
}
export void Func_20() { // 내보내기 선언
    Print();
}
```

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

```cpp
F:\Data\language_test\test\module>g++ -std=c++20 -fmodules-ts MyModule.cpp main.cpp -o Test  
```

# import 헤더 파일

```cpp
#include <iostream>
import <iostream>
import "MyHeader.h" // 단순한 파일 포함이 아니라 모듈처럼 취급

```
# 모듈 구조

```cpp
module; // 전역 모듈 조각(Global module fragment)

#include<> // 모듈에서 사용하는 헤더 파일

export module A; // 모듈 선언

import <iostream> // 모듈에 포함됨. #include를 하면 모든 선언과 정의가 모듈의 일부로 간주됨

export import <iostream> // 가져온 것을 내보냄

export void Func() {} // 내보낼 항목

```

# 모듈 인터페이스와 구현 분리

```cpp
// ----
// MyModuleInterface.cpp 파일에서
// ----
export module MyModule_20;

export void Func(); // 인터페이스 부분은 함수 선언만 합니다.

```

```cpp
// ----
// MyModuleImplement.cpp 파일에서
// ----
module;
#include <iostream> // 모듈에서 사용하는 헤더 파일
export module MyModule_20;

void Print() {
    std::cout << "Func_20" << std::endl;
}
void Func() { // export 를 붙이지 않습니다.
    Print();
}
```

```cpp
// ----
// main.cpp 파일에서
// ----
import MyModule_20;

int main() {
   Func(); // 모듈에서 Export한 함수를 사용합니다.
   return 0;
}
```

# 모듈 내보내기

```cpp
// ----
// MyModule.cpp 파일에서
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
export class T() {};
```

```cpp
// ----
// main.cpp 파일에서
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

# 모듈 분할

```cpp
// ----
// MyModule.cpp 파일에서
// ----
 export module MyModule_20;

 export import :Part1;
 export import :Part2;

// ----
// Part1.cpp 파일에서
// ----
export module MyModule_20:Part1;
import :Part3; // Part3을 가져오기 합니다. 내보내기 하지 않습니다.
export void Part1() {
    Part3(); // MyModule_20:Part3의 함수를 사용할 수 있습니다.
}

// ----
// Part2.cpp 파일에서
// ----
export module MyModule_20:Part2;

export void Part2() {}

// ----
// Part3.cpp 파일에서
// ----
module MyModule_20:Part3; // export 하지 않았습니다.

export void Part3() {}
```

```cpp
// ----
// main.cpp 파일에서
// ----
import MyModule_20; // MyModule_20.Part1, MyModule_20.Part2를 사용할 수 있습니다.
// import MyModule_20:Sub3; // export 하지 않아 사용할 수 없습니다.

int main() {
    Part1(); 
    Part2(); 
    // Part3(); // (X) 컴파일 오류.
    return 0;
}
```

# 하위 모듈

```cpp
// ----
// MyModule.cpp 파일에서
// ----
 export module MyModule_20;

 export import MyModule_20.Sub1;
 export import MyModule_20.Sub2;

// ----
// Sub1.cpp 파일에서
// ----
export module MyModule_20.Sub1;
import MyModule_20.Sub3; // Part3을 가져오기 합니다. 내보내기 하지 않습니다.
export void Sub1() {
    Sub3();
}

// ----
// Sub2.cpp 파일에서
// ----
export module MyModule_20.Sub2;

export void Sub2() {}

// ----
// Sub3.cpp 파일에서
// ----
export module MyModule_20.Sub3;

export void Sub3() {}
```

```cpp
// ----
// main.cpp 파일에서
// ----
import MyModule_20; // MyModule_20.Sub1, MyModule_20.Sub2를 사용할 수 있습니다.
import MyModule_20.Sub3; 

int main() {
    Sub1();
    Sub2(); 
    Sub3(); // MyModule_20.Sub3을 import 하면 사용할 수 있습니다.
    return 0;
}
```

# 모듈간 충돌

```cpp
// ----
// A.cpp 파일에서
// ----
export module A_20;

export Func() {}

// ----
// B.cpp 파일에서
// ----
export module B_20;

export Func() {}
```
```cpp
// ----
// main.cpp 파일에서
// ----
import A_20; 
import B_20; 

int main() {
    Func(); // (X) 컴파일 오류. A_20과 B_20 모듈에 모두 있습니다.
    return 0;
}
```





module: private; // 





