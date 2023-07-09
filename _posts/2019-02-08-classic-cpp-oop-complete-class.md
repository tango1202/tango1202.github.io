---
layout: single
title: "#8. [고전 C++ 개체 지향] 완전한 클래스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---





# 완전한 클래스


class D {
int i; T* p; D(const D& other) : i(other.i), p(new T(other.p)) {} void operator =(const D& other) { D temp(other); // 복제본
Swap(temp); // 복제본과 바꿔치기
}void Swap(D&other) { swap(i, other.i); swap(p, other.p); }
};

소멸자에서 모든 자원을 반드시 해제해라. 그리고 예외발생 금지!!!
