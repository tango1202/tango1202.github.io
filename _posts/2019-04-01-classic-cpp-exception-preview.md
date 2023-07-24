---
layout: single
title: "#1. [고전 C++ 가이드] 예외 안정 미리보기"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

기본 생성자는 피하라.
복사 생성자/대입 연산자의 컴파일 버전
소멸자에서 예외 발생 금지
Swap에서 예외 발생 금지
[완전한 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90) 자원 획득과 초기화를 한번에

스택풀기
https://en.cppreference.com/w/cpp/language/throw 참고

예외 안정 함수의 제어구조
사전에 정상적인 조건인지 확인후 실행할것