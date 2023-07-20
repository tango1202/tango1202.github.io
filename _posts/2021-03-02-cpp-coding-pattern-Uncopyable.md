---
layout: single
title: "#1. [C++ 코딩 패턴] 복사 방지 클래스"
categories: "cpp-coding-pattern"
tag: ["C++ 코딩 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---


```cpp
// 복사 생성과 대입 연산을 할 수 없는 개체
class Uncopyable {   
protected:
    Uncopyable() {} // 상속해서만 사용 가능
    ~Uncopyable() {}
private:
    Uncopyable(const Uncopyable& other) {} // 누군가가 접근하면 private여서 컴파일 오류
    Uncopyable& operator =(const Uncopyable& other) {return *this;}
};
```