---
layout: single
title: "#5. [고전 C++ 개체 지향] C++가 은근슬쩍 만들어 버리는 것들"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

도움이 되기도 하지만 독이 되기도 한다. 반드시 유효한지 검토하라

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
