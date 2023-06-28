---
layout: single
title: "#1. [고전 C++ 가이드] 예외안정 미리보기"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

기본 생성자는 피하라.
복사 생성자/대입 연산자의 컴파일 버전
소멸자에서 예외 발생 금지
Swap에서 예외 발생 금지
완전한 생성자 자원 획득과 초기화를 한번에

스택풀기
https://en.cppreference.com/w/cpp/language/throw 참고

예외 안정 함수의 제어구조
사전에 정상적인 조건인지 확인후 실행할것