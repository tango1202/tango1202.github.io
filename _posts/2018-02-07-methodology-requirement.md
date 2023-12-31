---
layout: single
title: "#7. 요구사항 분석은 대충 자주 하라"
categories: "methodology"
tag: ["개발방법론", "요구사항", "SRS"]
author_profile: false
sidebar: 
    nav: "docs"
---

우리는 보통 "무엇을 개발하라" 라는 요구사항이 떨어지면, 어떤것 부터 하게 되나요?

[폭포수 모델](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%ED%8F%AD%ED%8F%AC%EC%88%98-%EB%AA%A8%EB%8D%B8waterfall)을 사랑하신다면,

1. 요구사항 분석
2. 설계
3. 구현 및 테스트
4. 통합 및 테스트
5. 운영 및 유지보수

의 순서가 되겠고,

프로토타이핑을 사랑하신다면,

1. 요구사항 분석
2. 프로토타입 개발
3. 프로토타입 평가
4. 최종 개발

의 순서가 되겠고,

나선형 모델을 사랑하신다면,

1. 요구사항 분석
2. 위험 분석
3. 개발 및 테스트
4. 사용자 평가

의 순서로 끝없이 반복하겠고,

익스트림 프로그래밍을 사랑하신다면,

1. 사용자 스토리의 우선 순위 설정
2. 매일 통합
3. 테스트 주도 개발
4. 짝 프로그래밍

으로 매일 매일 SW를 개선하겠고,

스크럼을 사랑하신다면,

1. 제품 기능 목록 작성
2. 스프린트 계획 회의
3. 스프린트 수행
4. 스프린트 개발완료
5. 스프린트 검토

를 반복하면서 스프린트 주기로 SW를 개선하실 겁니다.

무슨 방법론을 쓰시던 뭘 개발할지 요구사항 구체화를 선행합니다. 그런데, 요구사항은 필연적으로 변동됩니다. 아무리 정성을 들이고 시간을 들이더라도 말이죠.

1. 사용자가 늘어나며 변경됩니다.
2. 사용자의 시스템 이해도가 높아지면서 변경됩니다.
3. 시간의 흐름에 따라 IT 업계의 환경이 변화하여 변경됩니다.
4. 구현시 예측못한 이슈가 !!!반드시!!! 발생하여 요구사항이 변경됩니다.

요구사항 변경에 우리는 어떻게 할까요?

> 애초에 요구사항 명세서에 없는 내용이다... 
> 해당 사항을 적용하려면 구조 개선이 필요하고 엄청난 시간이 걸린다... 

혹시 이러실까요?

새로운 요구사항이나 요구사항 변경을 받아들이지 못하는 구조는 잘못된 구조입니다.

[살금살금 기어오는 기능](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%82%B4%EA%B8%88%EC%82%B4%EA%B8%88-%EA%B8%B0%EC%96%B4%EC%98%A4%EB%8A%94-%EA%B8%B0%EB%8A%A5feature-creep)이라며 무조건 배척하지 마시고, 우선 순위를 선정해서 다음 배포 일정에 포함하는 걸 고민하는게 좋습니다.(*심지어 전혀 예상치 못한 [로이스 레인 계획](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EB%A1%9C%EC%9D%B4%EC%8A%A4-%EB%A0%88%EC%9D%B8-%EA%B3%84%ED%9A%8Dlois-lane-planning)이나 [연기와 거울](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%97%B0%EA%B8%B0%EC%99%80-%EA%B1%B0%EC%9A%B8smoke-and-mirrors)이라 하더라도요. 어떻게든 지킬 수 있는 일정만 확보되면, 세상을 구할 수 있으니까요.*)

프로그램 세계에서 10층 아파트를 100층으로 변경하는건 상수 하나만 바꾸면 되는 거잖아요? 우리는 그렇게 코드 구현을 해야 하잖아요? 우리가 슈퍼맨은 아니지만, 미래를 볼 수는 없지만, 3수 앞을 내다보는 구현 설계를 하셔야 합니다. 

혹시 못하더라도 상관 없습니다. 엉망진창인 [프랑켄코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%ED%94%84%EB%9E%91%EC%BC%84%EC%BD%94%EB%93%9Cfrankencode)가 되었더라도, !!끊임없이!! 리팩토링 해서 개선하면 됩니다.(*단, 이 리팩토링 일정도 개발 일정에 꼭 확보해 두셔야 합니다. 리팩토링 일정이 확보되지 않으면, 결국 개발팀의 헌신만으로 간신히 버티는 [죽음의 행진](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%A3%BD%EC%9D%8C%EC%9D%98-%ED%96%89%EC%A7%84death-march)을 하게 되니까요.*)

인터넷을 뒤져보면 좋은 요구사항명세서(SRS, Software Requirement Specification) 의 사례가 많습니다.

주로 하기 항목들을 포함해야 하고요, 정확하고, 명백하고, 완전하고, 일관되고, 중요도/우선 순위를 명시하고, 검증기준을 제시하고, 수정 가능해야 하고, 관련 요구사항을 추적할 수 있게 작성해야 합니다.

|항목|내용|
|--|--|
|외부 인터페이스 요구사항|시스템으로의 입력과 출력에 대한 요구사항. 사용자 인터페이스, 항목의 이름, 목적, 유효 범위, 다른 함수와의 호출 관계|
|기능 요구사항|기본적인 동작. 데이터 유효 범위, 정상 상황의 동작, 비정상 상황의 동작|
|성능 요구사항|수치적 요구사항|
|논리적 데이터베이스 요구사항|데이터 타입, 사용빈도, 접근 가능성, 데이터간 관계|
|설계 제약사항|표준이나 HW 제한에 따른 제약이나 조건|
|SW 시스템 속성|신뢰도, 사용가능성, 보안, 유지보수, 이식성등|

