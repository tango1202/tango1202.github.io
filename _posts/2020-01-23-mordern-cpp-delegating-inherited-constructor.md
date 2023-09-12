---
layout: single
title: "#23. [모던 C++] (C++11~) 생성자 위임, 생성자 상속"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 생성자 위임이 추가되어 생성자의 초기화 리스트 코드가 좀더 간결해 졌습니다.
> * 생성자 상속이 추가되어 부모 개체의 생성자도 상속받아 사용할 수 있어 자식 개체의 생성자 재정의 코드가 좀더 간결해 졌습니다.

# 생성자 위임

기존에는 다양한 버전의 생성자를 만들고자 할때 각각의 생성자에서 초기화 리스트를 사용하여 멤버 변수를 초기화 했습니다.
```cpp
class T {
    int m_X;
    int m_Y;
    int m_Z;
public:
    T() : m_X(0), m_Y(0), m_Z(0) {}
    explicit T(int x) : m_X(x), m_Y(0), m_Z(0) {}
    T(int x, int y) : m_X(x), m_Y(y), m_Z(0) {}
    T(int x, int y, int z) : m_X(x), m_Y(y), m_Z(z) {}  
};
```

C++11부터는 다른 생성자에 위임할 수 있어 좀더 간결하게 생성자 코드를 만들 수 있습니다.

```cpp
class T {
    int m_X;
    int m_Y;
    int m_Z;
public:
    T() : T(0, 0, 0) {} // T(int x, int y, int z)에 위임
    explicit T(int x) : T(x, 0, 0) {} // T(int x, int y, int z)에 위임
    T(int x, int y) : T(x, y, 0) {} // T(int x, int y, int z)에 위임
    T(int x, int y, int z) : m_X(x), m_Y(y), m_Z(z) {} 
};
```

# 생성자 상속

기존에는 자식 개체 생성자에서 부모 개체의 생성자를 명시적으로 호출해야 했습니다.

```cpp
class Base {
    int m_X;
    int m_Y;
public:
    Base(int x, int y) : m_X(x), m_Y(y) {}
};

class Derived : public Base {
public:
    // Derived 생성자에서 Base(x, y) 생성자를 명시적으로 호출
    Derived(int x, int y) : Base(x, y) {}
};

Derived d(10, 20);
```

C++11부터는 `using` 선언의 형태로 부모 개체의 생성자를 그대로 상속받아 사용할 수 있습니다.

```cpp
class Base {
    int m_X;
    int m_Y;
public:
    Base(int x, int y) : m_X(x), m_Y(y) {}
};

class Derived : public Base {
public:
    // 생성자 상속
    using Base::Base; 
};

Derived d(10, 20);
```
