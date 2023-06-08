---
layout: single
title: "#10. [개체지향 원칙] 헐리우드 원칙(Hollywood Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

헐리우드 원칙은 헐리우드 영화 제작사가 오디션 배우에게 **전화하지 마세요. 연락처 주시면 우리가 전화드릴께요.** 라고 안내한 말에서 유래된 원칙입니다.

조금 풀어 쓰면,

1. 배우(호출자) 와 영화사(프레임워크) 간에 연락처(Listener)개체의 정보를 Getter로 획득해서 개체 외부에서 로직을 구현하지 말고,

개체 정보가 은닉 되도록 내부에 로직을 구현하라.

라는 뜻입니다.


