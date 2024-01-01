---
layout: single
title: "#1. 설계하라"
categories: "methodology"
tag: ["개발방법론"]
author_profile: false
sidebar: 
    nav: "docs"
---

우리 팀은 프로젝트 하나가 백만라인이야~ 대단하지?

백만라인.... 대단하군요.

혹시, [여기서 발명되지 않았다](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%97%AC%EA%B8%B0%EC%84%9C-%EB%B0%9C%EB%AA%85%EB%90%98%EC%A7%80-%EC%95%8A%EC%9D%8Cnot-invented-here)며, [바퀴를 재발명](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B0%94%ED%80%B4%EC%9D%98-%EC%9E%AC%EB%B0%9C%EB%AA%85reinventing-the-wheel) 한것은 아닐까요? 백만라인이 되도록 왜 프로젝트를 내버려 두셨나요? 

과연 팀원 중에 백만라인의 소스코드를 모두 아는 사람은 몇명이나 될까요? 그중에 얼마나 많은 보석같은 알고리즘이 있을까요? 안타깝지만, 팀원중 누구도 보석같은 알고리즘을 찾기 위해 백만라인을 뒤지지 않을 것입니다.(*[빙산 클래스](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B9%99%EC%82%B0-%ED%81%B4%EB%9E%98%EC%8A%A4iceberg-class) 참고*) 몇번 할 수는 있겠죠. 하지만, 그걸 찾느니 새로 개발하는게 빠르다는 생각으로 코드를 새로 작성하는 경우가 많더군요.(*고백하자면, 저의 경우에는 활용을 위해 약간의 코드 수정이 필요한 경우, 사이드 이팩트가 두려워서 새롭게 만든 경우가 좀 있었습니다.*) 

소스코드는 금새 이백만 라인이 될 것이고, 그 누구도 리팩토링 하려고 하지 않을 것입니다. [코드에서 냄새](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BD%94%EB%93%9C-%EB%83%84%EC%83%88code-smells)가 나기 시작하고, 여기저기 [깨진 창문](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EA%B9%A8%EC%A7%84-%EC%B0%BD%EB%AC%B8broken-windows)이 생기고, 악취가 나기 시작할 겁니다. 누군가는 [인터넷에서 찾은 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%9D%B8%ED%84%B0%EB%84%B7%EC%97%90%EC%84%9C-%EC%B0%BE%EC%9D%8Cfound-on-internet)를 아무런 정제없이 [복사 붙여넣기](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B3%B5%EC%82%AC-%EB%B6%99%EC%97%AC%EB%84%A3%EA%B8%B0copy-paste-programming)해도 죄의식을 느끼지 않겠죠.

리팩토링의 주기는 길어질 것이며, 불필요한 [쓰레기 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%93%B0%EB%A0%88%EA%B8%B0-%EC%BD%94%EB%93%9Cgarbage-code)와 중복된 코드가 많아지고, [매직 상수](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%A7%A4%EC%A7%81-%EC%83%81%EC%88%98magic-constants)가 넘쳐나며, 소스코드는 [스파게티](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%8A%A4%ED%8C%8C%EA%B2%8C%ED%8B%B0-%EC%BD%94%EB%93%9Cspaghetti-code) 처럼 얽히고 섞여 상호 의존도가 높아질 것입니다.

사소한 기능 하나 고치는 데에도 다른 기능에 영향을 미치지나 않을까 불안에 떨 것이며, 그런 불안은 항상 현실로 다가오죠.

버그 하나를 고치면, 또다른 버그 10개씩 발생합니다. 유지보수의 기간은 기하 급수적으로 늘고, 개발팀에 대한 다른 팀의 불신은 점점 커지겠죠.

기타등등의 기능을 추가해야 하는데 얼마나 걸리나요?

