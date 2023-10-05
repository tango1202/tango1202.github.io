---
layout: single
title: "#6. [고전 C++ 개체 지향] 멤버 변수, 초기화 리스트"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 멤버 변수 초기화시, 생성후 대입하지 말고 초기화 리스트를 사용하라.(초기화 리스트의 순서는 멤버 변수 정의 순서에 맞춰라.)
> * 생성자에서 필요한 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 모두 나열하고 초기화하라.
> * 메모리 패딩을 고려하여 멤버 변수 정의 순서를 정하라.
> * [암시적 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [암시적 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)가 정상 동작하도록 멤버 변수로 스마트 포인터([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등)를 사용하라.

> **모던 C++**
> * 비정적 멤버 변수도 멤버 선언부에서 초기화를 할 수 있어 초기화 작성이 쉬워졌습니다.([멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/) 참고)
> * `alignas()` 와 `alignof()` 를 이용하여 메모리 정렬 방식을 표준화 했습니다.([alignas(), alignof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-align/) 참고)
> `align()`은 메모리 정렬된 포인터를 구합니다.([align()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#align) 참고)
 
# 개요 

|항목|내용|초기화|권장 초기화 방법|수명|
|--|--|--|--|--|
|멤버 변수|특정 개체에 속하는 변수|선택|초기화 리스트|개체 생성시 생성자 실행전 초기화 리스트에서 생성 ~ 소멸자 실행 후 종료|
|참조자 멤버 변수|멤버 변수와 동일|필수|초기화 리스트|멤버 변수와 동일|
|포인터 멤버 변수|멤버 변수와 동일<br/>널 지원|선택|초기화 리스트|멤버 변수와 동일|
|상수 멤버 변수|멤버 변수와 동일<br/>수정 불가|필수|초기화 리스트<br/>선언부 초기화|멤버 변수와 동일|
|[정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)|특정 개체에 속하지 않는 변수.<br/>클래스에 응집되어 있는 전역 변수.|선택|별도 정의|`main` 호출 ~ `main` 종료|
|정적 상수 멤버 변수|정적 멤버 변수와 동일<br/>수정 불가|필수|선언부 초기화<br/>별도 정의|멤버 변수와 동일|

멤버 변수는 개체의 데이터를 저장 및 관리합니다. 주로 `private`로 은닉하고, [Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)와 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)를 통해 외부에 노출합니다.

참조자 멤버 변수와 상수 멤버 변수는 초기화가 필수입니다. 반드시 값 생성자를 만들어 초기화 리스트를 이용해서 초기화해야 합니다.

```cpp
class T {
    int& m_Val2; // 참조자 멤버 변수. 참조자는 초기화 되어야 함
    const int m_Val4; // 상수 멤버 변수. 초기값이 세팅되어야 함
    ```
public: 
    // 참조자 멤버 변수와 상수 멤버 변수는 초기화 필수
    T(int& val2, int val4) :
        m_Val2(val2), // 참조자는 반드시 초기화 리스트에서 세팅되어야 함
        m_Val4(val4) {} // 상수 멤버 변수는 초기값이 세팅되어야 함
}; 
```

상수 멤버 변수와 정적 상수 멤버 변수는 선언부에서 초기화 할 수 있습니다.

```cpp
const int m_Val4 = 0; // (O) 선언부 초기화 지원
static const int s_c_m_Val6 = 0; // (O) 선언부 초기화 지원
```

> *C++11 부터는 비 정적 멤버 변수도 멤버 선언부에서 초기화를 할 수 있어 초기화 작성이 쉬워졌습니다.([멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/) 참고)*

[정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)는 선언과 정의를 분리해서 작성해야 합니다.([정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 보다는 [함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 사용을 권장합니다.)

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
    U(int x, int y) { // (△) 비권장. 초기화 리스트가 없어서 멤버 변수를 기본 생성자로 생성합니다.  
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

값 생성자의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 작성시에는 [명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 모든 요소를 나열하고 초기화 하는게 **코딩 계약**상 좋습니다. 

다음처럼 일부 멤버 변수만 초기화 하고, 나중에 별도 함수를 호출하여 초기화를 마무리하면, 사용자가 실수로 빼먹을 수도 있고, **예외 안전** 프로그래밍에도 좋지 않습니다. 불완전하게 생성된 개체에 별도 Setter를 호출하여 완전하게 만드는 중에 예외가 발생하면, 이미 생성된 예외 안전 처리를 위해 소멸시켜야 하는데, 혹시나 이미 이 개체를 참조하는 곳이 있다면, 찾기도 힘들고, 찾았더라도 처리를 어찌해야 할지 난감해 지니까요.([예외와 생성자](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8%EC%99%80-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)

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
    int m_A; // m_A, m_B, m_C의 순서로 초기화 됩니다.
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

생성자의 초기화 리스트에서는 멤버 변수명과 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)명이 같더라도 함께 사용할 수 있습니다.

1. `a(a)`로 사용한 경우, 멤버 변수 `a`의 복사 생성자에 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) `a`를 전달합니다.

2. 함수 본문에서는 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 멤버 변수를 가리므로 `this->멤버 변수명`으로 사용해야 합니다.

저는 헷갈려서 `m_XXXX`와 같이 `m_` 접두어를 선호합니다만, `XXXX_`와 같이 뒤에 `_`를 사용하는 방법도 있습니다.(다만 앞에 `_`를 사용하진 마세요. [`_` 선택적 금지](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-naming/#_-%EC%84%A0%ED%83%9D%EC%A0%81-%EA%B8%88%EC%A7%80) 참고)

   
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
# 개체 크기와 메모리 정렬

일반적으로 개체의 크기는 멤버 변수의 합입니다.

```cpp
class T1 {
    int m_X;
    int m_Y;
};
EXPECT_TRUE(sizeof(T1) == sizeof(int) * 2); // 8
```

하지만 다음의 경우를 보면, `char`(1byte) + `int`(4byte) 여서 5byte 일것 같지만, 사실은 8byte 입니다.

```cpp
class T2 { // 멤버 변수중 가장 큰 int 에 맞춰 정렬
    char m_X; // 1byte. 3 byte 패딩
    int m_Y;
};
EXPECT_TRUE(sizeof(T2) == sizeof(int) * 2); // 8
```

이는 메모리에서 멤버 변수의 데이터를 좀 더 빠른 속도로 읽기 위해, 멤버 변수 중 가장 크기가 큰 값으로 **메모리 정렬(Memory Alignment)** 을 하기 때문입니다. 메모리 정렬은 1byte이거나 2의 배수 byte(2, 4, 6, 8...) 일 수 있습니다. 

CPU는 메모리(RAM)에 접근하여 처리할 데이터를 읽어오는데, 이 접근 횟수가 많을 수록 속도가 느려집니다.

CPU가 한번에 데이터를 가져올 수 있는 크기가 4byte나 8byte로 정해져 있는데요, 4byte로 가져온다고 가정하고, 메모리를 정렬하지 않고 1byte 단위로 배치된 멤버 변수를 읽는다면, 상기 `T2`의 경우 `int`값(`m_Y`)을 읽으려면 2번 접근해야 합니다. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2b1a0f82-c0bc-44a3-9f7f-58df07c52508)

하지만, 메모리를 4byte 단위로 정렬해 두었다면 1번 접근하면 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5edca222-41d1-4edd-8f7b-2c741a1b9f2b)

이러한 이점 때문에 메모리 정렬을 수행하며, 메모리 정렬을 위해 추가된 byte를 패딩(Padding) 이라 합니다.

```cpp
class T3 { // 멤버 변수중 가장 큰 double에 맞춰 정렬
    char m_X; // 1byte. 7byte 패딩
    double m_Y;
};
EXPECT_TRUE(sizeof(T3) == sizeof(double) * 2); // 16

struct T4 { // 멤버 변수중 가장 큰 double에 맞춰 정렬.
    char m_X; // 1byte. 3byte 패딩
    int m_Y; // 4byte. 
    double m_Z;
};
EXPECT_TRUE(sizeof(T3) == sizeof(double) * 2); // 16
```

강제로 메모리 정렬 `byte` 크기를 변경하는 방법은 [`#pragma pack`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma) 을 이용하면 됩니다. [`#pragma pack`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma)을 이용하면, 메모리 정렬 `byte` 크기를 줄여 메모리 낭비는 줄일 수 있으나, 메모리 접근 속도는 저하됩니다.

> *C++11 부터는 `alignas()` 와 `alignof()` 를 이용하여 메모리 정렬 방식을 표준화 했습니다.([alignas(), alignof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-align/) 참고)*

**빈 클래스와 자식 개체의 크기**

멤버 변수가 없는 빈 클래스라도 자료형이므로 최소 1byte의 크기를 가집니다. 이를 상속한 개체에서 멤버 변수가 구현되었다면, 강제로 추가된 1byte는 빼고 크기가 계산됩니다.

```cpp
class Empty {}; // 빈 클래스는 강제로 1byte
EXPECT_TRUE(sizeof(Empty) == 1);

class EmptyDerived : public Empty { // 빈 클래스를 상속받으면, 강제 1byte는 빼고 크기가 설정됨
    int m_X;
};
EXPECT_TRUE(sizeof(EmptyDerived) == sizeof(int));
```

빈 클래스라도 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있다면 [가상 함수 테이블](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)이 생성됩니다. 컴파일러에 따라 다를 수도 있으나 대부분 8byte 입니다.

```cpp
class Base { // 멤버 변수는 없지만 virtual 이 있어 가상 함수 테이블이 생성됨
public:
    virtual ~Base() {}
};
EXPECT_TRUE(sizeof(Base) == 8);

class Derived : public Base { // 가상 함수 테이블 크기로 정렬됨
    char m_X;
};
EXPECT_TRUE(sizeof(Derived) == 8 * 2); 
std::cout << sizeof(Derived) << std::endl;    
```

# 메모리 할당에 따른 멤버 변수 정의 순서

멤버 변수의 메모리 정렬을 고려하여 멤버 변수를 정의하는게 좋습니다. 특히, `char`와 같이 4byte 이하인 멤버 변수들은 몰아서 정의하는게 좋습니다.

다음 코드는 패딩 작업에 의해 빈공간이 생겨 16byte 크기이지만,

```cpp
class T {
    char m_Char1; // 1byte, 3byte 패딩 
    int m_Int1; // 4byte
    char m_Char2; // 1byte, 3byte 패딩
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 16);
```

다음 코드는 패딩이 최소화되어 12byte입니다.

```cpp
class T {
    char m_Char1; // 1byte
    char m_Char2; // 1byte, 2byte 패딩
    int m_Int1; // 4byte
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 12);   
```

# 포인터 멤버 변수

포인터 멤버 변수는 복사 생성이나 대입 연산시 복사 되면서  [소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)을 하게 되며, `delete` 해야 할지 말아야 할지 상당히 고민되는 순간들을 많이 만들어 냅니다.

다음 코드에서 `T`는 `new`로 생성한 포인터도 전달 받고, 지역 변수 `val`의 주소도 전달받습니다. `T`가 알아서 이를 판단하기 어려워 호출하는 쪽에서 `delete`해주어야 합니다.

```cpp
class T {
    int* m_Ptr;
public:
    T(int* ptr) : 
        m_Ptr(ptr) {}
};
int* ptr = new int;
int val = 10;

T t1(ptr); // (△) 비권장. ptr는 new 한 것이기 때문에 m_Ptr은 delete 되어야 합니다.
delete ptr; // (△) 비권장. 그냥 밖에서 지워버렸습니다.

T t2(&val); // 요것은 delete하면 안됩니다.
```

이렇게 외부에서 일일이 포인터를 `delete`하면, 다음처럼 이미 `delete` 한 `t1`을 `t2`에 대입하는 실수도 빈번히 하게 됩니다. 아무리 꼼꼼히 검토하더라도요.

```cpp
T t1(ptr);
delete ptr; // delete 했습니다.

T t2(&val); 

t2 = t1; // (△) 비권장. 이미 지워버린 ptr을 가진 t1을 t2에 복사했습니다. 이런 실수 많이 하게 됩니다.
```

이런 고민을 하면서 코딩하다 보면 논리 자체에 대한 고민보다 포인터 처리에 대한 고민만 많아지므로, 포인터를 관리하는 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 이용하는게 좋습니다.

|항목|내용|
|--|--|
|스마트 포인터([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등)|소유권 분쟁시 소유권 이전을 할 것인지, 깊은 복제를 할 것인지, 자원을 공유할 것인지, 유일한 자원을 사용할 것인지에 따라 용도에 맞는 스마트 포인터를 사용합니다.([복사 대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고, 스마트 포인터([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등) 참고)|
|[Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)|소유권 분쟁이 없도록 복사 생성이나 대입 연산을 원천 차단하고, 획득된 자원을 해제하는 역할을 합니다.([Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 참고)|

스마트 포인터([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)는 멤버 변수로 사용시 다음 조건을 충족해야 합니다.

|항목|내용|
|--|--|
|암시적 복사 생성자와 호환|개체간 복사 생성시 포인터 멤버 변수의  [소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 없어야 합니다.([복사 대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고, 스마트 포인터([shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등) 참고)|
|암시적 대입 연산자와 호환|개체간 대입시 포인터 멤버 변수의  [소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 없어야 합니다.([복사 대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고, 스마트 포인터([shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등) 참고)<br/>예외 발생시 예외 안전적으로 이전 상태를 유지해야 합니다. 멤버 변수가 1개면 스마트 포인터로 가능하지만, 멤버 변수가 2개 이상이면 명시적으로 `swap`을 이용한 대입 연산자를 구현하거나 [PImpl 이디엄](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-pimpl/)으로 클래스를 구성합니다.([멤버 변수가 2개 이상인 경우 스마트 포인터와 대입 연산자와의 호환성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EA%B0%80-2%EA%B0%9C-%EC%9D%B4%EC%83%81%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0%EC%99%80-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%EC%9D%98-%ED%98%B8%ED%99%98%EC%84%B1) 참고)|
|암시적 소멸자와 호환|소멸자에서 별다른 `delete` 를 작성하지 않더라도 유효 범위를 벗어나면 자동 소멸되도록  만듭니다.([복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고, 스마트 포인터([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등) 참고, [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 참고)|


상기 조건이 충족된 클래스 예제는 [멤버 변수가 2개 이상인 경우 스마트 포인터와 대입 연산자와의 호환성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EA%B0%80-2%EA%B0%9C-%EC%9D%B4%EC%83%81%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0%EC%99%80-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%EC%9D%98-%ED%98%B8%ED%99%98%EC%84%B1)을 참고하기 바랍니다.

