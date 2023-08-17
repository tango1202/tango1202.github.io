---
layout: single
title: "#6. [고전 C++ STL] 타입 특성 클래스(Traits)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 타입 특성 클래스를 이용하여 템플릿 인자와 코딩 계약을 맺어라.
> * 타입 특성 클래스를 이용하여 타입 처리 방식을 응집하라.

# 개요

타입 특성 클래스(Traits)는 타입을 기반으로 하는 일반화 프로그래밍을 위한 프로그래밍 방법입니다. 템플릿 인자와 어떻게 코딩 계약을 맺을지, 타입을 어떻게 처리할지를 제공합니다.

# 템플릿 인자 코딩 계약

다음 코드에서 `Runner`는 외형적으로는 아무 타입이나 받아들일 수 있습니다. 하지만 내부에 `using`을 사용하여 `T`가 `Type`과 `PointerType` 을 제공하도록 강제하고 있습니다. 따라서 `Runner`를 사용하려면, `A` 클래스 처럼 `ObjTraits`를 상속하거나, `B`클래스 처럼 `Type`과 `PointerType`을 제공해야만 합니다.


```cpp
template<typename T> 
class ObjTraits {
public:
    typedef T Type;
    typedef T* PointerType;
};

template<typename T> 
class Runner {
    // T는 Type과 PointerType을 제공해야 합니다.
    using Type = typename T::Type; 
    using PointerType = typename T::PointerType;

    Type m_Val1;
    typename T::Type m_Val2; // using을 사용하지 않고 T::Type을 사용할 수 있습니다.
};

// Type과 PointerType을 제공하기 위해 ObjTraits를 사용합니다. 
class A : public ObjTraits<int> {};

// Type과 PointerType을 직접 제공합니다.
class B {
public:
    typedef int Type;
    typedef int* PointerType;
};

Runner<A> a;
Runner<B> b;
```

# 타입 처리 방법 공통화

`double` 을 사용하는 `A`, `B`, `C`, `D` 4개의 클래스를 더하기를 할때, `A`, `B`는 올림을 하고, `C`, `D`는 내림을 해야 한다고 합시다. 타입에 따라 `AddCeil()`이나 `AddFoor()`를 호출하면 되기는 합니다.

```cpp
// A와 B는 더하기시 올림을 해야 합니다.
class A {
public:
    double m_Val;
    explicit A(double val) : m_Val(val) {}    
};
class B {
public:
    double m_Val;    
    explicit B(double val) : m_Val(val) {}
}; 
// C와 D는 더하기시 내림을 해야 합니다.
class C {
public:
    double m_Val;  
    explicit C(double val) : m_Val(val) {}  
}; 
class D {
public:
    double m_Val;  
    explicit D(double val) : m_Val(val) {}   
};

template<typename T>
int AddCeil(T val1, T val2) {
    return std::ceil(val1.m_Val + val2.m_Val);
}
template<typename T>
int AddFloor(T val1, T val2) {
    return std::floor(val1.m_Val + val2.m_Val);
}

A a1(1.1), a2(1.2);
B b1(1.1), b2(1.5);
C c1(1.1), c2(1.2);
D d1(1.1), d2(1.5);

// (△) 비권장 A, B, C, D 타입에 따라 사용자가 다른 함수를 호출해야 합니다.
EXPECT_TRUE(AddCeil(a1, a2) == 3); // 올림
EXPECT_TRUE(AddCeil(b1, b2) == 3); // 올림
EXPECT_TRUE(AddFloor(c1, c2) == 2); // 내림
EXPECT_TRUE(AddFloor(d1, d2) == 2); // 내림     
```

하지만 `A`, `B`, `C`, `D` 사용자는 각 클래스의 올림과 내림 규칙을 숙지하고, 실수 없이 `AddCeil()`과 `AddFoor()`를 호출해야 합니다. 잘못 사용할 확률이 참 높죠.

이런 경우 

1. `CeilTag`, `FoorTag`와 같은 더미 클래스를 만들고, 
2. `A`, `B`, `C`, `D`에 올림을 할지 내림을 할지 `AddTag`로 `typedef`하고,
3. `Add()` 함수에서 `typename T::AddTag addTag;` 와 같이 오버로딩할 수 있게 더미 개체를 만들고,
4. `AddInternal()` 함수를 호출하여 올림과 내림을 수행할 수 있습니다.

이제 `A`, `B`, `C`, `D` 클래스 설계자가 지정한대로 올림과 내림이 호출되므로 사용자는 마음 편히 `Add()` 함수만 호출하면 됩니다.

