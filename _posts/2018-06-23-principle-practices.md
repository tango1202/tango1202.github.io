---
layout: single
title: "#23. [개발 원칙] 좋은 개발 관행"
categories: "principle"
tag: ["개발 원칙", "개발 관행"]
author_profile: false
sidebar: 
    nav: "docs"
---

# [좋은 코드] 집단 코드 소유권(Collective Code Ownership)

내가 작성한 코드가 아니라, 우리가 작성한 코드입니다. 함께 코드를 공유하고, 함께 이해하고, 함께 [짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming)하고, 함께 [코드 리뷰](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EB%A6%AC%EB%B7%B0code-review)를 하시는게 좋습니다. 선배가 작성한 성스러운 코드가 아닙니다. 수정하는데 두려워하지 마십시요. 

혹여나 잘못된 것은 동료가 [코드 리뷰](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EB%A6%AC%EB%B7%B0code-review)하여 수정해 줄테니까요.

# [좋은 코드] 코드 가독성(Code Readability)

> *Any fool can write code that a computer can understand. Good programmers write code that humans can understand. -- Martin Fowler*

***어떤 바보라도 컴퓨터가 이해할 수 있는 코드를 만들 수 있다. 좋은 프로그래머는 사람이 이해하는 코드를 만든다*** 는 뜻인데요, 여기서 "humans"는 몇년 뒤의 자기 자신이 될 수도 있죠. 제 경우에도 많이 그랬습니다.

각 팀이 논의하여 공통의 [코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)을 만들어 두면 일관되고 가독성 좋은 코딩 생활을 할 수 있습니다. 그럼에도 불구하고 알고리즘이 복잡하여 코드 자체가 [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)이 떨어진다면, 최대한 [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability) 있게 수정하시고, 한계가 있다면, 주석이나 문서를 잘 만들어 두시기 바랍니다.

일반적으로 잘못된 변수명, 짧고 비밀스러운 이름, 개인의 취향에 따른 접두어, 너무 긴 이름, 복잡한 제어문, 일관적이지 못한 코드 레이아웃(*들여쓰기, 줄바꿈, 띄어쓰기*), 흔하지 않은 구조, [단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/)을 따르지 않는 함수나 클래스, 코드 중복, [쓰레기 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%93%B0%EB%A0%88%EA%B8%B0-%EC%BD%94%EB%93%9Cgarbage-code) 등이 코드 가독성을 떨어뜨립니다.

