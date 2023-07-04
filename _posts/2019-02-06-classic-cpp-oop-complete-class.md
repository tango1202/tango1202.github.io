---
layout: single
title: "#6. [고전 C++ 개체 지향] 완전한 클래스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---




완전하게 생성시켜라

class Date { Date(); // (X)불필요한 기본생성자 금지
Date(int y, int m, int d); // (O)모든 데이터를 생성자에서 완전히 전달받아라. }; 

사용하지 않는다면 복사생성자, 대입연산자를 private으로 막아라.

복사생성자, 대입연산자가 사용된다면 컴파일러 기본 버전이 논리적으로 맞는지 확인!!!

class D {
int i; T* p; // p의 포인터를 단순히 복사해도
// 정말 상관없나?
};

class D {
int i; T* p; D(const D& other) : i(other.i), p(new T(other.p)) {} void operator =(const D& other) { D temp(other); // 복제본
Swap(temp); // 복제본과 바꿔치기
}void Swap(D&other) { swap(i, other.i); swap(p, other.p); }
};

소멸자에서 모든 자원을 반드시 해제해라. 그리고 예외발생 금지!!!
