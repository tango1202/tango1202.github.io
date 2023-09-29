---
layout: single
title: "#9. [고전 C++ 가이드] 구조체(struct)와 클래스(class) 와 공용체(union)와 비트 필드"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 구조체와 클래스의 차이는 초기화 방법과 기본 접근 지정자(구조체는 `public`, 클래스는 `private`) 뿐이다.
> * 공용체는 플랫폼에 따라, 컴파일러에 따라, 최적화 옵션에 따라 예기치 못한 동작을 할 수도 있으니 꼭 필요한 경우 한정적으로 사용하라.

> **모던 C++**
> * 공용체 멤버에서 생성자/소멸자/가상 함수 사용 제한이 풀려 메모리 절약을 위한 코딩 자유도가 높아졌습니다.([무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/) 참고)

# 개요

|항목|구조체|클래스|공용체|
|--|--|--|--|
|용량|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)들의 총합|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)들의 총합|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)들중 가장 큰값|
|초기화|`T t = {10, 20};` 지원|값 생성자|X|
|기본 접근 지정자|`public`|`private`|`public`|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|O|O|O|
|[멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)|O|O|O|
|참조자 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|O|O|X|
|재정의 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)|O|O|X|
|재정의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)|O|O|X|
|[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)|O|O|X|
|상속|O|O|X|

구조체와 클래스와 공용체는 타입이 다른 여러 데이터를 집합으로 묶어 관리할 수 있게 해줍니다.

특히, 구조체와 클래스는 [초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/) 방법과 기본 접근 지정자 외에는 모두 동일합니다.

```cpp
struct S {
    int x; // 기본적으로 public
    int y;
};

S s = {10, 20}; // 구조체는 중괄호 초기화 지원

class C {
    int m_X; // 기본적으로 private
    int m_Y;
public:
    C(int x, int y) {} // 값 생성자 정의
};
C c(10, 20); // 클래스는 값 생성자만 가능. 중괄호 초기화 미지원
```

공용체는 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)들끼리 메모리 영역을 공유합니다. 이에 따라 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)나 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 위한 다양한 기능들이 제약됩니다. 꼭 필요한 경우에만 한정적으로 사용하시기 바랍니다.

# 구조체와 클래스

구조체와 클래스는 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 위한 다양한 기능들을 제공합니다.

**멤버 사양**
   
클래스(구조체)의 멤버는 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)와 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/) 뿐만아니라, [열거형 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/), 중첩 클래스(구조체), [타입 재정의(`typdef`)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%B3%84%EC%B9%AD)를 포함할 수 있습니다.

