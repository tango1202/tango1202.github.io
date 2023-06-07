---
layout: single
title: "#7. [개체지향 원칙] 캡슐화(Encapsulation)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

캡슐화는 **개체의 내부 상태의 구현 원리를 개체 사용자는 알 필요 없게 하라** 는 원칙입니다.

조금 풀어 쓰면,

1. 개체의 멤버 변수나 함수는 꽁꽁 숨긴 뒤, 꼭 필요한 것만 외부에 public 으로 노출하고,
2. 단위 기능을 응집하고, 결합도는 낮춘뒤,
3. 잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게 구현하라

라는 뜻입니다. 

각도를 Degree 형태로 입력받는 `Rotate()` 함수를 구현한다 가정해 봅시다.





