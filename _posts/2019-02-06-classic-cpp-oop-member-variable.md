---
layout: single
title: "#6. [고전 C++ 개체 지향] 멤버 변수"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 멤버 변수 초기화시, 생성후 대입하지 말고 초기화 리스트를 사용하라.(초기화 리스트의 순서는 멤버 변수 정의 순서에 맞춰라.)
> * 생성자에서 필요한 인자를 모두 나열하고 초기화하라.
> * 복사 생성자가 필요하다면, 암시적 복사 생성자가 정상 동작하도록 멤버 개체 Handler를 구현하고, 필요없다면 못쓰게 만들어라.

> * 메모리 패딩을 고려하여 멤버 변수 정의 순서를 정하라.
 
**개요** 

|항목|내용|초기화|초기화 방법|수명|
|--|--|--|--|--|
|멤버 변수|특정 개체에 속하는 변수|선택|초기화 리스트|개체 생성시 생성자 실행전 초기화 리스트에서 생성 ~ 소멸자 실행 후 종료|
|참조자 멤버 변수|멤버 변수와 동일|필수|초기화 리스트|멤버 변수와 동일|
|포인터 멤버 변수|멤버 변수와 동일<br/>널 지원|선택|초기화 리스트|멤버 변수와 동일|
|상수 멤버 변수|멤버 변수와 동일<br/>수정 불가|필수|초기화 리스트<br/>선언부 초기화|멤버 변수와 동일|
|정적 멤버 변수|특정 개체에 속하지 않는 변수.<br/>클래스에 응집되어 있는 전역 변수.|선택|별도 정의|`main` 호출 ~ `main` 종료|
|정적 상수 멤버 변수|정적 멤버 변수와 동일<br/>수정 불가|필수|선언부 초기화<br/>별도 정의|멤버 변수와 동일|

멤버 변수는 개체의 데이터를 저장 및 관리합니다. 주로 `private`로 은닉하고, [Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#getter-%ED%95%A8%EC%88%98)와 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#setter-%ED%95%A8%EC%88%98)를 통해 외부에 노출합니다.

참조자 멤버 변수와 상수 멤버 변수는 초기화가 필수입니다. 반드시 값 생성자를 만들어 초기화 리스트를 이용해서 초기화해야 합니다.

상수 멤버 변수와 정적 상수 멤버 변수는 선언부에서 초기화 할 수 있습니다.

```cpp
const int m_Val4 = 0; // (O) 선언부 초기화 지원
static const int s_c_m_Val6 = 0; // (O) 선언부 초기화 지원
```

정적 멤버 변수는 선언과 정의를 분리해서 작성해야 합니다.

```cpp
class T {
public:
    static int s_m_Val5; // 정적 멤버 변수. 별도 정의 필요
};
int T::s_m_Val5; // 별도 정의 필요
```
전체적인 선언과 정의 방법은 다음과 같습니다.

```cpp
class T {
    int m_Val1; // 멤버 변수
    int& m_Val2; // 참조자 멤버 변수. 참조자는 초기화 되어야 함
    int* m_Val3; // 포인터 멤버 변수
    const int m_Val4; // 상수 멤버 변수. 초기값이 세팅되어야 함
    // const int m_Val4 = 0; // (O) 선언부 초기화 지원
public:    
    static int s_m_Val5; // 정적 멤버 변수. 선언만 했기에 별도 정의 필요
    static const int s_c_m_Val6; // 정적 상수 멤버 변수
    // static const int s_c_m_Val6 = 0; // (O) 선언부 초기화 지원
public: 
    // 참조자 멤버 변수와 상수 멤버 변수는 초기화 필수
    T(int& val2, int val4) :
        m_Val2(val2), // 참조자는 반드시 초기화 리스트에서 세팅되어야 함
        m_Val4(val4) {} // 상수 멤버 변수는 초기값이 세팅되어야 함
};

int T::s_m_Val5; // 선언만 했기에 별도 정의 필요
const int T::s_c_m_Val6 = 0; // 선언만 했다면 정의 필요. 선언부 초기화를 했다면 별도 정의 불필요
```

# 초기화 리스트

멤버 변수 초기화시 생성 후 대입하면, 불필요한 생성자가 여러번 호출되고 대입의 오버헤드가 생깁니다.

```cpp
class T {
public:
    T() {} // 기본 생성자
    explicit T(int val) {} // 값 생성자
};

class U {
    T m_X;
    T m_Y;
public:
    U(int x, int y) { // (△) 비권장. 멤버 변수를 기본 생성자로 생성합니다.  
        m_X = T(x); // (△) 비권장. 값 생성자로 임시 개체를 생성 후, 대입 연산자로 대입합니다.
        m_Y = T(y);
    }
};
```

따라서, 멤버 변수 초기화시, 생성후 대입하지 말고 초기화 리스트를 사용하는게 불필요한 생성이나 대입이 없어 좋습니다.

```cpp
class T {
public:
    T() {} // 기본 생성자
    explicit T(int val) {} // 값 생성자
};

class U {
    T m_X;
    T m_Y;
public:
    U(int x, int y) : 
        m_X(x), 
        m_Y(y) {} // (O) 값 생성자로 멤버 변수를 초기화 합니다. 
};
```

**필요한 인자를 모두 나열하고 초기화**

값 생성자의 인자 작성시에는 [명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 모든 요소를 나열하고 초기화 하는게 **코딩 계약**상 좋습니다. 다음처럼 일부 멤버 변수만 초기화 하고, 나중에 별도 함수를 호출하여 초기화를 마무리하면, 사용자가 실수로 빼먹을 수도 있고, **예외 안정** 프로그래밍에도 좋지 않습니다. 생성 후 대입 과정에서 예외가 발생하면 난감해지니까요.([예외 안정 생성자](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-constructor/) 참고)

```cpp
class T {
    int m_X;
    int m_Y;
public:
    explicit T(int x) : 
        m_X(x) {} // (△) 비권장. 멤버 변수 중 m_Y는 초기화하지 않았습니다.
    void SetX(int x) {m_X = x;}
    void SetY(int y) {m_Y = y;}
};

// (△) 비권장. 멤버 변수 중 m_Y는 초기화하지 않았습니다. 
// 초기화를 위해 추가로 필요한 요소가 뭔지 T 클래스를 파악해 봐야 합니다.
T t(10); 

// (△) 비권장. m_Y를 함수를 별도로 호출해야 합니다.
t.SetY(20); 
```

보다는 

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) : 
        m_X(x), // (O) 생성자에서 모든 멤버 변수를 초기화 합니다.
        m_Y(y) {} 
};

