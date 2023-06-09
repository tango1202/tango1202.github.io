---
layout: single
title: "#10. [개체지향 원칙] 헐리우드 원칙(Hollywood Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

헐리우드 원칙은 헐리우드 영화 제작사가 오디션 배우에게 **전화하지 마세요. 연락처 주시면 우리가 전화드릴께요.** 라고 안내한 말에서 유래된 원칙입니다.

조금 풀어 쓰면,

1. 배우(호출자) 가 연락처(Listener)를 남기면, 영화사(프레임워크) 가 연락을 주듯이,
2. 호출자(상위 수준 모듈)와 프레임워크(하위 수준 모듈)간에 느슨한 연결을 구현하라.

라는 뜻입니다.

데이터를 저장하는 기능을 가진 프레임워크를 개발하고 있다고 가정합시다. 하기와 같은 Save() 기능이 필요합니다.

```cpp
class App {
public:
    void Save() const {
       // 파일명이 없으면 다른 이름으로 저장 실행
       if (GetPathName().empty() {
           SaveAs();
       }
       // 저장할 필요가 있는지 확인
       if (!IsDirty()) { return; }
       
       // 문서 내용 저장
       SaveDoc();
       
       // 저장되었다고 플래그 설정
       SetDirty(false);
    }
};
```

템플릿 메서드 패턴을 이용하여 하위 수준 모듈에서 상위 수준 모듈에 접근함.
부모 클래스에서 자식 클래스 함수 호출.

Listener 를 이용하여 하위 수준 모듈에서 상위 수준 모듈에 접근함.


