---
layout: single
title: "#1. [레거시 C++ 가이드] 예외 안전 코드"
categories: "legacy-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [예외 안전(safe) 코드](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)는 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않는 코드가 아니라, [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)해도 안전하게 [복원](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)되고 계속 동작해도 무방하게 [예외 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/)하는 코드다.
> * 예외 상황을 사전에 예측하고 프로그래밍 하라.
> * 모든 함수는 실패할 수 있다고 가정하고 프로그래밍 해라.
> * [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했을때 메모리 릭이나 리소스 릭이 없게 하라.(*[기본 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)*)
> * [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했을때 [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 전의 상태로 [복원](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)하라.(*[강한 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)*)
> * 예외를 처리 할 수 없다면 상위 개체에 보고하라.

# 개요

[예외 안전(safe) 코드](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)는 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않는 코드가 아니라(*이건 사실 불가능합니다. 메모리가 부족할 수도 있고, 갑자기 랜선이 차단될 수도 있고*), [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)해도 안전하게 [복원](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)되고, 계속 동작해도 무방하게 만드는 코드입니다.

[예외에 안전](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)하려면 다음을 준수하여야 합니다.

1. 예외 상황을 사전에 예측하고 프로그래밍 해야 합니다. 모든 함수는 실패할 수 있다고 가정하세요.(*[자가 진단과 진단 최소화](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/) 참고*)
2. [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했을때 메모리 릭이나 리소스 릭은 없어야 합니다.(*[기본 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98) 참고*)
3. [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했을때 [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 전의 상태로 [복원](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)해야 합니다.(*[강한 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98) 참고*)

# 일반적인 예외 상황에서 대처 방안

**메모리 할당 실패**

일반적으로는 특별히 할 수 있는게 없습니만, 안전하게 프로그램을 종료 시킬 수 있도록 몇가지 추가 작업을 할 수 있습니다.

1. [new_handler](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)를 이용하여 미리 할당한 예약 영역을 해제하고, 프로그램의 정상 종료를 위해 뭔가 시도해 볼 수 있습니다.(*[NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/) 참고*)
2. 100,000,000 Gbyte 와 같이 엄청난 크기를 할당 요청을 하는 바람에 실패했다면, 메시지로 메모리 부족을 알리고 요청전의 상태로 [복원](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)할 수 있습니다.
   
**컨테이너에서 잘못 참조된 인덱스**

[예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하기 전에 사전 진단을 하여 보정하는 편이 좋습니다. 다만, 여러개의 개체가 중첩되어 호출되는 경우 이를 레이어링 하여 비교적 상위 레이어의 개체에서 진단과 보정을 하는게 좋습니다.(*[예외 레이어링](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%A0%88%EC%9D%B4%EC%96%B4%EB%A7%81) 참고*)

1. 비교적 상위 개체에서 사전에 잘못된 인덱스 인지 검사하고, 유효한 인덱스가 되도록 보정합니다.
2. 비교적 상위 개체에서 메시지 박스를 표시하거나, 아무짓도 안하거나, 마지막 요소를 사용하거나, 정의된 기본 동작을 수행합니다.
   
**잘못 전달된 인수**

주로 프로그래머의 실수입니다.

1. 정상 동작할 수 있는 값인지 사전에 검사한뒤 함수를 호출합니다.
2. 비정상적인 값인지 함수 내에서 진단합니다. 또한, 널검사가 필요한 포인터보다는 널검사가 필요없는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용하여, 널검사 진단 코드는 최소화 합니다.(*[자가 진단과 진단 최소화](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/) 참고*)
3. 스스로 보정할 수 있는 값이면 수정해서 사용합니다.
4. 잘못 사용하기 어렵도록 코딩 계약을 단단하게 맺습니다.
5. 비교적 상위 개체에서 메시지 박스를 표시하거나, 아무짓도 안하거나, 다른 인수값으로 재시도 합니다.

**오버 플로우/연산 오류**

주로 프로그래머의 실수입니다.

1. 비교적 상위 개체에서 잘못된 인수는 아닌지 사전에 검사하고, 유효한 값이 되도록 보정합니다.
2. 연산전에 안전한 값인지 검사합니다.
3. 재귀 호출일 경우 재귀 중단 조건에 예외 상황은 없는지 검사합니다.
4. 0으로 나누기가 되는지 검사합니다.
5. 비교적 상위 개체에서 메시지 박스를 표시하거나, 다른 인수값으로 재시도 합니다.

**인프라 오류**

파일 IO나 네트워크등 외부 인프라가 가정한 상황과 달라 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수 있습니다. 언제든 파일이 갑자기 삭제될 수 있고, 디스크의 손상된 섹터 때문에 읽지 못할 수 있고, 네트워크는 순간적으로 단절될 수 있습니다.

1. 현재의 오류 상태를 메시지 박스로 표시하고, 작업을 중단하거나 나중에 재시도 할 수 있게 합니다. 
2. 중단된 작업은 사용자 선택에 의해 이어서 진행(*파일 다운로드 이어받기 등*)할 수 있게 합니다.

# 예외 상황의 잘못된 대처

악마의 유혹으로 예외 상황에서 예외를 제대로 보고하지 않으면, 디버깅을 하느라 코딩 인생을 낭비하게 됩니다.
처리할 수 없다면, [throw](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키고 [전파](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)해서 좀 더 상위 개체에서 [try-catch()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 처리하게끔 미뤄두세요. 

|흔한 예외 상황 행동|바람직한 대처|
|--|--|
|프로그램을 종료합니다.|무책임합니다. 종료하더라도 상위 개체에서 메시지를 표시하고, 사용자 선택에 의해 종료하게 만들어야 합니다.|
|아예 신경쓰지 않습니다.|프로그램이 죽던지, 나중에 프로그래머가 디버깅하다가 스트레스로 죽던지 하니, 신경 쓰셔야 합니다.|
|오류 코드를 리턴합니다.|리턴된 오류는 대부분 무시될 수 있습니다. 엔진 안쪽의 하위 개체들이라면 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키고, 이를 처리할 수 있는 곳까지 [전파](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)하여, 상위 개체에서 처리할 수 있도록 해줘야 합니다.(*[예외 레이어링](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%A0%88%EC%9D%B4%EC%96%B4%EB%A7%81) 참고*)|
|전역 오류 코드를 세팅합니다.|호출한 곳에서 오류를 확인할 확률은 0.00001% 입니다. 함수 호출 후 `GetLastError()`를 다시 검사하는 경우는 거의 없잖아요?<br/>[예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키는게 더 낫습니다.|
|자가 진단 후 함수를 정상 종료 시킵니다.|`if (condition) return;` 와 같이 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 검사하고 그냥 종료 시키면, 잘못된 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 대한 처리가 수행되지 않습니다. 시스템이 정말 계속 안전하게 돌아갈지 의문이고, 이게 나중에 알 수 없는 버그의 원인이 되기도 합니다.<br/>[예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키는게 더 낫습니다.|
|[예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 경우 호출할 함수를 호출합니다.|[new_handler](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)가 이런 구조인데요, 사실 이런 구조는 사용법이 좀 어렵습니다.|


# 예외 상황의 올바른 대처

개발자는 현 함수에서 예측할 수 있는 모든 상황을 고려하여 코드를 작성해야 합니다. 예측할 수 없는 예외 상황이 발생한다면 대처할 수 없고, 프로그램은 오동작하다가 결국 멈춰버립니다. 

그러니, 예측할 수 없는 예외 상황이 없도록 레벌업을 하시기 바랍니다. 그리고 예외가 발생하지 않도록 사전에 철저히 점검하십시요.(*[공격적 자가진단](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8) 참고*)

이때, [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)의 원인이 명백한 코딩 실수라면, ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 하여 코딩 계약을 단단하게 하시고, 최후의 수단으로 [assert()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-diagonostics/)로 잘못된 사용을 안내하시기 바랍니다.

그럼에도 불구하고 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한다면, 함수 내부에서 예외를 처리할 수 있는지 없는지를 판단하여 예외에 대처하거나, [throw](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키고 상위 개체에 [예외를 전파](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)합니다. 그리고 예외를 처리할 수 있는 상위 개체에서 [try-catch()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 [예외를 탐지](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하고요. 

[탐지](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한 예외는 다음과 같이 처리할 수 있습니다.

* 작업을 중단하고 메시지 박스 표시(*현재 상태가 이러저러해서 못하니, 이렇게 해서 재시도 해보세요 등 원인과 해결방법 안내*)
* 자원을 정돈하고 재시도(*메모리 부족등*)
* 추후 재시도 할 수 있도록 상태 정리(*파일 다운로드등*)
* 인수를 보정하고 재시도(*잘못된 입력값. 시스템에서 보정하거나 사용자가 보정*)
* 정의된 기본 동작으로 재시도(*치명적이지 않은 경우 기본 동작으로 자동 실행*)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/fc331259-e119-499d-8b84-8a36e32b471f)

