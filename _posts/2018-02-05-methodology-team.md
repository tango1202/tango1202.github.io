---
layout: single
title: "#5. 버그를 수정하지 말고 방지하라"
categories: "methodology"
tag: ["개발방법론"]
author_profile: false
sidebar: 
    nav: "docs"
---

이럴수가… 한 동료가 기능을 만들어서 테스터에게 보냈는데 버그가 하루만에 10개 넘게 왔군요? 동료가 열심히 고쳐서 다시 테스터에게 보냈더니 이번엔 또다른 것 10개가 돌아왔네요. 또 동료가 열심히 고쳐서 다시 테스터에게 보냈더니 이번엔 예전에 고친 버그가 재현되네요.(*[테스트 자동화](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94test-automation)를 안했나 보네요.*) 

제가 경험한 어떤 관리자는 보고된 버그를 금주내에 모두 고치라고 !!!지시!!! 를 자주 하셨습니다. "버그가 몇개 보고되었나요? 금주까지 모두 수정 바랍니다."

실현 가능한 지시일까 고민해 봅니다. 열심히 하면 가능할 것도 같습니다.

그런데, 보고된 버그가 전체 버그라고 생각하는건 아니겠죠? 정말 지금 보고된 것만 수정되면 더이상 아무런 버그도 없는 좋은 프로그램이 될까요? 

모든 버그에는 원인이 있습니다. 

개발자의 단순한 실수일 수도 있지만, 대부분 요구사항의 잘못된 이해(*[버섯 관리](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EB%B2%84%EC%84%AF-%EA%B4%80%EB%A6%ACmushroom-management) 참고*)와 프로젝트나 플랫폼에 대한 부족한 기술 지식에서 비롯됩니다.  

특히 버그 수정시 다른 버그가 발생한다면 개발자가 현시스템이나 코드 구조에 대해 명확히 파악하지 못하고 버그를 수정하고 있다는 뜻입니다. 

이건 언제, 어떠한 방식으로 터질지 모르는 폭탄과 같습니다. 늘상 [지뢰밭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%A7%80%EB%A2%B0%EB%B0%AD%EC%9D%84-%EA%B1%B7%EB%8B%A4walking-through-a-minefield)을 걷는 셈인 거죠.

더군다나 테스터들과 긴밀히 협업하지 않는다면, 뒤늦게 버그가 발생되어 어떠한 수정에 의해 발생한 버그인지 원인도 모른채 밤을 새는 낭패를 경험할 수 있습니다.  

좋은 리더라면, 막연히 눈앞의 버그만 수정하라고 지시하지 않을 뿐만 아니라 버그가 발생하지 않도록 미연에 방지합니다. 

잊지마세요!!!  

1. 개발 초기에 요구사항을 같이 분석합니다.(*[행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development) 참고*)

2. 개발중에 상호설계를 하여 구조이해를 돕습니다. (*[집단 코드 소유권](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%91%EB%8B%A8-%EC%BD%94%EB%93%9C-%EC%86%8C%EC%9C%A0%EA%B6%8Ccollective-code-ownership), [짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming) 참고*)

3. 버그 발생시 원인은 무엇인지, 어떻게 수정하는게 좋은지, 관련 코드부분은 어디인지 서로 상의합니다.(*[짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming) 참고*)

4. 버그를 미연에 방지하기 위해 [방어적으로 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%B0%A9%EC%96%B4%EC%A0%81-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Ddefensive-programming) 하시고, 악마의 유혹에 빠지지 않도록 [짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming) 하세요.

5. 옆에 있는 장난감에 주의깊게 문제를 설명해 보세요. 문제의 원인을 알 수 있습니다.(*[러버덕 디버깅](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%9F%AC%EB%B2%84%EB%8D%95-%EB%94%94%EB%B2%84%EA%B9%85rubber-dock-debugging) 참고*)

6. 디버깅후 이전 테스트가 정상적으로 동작하는지 꼭 확인하세요.(*[테스트 자동화](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94test-automation), [지속적인 통합](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EC%A7%80%EC%86%8D%EC%A0%81%EC%9D%B8-%ED%86%B5%ED%95%A9continuous-integration) 참고*)

7. 제품을 전사적으로 실무에 사용해 보세요. 상상하지 못했던 버그들이 우수수 나옵니다.(*[자기의 개밥을 드세요](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%83%81%ED%92%88%EC%84%B1-%EC%9E%90%EA%B8%B0%EC%9D%98-%EA%B0%9C%EB%B0%A5%EC%9D%84-%EB%93%9C%EC%84%B8%EC%9A%94eating-your-own-dog-food) 참고*)