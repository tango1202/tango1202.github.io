---
layout: single
title: "#11. [개체지향 원칙] 제어의 역전 원칙(Inversion of Control Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

제어의 역전 원칙은 **클라이언트가 프레임워크 라이브러리를 제어하지 말고, 프레임워크 라이브러리의 제어를 따르라** 라는 원칙입니다.

조금 풀어 쓰면,

개체의 정보를 Getter로 획득해서 개체 외부에서 로직을 구현하지 말고,

개체 정보가 은닉 되도록 내부에 로직을 구현하라.

라는 뜻입니다.
