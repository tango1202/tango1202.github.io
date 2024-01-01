---
layout: single
title: "#24. [개발 원칙] 나쁜 개발 관행"
categories: "principle"
tag: ["개발 원칙", "나쁜 관행", "안티 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

# [나쁜 코딩 관행] 코드 냄새(Code Smells)

동작은 정상적으로 하지만, 잠재적인 문제를 가진 코드를 말합니다. 찾아내는 방법은 간단합니다. 코드 조각을 이용하여 세미나 하기 부끄럽거나, 코드를 읽는데 너무 어렵다면 코드에 냄새가 난다는 뜻입니다.

주로, 

1. [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability) 위배

    * 비밀스러운 이름
    * [코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)을 무시한 코딩
    * 코드 중복

2. 잘못된 구조

    * 흔하지 않은 구조나 [캘린더 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BA%98%EB%A6%B0%EB%8D%94-%EC%BD%94%EB%8D%94calendar-coder)의 중독된 구조
    * [단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/) 위배 : [블롭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B8%94%EB%A1%ADthe-blob)이 되어가는 너무 거대한 함수나 클래스
    * [리스코프 치환 원칙](https://tango1202.github.io/principle/principle-liskov-substitution/) 위배 : 자식 개체가 부모 개체를 상속하고 일부만 사용
    * [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 위배 : 높은 결합도(*다른 개체와 상호작용이 빈번함, 수정시 많은 개체를 함께 수정해야 함*), 느슨한 응집(*함께 사용하는 데이터와 함수가 분리됨, 캡슐화가 필요한 개체*)

3. 어지러운 논리

    * `isNotNoNeverOk` 따위의 참인지 거짓인지 어지러운 논리
    * 여러 용도의 상태값을 저장하는 변수
    * 너무 많은 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)
    * 과도한 주석(*[주석은 리팩토링의 신호다](https://tango1202.github.io/comment/comment-refactoring/) 참고*)

4. 의미없는 정성
    
    * 언젠간 유용할 것 같지만 여전히 유용하지 않은 [쓰레기 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%93%B0%EB%A0%88%EA%B8%B0-%EC%BD%94%EB%93%9Cgarbage-code)
    * 무의미하게 분리된 함수
    * 의미없는 함수 포워딩
    * 의미없는 관리자 개체

5. 코딩 금기

    * 불필요한 `public`(*[최소 public](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-complete-class/#%EC%B5%9C%EC%86%8C-public) 참고*)
    * [전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)
    * [다운 캐스팅](?https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#runtime-type-infortti%EC%99%80-%ED%98%95%EB%B3%80%ED%99%98) 혹은 모든 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)
    * [상수성 계약](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD) 위반
    * [매직 상수](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%A7%A4%EC%A7%81-%EC%83%81%EC%88%98magic-constants)

가 보인다면, 리팩토링으로 수정하셔야 합니다.

# [나쁜 코딩 관행] 깨진 창문(Broken Windows)

[깨진 창문](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EA%B9%A8%EC%A7%84-%EC%B0%BD%EB%AC%B8broken-windows)이 있는 건물에 낙서를 하고 쓰레기를 버리는건 죄의식이 생기지 않습니다. 내버려두면 담배 꽁초, 음식물 봉지들이 금새 쌓이게 됩니다. 악취가 나는 코드도 마찬가지입니다. 내버려두면 점점 쓰레기가 쌓이고 부패하게 됩니다.

누군가가 창문을 깨지 않도록, [코드 가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)을 높이시고, [공통 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision)을 수립하세요. 그리고, [보이스카우트 규칙](https://tango1202.github.io/principle/principle-boy-scout-rule/)에 따라 뭐든 수정해 두세요.

# [나쁜 코딩 관행] 쓰레기 코드(Garbage Code)

언젠간 유용할것 같아 정성들였지만, 결국 그런 코드는 쓰레기가 됩니다. 동료들의 저장소를 무의미하게 차지하고, 코드 분석에 눈을 어지럽히며, 빌드 시간만 좀먹습니다. 이런 코드는 개인 Github에 잘 보관했다가 필요할때 clone하세요.

* [인터넷에서 찾아서 추가](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%9D%B8%ED%84%B0%EB%84%B7%EC%97%90%EC%84%9C-%EC%B0%BE%EC%9D%8Cfound-on-internet) 하였으나 필요한 것보다 많이 복사된 코드
* [캘린더 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BA%98%EB%A6%B0%EB%8D%94-%EC%BD%94%EB%8D%94calendar-coder)가 맹목적으로 추가한 코드
* [과잉 설계된 디자인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%9E%EB%8B%A8%EC%9D%98-%ED%81%B0-%EB%94%94%EC%9E%90%EC%9D%B8big-design-up-front)으로 추가된 코드

# [나쁜 코딩 관행] 매직 상수(Magic Constants)

일반적으로 시스템 내에서 사용되는 숫자를 그대로 사용하는 경우 해당 의미를 파악하기 힘듭니다.

[매직 상수](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%A7%A4%EC%A7%81-%EC%83%81%EC%88%98magic-constants)를 그대로 사용하지 마시고, [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)을 위해 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)을 이용하던가 [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)를 이용하여 이름을 부여하여 사용하세요.

특히 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98) 같은 경우 다국어 처리가 있기 때문에 더욱 더 중요합니다. [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)가 [매직 상수](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%A7%A4%EC%A7%81-%EC%83%81%EC%88%98magic-constants)로 코드내에서 흩어져 있으면, 문자열의 내용 수정시 일부만 수정될 확률도 높아지고, 유사한 문자열이 여러개 만들어져 코드가 파편화되기 쉽습니다.

숫자나 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)에 이름을 부여하고, 본의아니게 중복 관리되지 않도록 [스스로 반복하지 마라](https://tango1202.github.io/principle/principle-dont-repeat-yourself/) 원칙을 따르세요.

# [나쁜 코딩 관행] 스파게티 코드(Spaghetti Code)

[스파게티 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%8A%A4%ED%8C%8C%EA%B2%8C%ED%8B%B0-%EC%BD%94%EB%93%9Cspaghetti-code)는 코드의 구조가 너무도 복잡하게 꼬여 읽고 이해할 수 없는 코드를 말합니다.

[코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)을 만들고, [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)을 준수하고 [덕트 테이프 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%8D%95%ED%8A%B8-%ED%85%8C%EC%9D%B4%ED%94%84-%EC%BD%94%EB%8D%94duct-tape-coder)가 없는지 관찰해 보세요.

# [나쁜 코딩 관행] 빈혈 모델(Anemic Model)

[개체 지향 프로그래밍](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-generic/#%EA%B0%9C%EC%B2%B4-%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)은 데이터와 이를 처리하는 기능을 응집하는데요, 기능들의 응집이 부족할때 [빈혈 모델](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B9%88%ED%98%88-%EB%AA%A8%EB%8D%B8anemic-model)이라 합니다.

[빈혈 모델](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B9%88%ED%98%88-%EB%AA%A8%EB%8D%B8anemic-model)에서는 대부분의 개체가 데이터들의 집합일 확률이 높고, 이를 활용하는 기능들이 부족하여 데이터를 사용하기가 어려워 집니다. 혹은 내부 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 그냥 노출되어 일일이 데이터를 직접 분석하고 가공해야 하는 어려움이 있을 수 있습니다.

데이터를 분석하고 가공하는 기능을 추상화해서 제공하거나, [관심사의 분리 원칙](https://tango1202.github.io/principle/principle-separation-of-concerns/)에 따라 비지니스 레이어를 만드시고, [묻지 말고 말하라 원칙](https://tango1202.github.io/principle/principle-tell-dont-ask/)에 따라 데이터를 사용하는 유용한 방법들을 제공하세요.

# [나쁜 코딩 관행] 빙산 클래스(Iceberg Class)

[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)한 [최소 public](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-complete-class/#%EC%B5%9C%EC%86%8C-public)을 따르겠다며 모든 것을 꽁꽁 숨겨두면, 숨겨진 곳에 많은 유용한 것들이 있는 걸 모르고 [바퀴를 재발명](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B0%94%ED%80%B4%EC%9D%98-%EC%9E%AC%EB%B0%9C%EB%AA%85reinventing-the-wheel)할 수 있습니다.

[의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/)에 따라 인터페이스화 하시고, [개방-폐쇄 원칙](https://tango1202.github.io/principle/principle-open-closed/)에 따라 유용한 것은 추출하세요. 그러면 내부적인 동작 흐름이 인터페이스화되어 재활용할 수 있는 요소들을 감지하기 쉽습니다.

# [나쁜 코딩 관행] 복사 붙여넣기(Copy Paste Programming)

이전과 유사한 코드 조각을 복사 붙여넣기하여 약간 수정하여 사용할 수 있는데요, 코드 중복을 만드는 악마의 유혹입니다. 

공통적인 부분을 추출하여 함수화하거나 부모 개체로 만들고 상속하십시요. 심지어 간단한 산술 연산도요. `val = a * b / c;` 와 같이 곱하고 나누는게 반복된다면, `MulDiv(a, b, c)`함수를 만드는 게 좋습니다.

# [나쁜 코딩 관행] 인터넷에서 찾음(Found on Internet)

인터넷에서 찾은 정보나 코드 조각을 아무런 검토없이 즉시 제품에 적용하는 관행을 말합니다. 제품의 [코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)과 맞지 않고, [덕트 테이프](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%8D%95%ED%8A%B8-%ED%85%8C%EC%9D%B4%ED%94%84-%EC%BD%94%EB%8D%94duct-tape-coder)로 덕지덕지 붙은 코드가 되기 쉽습니다.

인터넷의 정보나 코드 조각을 그냥 [복사 붙여넣기](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B3%B5%EC%82%AC-%EB%B6%99%EC%97%AC%EB%84%A3%EA%B8%B0copy-paste-programming)하지 마시고 정제하세요.

# [나쁜 코딩 관행] 큰 진흙 공(Big Ball of Mud)

[큰 진흙 공](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%ED%81%B0-%EC%A7%84%ED%9D%99-%EA%B3%B5big-ball-of-mud)은 아키텍처가 없는 무질서한 코드 덩어리를 말합니다. 아키텍처의 문서화가 어렵고, 모듈의 경계를 규정하기 어렵다면, 큰 진흙 공일 확률이 높습니다. 이러한 구조는 시스템 유지보수를 포기하게 만드는 가장 큰 원인입니다.

[공통 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision)을 수립하고, [관심사를 분리](https://tango1202.github.io/principle/principle-separation-of-concerns/)한뒤 개별 모듈 마다 [SOLID](https://tango1202.github.io/principle/principle-solid/)를 적용하여 하나하나 점진적으로 리팩토링 하시기 바랍니다.

# [나쁜 코딩 관행] 블롭(The Blob)

[블롭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B8%94%EB%A1%ADthe-blob)은 1958년 스티브 맥퀸이 출연한 B급 호러 영화입니다.(*영화는 여기에 있네요. [https://www.youtube.com/watch?v=2Ti-uUsaQOc](https://www.youtube.com/watch?v=2Ti-uUsaQOc)*) 여기 나오는 끈적이 괴물이 모든 것을 흡수하고 점점 커지는데요, 이러한 괴물같은 클래스를 만들지 마세요. 공포스럽습니다.

[단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/)을 실천하세요.

# [나쁜 코딩 관행] 가정 기반 프로그래밍(Assumption Driven Programming)

[가정 기반 프로그래밍](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EA%B0%80%EC%A0%95-%EA%B8%B0%EB%B0%98-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dassumption-driven-programming)은 모든 사용자와 개발자가 자기와 같다고 가정하는 개발자 관행을 말합니다. 자기가 생각하는 사용 방식대로 사용자가 실행한다고 가정하는 거죠.

하지만, 사용자는 설명서를 읽고 실행하지 않으며, 실행 경로는 언제나 개발자보다 창의적입니다. 

따라서 가정하지 마십시요. 잦은 피드백과 잦은 실패를 경험하고 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하십시요. 

# [나쁜 코딩 관행] 캘린더 코더(Calendar Coder)

소프트웨어 설계의 목표는 

1. 정상적으로 작동하는 소프트웨어
2. 다음 게임을 위한 준비

2가지 입니다.

특히, 좋은 설계는 다음 게임을 위한 유지보수의 편의성을 위하여

* 가독성,
* 유연성, 확장성, 이식성
* 모듈성, 캡슐화(*잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*)
 
가 좋아야 합니다. 

[캘린더 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BA%98%EB%A6%B0%EB%8D%94-%EC%BD%94%EB%8D%94calendar-coder)는 맹목적으로 좋다는 것은 모조리 가져다 쓰는 분들입니다. 위의 목표를 잊어 버린 채로 말이죠. 왜 패턴을 사용하는지 모른 채로, 패턴이 좋다니깐, 마구 패턴들을 사용하고 서로를 결합시킵니다. 결국 패턴 중독에 빠져 분석이 되지 않는 코드를 만들어 내곤 합니다. 

상황에 맞게 반영하는 판단력이 필요합니다. 판단의 기준은 "유지보수의 편의성"입니다. 

# [나쁜 코딩 관행] 덕트 테이프 코더(Duct Tape Coder)

[덕트 테이프 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%8D%95%ED%8A%B8-%ED%85%8C%EC%9D%B4%ED%94%84-%EC%BD%94%EB%8D%94duct-tape-coder)는 청테이프로 임시 땜빵을 잘하는 개발자입니다. 단기적으로는 정상적으로 작동하는 것처럼 보이지만, 다음 게임을 위한 준비를 할 수 없게 만들죠. [인터넷에서 찾은 것](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%9D%B8%ED%84%B0%EB%84%B7%EC%97%90%EC%84%9C-%EC%B0%BE%EC%9D%8Cfound-on-internet)을 [복사 붙여넣기](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B3%B5%EC%82%AC-%EB%B6%99%EC%97%AC%EB%84%A3%EA%B8%B0copy-paste-programming)하거나, [코드 냄새](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BD%94%EB%93%9C-%EB%83%84%EC%83%88code-smells)를 풍깁니다.

장기적으로 생각하세요. 우리의 제품이 잠깐 반짝이고 사라질게 아니라면요.

# [나쁜 코딩 관행] 프랑켄코드(Frankencode)

[프랑켄코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%ED%94%84%EB%9E%91%EC%BC%84%EC%BD%94%EB%93%9Cfrankencode) 서로 어울리지 않는 코드를 [덕트 테이프](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%8D%95%ED%8A%B8-%ED%85%8C%EC%9D%B4%ED%94%84-%EC%BD%94%EB%8D%94duct-tape-coder)로 덕지덕지 붙인 코드를 말합니다.

서로 독립적으로 만든 모듈이더라도 함께 사용한다면 어울려 작동할 수 있도록 [Adapter](https://tango1202.github.io/pattern/pattern-adapter/) 등을 활용해야 합니다.

# [나쁜 코딩 관행] 바퀴의 재발명(Reinventing the Wheel)

코드내에 [빙산 클래스](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B9%99%EC%82%B0-%ED%81%B4%EB%9E%98%EC%8A%A4iceberg-class)가 있어 재활용할 요소를 찾지 못하거나, [얼어붙은 원시인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%96%BC%EC%96%B4%EB%B6%99%EC%9D%80-%EC%9B%90%EC%8B%9C%EC%9D%B8frozen-caveman)이어서 오픈 소스나 상용 제품을 도입하지 않고, 모두 다시 재개발하는 관행입니다. 정작 필요한 핵심 기능에 집중하지 못하고 리소스가 분산되며 코드는 중복됩니다.

먼저 자사의 코드와 오픈 소스와 상용 제품에 활용할 것이 있는지 충분히 분석하고, 학습하는게 좋습니다.

# [나쁜 코딩 관행] 여기서 발명되지 않음(Not Invented Here)

자사가 개발하지 않은 것은 신뢰하지 않는 관행입니다. [바퀴를 재발명](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B0%94%ED%80%B4%EC%9D%98-%EC%9E%AC%EB%B0%9C%EB%AA%85reinventing-the-wheel)하느라 핵심 기능에 집중하지 못합니다. 심지어 [STL](https://tango1202.github.io/categories/mordern-cpp-stl/)도 안쓰기도 하지요.

[얼어붙은 원시인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%96%BC%EC%96%B4%EB%B6%99%EC%9D%80-%EC%9B%90%EC%8B%9C%EC%9D%B8frozen-caveman)이 되지 마세요.

# [나쁜 코딩 관행] 낙관적 예측

낙관적으로 일정을 축소하지 마세요. 초기의 요구사항은 실제 구축해야 하는 것의 일부일 뿐입니다. 구현하다 보면 추가로 구현해야 할 것들이 눈덩이처럼 늘어납니다. 특히 [마지막 10% 함정](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EB%A7%88%EC%A7%80%EB%A7%89-10-%ED%95%A8%EC%A0%95last-10-trap)은 더 많은 기간을 필요로 합니다.

[낙관적인 예측](??)은 [배송](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)을 지연시키지만, 그렇다고 예측을 위해 너무 많은 고민을 하다보면 [분석 마비](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%B6%84%EC%84%9D-%EB%A7%88%EB%B9%84analysis-paralysis)에 빠질 수 있습니다.

적절한 타협이 필요합니다.

# [나쁜 설계 관행] 분석 마비(Analysis Paralysis)

과잉 분석으로 구현에 대한 진전이 없고 계획에 너무 많은 시간을 허비하는 것을 말합니다. 잠재적인 위험이나 단점이나 대안에 집중하다 보니 정작 중요한 의사결정이 늦어지는 문제를 겪습니다. 진전없는 회의가 반복된다면, 분석 마비에 빠진 것입니다.

하루빨리 [행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)로 [당신이 어디로 가는지](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%B9%EC%8B%A0%EC%9D%B4-%EC%96%B4%EB%94%94%EB%A1%9C-%EA%B0%80%EB%8A%94%EC%A7%80-%EC%95%84%EB%8A%94%EA%B0%80know-where-you-are-going)를 파악한 뒤, 우선 순위를 선정하고, [아키텍처 민첩성 원칙](https://tango1202.github.io/principle/principle-architectural-agility/)에 따라 [불완전성을 허용](https://tango1202.github.io/principle/principle-tolerance-for-imperfection/)하여 빠르게 구조를 잡은 후, 잦은 피드백과 잦은 실패를 경험하고 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하십시요.

# [나쁜 설계 관행] 앞단의 큰 디자인(Big Design Up Front)

[앞단의 큰 디자인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%9E%EB%8B%A8%EC%9D%98-%ED%81%B0-%EB%94%94%EC%9E%90%EC%9D%B8big-design-up-front)은 [분석 마비](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%B6%84%EC%84%9D-%EB%A7%88%EB%B9%84analysis-paralysis)와 마찬가지로 과잉 분석에 따라 너무 포괄적이고 상세한 설계를 하는 것을 말합니다. 너무 포괄적이고 상세한 아키텍처는 유연성이 떨어지고, 개발 시간이 증가하며, 사용되지 않는 모듈이나 아키텍처는 종종 [쓰레기 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%93%B0%EB%A0%88%EA%B8%B0-%EC%BD%94%EB%93%9Cgarbage-code)가 될 수 있습니다.

[단순한 디자인](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%A8%EC%88%9C%ED%95%9C-%EB%94%94%EC%9E%90%EC%9D%B8simple-design) 후 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하세요.

# [나쁜 설계 관행] 빠른 속도(Fast Beats Right)

[앞단의 큰 디자인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%9E%EB%8B%A8%EC%9D%98-%ED%81%B0-%EB%94%94%EC%9E%90%EC%9D%B8big-design-up-front)은 과잉 분석에 따른 문제가 발생합니다. 그렇지만 너무 분석을 안해도 문제입니다. 불충분한 설계로 [배송](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)일정을 맞추기 위해 [덕트 테이프](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%8D%95%ED%8A%B8-%ED%85%8C%EC%9D%B4%ED%94%84-%EC%BD%94%EB%8D%94duct-tape-coder)로 덕지덕지 붙은 코드를 작성하면, 결국은 품질이 저하되고, 고객의 신뢰를 잃게 됩니다.

[단순한 디자인](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EB%8B%A8%EC%88%9C%ED%95%9C-%EB%94%94%EC%9E%90%EC%9D%B8simple-design)후, [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하세요.

# [나쁜 설계 관행] 암시적인 아키텍처(Architecture by Implication)

아키텍처가 모호하면, 조직원들이 아키텍처를 이해하지 못할 수 있습니다. 이런 경우 아키텍처가 무시되거나, 제각각 멋대로 구현되어 일관성을 잃을 수 있습니다. 조직원들끼리 의사소통도 잘 안됩니다. 또한, 시스템간 원할한 통신이 어려워지고, 결합도는 높아지고 응집도는 낮아집니다. 결국 시스템 이해도가 떨어져 유지보수가 어려워 지죠.

그러니, 아키텍처를 잘 설명하는 문서를 만들고, 조직원들이 이해할때까지 교육하고, 공유해야 합니다. 그리고, 모든 조직원이 공감할 수 있는 [공통 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision)을 수립하세요.


# [나쁜 설계 관행] 마녀가 만든 아키텍처(Witches Brew Architecture)

[마녀가 만든 아키텍처](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EB%A7%88%EB%85%80%EA%B0%80-%EB%A7%8C%EB%93%A0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98witches-brew-architecture)는 기술, 디자인 패턴, 아키텍처, [코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)이 여러개가 혼재된 것을 말합니다. 일관성이 없어 가독성은 떨어지고 확장성도 떨어집니다. 주로 [암시적인 아키텍쳐](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%94%EC%8B%9C%EC%A0%81%EC%9D%B8-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98architecture-by-implication)와 [프랑켄코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%ED%94%84%EB%9E%91%EC%BC%84%EC%BD%94%EB%93%9Cfrankencode) 때문에 발생할 수 있습니다. 

[코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)을 수립하고, [공통 아키텍처 비전](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EA%B3%B5%ED%86%B5-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EB%B9%84%EC%A0%84common-architectural-vision)을 수립하세요.

# [나쁜 운영 관행] 폴더 버전 관리 복사(Copy Folder Versioning)

소스 코드를 관리할때 폴더 단위로 버전 관리를 하면, 어떤 것이 가장 최근인지 명확하지 않고, 코드 롤백이나 변경 추적이 어려워 집니다. 또한 함수명등으로 검색할때 버전에 따라 검색 결과가 많이 나오기 때문에 좋지 않습니다.

[Git](https://tango1202.github.io/dev-setting/dev-git/)과 같은 버전 관리 툴을 이용하세요.

# [나쁜 운영 관행] 계획에 의한 죽음(Death by Planning)

너무 지나친 회의와 계획 수립은 리소스를 낭비합니다. 회의에 드는 비용을 고려하세요. 

구체적인 안건을 정하고, 회의에 참석하는 인원을 최소화하세요.

# [나쁜 운영 관행] 죽음의 행진(Death March)

모든 사람이 성공 가능성이 희박하다는 걸 알고 있고, [배송](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)이 지연된다면, 그럼에도 불구하고, [배송](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature) 일정을 맞추기 위해 개발팀의 헌신이 필요한 상황이라면, 이는 [죽음의 행진](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%A3%BD%EC%9D%8C%EC%9D%98-%ED%96%89%EC%A7%84death-march)을 하고 있는 것입니다. 개발팀은 언젠가는 지치게 되어 있고, 열정과 의욕은 사라질 수 밖에 없습니다. 이런 프로젝트는 반드시 망합니다.

[행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)로 시나리오를 다시 만드시고, [계획을 업데이트](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%9D%BC%EC%A0%95-%EA%B3%84%ED%9A%8D-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8update-the-plan)하십시요. 이때 절대 다시 [낙관적으로 예측](??)하지 마십시요. 프로젝트를 포기하거나, 어벤져스를 투입하는 것도 좋은 방법입니다.

# [나쁜 운영 관행] 지뢰밭을 걷다(Walking through a Minefield)

[배송은 기능](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)이고, [배송 일정 준수](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)는 잦은 피드백과 잦은 실패로 제품 품질을 향상시킵니다. 하지만 지난번에는 동작했는데, 이번에 동작이 안된다면 어떻까요? 이럴땐 제품의 품질 향상을 위한 피드백이 오지 않습니다. 오히려 불신만 쌓이게 되죠. [배송 일정 준수](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)는 중요하지만, 엉망진창인 지뢰밭인 제품을 전달해선 안됩니다. 고객은 버그 테스팅을 하는게 아니라 시나리오를 경험한 후 피드백을 주는 역할을 해야 합니다.

[지속적인 통합](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EC%A7%80%EC%86%8D%EC%A0%81%EC%9D%B8-%ED%86%B5%ED%95%A9continuous-integration)
에서 말씀드린 것처럼 [테스트를 자동화](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94test-automation) 하시고, 통과된 경우에만 [배송](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)하세요.


# [나쁜 운영 관행] 살금살금 기어오는 기능(Feature Creep)

잦은 피드백과 요구사항은 제품의 품질을 높여주기도 하지만, [배송](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)을 지연시키고, 설계를 복잡하게 만드는 경향이 있습니다.

[배송은 기능](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EB%B0%B0%ED%8F%AC-%EB%B0%B0%EC%86%A1%EC%9D%80-%EA%B8%B0%EB%8A%A5%EC%9E%85%EB%8B%88%EB%8B%A4shipping-is-a-feature)이니 일정을 해치는 요구사항이 있다면, 다음 배포 일정으로 미뤄야 하고, [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 위해 우선 순위를 선정하세요.

# [나쁜 운영 관행] 얼어붙은 원시인(Frozen Caveman)

맹목적으로 가져다 쓰는 [캘린더 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BA%98%EB%A6%B0%EB%8D%94-%EC%BD%94%EB%8D%94calendar-coder)도 좋지 않지만, 새로운 기술이나 방법론을 채택할 의지가 없는 [얼어붙은 원시인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EC%96%BC%EC%96%B4%EB%B6%99%EC%9D%80-%EC%9B%90%EC%8B%9C%EC%9D%B8frozen-caveman)도 문제입니다. 변화에 대한 이런 거부감은 비효율적인 프로세스로 발전할 수 있으며, 업계의 기대치에 뒤쳐질 수 있습니다.

그렇다고 모든 신기술이 [황금 망치](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%ED%99%A9%EA%B8%88-%EB%A7%9D%EC%B9%98golden-hammer)나 [빛나는 장난감](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EB%B9%9B%EB%82%98%EB%8A%94-%EC%9E%A5%EB%82%9C%EA%B0%90shiny-toy)은 아닙니다. 의심은 하되 거부하진 마십시요. 교육을 장려하고, 학습을 위한 테스트 프로젝트를 장려하는게 좋습니다. 그리고 점진적으로 신기술을 도입해 보세요. 서두르십시요. 늦어지면 나중에 할게 더욱더 많아집니다. 

# [나쁜 운영 관행] 황금 망치(Golden Hammer)

어떤 문제를 한방에 해결하는 [황금 망치](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%ED%99%A9%EA%B8%88-%EB%A7%9D%EC%B9%98golden-hammer)는 없습니다. 그런 툴은 언제나 트레이드 오프를 필요로 합니다. 좋은 기능을 제공하는 대신 속도가 느리던지, A는 되고 B는 안되던지 하죠.

맹신하지 마시고, [황금 망치](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%ED%99%A9%EA%B8%88-%EB%A7%9D%EC%B9%98golden-hammer)를 도입했을때 무얼 포기해야 할지 판단하세요.

# [나쁜 운영 관행] 빛나는 장난감(Shiny Toy)

최첨단 도구나 기술들이 모든 문제를 해결할 수 있다고 생각하는 관행입니다. [황금 망치](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%ED%99%A9%EA%B8%88-%EB%A7%9D%EC%B9%98golden-hammer)처럼요.

도입하고 싶은 도구나 기술들이 있다면, 허용 범위에 맞춰 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하십시요. 

# [나쁜 운영 관행] 마지막 10% 함정(Last 10% Trap)

프로젝트의 남은 마지막 10%는 의외로 많은 시간이 걸리고 난해하여 해결하기 어렵습니다. 

* 어렵고 힘든것은 뒤로 미뤄두기 마련이니까요. 
* 그리고 적당히 동작하는건 쉽게 구현되지만, 미려하게 동작하는건 언제나 쉽지 않습니다. 
* 또한 90%가 완성된 단계에서 10%를 추가하기 위해 이미 개발된 90%를 리팩토링해야 할 수도 있습니다.

따라서 때로는 프로젝트의 남은 10%가 전체 일정의 90%를 차지할 수도 있습니다.

남은 작업을 과소평가하지 마시고, 최대한 비관적으로 [계획을 업데이트](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%9D%BC%EC%A0%95-%EA%B3%84%ED%9A%8D-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8update-the-plan)하세요.


# [나쁜 운영 관행] 로이스 레인 계획(Lois Lane Planning)

슈퍼맨의 여자 친구인 로이스 레인은 일을 저지르거나 혹은 악당들에게 잡혔다가 슈퍼맨의 도움으로 구출됩니다. 개발팀을 슈퍼맨이라고 생각하는 영업 조직은 고객에게 어마어마한 약속을 하고 개발팀에 도움을 요청합니다. 아주 운이 좋게 개발팀이 구출할 수도 있지만, 개발팀은 슈퍼맨이 아니기에 지치고, 떠나고, 품질이 낮아지고, 다른 프로젝트 마저 삐걱거리게 됩니다.

[행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)로 서로 긴밀하게 협력하고, 지킬 수 있는 일정을 협의하세요.

# [나쁜 운영 관행] 연기와 거울(Smoke and Mirrors)

고객에게 그럴싸하게 연기와 거울을 보여주고 [로이스 레인 계획](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%9A%B4%EC%98%81-%EA%B4%80%ED%96%89-%EB%A1%9C%EC%9D%B4%EC%8A%A4-%EB%A0%88%EC%9D%B8-%EA%B3%84%ED%9A%8Dlois-lane-planning)처럼 개발팀이 해결해 주리라 기대하는 것이며, 마케팅 주도 개발이라고도 합니다. 비현실적인 기간내에 개발해야 할 때는 재앙으로 이어집니다.

[행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)로 서로 긴밀하게 협력하고, 지킬 수 있는 일정을 협의하세요.

# [나쁜 운영 관행] 버섯 관리(Mushroom Management)

개발팀을 어둠속에 가두고 버섯을 키우듯이 관리하지 마십시요. 고객과의 장벽을 설치하면, [가정 기반 프로그래밍](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EA%B0%80%EC%A0%95-%EA%B8%B0%EB%B0%98-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dassumption-driven-programming)을 하고, 요구사항을 잘못 이해합니다. 특히 잦은 피드백과 잦은 실패를 할 수 없어 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 할 수 없습니다.

[행동 중심 개발](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%ED%96%89%EB%8F%99-%EC%A4%91%EC%8B%AC-%EA%B0%9C%EB%B0%9Cbehavior-driven-development)로 시나리오를 작성하시고, [전체 팀](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%ED%8C%80-%EC%A0%84%EC%B2%B4-%ED%8C%80whole-team)을 구성하여 장벽을 없애세요.

# [나쁜 운영 관행] 폭포수 모델(Waterfall)

전통적으로 폭포수 모델의 개발 방법론(*시스템 요구사항 수집->소프트웨어 요구사항 수집->분석->설계->코딩->테스팅->배포 및 운영의 순서로 개발*)을 사용했는데요, 변화하는 환경이나 요구사항 반영에 민첩해지기 힘듭니다.

잦은 피드백과 잦은 실패를 경험하고 [점진적 발전과 지속적 개선](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A0%90%EC%A7%84%EC%A0%81-%EB%B0%9C%EC%A0%84%EA%B3%BC-%EC%A7%80%EC%86%8D%EC%A0%81-%EA%B0%9C%EC%84%A0incremental-progress-continuous-improvement)을 하십시요. 