정말 듣기 싫은 질문이죠. 뭐라고 답해야 할지. 요것 저것 따져보니 일주일이면 될 것 같지만 머릿속에선 백만라인이 맴을 돕니다. 일주일 만에 하더라도 그 기능을 추가함으로 해서 어떤 버그가 튀어나올지 도무지 감이 잡히지 않습니다.

많이들 경험해 보신 건가요? [죽음의 행진](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%A3%BD%EC%9D%8C%EC%9D%98-%ED%96%89%EC%A7%84death-march) 중인 겁니다.

조언을 해드리자면 빨리 소스코드를 버리라는 겁니다. 과감히 버리세요. 10만라인 정도라면 조심스럽게 리팩토링을 권하겠지만, 그이상이라면 리팩토링 자체가 대규모라 엄두가 나지 않습니다.

더 늦기 전에, 과감히 버리고 새롭게 정리하세요. 유지보수가 되지 않는 프로그램이 시장에서 살아남는게 그리 쉽지 않을 테니까요.

사실 버리기 아깝습니다. 더군다나 새롭게 개발하는데 기간은 상상조차 어렵습니다.

그런데, 대부분 공감하시겠지만, 백만라인의 코드를 분석해서 10개의 기능을 추가하고 나중에 디버깅하는 것 보단, 100개의 기능을 새로 만드는게 빠릅니다. 단위 테스트를 충실히 하고, 상호의존도를 최소화 하면 버그도 없을 겁니다.

10년동안 유지보수해야 될 것을 1년 만에 해치울 수 있습니다.

하지만, 새로 만든다면, 시장 진입이 늦어져 큰 타격을 입을지도 모릅니다. 경쟁사가 먼저 시장을 선점할지도 모릅니다. 누군가 그러더군요. Netscape가 망한 이유는 이전 소스코드를 버리고 새로 개발해서라고. 하지만 분명한건, 계속 유지보수 했다면 더 크게 망했을 겁니다.

그래도 새로 만든다면 최소한의 기회는 있습니다.

빠른 기능추가. 성실한 유지보수. 버그없는 제품. 등등등.

만약 이 최소한의 기회마저 포기한다면, 더딘 기능추가와 반복되는 버그로 시장에서 버림받을 수 있다고 생각합니다. 그리고, 회사의 신뢰가 떨어져 자사의 다른 제품마저도 외면될 수 있습니다. 만약 새롭게 개발할 시간적 여유가 없다면, 포기를 권합니다. 지지부진하게 유지보수를 해봤자 성과도 없고, 버그는 더 늘어날 것이며, 지친 팀원은 퇴사하고, 시장에서 매장되게 되어 있습니다.

차라리 그 팀원을 다른 곳에 투자하여 신규 제품을 개발하는 것이 회사에 더 이익이 되지 않을까 싶습니다.

그리고 새롭게 만들때는 의존도가 최소화 되도록 [관심사를 분리](https://tango1202.github.io/principle/principle-separation-of-concerns/)하여 프로젝트를 여러개로 나누고, 프로젝트 1개당 1 ~ 3만라인 정도로 작성될 수 있도록 [단순하게 설계](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%A8%EC%88%9C%ED%95%9C-%EB%94%94%EC%9E%90%EC%9D%B8simple-design) 하세요. !!!설계!!! 없이 막연히 개발을 시작한다면 뒤죽박죽으로 꼬인 백만라인을 다시 개발할 것입니다.

잊지마세요.

1. 프로젝트는 1~3만 라인정도로 분할되어야 합니다.

2. !!!설계!!!하세요.

3. 프로젝트의 소스코드가 방대해 지지 않도록 [단순하게 설계](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%A8%EC%88%9C%ED%95%9C-%EB%94%94%EC%9E%90%EC%9D%B8simple-design)하세요.

4. 이미 방대해져 버렸다면 [관심사를 분리](https://tango1202.github.io/principle/principle-separation-of-concerns/)하여 프로젝트를 분할하세요.

5. 설계하기 싫다면 !!!포기!!!하세요. 해봤자 망합니다.