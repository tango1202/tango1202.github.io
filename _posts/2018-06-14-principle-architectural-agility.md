---
layout: single
title: "#14. [개발 원칙] 아키텍처 민첩성 원칙(Architectural Agility Principle)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[아키텍처 민첩성 원칙](https://tango1202.github.io/principle/principle-architectural-agility/)은 ***요구사항 변화에 적응할 수 있도록 아키텍처를 설계하라*** 는 원칙입니다.

조금 풀어 쓰면,

1. 요구사항 변화를 수용하면서 점진적으로 아키텍처를 개선하고,(*[점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement) 참고*) 
2. 모듈을 독립적으로 개발하고 배포하고 확장하고,(*[관심사의 분리](https://tango1202.github.io/principle/principle-separation-of-concerns/), [배송은 기능입니다.](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature) 참고*)
3. 작고 느슨하게 결합할 수 있도록 설계하라.

라는 뜻입니다. 

개발 원칙이라기 보다는 철학에 가깝습니다. 아키텍처 변경이 오랜 기간이 걸린다며 !!!핑계!!! 대지 말고, 변화를 수용하셔서 점진적으로 아키텍처를 개선하세요. 

완벽한 아키텍처를 위한 고민은 시간 낭비가 될 확률이 높습니다.(*[분석 마비](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%B6%84%EC%84%9D-%EB%A7%88%EB%B9%84analysis-paralysis), [앞단의 큰 디자인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%9E%EB%8B%A8%EC%9D%98-%ED%81%B0-%EB%94%94%EC%9E%90%EC%9D%B8big-design-up-front) 참고*) 아키텍처의 [불완전성을 허용](https://tango1202.github.io/principle/principle-tolerance-for-imperfection/)하시고, 민첩하게 변화를 수용하세요.
