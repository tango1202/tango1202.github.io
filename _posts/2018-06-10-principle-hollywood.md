---
layout: single
title: "#10. [개체지향 원칙] 헐리우드 원칙(Hollywood Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

헐리우드 원칙은 헐리우드 영화 제작사가 오디션 배우에게 **전화하지 마세요. 연락처 주시면 우리가 전화드릴께요.** 라고 안내한 말에서 유래된 원칙입니다.

조금 풀어 쓰면,

1. 배우(호출자)가 연락처(Listener)를 남기면, 영화사(프레임워크)가 연락을 주듯이,
2. 하위 수준 모듈(프레임워크)이 상위 수준 모듈(호출자)에게 연락할 방법(*Listener 인터페이스 혹은 [Template Method 패턴](https://tango1202.github.io/pattern/pattern-template-method/)*)을 만들어라.

라는 뜻입니다.

**필요 사례**

데이터를 저장하는 기능을 가진 범용적인 프레임워크를 개발하고 있다고 가정합시다. 여러 어플리케이션에서 상속 받아 쓸 수 있게요.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9991a2f6-004f-457a-8871-bac0f728427b)

`App` 의 `Save()` 기능을 구현한다고 가정합시다. 상황에 따라 `SaveAs()`함수로 분기하고, `m_Dirty`플래그를 설정하여 내용이 수정된 경우에만 저장하도록 구현합니다.

```cpp
class App {
private:
    bool m_Dirty; // 내용이 수정되면 true입니다.
    std::wstring m_PathName;
public:
    App(bool dirty, const std::wstring& pathName) :
        m_Dirty(dirty),
        m_PathName(pathName) {} 
public:
    bool IsDirty() const {return m_Dirty;}
    void SetDirty(bool val) {m_Dirty = val;}
    const std::wstring& GetPathName() const {return m_PathName;}

    void Save() {

        // 파일명이 없다면 다른 이름으로 저장
        if (GetPathName().empty()) {
            SaveAs();
            return;;
        }

        // 저장할 필요가 있는지 확인
        if (!IsDirty()) {return;}

        // 문서 내용 저장
        SaveDoc();

        // 저장되었다고 플래그 설정
        SetDirty(false);
    }

    // 다른 이름으로 저장합니다.
    void SaveAs() {}

private:
    // 문서 내용을 저장합니다.
    void SaveDoc() { /* 구현해야 합니다. */ }
};
```

여기서 `SaveDoc()` 은 어떻게 자식 개체인 `MyApp` 의 문서를 저장할 수 있을까요? `MyApp`은 사실은 `App`을 상속받은 거니, 강제로 형변환하면 될까요?

```cpp
// App 을 상속받아 만들었습니다.
class MyApp : public App {
public:
    MyApp(bool dirty, const std::wstring& pathName) :
        App(dirty, pathName) {} 
public:
    // MyApp의 문서 내용을 저장합니다.
    void SaveMyDoc() {
        std::cout << "MyApp::SaveMyDoc()" << std::endl;
    }
};

// 문서 내용을 저장합니다.
void App::SaveDoc() {
    // 강제로 MyApp으로 변환합니다.
    reinterpret_cast<MyApp*>(this)->SaveMyDoc();
}
``` 
강제로 형변환하는 [reinterpret_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)도 견딜 수 없지만, 자식 개체에서 부모 개체를 참조해서 상호 참조하여 **의존성 부패** 가 되버렸습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e19a4379-80ae-4a5b-a0c4-042b328b20cd)

**의존성 부패** 도 문제지만, 이제 프레임워크는 `MyApp`에 의존되어 `MyApp`만 사용할 수 있기 때문에, 범용적인 프레임워크가 아닙니다. 범용적인 프레임워크가 되도록 의존성을 수정해야만 합니다.

**준수 방법 : Template Method 패턴**

