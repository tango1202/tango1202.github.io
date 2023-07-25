---
layout: single
title: "#11
. [고전 C++ 가이드] 추상 클래스, 인터페이스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
https://en.cppreference.com/w/cpp/language/abstract_class
추상클래스 참고

# 추상 클래스

순수가상함수(pure virtual function) : 순수가상함수가 1개 이상 있으면 추상클래스 임


마땅이 할게 없으면 다음과 같이 순가상 함
수로 만들어라. virtual void Draw() = 0; 순수 가상 함수가 1개 이상있으면 추상클래
스임. 추상 클래스는 인스턴스화 될 수 없음. Shape s; // (x) 컴파일 오류

# 인터페이스

추상 클래스는 다른 클래스의 기본 클래스
혹은 인터페이스로 사용될 수 있다. 파생 클래스의 기능을 명세화하여 서로간
의 계약에 의한 프로그래밍이 가능하다.

# 좋은 상속, 나쁜 상속

기본 클래스의 무의미한 구현



