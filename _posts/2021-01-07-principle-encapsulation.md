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

각도를 Degree 형태로 입력받는 `Rotate()` 함수를 구현한다 가정해 봅시다. 아마도 하기와 같이 가볍게 예외상황을 검사하고 `assert`를 작성할 수 있습니다.

```cpp
// 0~360 사이의 degree 값을 전달해야 합니다.
void Shape::Rotate(float delta) {
   assert(0 <= delta && delta < ;
   // 회전시킵니다.
}
```

`Rotate()`를 호출하기 전에는 하기와 같이 값을 검사하고, 유효한 값을 전달해야 합니다.


