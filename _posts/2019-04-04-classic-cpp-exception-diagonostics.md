---
layout: single
title: "#4. [고전 C++ 가이드] 자가 진단과 진단 최소화"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 공격적 자가진단

코드의 품질향상, 디버깅의 용이함을 위해 함수에 진단 코드를 삽입

ASSERT

Debug 환경에서의 논리 결함 진단
void f(int* p) {
    _ASSERTE(p && "p는 유효한 포인터이어야 한다.");
}

사전 가정
인자가 유효한가?
인자가 가정한 값인가?

사후 가정
연산 결과 확인
수행 결과 확인

오버 플로우/연산오류/인프라 오류는 끊임 없이 감시하라
---

# 최소 가정

가정이 최소화되면 코드의 가독성, 유지보수성, 사용 용이성은 향상된다.

효율적인 인자 사용

void f(T* obj) {
    _ASSERTE(obj); // 쓰기는 하는가?
    if (!obj) return; // 런타임 시에 없다면 죽는다.

    ......
}

보다는

void f(T& obj) {
   // Assert가 필요한가? 
   // null 검사가 필요한가?
    ......
}

데이터 추상화

void f(int angle) {
    _ASSERTE(0<= angle && angle < 360);
   if (!(0 <= angle && angle < 360)) return;
   ......
}
혹은,
void f(int angle) {
   angle = Normalize(angle); // 보정할 수 
 }                                          // 있다면 한다.


보다는

void f(const Degree& angle) {
   // Assert가 필요한가? 
   // null 검사가 필요한가?
   // 보정은 Degree 클래스에서 한다.
    ......
}