그런데요, 실상은 거의 대부분 수정되거나 코드 구현시 정립됩니다.(*그리고 놀랍게도, 정성스럽게 작성한 요구사항 명세서를 꼼꼼히 읽고 이행하는 개발자가 별로 없더라구요.*)

|항목|내용|
|--|--|
|외부 인터페이스 요구사항|사용자 인터페이스는 사용자의 사용빈도에 따라 달라집니다. 처음 사용할때는 상세한 안내나 절차가 있길 희망하고, 익숙해 지면 한화면에 와르르 나와주길 희망합니다. 시스템 인터페이스는 실제 구축하고 통합해보면 그제서야 정립됩니다.|
|기능 요구사항|기능들이 추가되거나 변경됩니다. 비정상 상황은 구현을 해보고 테스트를 했을때 뒤늦게 나타날 때가 많습니다. 그나마 출시 전이면 다행입니다. 출시후 몇년뒤에 본격적으로 고객이 사용할때 많이 나타나죠.|
|성능 요구사항|사실 개발자의 능력에 따라 달라지지만, 언제나 고객의 결론은 할 수 있는 한 !!!최대한 빠르게!!! 입니다. 결국 숱한 타협끝에 구현된 결과에 맞게 성능 요구사항이 수정되곤 합니다.|
|논리적 데이터베이스 요구사항|기능 요구사항이 변경되면서 수정됩니다.|
|설계 제약사항|잘 변하진 않습니다만, 표준이 바뀌고, 사용하는 HW도 변경될 소지가 높습니다.|
|SW 시스템 속성|잘 변하진 않습니다만, 통합 테스트 후 운영 테스트를 하면서 확 바뀔 수 있습니다. 확장성을 고려하지 않으면, 구조 변경이 필요할 수 있습니다. 특히 멀티 플랫폼 이슈는 미래를 위해 항상 고려되지만, 실제 사용할 때가 다가오면, 다른 프레임워크로 대체되어 결국 다시 개발되곤 합니다.|

요구사항은 어짜피 바뀝니다. 그리고 바뀌지 않는다면, 그 SW는 시대에 뒤떨어지고, 다양한 의견을 반영하지 못한 정체성 때문에 시장에서 외면 받습니다. 그래서 요구사항은 바껴야만 합니다.

그러니 불필요하게 상세히 작성하여 시간을 낭비하지 마세요. [분석 마비](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%B6%84%EC%84%9D-%EB%A7%88%EB%B9%84analysis-paralysis)나 [앞단의 큰 디자인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%9E%EB%8B%A8%EC%9D%98-%ED%81%B0-%EB%94%94%EC%9E%90%EC%9D%B8big-design-up-front)만 초래하여 시간 낭비만 할 수 있고, 회의만 하다가 [계획에 의한 죽음](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EA%B3%84%ED%9A%8D%EC%97%90-%EC%9D%98%ED%95%9C-%EC%A3%BD%EC%9D%8Cdeath-by-planning)을 맞이할 수 있습니다.

그렇다고 너무 대충 하시면 안됩니다. [암시적인 아키텍처](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%94%EC%8B%9C%EC%A0%81%EC%9D%B8-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98architecture-by-implication)때문에 [마녀가 만든 아키텍처](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%A7%88%EB%85%80%EA%B0%80-%EB%A7%8C%EB%93%A0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98witches-brew-architecture)가 될 수 있습니다. 기본적으로 무슨 목적으로 사용되고, 어떠한 기능들이 있는지는 아셔야 됩니다. 큰틀의 클래스 다이어그램과 용어사전을 작성할 정도로만 확인하고 구현을 시작하시길 추천합니다.

그리고, 리팩토링 주기가 다가오기 전에 요구사항을 다시 확인하세요. 리팩토링의 방향을 알려줄 수 있습니다.

잊지마세요!!

 1. 요구사항을 파악하기 위해 시간을 낭비하지 마세요. 큰틀의 클래스 다이어그램과 용어사전만 작성할 수 있으면 우선 당신이 어디로 가야 하는지 알 수 있습니다.(*[통증 주도 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%ED%86%B5%EC%A6%9D-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9Cpain-driven-development), [당신이 어디로 가는지 아는가](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%B9%EC%8B%A0%EC%9D%B4-%EC%96%B4%EB%94%94%EB%A1%9C-%EA%B0%80%EB%8A%94%EC%A7%80-%EC%95%84%EB%8A%94%EA%B0%80know-where-you-are-going), [단순한 디자인](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%A8%EC%88%9C%ED%95%9C-%EB%94%94%EC%9E%90%EC%9D%B8simple-design), [공통 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision) 참고*)

 2. 요구사항을 자주 확인하고 수정하세요. 리팩토링의 방향을 알려줍니다.(*[점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement) 참고*)

 3. 변경된 요구사항 적용하고 통합하여 고객에게 전달하여 새로운 요구사항을 받으세요.(*[점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement), [지속적인 통합](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EC%A7%80%EC%86%8D%EC%A0%81%EC%9D%B8-%ED%86%B5%ED%95%A9continuous-integration), [배송은 기능입니다.](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature) 참고*)
 
