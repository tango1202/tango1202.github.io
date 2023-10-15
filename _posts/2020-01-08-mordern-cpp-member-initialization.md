---
layout: single
title: "#8. [모던 C++] (C++11~) 멤버 선언부 초기화"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) 비정적 멤버 변수도 [멤버 선언부에서 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/)를 할 수 있어 초기화 작성이 쉬워졌습니다.
> * (C++14~) 비정적 멤버 변수의 [멤버 선언부 초기화를 했더라도 집합 초기화를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/#c14-%EB%B9%84%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EB%A9%A4%EB%B2%84-%EC%84%A0%EC%96%B8%EB%B6%80-%EC%B4%88%EA%B8%B0%ED%99%94%EC%8B%9C-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)합니다. 기존에는 컴파일 오류였습니다.

# 개요

기존에는 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서만 멤버 변수를 초기화 할 수 있었는데요, 

C++11 부터는 멤버 변수 선언시에도 초기화 할 수 있습니다.
단, 정적 멤버 변수는 기존과 동일하게 별도 정의부에서 초기화해야 합니다.

```cpp
class A {
public:
    explicit A(int, int) {}    
};

class T {
    int m_A_11 = 0; // C++11. 멤버 변수 초기화
    int m_B_11{0}; // C++11. 중괄호 초기화
    int m_C_11 = {0}; // C++11. 중괄호 초기화
    int* m_D__1 = nullptr; // C++11. 포인터 초기화
    std::vector<int> m_E_11{1,2,3}; // C++11. 중괄호 초기화
    A m_F_11{10, 20}; // C++11. 중괄호 초기화
    // A m_F_11(10, 20); // (X) 컴파일 오류. A를 리턴하는 m_F 함수 정의

    const int m_G_11 = 0; // 기존과 동일하게 상수 멤버 변수 초기화
    static const int m_H_11 = 0; // 기존과 동일하게 선언부에 정적 상수 멤버 변수 초기화
    // static int m_H_11 = 0; // (X) 컴파일 오류. 기존과 동일하게 정적 멤버 변수는 별도 초기화해야 함 
};  
```
# (C++14~) 비정적 멤버 변수의 멤버 선언부 초기화시 집합 초기화

C++11의 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 이용하면, 다음과 같이 [집합 타입](https://tango1202.github.io/mordern-cpp/mordern--category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)에 대해 초기화를 할 수 있습니다. 
```cpp
class A {
public:
    int m_X;
    int m_Y;
};

A a_11{0, 1}; 
EXPECT_TRUE(a.m_X == 0 && a.m_Y == 1);
```

하지만, C++14 이전 버전은 [멤버 선언부에 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/)와 함께 사용하면 컴파일 오류가 발생했는데요, C++14 부터는 이를 완화하여 비정적 멤버 변수를 [멤버 선언부에 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/)하더라도 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 사용할 수 있습니다.

```cpp
class A {
public:
    int m_X_11{0}; // 비정적 멤버 변수 초기화
    int m_Y_11{1};
};

// (X) ~C++14 컴파일 오류. no matching function for call to 'main()::A::A(<brace-enclosed initializer list>)'
// (O C++14~
A a_14{0, 1}; // 생성자를 호출하는 중괄호 직접 초기화가 아니라 중괄호 집합 초기화 입니다.
EXPECT_TRUE(a_14.m_X_11 == 0 && a_14.m_Y_11 == 1);   
```


