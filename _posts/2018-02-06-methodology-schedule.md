---
layout: single
title: "#6. 성급히 일정을 말하지 마라. 묻지도 마라"
categories: "methodology"
tag: ["개발방법론"]
author_profile: false
sidebar: 
    nav: "docs"
---

이거 얼마나 걸릴까? (*약 10초뒤*) 3주요. 

어 그럼 일정 지켜서 수고좀 해봐.  

예전 회사 개발회의에서 팀장님의 질문에 한 동료가 대답했고 팀장님은 잘해보라고 격려했습니다. 

익숙하신 분들 있으시죠? 저는 막 입사하고서 이 광경이 참 신기했습니다.  

저 사람은 어떻게 저렇게 기간 산정을 빨리할 수 있을까? 뭘 구현해야 할지 요구사항도 제대로 이해가 안되는데...

3주뒤 팀장님이 확인하셨고 그 동료는 이러저러한 문제로 일주일 더 걸린다고 했습니다. 그리고 일주일뒤 아시죠? 기간연장이 반복되는거… 사실 낯선 환경이나 기능을 개발하다보면 흔히들 경험하고 반복하는 일이기도 합니다. 

요구사항을 면밀히 분석하지 않고 감으로 일정을 추론한다면, 결국 거짓말이 될 수 밖에 없습니다. 개인차에 따라 책임감과 자기희생으로 일정을 지킬수는 있겠지만, 자신의 건강만 해치는 결과를 낳을 수 있습니다. 

낙관적으로 일정을 축소하지 마세요. 초기의 요구사항은 실제 구축해야 하는 것의 일부일 뿐입니다. 구현하다 보면 추가로 구현해야 할 것들이 눈덩이처럼 늘어납니다. 보통 마지막 10%가 더 많은 기간을 필요로 합니다.(*[낙관적 예측](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%82%99%EA%B4%80%EC%A0%81-%EC%98%88%EC%B8%A1), [마지막 10% 함정](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EB%A7%88%EC%A7%80%EB%A7%89-10-%ED%95%A8%EC%A0%95last-10-trap) 참고*)

혹은 너무 비관적으로 일정을 뻥튀기하여 먼저 개발을 끝내는 바람에 테스터와의 일정이 틀어져 코드를 다 잊었을 즈음 버그가 리포팅 될지도 모릅니다. 

그러면 기간을 요구하는 팀장님께 뭐라고 해야 할까요?  

사실 팀장들은 하는일 없는 것 같지만 무지 바쁘고 머리도 아픕니다. 참가해야 할 회의도 많고, 신기술도 습득해야 하고, 다른팀과 협업도 조율해야 하고, 팀원의 업무도 파악해야 하고, 신입사원이 사고치지 않게 교육도 시켜야 하고, 윗사람이 말도 안되는 아이디어를 진행 하라고 시키면, 하는척하거나 반대 자료를 만들어야 하고, 의기 소침해진 팀원 격려도 해야 하고, 회의 직전까지 분석했던 버그도 빨리 수정해야 하고…  

팀장은 모호한 프로젝트의 일정을 대충이라도 알고 싶어 여러분에게 기간을 물을 뿐이고, 이 회의가 빨리 끝나기를 바랄 뿐입니다. 여러분보다 훨씬 더요. 

하지만, 팀장들의 이러한 질문은 [빠른 속도](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%B9%A0%EB%A5%B8-%EC%86%8D%EB%8F%84fast-beats-right)를 내는게 중요하다는 착각을 하게 만듭니다. 결국 다음과 같은 부작용이 생깁니다.

1. 사용자의 다양한 사용 방식을 고려하지 않고 대충 가정하는 [가정 기반 프로그래밍](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EA%B0%80%EC%A0%95-%EA%B8%B0%EB%B0%98-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dassumption-driven-programming)이 만연해지고, 
2. 깊이있는 고민없이 좋다는 것은 대충 가져다 쓰는 [캘린더 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BA%98%EB%A6%B0%EB%8D%94-%EC%BD%94%EB%8D%94calendar-coder)가 생기며,
3. 대충 덕지덕지 붙여서 개발하는 [덕트 테이프 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%8D%95%ED%8A%B8-%ED%85%8C%EC%9D%B4%ED%94%84-%EC%BD%94%EB%8D%94duct-tape-coder)가 생깁니다.

결국 코드는 이것저것 혼종이 된 [프랑켄코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%ED%94%84%EB%9E%91%EC%BC%84%EC%BD%94%EB%93%9Cfrankencode)가 되어 유지보수가 점점 힘들게 됩니다.

아직도 팀장님이 일정을 물어보시나요? 왠지 3주라고 말하고 싶다구요? 그럼 이렇게 말하세요. 지금은 3주정도 걸릴것 같지만, 요구사항을 좀 더 파악해 보고 기술적 문제는 없는지 검토한 후 3~4일 내에 다시 보고하겠습니다. 라구… 팀장이 뜨끔할 것이고 갑자기 일정을 말하라고 요구하는 일도 점차 없어질 겁니다. 

 잊지마세요!!!  
 
 1. 모호하게 감으로 기간을 말하지 마세요.(*[계획 업데이트](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%9D%BC%EC%A0%95-%EA%B3%84%ED%9A%8D-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8update-the-plan) 참고*)

 2. 요구사항 명세를 공유하지 않고, 일정을 묻지 마세요.
 
 2. 요구사항을 파악한 후 피로감 없이 개발할 수 있는 일정을 산정하세요.(*[타임박싱](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%9D%BC%EC%A0%95-%ED%83%80%EC%9E%84%EB%B0%95%EC%8B%B1timeboxing) 참고*)