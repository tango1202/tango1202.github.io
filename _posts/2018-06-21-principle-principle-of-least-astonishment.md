---
layout: single
title: "#21. [개발 원칙] 최소한의 놀라움 원칙(Principle of Least Astonishment)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[최소한의 놀라움 원칙](https://tango1202.github.io/principle/principle-principle-of-least-astonishment/)은 ***사용자나 동료가 예측 가능하게 하라*** 라는 원칙입니다.

일반적으로, UI나 API 설계시에 강조됩니다. 

사용자가 버튼을 클릭했을때 예측하는건, 무언가를 수행하고 예상된 결과 화면이 나타나는 것입니다. 만일 오류가 발생한다면, 오류 메시지가 나오고요.

그런데, 결과 화면이 나타나지 않는다면 어떤 행동을 할까요? 클릭이 안됐나 싶어 여러번 재시도 하지 않을까 싶네요.

몇번 경험들 해보셨을까요?

다음의 경우로 예상됩니다.

1. 성공적으로 수행했으나, 화면 변경이 없는 기능입니다.
2. 오류가 발생해서 실패했습니다.
3. 실행할 수 없는 상태이기에 비활성 버튼으로 표시해야 하나, 비활성 버튼인지 검사하면, 시스템 성능이 떨어집니다.

다음과 같이 해결하면, 사용자가 조금 덜 놀랍니다.(*[설명하는 오류 메시지](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%A0%9C%ED%92%88---%EC%83%81%ED%92%88%EC%84%B1-%EC%84%A4%EB%AA%85%ED%95%98%EB%8A%94-%EC%98%A4%EB%A5%98-%EB%A9%94%EC%8B%9C%EC%A7%80descriptive-error-messages)의 4H 원칙 참고*)

1. 성공 메시지를 표시합니다.
   * 저장하기는 화면 변경이 없을 수 있습니다. 그렇다고 성공했다고 메시지 박스를 표시하면 사용자가 번거로워 할 수 있습니다. 이런 경우, 기능 버튼을 비활성 시키거나, 알림형 메시지를 잠시 표시하거나, 진행바를 표시함으로서 화면 변경을 줄 수 있습니다.
2. 실패 메시지를 표시합니다.
   * 불친절하게 오류 코드만 표시하지 말고, 뭘해야 정상 실행이 되는지 안내해 주세요.
3. 왜 실행할 수 없는 상태인지 메시지를 표시합니다.

API 작성시에도 동료들이 놀라지 않게 하세요.

1. 잘못 사용하기에 어렵게, 바르게 사용하기에 쉽게 구현하세요.
2. 기능명과 동일한 기능을 수행해야 합니다. `Add()` 함수인데, `Remove()`를 수행하면 난감합니다(*이정도인 분은 없겠으나, 개체의 데이터를 구하는 `GetData()` 시에는 종종 난감한 경우가 있습니다.*). 상식적인 함수명과 함수 원형을 사용하세요.
    * `GetData()`는 데이터를 구하는 함수입니다. `data = object.GetData();` 로 사용하는게 상식적입니다. `object.GetData(&data)` 처럼 사용하도록 강제하지 마세요. 리턴시 데이터 복제가 없도록 하면 됩니다. 
    * `GetData()` 시 데이터 복제를 하지 마세요. 복제를 하는 것이면, `object.CloneData();`로 작성하세요. 
3. 오류가 발생하면, 예외를 발생시키던지, 오류코드를 리턴하세요.(*[예외 상황의 올바른 대처](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/#%EC%98%88%EC%99%B8-%EC%83%81%ED%99%A9%EC%9D%98-%EC%98%AC%EB%B0%94%EB%A5%B8-%EB%8C%80%EC%B2%98) 참고*)
4. 일관된 방식으로 코드를 구성하세요. 어떤 함수는 예외를 발생하고, 어떤 함수는 오류 코드를 리턴하고 그러면 헷갈립니다.
5. [리스코프 치환 원칙](https://tango1202.github.io/principle/principle-liskov-substitution/)에 따라 자식 개체가 엉뚱한 짓을 하면 안됩니다.
6. [명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 **[완전한 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90)** 와 **[완전한 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%ED%95%A8%EC%88%98)** 를 제공하세요.
7. [묻지말고 말하라 원칙](https://tango1202.github.io/principle/principle-tell-dont-ask/)에 따라 한번만 물으면 알아서 척척 동작하도록 제공해 주세요.