```cpp
// 올림, 내림 오버로딩을 위한 더미 클래스
class CeilTag {};
class FloorTag {};

// A와 B는 더하기시 올림을 해야 합니다.
class A {
public:
    typedef CeilTag AddTag; // 올림
    double m_Val;
    explicit A(double val) : m_Val(val) {}    
};
class B {
public:
    typedef CeilTag AddTag; // 올림
    double m_Val;    
    explicit B(double val) : m_Val(val) {}
}; 
// C와 D는 더하기시 내림을 해야 합니다.
class C {
public:
    typedef FloorTag AddTag; // 내림
    double m_Val;  
    explicit C(double val) : m_Val(val) {}  
}; 
class D {
public:
    typedef FloorTag AddTag; // 내림
    double m_Val;  
    explicit D(double val) : m_Val(val) {}   
};

// T::AddTag에 따라 올림과 내림을 오버로딩을 통해 분기하여 호출합니다.
template<typename T> 
int Add(T val1, T val2) {
    typename T::AddTag addTag;
    return AddInternal(val1, val2, addTag);
} 
// 올림을 합니다.
template<typename T>
int AddInternal(T val1, T val2, CeilTag) {

    return std::ceil(val1.m_Val + val2.m_Val);
}
// 내림을 합니다.
template<typename T>
int AddInternal(T val1, T val2, FloorTag) {
    return std::floor(val1.m_Val + val2.m_Val);
}

A a1(1.1), a2(1.2);
B b1(1.1), b2(1.5);
C c1(1.1), c2(1.2);
D d1(1.1), d2(1.5);

// (O) A, B, C, D에 정의된 타입 특성으로 알아서 올림과 내림을 호출합니다.
EXPECT_TRUE(Add(a1, a2) == 3); // 올림
EXPECT_TRUE(Add(b1, b2) == 3); // 올림
EXPECT_TRUE(Add(c1, c2) == 2); // 내림
EXPECT_TRUE(Add(d1, d2) == 2); // 내림 
```

상기 코드에서 타입을 처리하는 특성만 따로 뽑아내어 `AddTraits` 를 만들 수 있습니다. 타입 처리 방식이 응집되어 유지보수가 좀더 간결해 집니다.

1. `AddTraits` 클래스를 만들고 일반적인 것들은 올림이 되도록 `AddTag`로 `typedef`하고,
2. `C`, `D` 는 내림이 되도록 `typedef`합니다.
3. `Add()` 함수에서 `typename AddTraits<T>::AddTag addTag;` 와 같이 `AddTraits`를 통해 오버로딩할 수 있게 더미 개체를 만듭니다.
   
```cpp
// 템플릿 특수화 컴파일을 위한 전방 선언
class C;
class D;

// 올림, 내림 오버로딩을 위한 더미 클래스
class CeilTag {};
class FloorTag {};

// 일반적으로 모든 타입은 올림을 사용합니다.
template<typename T>
class AddTraits {
public:
    typedef CeilTag AddTag; 
};
// C와 D는 내림을 사용합니다.
template<> 
class AddTraits<C> {
public:
    typedef FloorTag AddTag;         
};
template<> 
class AddTraits<D> {
public:
    typedef FloorTag AddTag;         
};

class A {
public:
    double m_Val;
    explicit A(double val) : m_Val(val) {}    
};
class B {
public:
    double m_Val;    
    explicit B(double val) : m_Val(val) {}
}; 
class C {
public:
    double m_Val;  
    explicit C(double val) : m_Val(val) {}  
}; 
class D {
public:
    double m_Val;  
    explicit D(double val) : m_Val(val) {}   
};

// AddTraits<T>::AddTag에 따라 올림과 내림을 오버로딩을 통해 분기하여 호출합니다.
template<typename T> 
int Add(T val1, T val2) {
    typename AddTraits<T>::AddTag addTag;
    return AddInternal(val1, val2, addTag);
} 
// 올림을 합니다.
template<typename T>
int AddInternal(T val1, T val2, CeilTag) {

    return std::ceil(val1.m_Val + val2.m_Val);
}
// 내림을 합니다.
template<typename T>
int AddInternal(T val1, T val2, FloorTag) {
    return std::floor(val1.m_Val + val2.m_Val);
}

A a1(1.1), a2(1.2);
B b1(1.1), b2(1.5);
C c1(1.1), c2(1.2);
D d1(1.1), d2(1.5);

// (O) AddTraits에 정의된 A, B, C, D 타입 특성에 따라 올림과 내림을 호출합니다.
EXPECT_TRUE(Add(a1, a2) == 3); // 올림
EXPECT_TRUE(Add(b1, b2) == 3); // 올림
EXPECT_TRUE(Add(c1, c2) == 2); // 내림
EXPECT_TRUE(Add(d1, d2) == 2); // 내림   
```
