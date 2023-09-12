---
layout: single
title: "#24. [모던 C++] (C++11~) 무제한 공용체(unrestricted Union)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 공용체 멤버에서 생성자/소멸자/가상 함수 사용 제한이 풀려 메모리 절약을 위한 코딩 자유도가 높아졌습니다.

**개요**

[공용체](
https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 에서의 언급처럼, 기존에는 공용체에서는 생성자/소멸자/가상 함수가 없는 trivial 타입만 공용체의 멤버가 될 수 있었는데요, C++11 부터는 이를 완화하였습니다.
대신 멤버들의 생성자와 소멸자가 호출되지 않으므로, 이를 수동으로 제어해야 합니다.

다음에서 `MyUnion`은 non-trivial 타입인 `A`와 `B`, `Derived`를 멤버로 사용하고. 크기가 가장 큰 개체의 크기 만큼 메모리를 할당합니다.

```cpp
// 생성자와 소멸자가 있어서 non-trivial 타입입니다.
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) : m_X(x), m_Y(y) {
        std::cout<<"A : Constructor"<<std::endl;
    }
    ~A() {
        std::cout<<"A : Destructor"<<std::endl;    
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
    
    explicit B(const char* str) : m_Str(str) {
        std::cout<<"B : Constructor"<<std::endl;    
    }
    ~B() {
        std::cout<<"B : Destructor"<<std::endl;       
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
        std::cout<<"Derived : Constructor"<<std::endl;    
    }
    ~Derived() {
        std::cout<<"Derived : Destructor"<<std::endl;  
    }
    
    virtual int Func() override {return 2;} // #2        
};

union MyUnion {
    A m_A; // non-trivial 타입입니다.
    B m_B; // non-trivial 타입입니다.
    Derived m_Derived; // non-trivial 타입입니다.
    MyUnion() {
        std::cout<<"MyUnion : Constructor"<<std::endl;  
    }
    ~MyUnion() {
        std::cout<<"MyUnion : Destructor"<<std::endl;  
    }
};

// A, B, Derived 는 non-trival 입니다.
EXPECT_TRUE(std::is_trivial<A>::value == false);
EXPECT_TRUE(std::is_trivial<B>::value == false);  
EXPECT_TRUE(std::is_trivial<Derived>::value == false); 

// MyUnion 은 A, B, Derived 중 크기가 큰 개체만큼 메모리를 할당합니다.
EXPECT_TRUE(sizeof(Derived) <= sizeof(A) && sizeof(A) <= sizeof(B) && sizeof(B) == sizeof(MyUnion));

// A, B, Derived의 생성자와 소멸자가 호출되지 않습니다.
MyUnion obj;
```

상기 코드를 실행하면, `A`, `B`, `Derived`의 생성자와 소멸자는 호출되지 않고, `MyUnion`만 생성자와 소멸자가 호출됩니다.

```cpp
MyUnion : Constructor
MyUnion : Destructor
```

`MyUnion`의 사용을 위해선 다음처럼 각 멤버의 

1. Placement New(위치 지정 생성)를 이용하여 생성자를 호출하고, 
2. 소멸자를 호출해야 합니다.

```cpp
MyUnion obj;

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

실행 결과를 보면 각 멤버의 생성자와 소멸자가 호출된 것을 확인 할 수 있습니다.

```cpp
MyUnion : Constructor
A : Constructor
A : Destructor
B : Constructor
B : Destructor
Derived : Constructor
Derived : Destructor
MyUnion : Destructor
```

수동으로 생성자와 소멸자를 제어해야 해서 많이 불편하지만, 서로 다른 개체 타입을 관리해야 하고, 상황에 따라 여러개 중에 1개만 활성화해서 관리해야 할때 메모리를 알뜰하게 사용할 수 있습니다.
