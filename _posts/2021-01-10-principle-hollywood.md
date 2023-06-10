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

1. 배우(호출자)가 연락처(Listener)를 남기면, 영화사(프레임워크)가 연락을 주듯이,
2. 호출자(상위 수준 모듈)와 프레임워크(하위 수준 모듈)간에 느슨한 연결을 구현하라.

라는 뜻입니다.

**위반 사례**

데이터를 저장하는 기능을 가진 프레임워크를 개발하고 있다고 가정합시다. 여러 어플리케이션에서 상속 받아 쓸 수 있게요.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9991a2f6-004f-457a-8871-bac0f728427b)

하기와 같이 `App` 의 `Save()` 기능을 구현할 수 있습니다. 상황에 따라 `SaveAs()`함수로 분기하고, `dirty`플래그를 설정합니다.

```cpp
class App {
private:
    bool dirty;
    std::wstring pathName;
public:
    explicit App(bool dirty, const std::wstring& pathName) {
        this->dirty = dirty;
        this->pathName = pathName;
    } 
public:
    bool IsDirty() const { return dirty; }
    void SetDirty(bool val) { dirty = val; }
    const std::wstring& GetPathName() const { return pathName; }

    void Save() {

        // 파일명이 없다면 다른 이름으로 저장
        if (GetPathName().empty()) {
            SaveAs();
            return;;
        }

        // 저장할 필요가 있는지 확인
        if (!IsDirty()) { return; }

        // 문서 내용 저장
        SaveDoc();

        // 저장되었다고 플래그 설정
        SetDirty(false);
    }

    // 다른 이름으로 저장합니다.
    void SaveAs() {}

    // 문서 내용을 저장합니다.
    void SaveDoc() { /* 구현해야 합니다. */ }
};
```

여기서 `SaveDoc()` 은 어떻게 자식 개체인 `MyApp` 의 문서를 저장할 수 있을까요? 하기처럼 `MyApp`은 사실은 `App`을 상속받은 거니, 강제로 캐스팅 하면 될까요?

```cpp
// App 을 상속받아 만들었습니다.
class MyApp : 
    public App {
public:
    explicit MyApp(bool dirty, const std::wstring& pathName) :
        App(dirty, pathName) {
    } 
public:
    // MyApp의 문서 내용을 저장합니다.
    void SaveMyDoc() {
        std::cout <<"MyApp::SaveDoc()"<<std::endl;
    }
};

// 문서 내용을 저장합니다.
void App::SaveDoc() {
    // 강제로 MyApp으로 변환합니다.
    reinterpret_cast<MyApp*>(this)->SaveMyDoc();
}
``` 
강제로 캐스팅하는 `reinterpret_cast`도 견딜 수 없지만, 자식 개체에서 부모 개체를 참조해서 상호 참조하여 **의존성 부패** 가 되버렸습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b49e34dc-fc63-41b5-8937-e55a6036680a)

**의존성 부패** 도 문제지만, 이제 프레임워크는 `MyApp`만 사용할 수 있고, `YourApp`은 사용할 수 없습니다.

**준수 방법 : 템플릿 메서드 패턴**
하기 그림과 같이 [템플릿 메서드 패턴](https://tango1202.github.io/pattern_template_method) 을 사용한다면, 부모 클래스(`App`)에서 자식 클래스(`MyApp` 또는 `YourApp`)의 가상함수인 `SaveDoc()`을 호출하여 해결할 수 있습니다.

7ㄱ림

코드로 구현하면 하기와 같습니다.

```cpp
class App {
    // 문서 내용을 저장합니다.
    virtual void SaveDoc() = 0;
};

void SaveDoc()
:SaveDoc"<<endl:
    }
};
```

템플릿 메서드 패턴을 이용하여 하위 수준 모듈에서 상위 수준 모듈에 접근함.
부모 클래스에서 자식 클래스 함수 호출.

**준수 방법 : Listener**

Listener 를 이용하여 하위 수준 모듈에서 상위 수준 모듈에 접근함.