// (O) 생성자에서 모든 멤버 변수를 초기화 합니다.
// 필요한 요소가 뭔지 생성자만 봐도 알 수 있습니다. 
T t(10, 20);  
```

가 낫습니다.

**멤버 변수 정의 순서와 초기화 리스트 순서**

초기화 리스트에 기재된 순서가 아닌, 멤버 변수 정의 순서로 초기화 됩니다. 따라서 헷갈리지 않도록 멤버 변수 정의 순서에 따라 초기화 리스트를 작성하세요.

```cpp
class T {
    int m_A;
    int m_B;
    int m_C;
public:
    T(int a, int b, int c) :
        m_C(c + m_B), // (△) 비권장. 3
        m_B(b + m_A), // (△) 비권장. 2
        m_A(a) {} // (△) 비권장. 1
    int GetA() const {return m_A;}
    int GetB() const {return m_B;}
    int GetC() const {return m_C;}
};
T t(10, 20, 30);
EXPECT_TRUE(t.GetA() == 10 && t.GetB() == 30 && t.GetC() == 60);
```

**멤버 변수명과 인자명이 같은 경우**

멤버 변수명과 인자명이 같더라도 함께 사용할 수 있습니다.

1. 생성자의 초기화 리스트에서는 멤버 변수와 인자를 함께 사용할 수 있습니다. `a(a)`로 사용한 경우, 멤버 변수 `a`의 복사 생성자에 인자 `a`를 전달합니다.

2. 함수 본문에서는 인자가 멤버 변수를 가리므로 `this->멤버 변수명`으로 사용해야 합니다.
   
```cpp
class T {
public:
    int a;
    int b;
    int c;
    T(int a, int b, int c) : // 멤버 변수명과 인자명이 같더라도 초기화 리스트에서 사용 가능합니다.
        a(a), 
        b(b),
        c(c) {
        // 함수 본문에서 멤버 변수명과 인자명이 같으면, 멤버 변수는 this를 써서 접근합니다.
        this->a += 1; // 멤버 변수 a를 수정함
        a += 2; // 인자 a를 수정함       
    }
};
T t(10, 20, 30); 
EXPECT_TRUE(t.a == 11 && t.b == 20 && t.c == 30);
```








복사 생성자 호환성

대입 연산자 호환성

소멸자 호환


# 메모리 할당에 따른 멤버 변수 정의 순서

컴파일러는 클래스나 구조체의 멤버 변수를 할당하는데 있어, 메모리 접근 편의를 위해 4byte단위로 멤버 변수를 할당합니다.(이를 패딩(padding)이라 합니다. [`#pragma`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma) 언급) 

다음 코드는 패딩 작업에 의해 빈공간이 생겨 16byte 크기이지만,

```cpp
class T {
    char m_Char1; // 1byte, 메모리 접근 편의를 위해 32bit(4byte) 단위로 할당(패딩). 3byte 빈공간이 생김 
    int m_Int1; // 4byte
    char m_Char2; // 1byte, 메모리 접근 편의를 위해 32bit(4byte) 단위로 할당(패딩). 3byte 빈공간이 생김 
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 16);
```

다음 코드는 패딩이 최소화되어 12byte입니다.

```cpp
class T {
    char m_Char1; // 1byte
    char m_Char2; // 1byte, 패딩을 위해 2byte 빈공간이 생김
    int m_Int1; // 4byte
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 12);      
```

멤버 변수가 4byte 단위로 할당되므로, `char`와 같이 4byte 이하인 멤버 변수들은 몰아서 정의하는게 좋습니다.(`pragma pack`을 이용하면, 데이터 버스 크기를 줄여 메모리 낭비는 줄일 수 있으나, 메모리 접근 속도는 저하됩니다. [`#pragma`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma) 참고) 



