---
layout: single
title: "#2. [개체지향 원칙] 단일 책임 원칙(Single Responsibility Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/)은 ***개체는 단 하나의 책임만 가져야 한다*** 는 원칙입니다.

조금 풀어 쓰면,

1. 하나의 기능만 담당하고, 
2. 개체간의 종속성/연관성은 최소화 하고,
3. 단위 기능의 응집력은 높이고,
4. 해당 기능에 대한 예외 상황에 책임을 지고 대처하라.

라는 뜻입니다.

한 개체에 책임이 많아지면 많은 기능을 포함해야 하고, 자연스레 기능끼리의 결합도가 높아지며, 코드 수정시에 **사이드 이펙트** 가 발생할 소지가 높습니다. 그리고 무엇보다 [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)이 떨어져 분석이 힘들어지죠.

그렇다고, 그냥 기능 단위로 개체를 볶음밥 야채처럼 잘게 쪼개면 안됩니다.

잘못 쪼개지면, 오히려 여러 개체에서 기능과 책임을 분산해서 처리하기 때문에, 코드 수정의 범위가 넓어지고, 디버깅과 문제 파악이 어려워져 수정시 **사이드 이펙트** 가 발생할 확률이 높아집니다.

문장 자체는 참 이해하기 쉽고 공감가는 원칙이지만, 준수하기에는 참 어려운 원칙중 하나입니다.

**잘 만들어라...** 라는 느낌과 좀 비슷하다 랄까요?

이 원칙을 준수하면,

1. 단위 기능별로 코드가 나눠져 있어 [코드 가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)이 향상됩니다.
2. 단위 기능별로 응집되어 있어 코드 분석 용이성이 향상됩니다.
3. 단위 기능별로 [예외 처리](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/)가 응집되어 [예외 안전성](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)이 향상됩니다.
4. 단위 기능에 집중하므로 유지보수시 복잡성이 낮아집니다.
5. 단위 기능으로 간소해져 구현 편의성이 향상됩니다.

**준수 방법 : 클래스 쪼개기**

1. 클래스 주제에 맞게 쪼개라.
    
    데이터를 저장하는 클래스인지, 데이터를 처리하는 클래스인지, 데이터를 표시하는 클래스 인지 주제를 나누고, 혹은 이들을 모두 처리하는 컴포넌트나 모듈로 주제를 나누고 쪼갤 수 있겠습니다. 데이터도 고객 데이터인지, 직원데이터인지 구분해서 쪼갤 수 있겠고요. 한 단위의 주제로 응집될 수 있다면 쪼갭니다.

2. 클래스 설명이 1개 문장으로 표현될 수 있도록 쪼개라.
    
    설명이 길여지면 기능 단위로 나눕니다.

