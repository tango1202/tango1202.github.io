---
layout: single
title: "#24. [개발 원칙] 나쁜 관행(작성중)"
categories: "principle"
tag: ["개발 원칙", "나쁜 관행", "안티 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 분석 마비(Analysis Paralysis)

과잉 분석으로 구현에 대한 진전이 없고 계획에 너무 많은 시간을 허비하는 것을 말합니다. 잠재적인 위험이나 단점이나 대안에 집중하다 보니 정작 중요한 의사결정이 늦어지는 문제를 겪습니다. 진전없는 회의가 반복된다면, 분석 마비에 빠진 것입니다.

하루빨리 [행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)로 [당신이 어디로 가는지](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%B9%EC%8B%A0%EC%9D%B4-%EC%96%B4%EB%94%94%EB%A1%9C-%EA%B0%80%EB%8A%94%EC%A7%80-%EC%95%84%EB%8A%94%EA%B0%80know-where-you-are-going)를 파악한 뒤, 우선 순위를 선정하고, [아키텍처 민첩성 원칙](https://tango1202.github.io/principle/principle-architectural-agility/)에 따라 [불완전성을 허용](https://tango1202.github.io/principle/principle-tolerance-for-imperfection/)하여 빠르게 구조를 잡은 후, 잦은 피드백과 잦은 실패를 경험하고 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하십시요.

# 암시적인 아키텍처(Architecture by Implication)

아키텍처가 모호하여 조직원들이 아키텍처를 이해하지 못하는 경우입니다.

이런 경우 아키텍처를 무시하거나, 서로 의사 소통이 안되는 경우가 발생합니다. 

또한 아키텍처가 일치하지 않으므로, 시스템간 원할한 통신이 어려워지고, 결합도는 높아지고 응집도는 낮아집니다. 결국 시스템 이해도가 떨어져 유지보수가 어려워 지죠.

아키텍처를 잘 설명하는 문서를 만들고, [공통 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision)을 수립하세요.

# 가정 기반 프로그래밍(Assumption Driven Programming)

[가정 기반 프로그래밍](??)은 모든 사용자와 개발자가 자기와 같다고 가정하는 개발자 관행을 말합니다. 자기가 생각하는 사용 방식대로 사용자가 실행한다고 가정하는 거죠.

사용자는 설명서를 읽고 실행하지 않으며, 실행 경로는 의외로 여러가지입니다. 

따라서 가정하지 마십시요. 잦은 피드백과 잦은 실패를 경험하고 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하십시요. 


# 빈혈 모델(Anemic Model)

# 큰 진흙 공(Big Ball of Mud)

[큰 진흙 공](??)은 아키텍처가 없는 무질서한 코드 덩어리를 말합니다. 아키텍처의 문서화가 어렵고, 모듈의 경계를 규정하기 어렵다면, 큰 진흙 공일 확률이 높습니다. 이러한 구조는 시스템 유지보수를 포기하게 만드는 가장 큰 원인입니다.

[공통 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision)을 수립하고, [관심사를 분리](https://tango1202.github.io/principle/principle-separation-of-concerns/)한뒤 개별 모듈 마다 [SOLID](https://tango1202.github.io/principle/principle-solid/)를 적용하여 하나하나 점진적으로 리팩토링 하시기 바랍니다.

# 빅 디자인 업 프론트(Big Design Up Front)

블롭(The Blob)
깨진 창문(Broken Windows)
캘린더 코더(Calendar Coder)
폴더 버전 관리 복사(Copy Folder Versioning)
프로그래밍 복사 붙여넣기(Copy Paste Programming)
계획에 의한 죽음(Death by Planning)
죽음의 행진(Death March)
덕트 테이프 코더(Duct Tape Coder)
빠른 비트 오른쪽(Fast Beats Right)
기능 크리프(Feature Creep)
개체에 대한 플래그(Flags over Objects)
인터넷에서 찾음(Found on Internet)
프랑켄코드(Frankencode)
얼어붙은 원시인(Frozen Caveman)
골든 해머(Golden Hammer)
빙산 클래스(Iceberg Class)
마지막 10% 함정(Last 10% Trap)
로이스 레인 계획(Lois Lane Planning)
매직 스트링(Magic Strings)
버섯 관리(Mushroom Management)
여기서 발명되지 않음(Not Invented Here)
그들 모두를 지배하는 한가지(One Thing to Rule Them All)
바퀴의 재발명(Reinventing the Wheel)
하나씩 일어나는 것(Singleton)
서비스 로케이터(Service Locator)
빛나는 장난감(Shiny Toy)
연기와 거울(Smoke and Mirrors)
스파게티 코드(Spaghetti Code)
정적 집착(Static Cling)
지뢰밭을 걷다(Walking through a Minefield)
폭포(Waterfall / Waterfail)
위치스 브루 아키텍처(Witches Brew Architecture)햣
