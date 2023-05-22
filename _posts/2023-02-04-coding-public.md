---
layout: single
title: "#4. 최소 public"
categories: "coding"
tag: ["코딩"]
author_profile: false
sidebar: 
    nav: "docs"
---

`public`의 갯수를 최소화하여 은닉하면 사용법이 쉬워집니다.

다른 `public` 메서드를 이용하여 구현 할 수 있는 것은 굳이 `class`의 `public` 메서드로 만들 필요가 없습니다.

 

C#에서는 확장 메서드라 하여 이러한 유틸리티성 함수들을 `object.ExtentionMethod()` 로 호출가능하게 합니다. 사실은 `ExtentionMethod(object)`인 `static` 함수 호출인 거죠.