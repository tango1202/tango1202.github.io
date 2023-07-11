---
layout: single
title: "#5. [고전 C++ 가이드] 개체 생성과 소멸"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---





`operator new`와 `operator delete`

delete p; // p가 NULL이어도 안전

위치 지정 생성((Placement New))
T* p = new(buf) T; // 명시적 delete 호출


heap 객체 : new 시 생성, delete 시 소멸

T* p = new T; delete p; // (O) delete p; // (X) 두번 죽일 순 없다.

new - delete 쌍
new[] - delete [] 쌍(delete를 하면 첫번째 것만 소멸됨)

new 는 bad_alloc 예외를 던짐
T* p = new T;
if (!p) {} // 예외를 던진다는데 왜 널검사를 하니? 널검사 하고 싶으면 다음처럼... T* p = new (std::nothrow) T; // 실패하면 0 리턴




위치 지정 new, 명시적 소멸자 호출(재정의 방법은 EC++ 8장 참고)

void * operator new(size_t, void* p) {return p}; void* buf = malloc(sizeof(X)); // X 클래스만큼 할당
X* p = new(buf)X; // buf에 X 객체를 만든다. p->~X(); // 명시적 소멸자 호출
free(buf); // 할당된 


