---
layout: single
title: "#4. [고전 C++ 개체 지향] 클래스의 암시적 정의(은근슬쩍 만들어 지는 것들)"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 은근슬쩍 만들어 지는 것들은 유효한지 검토하고, 유효하지 않다면 사용하지 못하게 막아라.

# 개요

|항목|내용|
|--|--|
|`T() {}`|기본 생성자<br/>([암시적 기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90/) 참고)|
|`T(const T& other) {}`|복사 생성자<br/>([암시적 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)|
|`T& operator =(const T& other) {}`|대입 연산자<br/>([암시적 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)|
|`~T() {}`|소멸자<br/>([암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고)|


클래스를 정의할 때, 생성자, 대입 연산자, 소멸자를 정의하지 않으면 암시적으로 정의합니다.

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

에서, 컴파일러에 의해 클래스 `T`는 암시적으로 다음처럼 정의됩니다.

```cpp
class Object {
    int m_X;
};        
class T {
    int m_X;
    int m_Y;
    Object m_Object;
public:
    // 기본 생성자
    // (△) 암시적으로 기본 생성자가 자동 제로 초기화된다고 하는데, GCC 디버그 모드에서는 0이 아닙니다. 명시적으로 초기화 하세요.
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

# 암시적 정의 수정 권장 방법

도움이 되기도 하지만,  타입 안정성이나 예외 안정성에 독이 되기도 하므로, 반드시 유효한지 검토하고, 유효하지 않다면 사용 못하게 막거나 재구현 해야 합니다.


|항목|문제점|권장 방법|
|--|--|--|
|기본 생성자|[명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 모든 요소를 나열하고 초기화 하는게 **코딩 계약**상 좋음|다른 생성자(값 생성자던, 복사 생성자던) 정의<br/>`private` 또는 `protected`를 이용하여 사용 제한|
|복사 생성자|[포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 발생함|`private` 또는 `protected`를 이용하여 사용 제한<br/>멤버 변수로 스마트 포인터 사용([복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고<br/>[스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/) 참고)|
|대입 연산자|[포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 발생함<br/>|`private` 또는 `protected`를 이용하여 사용 제한<br/> 멤버 변수가 1개인 경우 스마트 포인터 사용([대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고. [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/) 참고)<br/>[멤버 변수가 2개 이상인 경우 `swap`을 이용하여 구현](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EA%B0%80-2%EA%B0%9C-%EC%9D%B4%EC%83%81%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0%EC%99%80-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%EC%9D%98-%ED%98%B8%ED%99%98%EC%84%B1), 혹은 [PImpl 이디엄](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#pimpl-%EC%9D%B4%EB%94%94%EC%97%84/)을 이용하여 멤버 변수를 1개로 구현
|소멸자|[다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 주의|멤버 변수에 스마트 포인터 사용([복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고. [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/) 참고)<br/>복사 생성이나 대입연산이 필요없다면 Holder 사용([Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 참고)<br/>일반적인 경우 `public` Non-Virtual<br/>상속 전용 클래스 `protected` Non-Virtual<br/>`has-a`관계에서는 `protected` Non-Virtual<br/>`is-a`관계에서는 `public` Virtual|