[코드에서 냄새](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BD%94%EB%93%9C-%EB%83%84%EC%83%88code-smells)가 난다면, [보이스카우트 규칙](https://tango1202.github.io/principle/principle-boy-scout-rule/)에 따라 뭐든 수정하세요.

# [좋은 코드] 이름 짓기(Naming Things)

좋은 이름은 [코드 가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)을 높여 생산성을 향상시켜 줍니다. [작명하라](https://tango1202.github.io/methodology/methodology-naming/)를 참고하세요.

# [좋은 코드] 코딩 컨벤션(Coding Conventions)

[코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)은 프로젝트에서 준수해야할 코딩 스타일입니다. [작명](??)에서부터 코드 레이아웃(*들여쓰기, 줄바꿈, 띄어쓰기*)과 언어에 따른 특정 코딩 방법들을 기술합니다.

모두가 동일한 형태로 코드를 작성하므로 [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)이 향상됩니다.

# [좋은 코드] 방어적 프로그래밍(Defensive Programming)

모든 코드는 오류를 발생시킬 수 있습니다. 코드의 논리는 멀쩡하더라도 메모리가 없을 수도 있고, 갑자기 인터넷이 안될 수도 있죠. 

오류가 발생했을때 어떻게든 비교적 안전하게 작동할 수 있도록 코드를 작성하십시요.(*[예외 안전 코드](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/) 참고*)
또한 그런 오류가 발생하지 않도록 사전에 철저히 점검하십시요.(*[공격적 자가진단](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8) 참고*)

# [좋은 코드] 짝 프로그래밍(Pair Programming)

[짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming)은 둘이 짝이 되어 함께 나란히 앉아 프로그래밍하는 방법입니다. 한명은 키보드를 사용하고, 한명은 마우스를 사용하는 것도 좋습니다. 

둘이서 각자 개발하면 더 빨리 될텐데, 왜 이런 방법으로 인력을 낭비하냐 의심할 수도 있을텐데요, 

이 방법을 사용하면 둘이 하나가 되어 코드에 대한 이해 수준을 맞출뿐만 아니라, 서로의 실수를 보완하고, 서로를 독려하기 때문에 코드 품질을 높일 수 있게 됩니다. 특히 두사람이 분석하고 서로 감시하기 때문에, 오류 처리를 대충 하려는 악마의 유혹을 사전에 차단하는 효과도 볼 수 있습니다. 이덕에 향후에 발생할 버그들이 사전에 차단되어 디버깅 시간을 줄일 수 있고 전체적인 개발 생산성도 향상됩니다. 

또한 함께 습득한 방법으로 향후 각자의 코드를 작성하다보면 일관성도 확보됩니다. 그래서 전체적으로 코드 품질이 상향 평준화 될 수 있습니다. 

# [좋은 코드] 코드 리뷰(Code Review)

[짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming)을 하기 좀 곤란하더라도 [코드 리뷰](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EB%A6%AC%EB%B7%B0code-review)를 통해 집단 지성의 슈퍼 파워를 가질 수 있습니다. 동료에게 기여하고, 기여받으십시요.

작은 [코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions) 하나라도 함께라면 놓치지 않습니다.

# [좋은 코드] 러버덕 디버깅(Rubber Dock Debugging)

러버덕에게 문제를 설명하다보면 버그의 원인을 찾을 수도 있습니다. 러버덕을 [짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming)의 보조자로 생각하고 주의깊게 설명해 보세요.

# [좋은 코드] 테스트 주도 개발(Test Driven Development)

테스트를 먼저 개발하여 ***작동하는 깔끔한 코드(Clean Code That Works)*** 를 만드는 마법 같은 개발 방법론입니다. 자세한 내용은 [테스트를 먼저 개발하라](https://tango1202.github.io/methodology/methodology-tdd/)를 참고하세요.

# [좋은 코드] 통증 주도 개발(Pain Driven Development)

[통증 주도 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%ED%86%B5%EC%A6%9D-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9Cpain-driven-development)은 통증이 발생할때만 문제 해결을 시도하는 방법론입니다. 통증이 발생할 것이 두려워 미리 꼼꼼하게 설계하고 힘들게 구현해봤자, 유용하기 보다는 [쓰레기 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%93%B0%EB%A0%88%EA%B8%B0-%EC%BD%94%EB%93%9Cgarbage-code)가 될 가능성이 더 높습니다.

[필요할때 구현하라](https://tango1202.github.io/principle/principle-you-arent-gonna-need-it/)의 다른 변형이고, 아키텍처의 [불완전성을 허용](https://tango1202.github.io/principle/principle-tolerance-for-imperfection/)하는 것입니다. 

하지만, 너무 [빠른 속도](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%B9%A0%EB%A5%B8-%EC%86%8D%EB%8F%84fast-beats-right)나 [암시적인 아키텍처](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%B9%A0%EB%A5%B8-%EC%86%8D%EB%8F%84fast-beats-right)에 빠지지 않도록 해야 합니다. [단순한 디자인](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%A8%EC%88%9C%ED%95%9C-%EB%94%94%EC%9E%90%EC%9D%B8simple-design) 후 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하세요. 

# [좋은 코드] 점진적 발전과 지속적 개선(incremental progress, continuous improvement)

한번에 완벽한 소프트웨어를 개발할 수는 없습니다. 시장 상황에 따라 구현 환경이 변하고, 고객이 실제로 사용하다 보면 새로운 요구사항이나 변경 사항이 발생하기 때문입니다.

따라서 고객의 피드백을 최대한 빠르게 받아야 시행착오가 줄어드는데요, 받은 피드백은 점진적, 그리고 지속적으로 개선한뒤 다시 고객에게 전달하여 피드백을 받습니다. 이러한 잦은 피드백과 잦은 실패는 소프트웨어를 조금씩 완벽하게 만들어 줍니다. 

다음을 준수하십시요.

1. 우선 순위 선정

    고객의 피드백을 무조건 반영하면 안됩니다. 우선 순위를 잘 선정하십시요. 보통 중요도와 긴급도에 따라 우선 순위를 선정할 수 있는데요, 긴급도는 사실 나중에 긴급하지 않을 수 있으니, 중요한 것부터 먼저 처리하는게 좋습니다.

2. 빠른 배송
    
    잦은 피드백과 잦은 실패를 위해서 제품을 빠르게 고객에게 전달하는게 좋습니다. [통합을 자동화](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EC%A7%80%EC%86%8D%EC%A0%81%EC%9D%B8-%ED%86%B5%ED%95%A9continuous-integration)하고, [배송 일정을 준수](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)해서 약속된 일정에 고객에게 제품을 전달할 수 있게 해주십시요. 
    
    이때 충분한 회귀 테스트가 되지 않으면, 지난번에 성공했던 기능이 이번에는 실패하는 [지뢰밭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%A7%80%EB%A2%B0%EB%B0%AD%EC%9D%84-%EA%B1%B7%EB%8B%A4walking-through-a-minefield)을 걷게 됩니다. 이런 경우 오히려 고객에게 신뢰를 잃게 되니 테스트가 성공한 경우에만 [배송](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)해야 합니다.

# [좋은 코드] 당신이 어디로 가는지 아는가(Know Where You Are Going)

대부분의 소프트웨어는 고객들이 원하는 것을 수행하기 위해 동작합니다.

그런데, 고객이 진정으로 원하는 것이 뭔지도 모르는 상태로 개발하면 어떻게 될까요? 산으로 갑니다. 고객에게 중요하지도 않은 것에 집중하여 시간을 낭비하거나, 어쩌면 고객이 원하는 것과는 상관없는 시스템 구조적인 문제로 시간을 허비할 수도 있습니다.

이러한 문제가 발생하지 않도록 [행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)로 고객과 함께 시나리오를 정의하고, 잦은 피드백과 잦은 실패를 경험하며 제품을 개선해 나아가야 합니다. 

# [좋은 코드] 설명서 읽기(Read the Manual)

API 사용시 이런 저런 테스트를 작성해서 기능을 습득하거나, 웹 서핑을 통해 습득할 수도 있지만, 제대로된 설명서가 있다면, 보통은 더 많은 양질의 정보를 빠르게 얻을 수 있습니다. 설명서가 있으면, 먼저 읽으세요.

# [좋은 코드] 단순한 디자인(Simple Design)

`extreme programming`의 창시자인 `Kent Beck`은 단순한 디자인으로 개발하기 위한 4가지 규칙을 우선 순위에 따라 정의했습니다.

1. 최우선으로 모든 테스트를 통과해야 합니다.
2. 작성자의 의도를 표현해야 합니다.
3. 중복을 제거합니다.
4. 클래스, 함수, 모듈의 수를 최소화 합니다.

가까스로 작동하는게 가장 완전한 프로그램입니다. 군더더기를 붙이지 마세요. 상기 4가지의 규칙으로 최대한 단순하게 만들고, 추가된 요구사항이 있다면, [필요할때 구현하라](https://tango1202.github.io/principle/principle-you-arent-gonna-need-it/)를 따르세요.

# [좋은 코드] 공통 아키텍처 비전(Common Architectural Vision)

소프트웨어를 유지보수하고, 기능을 추가하다보면 서로 다른 아키텍처로 구성될 수 있습니다. 이렇게 각자의 아키텍처로 구성되다 보면, [창문이 깨져](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EA%B9%A8%EC%A7%84-%EC%B0%BD%EB%AC%B8broken-windows) [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)이 떨어지거나, [큰 진흙 공](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%ED%81%B0-%EC%A7%84%ED%9D%99-%EA%B3%B5big-ball-of-mud)이 만들어져 유지보수가 어렵거나, [마녀가 만든 아키텍처](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%A7%88%EB%85%80%EA%B0%80-%EB%A7%8C%EB%93%A0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98witches-brew-architecture)처럼 일관성이 없어져 전체적인 코드 품질이 떨어질 수밖에 없는데요,

그렇다고, 동일한 아키텍처를 유지하기 위해 억지로 하나의 아키텍처만을 고수하는건 고집스럽기만할 뿐 합리적이진 못합니다.

먼 미래에 사용해도 완벽한 아키텍처를 지금 당장 만들어 낼 수는 없습니다. [불완전성을 허용](https://tango1202.github.io/principle/principle-tolerance-for-imperfection/)하고, 개발 일정 - 품질 완성도 - 향후 유지보수성 - 안정성 - 기능성 - 사용성 - 속도 성능 - 요구사항 변경간의 **트레이드 오프**도 하셔야 합니다.

그러니, [관심사를 분리](https://tango1202.github.io/principle/principle-separation-of-concerns/)하여 아키텍처간의 영향을 최소화하도록 작은 독립 모듈로 나누고, 각 모듈에 현 상황에 가장 합리적인 아키텍처를 적용하십시요. 그 뒤 [공통의 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision)에 맞게 작은 모듈을 단계적으로 리팩토링하십시요.

# [좋은 제품 - 배포] 테스트 자동화(test automation)

어떤 코드를 작성했을때 사이드 이펙트가 있는지 없는지 꼭 확인하셔야 합니다. 본인이 작성한 부분만 확인하고 넘어가지 마세요. 논리적으로 완벽한 코드라도 엉뚱한 곳에서 오동작 할 수 있습니다. 메모리를 조금더 사용함에 따라 기존에 잘 사용하던 메모리 풀이 동작 안할 수도 있거든요.

따라서 기존 테스트 케이스들을 항상 회귀 테스트하는게 필요합니다. 다만 이런 테스트들이 수동으로 이루어진다면, 귀찮아서 안할 수도 있기 때문에 자동으로 테스트 되도록 시스템을 구축해야 합니다.

# [좋은 제품 - 배포] 지속적인 통합(Continuous Integration)

[테스트 자동화](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94test-automation)는 기존 코드에서의 사이드 이펙트를 감지할 수 있지만, 현재 작성중인 동료의 코드와의 충돌을 확인하려면 [지속적인 통합](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EC%A7%80%EC%86%8D%EC%A0%81%EC%9D%B8-%ED%86%B5%ED%95%A9continuous-integration)이 필요합니다.

만약, 통합 테스트를 위한 환경 구축이 수동으로 이루어진다면, 환경 구축 자체가 부담이라 빠른 확인은 어려워지고, 시간 낭비라고 생각해 통합 테스트가 미뤄질 수 있습니다.

따라서 수동 구축의 부담을 덜기 위해 자동으로 시스템을 구축하여야 합니다.

1. 개발자가 정기적으로 `push`하면,
2. 자동으로 빌드되고,
3. 자동으로 테스트되어야 합니다.

1일 1회 이상 충분히 자주 통합하는게 좋고, 일일 빌드는 필수입니다. 아침에 출근하면 지난 밤 모든 것이 자동으로 통합되고 테스트되어 시스템 오류를 즉시 감지할 수 있고, 문제를 수정할 수 있습니다.

# [좋은 제품 - 배포] 배송은 기능입니다.(Shipping Is A Feature)

제품을 만들고 배포하는데 있어서 갑작스런 버그 발견으로 배포 일정이 지연되거나, 추가 요구사항을 반영하다가 배포 일정이 지연될 수 있습니다.

하지만 배포의 지연은 잦은 피드백과 잦은 실패를 이용한 제품 개선을 방해하고 [자기의 개밥](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%83%81%ED%92%88%EC%84%B1-%EC%9E%90%EA%B8%B0%EC%9D%98-%EA%B0%9C%EB%B0%A5%EC%9D%84-%EB%93%9C%EC%84%B8%EC%9A%94eating-your-own-dog-food)을 먹을 수 없게 합니다. 결국 제품 품질은 떨어지게 되죠. 

[불완전성을 허용](https://tango1202.github.io/principle/principle-tolerance-for-imperfection/)하시고 배포 일정을 준수하세요. 다음 배포 주기에 부족한 부분을 보강하면 됩니다. 

이때 고객이 [지뢰밭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%A7%80%EB%A2%B0%EB%B0%AD%EC%9D%84-%EA%B1%B7%EB%8B%A4walking-through-a-minefield)을 걷지 않도록 충분한 회귀 테스트를 해야 합니다. 그렇지 않은 경우 고객에게 신뢰를 잃으니까요.

# [좋은 제품 - 일정] 타임박싱(Timeboxing)

[타임박싱](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%9D%BC%EC%A0%95-%ED%83%80%EC%9E%84%EB%B0%95%EC%8B%B1timeboxing)은 집중 근로 시간을 갖자로 이해하셔도 됩니다. 집중력을 유지할 수 있는 기간을 설정하고, 이 기간 동안은 할당된 작업을 완료하는데에만 집중합니다. 피로를 예방할 수 있도록 일과 삶 사이에 명확한 경계를 두십시요. 좀더 추진력 있는 개발을 할 수 있습니다. 

짧게는 몇시간일 수 있고, 길게는 스크럼의 스프린트가 될 수 있습니다.

# [좋은 제품 - 일정] 계획 업데이트(Update the Plan)

일정 지연이 발생할 경우 막연히 내일까지로 재정의 하지 마세요. 막연한 계획은 대부분 거짓말이 될 뿐더러 일정 준수에 대한 열정마저 사라지게 합니다. 

일정 수립에 공을 들이시고 지킬 수 있는 일정으로 업데이트 하세요. 

# [좋은 제품 - 상품성] 설명하는 오류 메시지(Descriptive Error Messages)

오류가 발생했을때 사용자에게 보여지는 메시지는 신중하게 설명해야 합니다. 잘못된 설명은 분노만 더 유발하니까요.

특히 오류 메시지는 간결해야 합니다. 사용자는 오류 발생으로 이미 정신이 혼미해진 상태이기 때문에, 장황한 메시지를 만나게 되면 읽지 않고 겁이 나서 닫아버릴 수 있습니다.
    
오류 메시지는 다음의 4H 원칙에 준해서 작성하는게 좋습니다.

1. Human. 인간적으로 작성하세요.

    개발팀만 알거나, 컴퓨터 전공자들만 아는 메시지는 피합니다. 우리의 소프트웨어는 대부분 컴퓨터 비전공자가 사용하니까요.

    "오류 코드 404"는 컴퓨터 전공자들을 위한 메시지이고, "웹페이지를 표시할 수 없습니다."는 막연하며, "페이지가 이동되었거나 삭제되었습니다."가 좀더 사용자 친화적입니다.

2. Helpful. 도움을 주세요.

    오류 발생시 원인이 무엇이고, 사용자가 무엇을 해야 해결할 수 있을지 안내하는게 좋습니다.

    원인과 해결책을 알려주지 않으면, 사용자는 본인의 잘못인지, 시스템의 문제인지, 환경의 문제인지 몰라 잘못된 시도를 할 수 있습니다. 대표적인 예는 불필요한 재부팅이죠.
    
    "서버에 접속할 수 없습니다." 보다는 "사용자 수가 많습니다. 잠시 후 재시도 하세요."가 낫습니다.

3. Humorous. 가볍게 안심시키세요.

    사소한 오류는 가벼운 톤으로 안심시키는게 좋습니다. 

    "아이디가 중복되었습니다. 다른 아이디를 사용하세요."와 같이 딱딱한 명령조보다는 "이미 사용중인 아이디 입니다. 다른 아이디를 사용해 보세요."와 같이 권유하는게 조금 더 사용자를 안심시킵니다. 프로그램의 종류에 따라 "이런", "앗", "헉", "죄송요"등의 멘트들도 고려해 볼만합니다. 

4. Humble. 겸손하세요.

    사용자의 실수로 오류가 발생하는게 아닙니다. 사용자가 실수하도록 유발한 프로그램의 잘못이죠. 사용자 탓이 아니라 프로그램 탓이라는 걸 잊지 마세요.

    "아이디를 입력해야 합니다. 아이디를 입력하세요." 라고 사용자의 실수를 굳이 꼭 찝어 알려주고 명령하지 마세요. "아이디가 필요합니다. 아이디를 입력해 주세요." 와 같이 부탁하세요.

# [좋은 제품 - 상품성] 자기의 개밥을 드세요(Eating your own dog food)

[자기의 개밥을 드세요](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%83%81%ED%92%88%EC%84%B1-%EC%9E%90%EA%B8%B0%EC%9D%98-%EA%B0%9C%EB%B0%A5%EC%9D%84-%EB%93%9C%EC%84%B8%EC%9A%94eating-your-own-dog-food)는 자사의 제품을 스스로 사용하라는 뜻입니다. "Alpo" 개밥의 TV 광고에서 광고 모델이 "Alpo" 개밥을 자신의 개에게 먹였다고 말한데서 유래되었다네요.

스스로 자사의 제품을 사용해 보면, 설계시에는 고려하지 못했던 사용성, 기능성이 확보됩니다. 디버깅은 덤입니다.

아마존 서비스 초창기에 `Jeff Bezos`는 모든 임직원들에게 아마존 서비스를 사용하도록 지시했고, "이것을 수행하지 않는 사람은 모두 해고됩니다. 감사합니다. 좋은 하루 보내세요!"라고 말했습니다. 그결과 엄청난 성공을 거두었죠.

# [좋은 팀] 전체 팀(Whole Team)

프로젝트를 완성하기 위한 이해관계자인 기획자-UX 디자이너-개발자-테스터-프로젝트 관리자-비즈니스 대표가 하나의 팀이 되어 직무간 장벽을 없앤 팀을 말합니다. 이런 팀은 공동의 목표를 가지고 있기 때문에 성공 가능성이 높아집니다.

# [좋은 팀] 행동 중심 개발(Behavior Driven Development)

[행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)은 개발자, 테스터, 비지니스 이해관계자가 모두 쉽게 이해할 수 있는 언어로 시스템의 동작을 협력하여 정의하고, 시나리오의 [테스트를 자동화](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94test-automation)하여 개발하는 모델입니다. 

여기서 이해할 수 있는 언어로 협력하는 것이 중요합니다. 알아들을 수 없는 말로 서로 협력하긴 힘들죠.

개발자, 테스터, 비지니스 이해 관계자의 협업이 잘 이루어져 서로간의 이해도가 높다면, 시스템의 동작 정의가 좀더 명확해지고, 시행착오는 줄어들게 됩니다.

1. 사용자 스토리 : 최종 사용자 관점의 높은 수준의 설명으로서 어떤 동작을 원하는지, 그 이유는 무엇인지를 설명합니다. 이유를 이해하는데 주력합니다.

2. 시나리오 : 주어진 조건(Given)인 상황에서 언제(When) 무엇을(Then) 해야 하는지 설명합니다. 이때 무슨 동작을 하는지 이해하는데 주력합니다. 시나리오가 만들어졌다면, 꼭 [테스트 자동화](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94test-automation)를 하세요. 시나리오는 필수적으로 테스트되어야 합니다. 그렇지 않으면 비지니스 이해 관계자는 [지뢰밭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%A7%80%EB%A2%B0%EB%B0%AD%EC%9D%84-%EA%B1%B7%EB%8B%A4walking-through-a-minefield)을 걷게 되고 신뢰만 떨어집니다. 