하기 그림과 같이 [Template Method 패턴](https://tango1202.github.io/pattern/pattern-template-method/) 을 사용한다면, 부모 클래스(`App`)에서 자식 클래스(`MyApp` 또는 `YourApp`)의 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)인 `SaveDoc()`을 호출하여 해결할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/237506b6-090d-4a7a-8bc4-31438116b97c)

코드로 구현하면 하기와 같습니다.

```cpp
class App {
    ...
protected: // 자식 개체만 접근하므로 protected
    // 자식 개체에서 문서 내용을 저장합니다. 
    virtual void SaveDoc() = 0;
};

// App 을 상속받아 만들었습니다.
class MyApp : public App {
public:
    MyApp(bool dirty, const std::wstring& pathName) :
        App(dirty, pathName) {} 
protected:
    // MyApp의 문서 내용을 저장합니다.
    virtual void SaveDoc() override {
        std::cout << "MyApp::SaveDoc()" << std::endl;
    }
};

// App 을 상속받아 만들었습니다.
class YourApp : public App {
public:
    YourApp(bool dirty, const std::wstring& pathName) :
        App(dirty, pathName) {} 
protected:
    // YourApp의 문서 내용을 저장합니다.
    virtual void SaveDoc() override {
        std::cout << "YourApp::SaveDoc()" << std::endl;
    }
};
```

다음과 같이 테스트 할 수 있습니다.

```cpp
MyApp myApp(true, L"test.txt");
myApp.Save(); // MyApp::SaveDoc() 출력        

YourApp yourApp(true, L"test.txt");
yourApp.Save(); // YourApp::SaveDoc() 출력  
```

**준수 방법 : Listener**

상속 관계 외에 [Observer 패턴](https://tango1202.github.io/pattern/pattern-observer/)의 개념을 이용하여 `Listener`로 하위 수준 모듈에서 상위 수준 모듈에 접근하게 할 수도 있습니다.
구조는 다음과 같습니다. `App`은 `ISaveListener`를 전달받아 `SaveDoc()`을 호출하고, `MyApp`에서는 `ISaveListener`를 상속받아 `App`에 전달합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8d03a4a5-92d8-4cd4-94a2-1f0b1c7ba5bc)

코드는 다음과 같습니다.

```cpp
// App 에서 문서 저장시 호출합니다.
class ISaveListener {
protected:
    ~ISaveListener() {}  // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.

public:
    virtual void SaveDoc() = 0;
};

// Save시 Listener를 사용합니다.
class App {
private:
    bool m_Dirty; // 내용이 수정되면 true입니다.
    std::wstring m_PathName;
    ISaveListener* m_SaveListener;
public:
    App(bool dirty, const std::wstring& pathName, ISaveListener* saveListener) : 
        m_Dirty(dirty),
        m_PathName(pathName),
        m_SaveListener(saveListener) {}
public:
    bool IsDirty() const {return m_Dirty;}
    void SetDirty(bool val) {m_Dirty = val;}
    const std::wstring& GetPathName() const {return m_PathName;}

    void Save() {

        // 파일명이 없다면 다른 이름으로 저장
        if (GetPathName().empty()) {
            SaveAs();
            return;;
        }

        // 저장할 필요가 있는지 확인
        if (!IsDirty()) {return;}

        // 문서 내용 저장
        if (m_SaveListener != nullptr) {
            m_SaveListener->SaveDoc();
        }

        // 저장되었다고 플래그 설정
        SetDirty(false);
    }

    // 다른 이름으로 저장합니다.
    void SaveAs() {}
};

// App 을 상속받아 만들었고, ISaveListener를 App에 전달합니다.
class MyApp : 
    public App,
    public ISaveListener {
public:
    MyApp(bool dirty, const std::wstring& pathName) :
        App(dirty, pathName, this) {} // 부모인 App에 this를 전달하여 ISaveListener를 전달합니다.
public:
    // MyApp의 문서 내용을 저장합니다.
    virtual void SaveDoc() override {
        std::cout << "MyApp::SaveDoc()" << std::endl;  
    }
};
```

다음과 같이 테스트 할 수 있습니다.

```cpp
MyApp myApp(true, L"test.txt");
myApp.Save(); // MyApp::SaveDoc() 출력        
```


