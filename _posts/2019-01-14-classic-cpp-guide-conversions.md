---
layout: single
title: "#14. [고전 C++ 가이드] 암시적 형변환, 명시적 형변환(const_cast, static_cast, dynamic_cast, reinterpret_cast), 형변환 정의, explicit"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 암시적으로 형변환하기 보다는 명시적으로 형변환 하라.
> * bool 형변환 정의는 하지 마라. 나아가 모든 타입의 형변환 정의를 하지 마라. 뜻하지 않게 몰래 암시적 형변환 한다.
> * 할 수 있는한 최선을 다하여 형변환 하지 마라.

**암시적 형변환**

각 자료형은 하기 규칙에 따라 암시적으로 형변환 됩니다. 하지만 명시적으로 변환하는 것이 추후 코드 분석에 도움이 됩니다. C언어의 나쁜 잔재이니 사용하지 마세요.

```cpp
{
    // char, signed char, unsigned char, short 는 가능하면 int로 변환, 안되면 unsigned int
    int a = (char)'a';
    int b = (signed char)'a';
    int c = (unsigned char)'a';
    int d = (short)1;
}
{
    // wchar_t는 int, unsigned int, long, unsigned long 으로 변환
    int a = (wchar_t)L'a';
    unsigned int b = (wchar_t)L'a';

    long c = (wchar_t)L'a';
    unsigned long d = (wchar_t)L'a';
}
{
    // 열거형은 int, unsigned int, long, unsigned long 으로 변환
    enum Color {Black};
    int a = (Color)Black;
    unsigned int b = (Color)Black;
    long c = (Color)Black;
    unsigned d = (Color)Black;
}
{
    // bool 은 int(true : 1, false : 0) 로 변환
    int a = (bool)true;
}
{
    // float 은 double 로 변환
    double a = (float)1.0F;
}
{
    // none-const는 const로 변환. 반대는 컴파일 오류
    int a = 10;
    const int* b = &a;
    // int* c = b; // (X) 컴파일 오류. const는 none-const로 변환 안됨
}
{
    // 모든 포인터는 void*로 변환. NULL은 모든 포인터로 변환
    int a = 10;
    void* p = &a; // 모든 포인터는 void*로 변환됨
    p = NULL; // NULL 은 모든 포인터로 변환됨
}
{
    // 자식 개체는 부모 개체로 변환됨(Up Casting)
    class Base {};
    class Derived: public Base {};

    Derived d;
    Base& b = d;
}
```

**명시적 형변환**

암시적으로 형변환 되지 않는다면, 하기와 같이 괄호를 이용하여 명시적으로 형변환 할 수 있습니다.(잘못 사용하면 예기지 못한 데이터 절삭이나 변환으로 예외가 발생할 수 있습니다. 사용하지 말아야 합니다. 심지어 검색도 어렵습니다. C언어의 나쁜 잔재입니다.)

```cpp
{
    // 괄호를 이용한 형변환
    // (X) 비권장. 잘못 사용하여 데이터 절삭으로 고생할 수 있으니, 
    // 나중에 검색이라도 쉽게 되도록 `static_cast`를 사용하세요.
    float f = 10.F;
    int a = (int)f;
    int b = int(f);

    EXPECT_TRUE(a == 10 && b == 10);
}
```

`const_cast`를 이용하여 상수성을 뗄 수 있습니다.(그러나 **상수성 계약**위반이니 하지 마세요.)

```cpp
{
    int a = 10;
    const int& r = a;

    // r이 상수성 계약으로 참조하는데 멋대로 바꿨습니다.
    // (X) 비권장. 코딩 계약상 금지입니다.
    const_cast<int&>(r) = 20; // 포인터와 참조자만 가능

    EXPECT_TRUE(a == 20); 
}
```

`static_cast`를 이용하여 암시적으로 형변환되지 않는 것들을 명시적으로 할 수 있습니다.(하지만 **코딩 계약** 위반이니 사용하지 마세요.)

```cpp
{
    // 실수를 정수로 명시적 변환. 
    // (X) 비권장. 그냥 내림, 반올림, 올림 함수를 쓰세요.
    int a = static_cast<int>(3.14F);
    EXPECT_TRUE(a == 3); 

    // 정수를 열거형 상수로 변환. 
    // (X) 비권장. 변환 함수를 따로 만드세요.
    enum Color {Black = 0};
    Color b = static_cast<Color>(0);

    // void* 를 다른 포인터로 변환. 
    // (X) 비권장. 괜히 void*로 바꿔놓지 말고 원래 포인터 타입으로 쓰세요.
    int c = 10;
    void* v = &c;
    int* p = static_cast<int*>(v);

}
{
    // 부모 개체를 자식 개체로 변환(Down Casting)
    class Base {};
    class Derived: public Base {}; 
    class Other {};

    Derived d;
    Base& b = d;
    // static_cast는 상속관계가 있어야 함(런타임 검사를 수행하지 않음). 
    // (X) 비권장. 자식 개체에 의존성을 두지 마세요.
    Derived& downCasting = static_cast<Derived&>(b);
    
    // (X) 컴파일 오류. b는 Other와 아무런 상속관계가 없음
    Other& other = static_cast<Other&>(b);
}
```