3. View와 Model을 쪼개고, 그 둘을 이어주는 개체를 만들어라.(*MVC, MVP, MVVM 등*)
    
    데이터에 따라 다양한 View(*화면, 프린터, 미리보기, 차트로 보기, 표로 보기, json 보기*)가 있을 수 있고, 향후에 새로운 View 형태가 추가 될 수도 있습니다. 그리고 대부분의 View는 시스템 종속적이고, 수정사항이 많습니다. 그래서 View는 데이터와 쪼개고 [Observer](https://tango1202.github.io/pattern/pattern-observer/)로 느슨하게 연결합니다.

4. 파일 입출력을 합치지 말고, Load, Save로 쪼개라. 

    불러오기용 파일 필터와 저장하기용 파일 필터가 서로 다를 수 있기 때문에 쪼개는게 좋습니다.

5. 시스템 리소스에 접근하는 개체는 분리하라.
    
    시스템 리소스 접근 방식이나 연결 방식이 변경될 수 있기 때문에 쪼개는게 좋습니다.(*[Adapter](https://tango1202.github.io/pattern/pattern-adapter/), [Proxy](https://tango1202.github.io/pattern/pattern-proxy/) 참고*)

6. 출력 방법은 분리하라.
    
    프레임워크나 장치별로 렌더링 엔진은 변경될 수 있습니다. [Bridge](https://tango1202.github.io/pattern/pattern-bridge/)로 쪼개두는게 좋습니다.

7. 입력 방법은 분리하라.
    
    프레임워크나 장치별로 입력 방법이 달라질 수 있습니다. 쪼개는게 좋습니다.

8. UI 프레임워크는 쪼개라.

    UI 프레임워크는 종종 유행에 따라 변경됩니다. 엔진 모듈에 영향이 없도록 쪼개두는게 좋습니다.

**준수 방법 : 3분할 원칙**

주어진 개체를 3개로 쪼개는 것으로 고민합니다.
3개를 무얼로 쪼갤지는 생각하기 나름입니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/038760bc-dab7-4874-b580-546e87714129)

`확대/축소 대화상자`를 개발한다고 가정합시다. `Model`에는 비율값이 저장되겠고요, 화면은 다음과 같다고 합시다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8d511cbc-d698-444e-8794-12edb88aaba8)

일반적으로 대화상자 코드에서 `Model`의 비율값을 보고 `100`이면 2번 라디오 버튼을 선택하고, `130`이면 선택할 라디오 버튼이 없으니 5번 라디오 버튼을 선택하고, 에디트박스에 130을 출력하면 됩니다.

`View`와 `Model`로 나누어 졌다면, 대략 다음과 같이 역할을 분리됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/af257ec7-ef94-4377-b09d-26050b62901a)

상기의 로직은 대화상자에서 `Model`값을 해석하는 역할과 라디오 버튼을 선택하는 역할의 2가지 책임을 담당하고 있습니다.(*[단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/) 위반입니다.*) 

이런 스타일은 UI가 복잡해질수록 코드도 상당히 복잡해 지므로, 이를 View-ViewModel-Model(**MVVM**)을 이용하여 [단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/)을 준수하도록 수정하는게 좋습니다.

**MVVM**은 `View`와 `Model`을 중재하는 중간 데이터인 `ViewModel`을 두어 분리하는 패턴입니다.
다음과 같이 대화상자와 `Model` 사이에 있으면서, 어느 라디오 버튼을 선택 표시할지 인덱스를 계산해 주는 역할을 합니다.(*[Mediator](https://tango1202.github.io/pattern/pattern-mediator/)와는 미묘하게 다릅니다. [Mediator](https://tango1202.github.io/pattern/pattern-mediator/)는 중재를 위해 직접 어떠한 행위를 하는 것이고, `ViewModel`은 어떠한 행위를 하도록 데이터를 만들어 주는 것입니다.*)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/54a4e196-32c8-4640-8adb-1136eb4c6249)

아마도 `ViewModel`은 다음과 같이 구현될 수 있습니다. 

```cpp
std::pair<int, int> CalcZoomDlgData(int percent) {
    int index = 5; // 기본적으로 사용자 정의 선택
    switch(percent) {
        case 50: index = 0; break;
        case 75: index = 1; break;
        case 100: index = 2; break;
        case 150: index = 3; break;
        case 200: index = 4; break;
    }
    return std::make_pair(index, percent);
}
```

상기와 같이 하면, 

1. `View` 는 화면 출력에 집중하고, 
2. `Model`은 데이터 관리에 집중하며,
3. `ViewModel`은 둘간의 데이터 연결에 집중하게 됩니다. 

향후 `Model`에서 `percent`의 타입이 `int`에서 `float`으로 변경되더라도, `ViewModel`만 수정하면 되므로 유지보수가 용이합니다. 

**준수 방법 : 쪼개는 기준는 데이터 중심일 수도, 행위 중심일 수도 있다**

비교적 기본 타입처럼 작은 개체인 경우에는 데이터와 이를 처리하는 기능을 응집하여 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하고, 큰 개체인 경우에는 데이터와 이를 처리하는 기능을 분리합니다.

|데이터와 행위 응집|데이터와 행위 분리|
|--|--|
|* 데이터 처리가 개체에 집중되어 있어 사용하기 편리합니다.<br/>* 처리 함수가 추가될수록 개체가 복잡해 집니다.<br/>* 유지보수를 거치다 보면 개체가 엄청나게 커져 [블롭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B8%94%EB%A1%ADthe-blob)이 될 수 있습니다.|* 행위 수행을 위해 데이터가 노출되어야 하므로 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)가 어려워질 수 있습니다.<br/>* 데이터 처리가 분리되어 있어 확장이 편합니다.<br/>* 클래스의 수가 많아져 혼란스러울 수 있습니다.<br/>* 클래스가 작고 간결합니다.|

예를 들어 개체 저장 기능을 살펴봅시다.

데이터와 행위를 응집하면 다음처럼 멤버 함수가 늘어나 개체가 점점 커지게 됩니다.

```cpp
class Object {
public:
    void SaveMem();
    void SaveBinFile();
    void SaveXml();
};

Object obj;
obj.SaveMem();
obj.SaveBinFile();
obj.SaveXml();
```

하지만 데이터와 행위를 분리하면, 데이터 개체는 커지지 않지만, 행위를 처리하는 개체가 늘어나 혼란스러울 수 있습니다.

```cpp
class Saver {};
class MemSaver : public Saver {};
class BinFileSaver : public Saver {};
class XmlSaver : public Saver {};

class Object {
public:
    void Save(Saver& saver) {}
};
Object obj;
MemSaver memSaver;
BinFileSaver binFileSaver;
XmlSaver xmlSaver;

obj.Save(memSaver);
obj.Save(binFileSaver);
obj.Save(xmlSaver);
```

**준수 방법 : 잘못 쪼갰다면 산탄총 수술로 합쳐라**

만약 코드를 수정할때 여러 개체들을 수정해야 하거나 코드 중복이 발견됐다면, 너무 세분화해서 쪼갰다는 뜻입니다. 응집력이 낮다는 신호죠. 리팩토링의 냄새가 나죠? 이럴때 **산탄총 수술**(*흩어져 있는걸 합치는 리팩토링 기술입니다.*) 이 필요합니다.

하기는 개체의 로그를 저장하는 개체들입니다. 로그 저장 로직을 [단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/)에 따라 각자 구현했지만, 매번 세트로 함께 수정된다면, 이를 합치는게 유리합니다. 이제 단일 책임은 `RectangleLogWriter`, `EllipseLogWriter`, `CircleLogWriter`로 세분화 되는게 아니라, `LogWriter`로 퉁쳐진다고 보시면 되겠습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/66e12747-3705-4074-9032-1f7ef8d60f46)



