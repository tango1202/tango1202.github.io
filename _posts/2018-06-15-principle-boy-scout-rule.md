---
layout: single
title: "#15. [개발 원칙] 보이 스카우트 규칙(Boy Scout Rule)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[보이 스카우트 규칙](https://tango1202.github.io/principle/principle-boy-scout-rule/)은 ***캠핑장은 처음 왔을 때보다 더 깨끗하게 하고 떠나라*** 라는 원칙입니다.

조금 풀어 쓰면,

1. push된 코드는 그전보다 조금 더 나은 상태여야 하고, 
2. 원래 코드를 작성했던 사람이 누구이던지 간에, 조금씩 노력하여 모듈을 개선시켜라.  

라는 뜻입니다. !!!원래 코드를 작성했던 사람이 누구이던지 간에!!!말이죠.

코드를 완벽하게 만들어 push 할 필요는 없습니다. 단지 이전보다 조금이라도 나은 상태이면 됩니다. 이런것이 조금씩 쌓인다면, 언젠간 완벽한 코드가 될 수 있습니다.

1. 단순한 리팩토링이라도 합니다.
   * 변수나 함수 이름을 [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability) 좋게 수정합니다.
   * [단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/)을 준수하도록 뚱뚱한 함수나 클래스를 날씬하게 쪼갭니다.
   * 함수나 클래스를 사용하기 쉽게 변경합니다.
   * 인터페이스 계약에 맞게 동작하도록 수정합니다.
   * 인터페이스를 쪼갭니다.
   * 의존성을 줄입니다.
   * 반복되는 코드를 제거합니다.
2. 주석을 코드와 동기화 시킵니다.
3. 문서를 코드와 동기화 시킵니다.
4. 작은 버그도 수정합니다.
5. 테스트 코드를 작성합니다.(*[단위 테스트하라](https://tango1202.github.io/methodology/methodology-unittest/) 참고*)
