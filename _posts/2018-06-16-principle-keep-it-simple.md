---
layout: single
title: "#16. [개발 원칙] 단순하게 유지하라(Keep It Simple)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

단순하게 유지하라는 원칙은 흔히 **KISS(Keep It Simple, Stupid)** 라고도 불립니다.

시스템이나 코드가 단순하게 유지될 때,

1. 가장 잘 작동되고,
2. 문제를 찾아내기 쉽고,
3. 수정하기 쉬우며,
4. 확장하기 쉽기 때문입니다.

간단하게 만드는게 사실 더 어렵습니다.

1. 함수명, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)명을 간결하고, 명확하게 합니다.
2. 설명이 필요 없도록 상황에 맞는 디자인 패턴을 사용합니다.
3. 사용자 친화적인 방법을 사용합니다.
4. 팀내에서 공통된 [코딩 컨벤션](??)을 준수합니다.
5. 직관적이고, 객관적이고, 일관된 방법을 사용합니다.
6. 외부에서 사용할 함수들만 `public`로 만듭니다. 
7. 언젠가 유용할것 같은 것들은, 필요할 때 작성합니다.


