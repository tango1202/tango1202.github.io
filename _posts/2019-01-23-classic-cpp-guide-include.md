---
layout: single
title: "#23. [고전 C++ 가이드] 파일 구성"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 헤더 파일에서 다른 헤더 파일을 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하는 것은 최소화 하라.
> * 선언과 정의 분리, [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)으로 컴파일 종속성을 최소화 하라.

> **모던 C++**
> * (C++17~) [__has_include](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c17-__has_include)가 추가되어 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하기 전에 파일이 존재하는지 확인할 수 있습니다.
> * (C++20~) [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)이 추가되어 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/) 사용 방식을 개선하여 컴파일 속도를 향상시키고, [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제, [선언과 정의 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 구성의 불편함, 기호 충돌 문제를 해결했습니다.

# 선언과 정의 분리

C++에서는 일반적으로 클래스 선언부는 헤더 파일에 작성하고, 클래스 멤버 함수 정의부는 cpp 파일에 작성 합니다. 이때 선언부를 포함하기 위해 cpp 파일에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)를 사용합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7912cb35-8fe3-4961-8157-0092b078b85d)

> *(C++20~) [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)이 추가되어 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/) 사용 방식을 개선하여 컴파일 속도를 향상시키고, [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제, [선언과 정의 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 구성의 불편함, 기호 충돌 문제를 해결했습니다.*

# 인클루드 가드

프로젝트 규모가 커지면, 여러 cpp에서 헤더 파일을 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하다가 중복 포함이 될 수 있습니다. 만약 헤더 파일에 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98) 정의나 함수 정의 등이 있다면, 중복 정의되었다며 컴파일 오류가 발생하게 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/fefed6ef-c1b3-41ba-83bd-9725ab4df01d)

따라서 관례적으로 헤더 파일은 [인클루드 가드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%9D%B8%ED%81%B4%EB%A3%A8%EB%93%9C-%EA%B0%80%EB%93%9C)를 이용하여 1회만 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)되도록 만듭니다. 

다음 코드를 보면 `#ifndef MyClass_h` 전처리를 이용하여 `MyClass_h`가 정의되지 않은 경우만 포함시킵니다. 포함된 경우에는 `#define MyClass_h`로 `MyClass_h` [매크로 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)를 정의합니다. 

따라서 다음번 부터는 `MyClass_h` [매크로 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)가 정의되었으므로, 더이상 포함되지 않습니다.(*컴파일러에 따라 [#pramga once](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma)를 제공하기도 합니다. 하지만, 비표준이니까 사용하지 않는게 좋습니다.*)

```cpp
// ----
// MyClass.h 헤더 파일에서
// ----
#ifndef MyClass_h // MyClass_h 가 이미 정의되었다면 제외합니다.
#define MyClass_h // 인클루드 가드

class MyClass {
private:
    int m_Val;
public:
    int Func() const; // Func() 멤버 함수 선언   
};

#endif // MyClass_h
```

# 선언과 정의 분리 효과

선언과 정의를 분리하면,

1. 컴파일 속도 향상과,
2. 실제 구현 코드의 은닉이 가능합니다.

다음은 선언과 정의의 분리 예입니다.

헤더 파일에서,

```cpp
// ----
// MyClass.h 헤더 파일에서
// ----
#ifndef MyClass_h
#define MyClass_h // 인클루드 가드

class MyClass {
private:
    int m_Val;
public:
    int Func() const; // Func() 멤버 함수 선언   
};

#endif // MyClass_h
```

cpp 파일에서,

```cpp
// ----
// MyClass.cpp 소스 파일 에서
// ----
# include "MyClass.h"

// Func 함수 정의 
int MyClass::Func() const {
    return m_Val;
}
```

`MyClass`를 사용하는 곳에서,

```cpp
// ----
// A.cpp 소스 파일 에서
// ----
#include "MyClass.h" // MyClass 선언을 포함합니다.

void f() {
    MyClass myClass; // MyClass 를 사용합니다.
    myClass.Func();
}

// ----
// B.cpp 소스 파일 에서
// ----
#include "MyClass.h" // MyClass 선언을 포함합니다.

void g() {
    MyClass myClass; // MyClass 를 사용합니다.
    myClass.Func();
}
```

상기와 같이 코드를 구성하면,

1. `MyClass.h`를 수정하면 이를 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)한 `MyClass.cpp`, `A.cpp`, `B.cpp`가 컴파일 되지만, `MyClass.cpp`를 수정하면, 수정한 `MyClass.cpp`파일만 컴파일 됩니다. 결과적으로 선언과 정의를 분리하고 적절히 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하면, 컴파일 종속성이 낮아져 불필요한 컴파일을 하지 않으므로 빌드 속도가 향상됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/10a80bdf-9ce0-4561-955a-656fa5afef31)

2. 외부에 모듈을 제공할때 `MyClass.h`와 컴파일된 파일만 제공하면 되므로 소스 코드를 은닉할 수 있습니다.

> *(C++20~) [모듈](https://tango1202.github.io/mordern-cpp/mordern-cpp-module/)이 추가되어 [전처리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/) 사용 방식을 개선하여 컴파일 속도를 향상시키고, [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제, [선언과 정의 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 구성의 불편함, 기호 충돌 문제를 해결했습니다.*

# 전방 선언

`MyClass`를 여러 클래스에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하여 사용하는 구성을 생각해 봅시다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/19c036c4-78b2-4ee0-9802-34dde81d89df)

