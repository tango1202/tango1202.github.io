---
layout: single
title: "#7. [개체지향 원칙] 캡슐화(Encapsulation)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

캡슐화는 **개체 사용자가 개체의 내부 상태의 구현 원리를 알 필요 없게 하라** 는 원칙입니다.

조금 풀어 쓰면,

1. 개체의 멤버 변수나 함수는 꽁꽁 숨긴 뒤, 꼭 필요한 것만 외부에 public 으로 노출하고,
2. 단위 기능을 응집하고, 결합도는 낮춘뒤,
3. 잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게 구현하라

라는 뜻입니다. 

**위반 사례**

각도를 Degree 형태로 입력받는 `Rotate()` 함수를 구현한다 가정해 봅시다. 아마도 하기와 같이 가볍게 예외상황을 검사하고 `assert`를 작성할 수 있습니다.

```cpp
// delta : delta를 angle과 더했을때, 0~360 사이의 값이 되도록 전달해야 합니다.
void Shape::Rotate(float delta) {
   float angle = angle + delta:
   assert(0 <= angle && angle < 360);
   // 회전시킵니다.
}
```

상기의 `Rotate()`를 호출하기 전에는 하기와 같이 `delta` 값을 검사하고, 유효한 값을 전달해야 합니다.

```cpp
// shape의 현재 각도
float angle = shape.GetAngle();

// delta만큼 회전한 후의 각도
float finalAngle = angle + delta;

// filnalAngle이 0~360이 되도록 delta를 조정한다.
if (finalAngle < 0) {
    delta = 360 + finalAngle - angle;
}
else if (360 <= finalAngle) {
    delta = finalAngle - 360 - angle;
}

// delta만큼 더 회전시킵니다.
shape.Rotate(delta);
```

좀 지저분하지만 참을만 할까요? 한번은 참을만한데, `Rotate()` 함수 호출할 때마다 값을 보정하기 위해 저리도 긴 if문을 쓰는건 견디기 힘든 중복으로 이어집니다.

임시 방편으로 중복을 피하기 위해 하기와 같이 `Util::CalcShapeRotateDelta()`를 만들어 코드 중복은 어찌어찌 회피할 수는 있습니다.

```cpp

```

하지만, `Rotate()` 호출전에 `delta`값을 보정하기 위해 `Util::CalcShapeRotateDelta()` 을 항상 호출해야 하는 것도, 어찌보면 코드 중복이라 볼 수 있고, 클래스 간의 결합도도 하기 그림과 같이 복잡해 집니다.

그림...

여전히 단위 기능의 응집도도 낮고, 결합도는 높으며, `Util::CalcShapeRotateDelta()` 호출을 빼먹어서 잘못 사용하기에 쉽습니다. 캡술화 위반입니다.

**준수 방법**

상기 예제는 Degree 형식을 처리하는 과정이 함수 호춯전과 함수내에 흩어져 있기 때문에 발생한 문제입니다. `Rotate()`함수 바깥에선 `delta`값을 보정하고, 안에서는 `angle`과 `delta`를 더한 뒤 회전시키니까요.

이렇게 흩어진 처리방식을 해결하기 위해, Degree 형식을 처리하는 `Degree` 타입을 만들고, 데이터 보정 처리를 응집해 줍니다. 그러면 Degree 형식 처리방법이 캡슐화 되고, 응집력은 높아지고, 결합도는 낮아집니다. 또한 함수 호출이 제대로 사용하기 쉽게 변경됩니다.

하기는 `Degree`의 구현코드 입니다. 어떠한 실수를 더하던, 빼던 0~360사이의 값을 유지합니다.

```cpp
```

`Rotate()`함수는 다음과 같이 구현할 수 있습니다.

다음과 같이 테스트를 작성할 수 있습니다.

```cpp
```

클래스 다이어그램은 다음과 같습니다.

