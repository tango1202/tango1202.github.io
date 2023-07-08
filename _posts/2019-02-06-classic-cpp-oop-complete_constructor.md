---
layout: single
title: "#6. [C++ 코딩 패턴] 완전한 생성자"
categories: "classic-cpp-oop"
tag: ["C++ 코딩 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 완전한 생성자
완전하게 생성시켜라

class Date { Date(); // (X)불필요한 기본생성자 금지
Date(int y, int m, int d); // (O)모든 데이터를 생성자에서 완전히 전달받아라. }; 

사용하지 않는다면 복사생성자, 대입연산자를 private으로 막아라.

복사생성자, 대입연산자가 사용된다면 컴파일러 기본 버전이 논리적으로 맞는지 확인!!!

class D {
int i; T* p; // p의 포인터를 단순히 복사해도
// 정말 상관없나?
};

예외에 안정. 그러면 스마트 포인터 사용