특히, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/), [대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 정의하지 않으면, 컴파일러가 암시적으로 정의해 줍니다.([클래스의 암시적 정의](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-implicit-definition/) 참고)

```cpp
class T {
    // 멤버 변수
    int m_D1; 

    // 정적 멤버 변수. 단 중첩 클래스, 함수 내부 로컬 클래스에서는 사용 못함
    static const int s_D2 = 1; 

    // ----
    // 멤버 함수
    // ----
    T() {} // 기본 생성자
    T(const T& other) {} // 복사 생성자
    ~T() {} // 소멸자
    T& operator =(const T& other) {return *this;} // 대입 연산자
    
    operator int() const {return 0;} // 형변환 연산자

    void f1(int) {} // 멤버 함수
    void f2(int) {} // 상수 멤버 함수

    virtual void f3(int) {} // 가상 함수    
    virtual void f4(int) = 0; // 순가상 함수

    static void f5(int) {} // 정적 멤버 함수
    // ----

    // 열거형 상수
    enum {Left, Top, Right, Bottom}; 

    // 중첩 클래스(구조체)
    class NestedClass { 
        int m_D3;
    };
    
    // 타입 재정의
    typedef NestedClass Inner; 
};
```

각 항목은 다음을 참고하기 바랍니다.

|항목|내용|
|--|--|
|멤버 변수|개체의 데이터를 저장 및 관리합니다.<br/>([멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 참고)|
|기본 생성자|인수없이 개체를 생성합니다.<br/>([기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)|
|복사 생성자|같은 타입의 개체를 복사 생성합니다.<br/>([복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)|
|소멸자|개체 소멸시 호출됩니다.<br/>([소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 참고)|
|대입 연산자와 연산자 오버로딩|개체 대입 연산자 와 그외 연산자를 오버로딩할 수 있습니다.<br/>([대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/) 와 [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고)|
|형변환 연산자|여러 타입으로 형변환 할 수 있도록 연산자 오버로딩을 할 수 있습니다.<br/>([형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고)|
|멤버 함수|데이터를 처리하는 함수입니다.<br/>([멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 참고)|
|상수 멤버 함수|멤버 함수중 상수인 함수입니다.<br/>([상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 참고)|
|가상 함수|자식 개체에서 재구현하는 함수입니다.<br/>([가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 참고)|
|순가상 함수| 실제 구현없이 함수 규약만 정의할때 사용하는 함수입니다.<br/>([순가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 참고)|
|정적 멤버 함수|특정 개체에 속하지 않는 함수입니다.<br/>([정적 멤버 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 참고)|
|열거형 상수|정수형 상수 집합을 정의합니다.<br/>([열거형 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 참고)|
|중첩 클래스(구조체)|클래스내의 또다른 클래스를 정의합니다.
|타입 재정의(`typdef`)|타입의 별칭을 정의합니다.<br/>([타입 재정의(`typdef`)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%B3%84%EC%B9%AD) 참고)|

# 전방 선언

서로 다른 클래스(구조체)가 상호 참조할 경우, 컴파일이 안될 수도 있습니다.

```cpp
class MyClass {
    YourClass m_Your; // (X) 컴파일 오류. YourClass가 뭔지 모릅니다.
};
class YourClass {
    MyClass m_My; // MyClass는 상위에 정의되어 사용할 수 있습니다.
}; 
```

이런 경우, 전방 선언으로 `YourClass`가 무언지 컴파일러에게 알려줘야 합니다. 

이때 전방 선언만 참조하는 곳(`MyClass`)은 

1. 구체 정의가 아닌 [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)로 사용하여야 하고, 
2. 전방 선언한 것의 실제 선언(`YourClass` 선언) 후 해당 개체를 사용해야 합니다. 
 
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

중첩 클래스는 전방 선언을 할 수 없습니다.

```cpp
class T; // (O)
class T::Nested; // (X) 컴파일 오류. 중첩 클래스는 전방 선언을 할 수 없습니다.
class T {
public:
    class Nested {};
};
```

클래스 내에서 중첩 클래스를 전방 선언할 수 있으나 접근 지정자가 동일해야 합니다.

```cpp
class T {
private:
    class Nested;
private:
    class Nested {}; // (O)
public:
    class Nested {}; // (X) 컴파일 오류. 전방 선언에선 private이고 실제 선언은 public 입니다.
};
```

# 인라인 함수

클래스(구조체) 선언부에 정의한 함수는 컴파일러 판단에 따라 [인라인](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-inline/)화 됩니다.

```cpp
// h에서
class T {
    int f1() {} // 컴파일러 판단에 따라 inline화 됨
    int f2(); // 선언만 되었기에 inline화 안됨
};

// cpp에서
int T::f2() {} // inline화 안됨
```

# 분할 컴파일을 위한 선언과 정의 분리

C++에서는

1. 컴파일 속도 향상과,
2. 실제 구현 코드의 은닉을 위하여,

클래스 선언과 정의를 분리하여 파일을 구성합니다. 

헤더 파일에서(`#define MyClass_h`는 헤더 파일을 `include`시 1회만 포함하게 하는  인클루드 가드입니다.([인클루드 가드](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-include-guard/) 참고)),

```cpp
// MyClass.h 헤더 파일에서
#ifndef MyClass_h
#define MyClass_h // 인클루드 가드

class MyClass {
public:
    void Func(); // Func() 멤버 함수 선언   
};

#endif // MyClass_h
```

cpp 파일에서,

```cpp
// MyClass.cpp 소스 파일 에서
void MyClass::Func() {
    // Func 함수 정의 
}
```

`MyClass`를 사용하는 곳에서,

```cpp
#include "MyClass.h" // MyClass 선언을 포함합니다.

void f() {
    MyClass obj; // MyClass 를 사용합니다.
    obj.Func();
}
```

상기와 같이 코드를 구성하면,

1. `MyClass.cpp` 가 1회만 컴파일되고, `MyClass`를 사용하는 곳은 컴파일 결과물을 링크만 하여, 빌드 속도가 향상됩니다.
2. 외부에 모듈을 제공할때 `MyClass.h`와 컴파일된 파일만 제공하면 되므로 소스 코드를 은닉할 수 있습니다.

# 접근 지정자
 
접근 지정자로 외부 접근을 통제할 수 있습니다.

|항목|내용|
|--|--|
|`private`(클래스 기본값)|자기 자신만 사용|
|`protected`|상속받은 자식 개체에만 허용| 
|`public`(구조체 기본값)|외부 접근 허용|

**`using` 선언**

`using`을 사용하면 부모 클래스에서 지정한 접근 지정자를 강제로 변경할 수 있습니다. 하지만, 사용하지 마세요. 부모에서 설정한 행동을 임의로 바꾸는 건 좋지 않습니다.([리스코프 치환 원칙](https://tango1202.github.io/principle/principle-liskov-substitution/) 참고)

```cpp
class Base {
protected:
    int m_Val;
};

class Derived : public Base {
public:
    using Base::m_Val; // (△) 비권장. Base m_Val을 public으로 변경합니다.
};
Derived d;
d.m_Val = 10; // 이제 public이라 접근 가능합니다.
```

# friend

일반적으로 `public`만 외부에서 접근할 수 있으나 특별히 `friend`로 허용한 [클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)와 [함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/)는 `protected`와 `private`에 접근할 수 있습니다. 

이때

1. `friend`인 클래스와 상속 관계거나, 
2. `friend`인 클래스와 `friend`관계인 클래스는 
   
접근 할 수 없습니다.  

`friend`는 은닉을 통한 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 해칠 수 있기 때문에 사용하지 않는게 좋습니다.

```cpp
class FriendT {
    friend class U; // U 의 전방 선언이 없어도 됨
    friend void Func(); // Func의 전방 선언이 없어도 됨
private:
    int m_Val1;
    void f() {}
protected:
    int m_Val2;
    void g() {}

};
void Func() {
    FriendT t;
    t.m_Val1; // (△) 비권장. private 접근
    t.f();
    t.m_Val2; // (△) 비권장. protected 접근
    t.g();
}
class U {
    friend class W;
    void f() {
        FriendT t;
        t.m_Val1; // (△) 비권장. private 접근
        t.f();
        t.m_Val2; // (△) 비권장. protected 접근
        t.g();            
    }
};
class V : public U {
    void f() {
        FriendT t;
        t.m_Val1; // (X) 컴파일 오류. 상속받은 클래스에서는 접근 불가
        t.f();
        t.m_Val2; // (X) 컴파일 오류. 상속받은 클래스에서는 접근 불가
        t.g();            
    }
};
class W { // T의 friend인 U 의 friend
    void f() {
        FriendT t;
        t.m_Val1; // (X) 컴파일 오류. friend의 friend는 접근 불가
        t.f();
        t.m_Val2; // (X) 컴파일 오류. friend의 friend는 접근 불가
        t.g();            
    }
};    
```

# 공용체

공용체는 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)들끼리 메모리 영역을 공유하다 보니 하나의 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 수정하면, 다른 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)도 값이 수정된 효과를 볼 수 있습니다. 그러나, 타입의 크기나 [메모리 졍렬 방식](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)이 바뀌면(플랫폼에 따라, 컴파일러에 따라, 최적화 옵션에 따라) 오동작을 할 수 있으니, 주의해서 사용해야 합니다.

또한, 구조체와 클래스와 달리 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)와 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 가질 수 없습니다.

```cpp
class C {
private:
    int m_Val1;
    int m_Val2;
public:
    // C(int v1, int v2) {} // (X) 컴파일 오류. 공용체 멤버는 생성자를 가질 수 없음
    // ~C() {} // (X) 컴파일 오류. 공용체 멤버는 소멸자를 가질 수 없음
    int GetVal1() const {return m_Val1;} // 멤버 함수가 있어도 되나, virtual 이면 안됨
    void SetVal1(int val) {m_Val1 = val;}
};

struct S1 {
    int x;
    int y;   
};
struct S2 {
    int x;
    int y;
    int z;        
};    
union U {
    C c; // 멤버 변수들끼리 메모리를 공유함
    S1 s1;
    S2 s2;
public:
    int GetX() const {return s1.x;} // 공용체도 함수를 가질 수 있음 
};

U u;
EXPECT_TRUE(sizeof(u) == sizeof(S2)); // 공용체 멤버 변수중 용량이 가장 큰 개체

u.s1.x = 10; // s1을 바꿨지만, c와 s2도 변경됩니다.
EXPECT_TRUE(u.GetX() == 10);
EXPECT_TRUE(u.c.GetVal1() == 10);
EXPECT_TRUE(u.s2.x == 10);
   
u.c.SetVal1(20); // c를 바꿨지만, s1과 s2도 변경됩니다.
EXPECT_TRUE(u.c.GetVal1() == 20);
EXPECT_TRUE(u.s1.x == 20);
EXPECT_TRUE(u.s2.x == 20);
```

# 비트 필드

구조체나 클래스 멤버 변수를 비트 단위로 쪼개어 사용할 수 있습니다. 

다음 코드는 비트 필드의 구현 예입니다. 

1. 멤버 변수 뒤에 `: 비트 수`를 지정하여 만들 수 있습니다.
2. 저장하려는 값이 비트 범위를 벗어날 경우에는 상위 비트를 버립니다. 
3. 포인터나 참조자를 제공하지 않습니다.

```cpp
class Flag {
public:
    unsigned char m_Val1 : 2; // 2bit 00(0), 01(1), 10(2), 11(3)
    unsigned char m_Val2 : 3; // 3bit 000(0), 001(1), 010(2), 011(3), 100(4), 101(5), 110(6), 111(7)
};

Flag flag;
EXPECT_TRUE(sizeof(flag) == sizeof(unsigned char));

// 주어진 비트 범위내의 데이터는 잘 저장함
flag.m_Val1 = 3; // 0~3 저장
flag.m_Val2 = 7; // 0~7 저장

EXPECT_TRUE(flag.m_Val1 == 3);
EXPECT_TRUE(flag.m_Val2 == 7);

// 저장 공간이 부족하면 상위 비트를 버림
flag.m_Val1 = 5; // (△) 비권장. 101을 대입하면 앞의 1은 저장하지 못하고 01만 저장됨  
flag.m_Val2 = 15; // (△) 비권장. 1111을 대입하면 앞의 1은 저장하지 못하고 111만 저장됨  

EXPECT_TRUE(flag.m_Val1 == 1);
EXPECT_TRUE(flag.m_Val2 == 7);

unsigned char* ptr =  &flag.m_Val1; // (X) 컴파일 오류. 비트 필드는 포인터를 지원하지 않습니다.
unsigned char& ref =  flag.m_Val1; // (X) 컴파일 오류. 비트 필드는 레퍼런스를 지원하지 않습니다.
```

# 함수 내부의 로컬 클래스

함수 내부에 클래스(구조체, 공용체)를 정의하여, 함수 내부에서만 한정해서 사용할 수 있습니다.
```cpp
void OuterFunc() {
    class InnerClass { // 클래스 가능
    public:
        int m_X;
    };
    struct InnerStruct {}; // 구조체 가능
    union InnerUnion {}; // 공용체 가능

    InnerClass t; // 함수내에서만 사용 가능
    t.m_X = 10; 
    EXPECT_TRUE(t.m_X == 10);
}
```

# 중첩 클래스

다른 개체 내부에 정의된 클래스(구조체, 공용체)입니다.

```cpp
// 클래스에서 가능
class Class {
    class NestedClass {}; // 클래스 가능
    struct NestedStruct {}; // 구조체 가능
    union NestedUnion {}; // 공용체 가능
};
// 구조체에서 가능
struct Struct {
    class NestedClass {};
    struct NestedStruct {};
    union NestedUnion {};
};
// 공용체에서 가능
union Union {
    class NestedClass {}; 
    struct NestedStruct {}; 
    union NestedUnion {}; 
};
```

중첩 클래스는 바깥 클래스의 `private`에 접근 가능합니다. 외부에서 접근할 경우에는 범위 확인 연산자(`::`)를 사용합니다.

```cpp
class Outer {
private:
    int m_Val;
    static int s_Val;
public:
    class Nested {
    public:
        void f(Outer *outer) {
            s_Val; // (O) Outer의 private 정적 멤버 변수 접근 가능
            outer->m_Val; // (O) outer 개체 참조로 private 접근 가능
        }
    };
    void g() {
        Nested nested;
        nested.f(this); // (O) 개체를 인스턴스화 하여 접근 가능
    }
};
Outer::Nested nested; // (O) 외부에서 접근할 경우 :: 사용
```

# this 포인터

`this` 포인터는 개체 자신을 가리키는 포인터 입니다. 

```cpp
class Outer {
    int m_OuterVal;
    void f() {
        class Inner {
            int m_InnerVal;
            void f() {
                this->m_InnerVal; // Inner 클래스의 this
            }
        };
        this->m_OuterVal; // Outer 클래스의 this
    }
    class Nested {
        int m_NestedVal;
        void f() {
            this->m_NestedVal; // Nested 클래스의 this
        }
    };
    static void Func() {
        this->m_OuterVal; // (X) 컴파일 오류. 정적 멤버 함수에서는 접근 불가
    }
};
```

[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서는 사용할 수 없습니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) :
        // this->m_X(x), // (X) 컴파일 오류. 초기화 리스트에서 사용 불가능
        m_Y(this->m_X) { // (O) 초기화 리스트에서 대입값으로는 사용 가능 
        this->m_X; // (O) 생성자 본문에서 사용 가능
    }
};  
```

`delete this;` 로 자기 자신을 소멸시킬 수 있습니다. 단, [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 생성된 변수를 `delete`하면 예외가 발생합니다.

```cpp
class T {
public:
    ~T() {
        std::cout << "T::~T();" << std::endl;
    }
    void Release() {
        delete this; // (O) 자기 자신을 소멸 시킴(소멸자가 호출됨)
    }
};

T t1; // 스택에 생성된 변수
t1.Release(); // (X) 예외 발생. 스택에 생성된 개체인데 delete 함

T* t2 = new T; // 힙에 생성된 변수
t2->Release(); // (O)  
```

