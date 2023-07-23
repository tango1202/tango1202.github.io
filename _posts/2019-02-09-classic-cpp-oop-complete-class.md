---
layout: single
title: "#9. [고전 C++ 개체 지향] 완전한 클래스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


완전한 생성자

|항목|내용|
|--|--|
|[기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)|필요없다면 못쓰게 만들어라.|
|[값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)|필요한 모든 인자를 나열하고 초기화 하라.|
|[형변환 생성자]


복사 생성자

대입 연산자

소멸자

형변환 연산자

대소 비교

Swap

완전한 인자


# 완전한 생성자

완전하게 생성시켜라

```cpp
class Date { Date(); // (X)불필요한 기본생성자 금지
Date(int y, int m, int d); // (O)모든 데이터를 생성자에서 완전히 전달받아라. }; 
```
# 사용하지 않는다면 복사생성자, 대입연산자를 private으로 막아라.

# 복사생성자, 대입연산자가 사용된다면 컴파일러 기본 버전이 논리적으로 맞는지 확인!!!

```cpp
class D {
int i; T* p; // p의 포인터를 단순히 복사해도
// 정말 상관없나?
};
```



```cpp
class D {
int i; T* p; D(const D& other) : i(other.i), p(new T(other.p)) {} void operator =(const D& other) { D temp(other); // 복제본
Swap(temp); // 복제본과 바꿔치기
}void Swap(D&other) { swap(i, other.i); swap(p, other.p); }
};
``````
# 소멸자에서 모든 자원을 반드시 해제해라. 그리고 예외발생 금지!!!