상기 구성에서 `A`와 `B` 클래스는 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)로 `MyClass`를 사용하므로, `MyClass.h`를 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하였습니다. 이에 따라 `MyClass.h`가 수정되면 `MyClass.cpp`, `A.h`, `A.cpp`, `B.h`, `B.cpp`, `Other.cpp`가 모두 빌드 됩니다. 특히, `Other.cpp`처럼 `A.h`, `B.h`를 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)한 다른 곳이 있다면, 연쇄적으로 다시 빌드 됩니다. [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)를 잘못 구성하면, 뭐 하나 수정할 때마다 전체 빌드되어 컴파일 속도가 현저히 떨어지죠.

그래서 컴파일 종속성을 낮추기 위해 ***헤더 파일에서 다른 헤더 파일을 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하는 것은 최소화*** 하는게 좋습니다.

상기 예에서 `A`, `B` 클래스가 `MyClass`를 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)로 사용하기 때문에 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하였는데요,

```cpp
#include "MyClass.h"

class A {
    MyClass m_MyClass; // MyClass 선언을 include 해야 합니다.
public:
    int f() const;
};
```

다음과 같이 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 정의시 [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)를 사용하면 굳이 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하지 않고, ***해당 개체는 클래스이다*** 라고 알려주는 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)만 해도 됩니다.

```cpp
class MyClass; // 전방 선언

class A {
    MyClass* m_MyClass; // MyClass 선언을 include 하지 않고 전방 선언만 해도 됩니다.
public:
    int f() const;
};
```

단, 포인터형 변수이므로, 생성자에서 생성하고, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 소멸시켜줘야 합니다. 전체적으로는 다음과 같이 구성됩니다.

```cpp
// ----
// MyClass.h 헤더 파일에서
// ----
#ifndef MyClass_h
#define MyClass_h // 인클루드 가드

class MyClass {
private:
    int m_Val;
public:
    int Func() const; // Func() 멤버 함수 선언   
};

#endif // MyClass_h

// ----
// MyClass.cpp 소스 파일 에서
// ----
# include "MyClass.h"

// Func 함수 정의 
int MyClass::Func() const {
    return m_Val;
}

// ----
// A.h 헤더 파일에서
// ----
class MyClass; // 전방 선언

class A {
    MyClass* m_MyClass; // MyClass 선언을 include 하지 않고 전방 선언만 해도 됩니다.
public:
    A();
    ~A();
    int f() const;
};

// ----
// A.cpp cpp 파일에서
// ----
#include "A.h"
#include "MyClass.h" // MyClass 실제로 사용하므로 include 합니다.

A::A() : m_MyClass(new MyClass()) {} // 생성합니다.
A::~A() {delete m_MyClass;} // 소멸합니다.
int A::f() const {return m_MyClass->Func();} // MyClass를 사용합니다.
```

상기 구성을 그림으로 보면 다음과 같습니다. `MyClass.h` 를 수정하면 이를 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 한 `MyClass.cpp`, `A.cpp`, `B.cpp`만 컴파일되어 연쇄적으로 빌드되는 걸 방지합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e0c87b61-73c5-45c6-84fc-0b774ca1a38d)

# 전방 선언을 이용한 상호 참조 해결

서로 다른 클래스/구조체가 상호 참조할 경우, 컴파일이 안될 수도 있습니다.

```cpp
class MyClass {
    YourClass m_Your; // (X) 컴파일 오류. YourClass가 뭔지 모릅니다.
};
class YourClass {
    MyClass m_My; // MyClass는 상위에 정의되어 사용할 수 있습니다.
}; 
```

이런 경우, [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)으로 `YourClass`가 무언지 컴파일러에게 알려줘야 합니다. 

이때 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)만 참조하는 곳(*`MyClass`*)은 

1. 구체 정의가 아닌 [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)로 사용하여야 하고, 
2. [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)한 것의 실제 선언(*`YourClass` 선언*) 후 해당 개체를 사용해야 합니다. 
 
이에 따라 클래스 선언과 정의를 분리하고 다음 순서대로 작성해야 합니다.

```cpp
// 1. YourClass 전방 선언
class YourClass; 

// 2. MyClass 선언
class MyClass {
    // (O) 전방 선언을 통해 YourClass가 대충 클래스라는 걸 압니다. 
    // 반드시 포인터나 참조자와 같은 참조 형식이어야 합니다.
    YourClass* m_Your; 

    // YourClass의 구체 정의가 필요하여 선언만 합니다.
    void f(); 
};

// 3. YourClass 선언
class YourClass {
    // MyClass는 상위에 정의되어 사용할 수 있습니다.
    MyClass m_My; 

public:
    void g() {}
}; 

// 4. MyClass 정의 - YourClass를 사용하고 있어 YourClass 선언 후 작성합니다.
void MyClass::f() {
    m_Your->g(); 
}
```

**중첩 클래스의 전방 선언**

[중첩 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A4%91%EC%B2%A9-%ED%81%B4%EB%9E%98%EC%8A%A4)는 클래스 바깥에서 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)을 할 수 없습니다. 클래스 내에서 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)해야 합니다.

```cpp
class T; // (O)
class T::Nested; // (X) 컴파일 오류. 중첩 클래스는 클래스 바깥에서 전방 선언을 할 수 없습니다.

class T {
public:
    class Nested; // (O) 클래스 내에서 전방 선언 해야 합니다.
    class Nested {};
};
```

또한 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)시에 [접근 지정자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90)가 동일해야 합니다.

```cpp
class T {
private:
    class Nested; // 전방 선언을 private로 했습니다.
private:
    class Nested {}; // (O)
public:
    // class Nested {}; // (X) 컴파일 오류. 전방 선언에선 private이고 실제 선언은 public 입니다.
};
```

