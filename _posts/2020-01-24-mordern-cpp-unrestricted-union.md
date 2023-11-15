---
layout: single
title: "#24. [모던 C++] (C++11~) 무제한 공용체(unrestricted Union)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 멤버에서 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)/[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 사용 제한이 풀린 [무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/)가 추가되어 메모리 절약을 위한 코딩 자유도가 높아졌습니다.
> * (C++17~) [variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/)가 추가되어 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다.

**개요**

기존의 [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4)에서는 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)/[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 없는 [trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85)만 [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4)의 멤버가 될 수 있었는데요(*[공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 참고*), 

C++11 부터는 이를 완화하였습니다.
대신 멤버들의 생성자와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출되지 않으므로, 이를 수동으로 제어해야 합니다.

다음에서 `MyUnion_11`은 non-trivial 타입인 `A`와 `B`, `Derived`를 멤버로 사용하고, 크기가 가장 큰 개체의 크기만큼 메모리를 할당합니다.

```cpp
// 생성자와 소멸자가 있어서 non-trivial 타입입니다.
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) : m_X{x}, m_Y{y} {
        std::cout << "A : Constructor" << std::endl;
    }
    ~A() {
        std::cout << "A : Destructor" << std::endl;    
    }
    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}

    void SetX(int x) {m_X = x;}
    void SetY(int y) {m_Y = y;}
};

// 생성자와 소멸자가 있어서 non-trivial 타입입니다.
class B {
    std::string m_Str;
public:
    
    explicit B(const char* str) : m_Str{str} {
        std::cout << "B : Constructor" << std::endl;    
    }
    ~B() {
        std::cout << "B : Destructor" << std::endl;       
    }
    const std::string& GetString() const {return m_Str;}
};

class Base {
public:
    virtual int Func() {return 1;} // #1
};

// 생성자와 소멸자, 가상 함수가 있어서 non-trivial 타입입니다.
class Derived : public Base {
public:
    Derived() {
        std::cout << "Derived : Constructor" << std::endl;    
    }
    ~Derived() {
        std::cout << "Derived : Destructor" << std::endl;  
    }
    
    virtual int Func() override {return 2;} // #2        
};

union MyUnion_11 {
    A m_A; // non-trivial 타입입니다.
    B m_B; // non-trivial 타입입니다.
    Derived m_Derived; // non-trivial 타입입니다.
    MyUnion_11() {
        std::cout << "MyUnion_11 : Constructor" << std::endl;  
    }
    ~MyUnion_11() {
        std::cout << "MyUnion_11 : Destructor" << std::endl;  
    }
};

// A, B, Derived 는 non-trival 입니다.
EXPECT_TRUE(std::is_trivial<A>::value == false);
EXPECT_TRUE(std::is_trivial<B>::value == false);  
EXPECT_TRUE(std::is_trivial<Derived>::value == false); 

// MyUnion 은 A, B, Derived 중 크기가 큰 개체만큼 메모리를 할당합니다.
EXPECT_TRUE(sizeof(Derived) <= sizeof(A) && sizeof(A) <= sizeof(B) && sizeof(B) == sizeof(MyUnion_11));

// A, B, Derived의 생성자와 소멸자가 호출되지 않습니다.
MyUnion_11 obj;
```

상기 코드를 실행하면, `A`, `B`, `Derived`의 생성자와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)는 호출되지 않고, `MyUnion_11`만 생성자와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출됩니다.

```cpp
MyUnion_11 : Constructor
MyUnion_11 : Destructor
```

이러면 개체 소멸이 되지 않아 문제죠. 

따라서, `MyUnion`의 사용을 위해선 다음처럼 각 멤버의 

1. [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)를 이용하여 생성자를 호출하고, 
2. [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출해야 합니다.

```cpp
MyUnion_11 obj;

// A를 사용하기 위해 A의 생성자를 호출합니다.
new (&obj.m_A) A(10, 20);
EXPECT_TRUE(obj.m_A.GetX() == 10 && obj.m_A.GetY());

// A말고 B를 사용하기 위해 기존 A는 소멸시키고 B의 생성자를 호출합니다.
obj.m_A.~A();
new (&obj.m_B) B("Hello");
EXPECT_TRUE(obj.m_B.GetString() == "Hello");

// B 말고 Derived를 사용하기 위해 기존 B는 소멸시키고 Derived의 생성자를 호출합니다.
obj.m_B.~B();
new (&obj.m_Derived) Derived;
EXPECT_TRUE(obj.m_Derived.Func() == 2);

// obj 사용이 끝나서 Derived 도 소멸합니다.
obj.m_Derived.~Derived();
```

실행 결과를 보면 각 멤버의 생성자와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출된 것을 확인 할 수 있습니다.

```cpp
MyUnion_11 : Constructor
A : Constructor
A : Destructor
B : Constructor
B : Destructor
Derived : Constructor
Derived : Destructor
MyUnion_11 : Destructor
```

수동으로 생성자와 소멸자를 제어해야 해서 많이 불편하지만, 서로 다른 개체 타입을 관리해야 하고, 상황에 따라 여러개 중에 1개만 활성화해서 관리해야 할때 메모리를 알뜰하게 사용할 수 있습니다.

> *(C++17~) [variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/)가 추가되어 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다.*