`dynamic_cast`는 상속 관계가 있는 개체간의 변환을 합니다.(RTTI(Runtime Type Info)가 있는 개체(가상함수가 있는 개체)만 가능.) 변환에 실패하면, 포인터 유형인 경우 널(`NULL`)을 리턴하고, 참조 유형인 경우 `bad_cast` 예외를 발생시킵니다. 

```cpp
class Base {
public:
    // dynamic_cast를 사용하려면 가상함수가 있어야 합니다.
    // RTTI(Runtime Type Info) 가 필요하기 때문입니다.
    virtual ~Base() {} 
};
class Derived: public Base {}; 
class Other {};

Derived d;
Base* b = &d;
// dynamic_cast는 상속관계가 있어야 함(런타임 검사를 수행함)
// dynamic_cast를 사용하려면 가상함수가 있어야 합니다.(가상함수가 없으면 컴파일 오류)
// (X) 비권장. 자식 개체에 의존성을 두지 마세요. 부모 개체의 인터페이스가 충분한지 확인하세요.
Derived* downCasting = dynamic_cast<Derived*>(b);      
EXPECT_TRUE(downCasting != NULL);

// (O) 상속관계가 아니어서 캐스팅이 안되면 포인터의 경우 NULL을 리턴하고, 참조유형은 bad_cast 예외를 발생시킵니다.
Other* other = dynamic_cast<Other*>(b);
EXPECT_TRUE(other == NULL);
```

`reinterpret_cast`는 상속관계를 무시하고 그냥 변환해 버립니다. 하지만, 상수성은 지켜 줍니다. 아무튼 타입에 기반한 **코딩 계약** 을 위반하니 사용하지 마세요.

```cpp
{
    class Base {};
    class Other {};
    Base b;
    // (X) 비권장. 상속관계가 없더라도 변환해 줍니다.
    Other& other = reinterpret_cast<Other&>(b);
    
    // (X) 컴파일 오류. 다행히 개체의 주소를 그냥 char에 담아버리지는 않습니다.
    char a = reinterpret_cast<char>(&b);

    int i = 10;
    const int* p1 = &i;
    // (X) 컴파일 오류. 상수성은 지켜 줍니다.
    int* p2 = reinterpret_cast<int*>(p1);
}
```

**형변환 정의**

하기와 같이 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 위해 사용자 정의 형변환을 정의할 수 있습니다. 

```cpp
class T {
public:
    // int로 형변환 되면 10을 리턴합니다.
    operator int() const { return 10; }
    
    // char로 형변환 되면 1을 리턴합니다.
    operator char() const { return 1; }
};

T t;
int i = t;
char c = t;

EXPECT_TRUE(i == 10);
EXPECT_TRUE(c == 1);
```

하지만, 기본 암시적 형변환과 어우러 지면서 예기치 못한 경우에 멋대로 형변환 될 수 있습니다. 심지어 코드 분석도 힘들어 집니다. 

하기와 같이 멤버함수를 작성하는게 충분히 사용하기 좋고, 사용 위치를 검색하기 좋고, 분석하기 좋습니다.

```cpp
class T {
public:
    // int로 변환합니다.
    int ToInt() const { return 10; }
    
    // char로 변환합니다.
    char ToChar() const { return 1; }
};

T t;
int i = t.ToInt();
char c = t.ToChar();

EXPECT_TRUE(i == 10);
EXPECT_TRUE(c == 1);
```

**안전하지 않은 bool 형변환 정의**

`bool`은 암시적으로 `int`로 형변환되면서 뜻하지 않은 동작을 할 수 있습니다. **코딩 계약** 이 힘들어 지니, `bool` 형변환 정의를 하지 마세요.

```cpp
class T {
public:
    operator bool() { return true; }
};

T t;
bool status = false;
// (X) 비권장. 컴파일 오류가 나지 않는 비극
// t가 bool로 변환되어 true가 되고, 
// 암시적으로 true가 int로 변환되어 1이 되고 조건식이 참이 됨
// 의도한 코드인지를 모르겠으나,
// 이렇게 암시적인 상황을 활용해서 복잡하게 의도하는 건 코드 분석이나 코딩 계약에 좋지 않음
if (0 < t) { 
    status = true;
}
EXPECT_TRUE(status == true);
```

**explicit**


