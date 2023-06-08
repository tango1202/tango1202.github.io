---
layout: single
title: "#9. [개체지향 원칙] 묻지말고 말하라 원칙(Tell, Don't Ask Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

묻지말고 말하라 원칙은 **개체의 내부 정보를 자꾸 묻지 말고 그냥 원하는 작업을 시켜라** 는 원칙입니다.

조금 풀어 쓰면,

1. 개체의 내부정보를 Getter로 획득하여 내부 정보를 오남용하지 말고,
2. 개체 외부가 아닌 내부에 로직을 구현하라.

라는 뜻입니다. 

하기 그림처럼 말이죠.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e1808b55-c5f3-4a9b-8e44-305f4fad452e)

이렇게 개체의 정보와 행동을 결합시켜 응집력을 강화하면 캡슐화가 좋아지고, 은닉성도 좋아집니다. 


