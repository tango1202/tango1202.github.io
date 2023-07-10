---
layout: single
title: "#4. [고전 C++ 개체 지향] 클래스의 암시적 정의(은근슬쩍 만들어 지는 것들)"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

|항목|내용|
|--|--|
|`T() {}`|기본 생성자<br/>([암시적 기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90/) 참고)|
|`T(const T& other) {}`|복사 생성자<br/>([생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 참고)|
|`T& operator =(const T& other) {}`|대입 연산자<br/>([암시적 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)|
|`~T() {}`|소멸자<br/>([암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고)|


클래스를 정의할 때, 생성자, 대입 연산자, 소멸자를 정의하지 않으면 암시적으로 정의합니다.

도움이 되기도 하지만 타입 안정성이나 예외 안정성에 독이 되기도 하므로, 반드시 유효한지 검토하고, 유효하지 않다면 사용 못하게 막아야 합니다.

```cpp
class Object {
    int m_X;
};
class T {
    int m_X;
    int m_Y;
    Object m_Object;
};
```

에서, 클래스 `T`는 암시적으로 다음처럼 정의됩니다.

```cpp
class Object {
    int m_X;
};        
class T {
    int m_X;
    int m_Y;
    Object m_Object;
public:
    // 기본 생성자 - 자동 제로 초기화(멤버 변수의 메모리 영역이 0)
    T() {}

    // 복사 생성자 - 멤버별 복사 생성자 호출
    T(const T& other) :
        m_X(other.m_X),
        m_Y(other.m_Y),
        m_Object(other.m_Object) {}

    // 대입 연산자 - 멤버별 대입
    T& operator =(const T& other) {
        m_X = other.m_X;
        m_Y = other.m_Y;
        m_Object = other.m_Object;
    
        return *this;
    }
    // 소멸자 - 특별한 작업 없음
    ~T() {}
};

```


class T { public: T() {}
T(const T& other) {....}
~T() {}
T& operator = (const T& other) {}
};

기본 생성자. 인자 있는 생성자(복사
생성자 제외) 를 정의하면 만들어지
지 않는다.

복사 생성자. 모든 멤버변수를 대입
한다. 

소멸자. 기본클래스의 가상성을 따라
감

대입 연산자. 모든 멤버변수를 대입
한다. T a = b = c; 를 위해 T& 리턴
