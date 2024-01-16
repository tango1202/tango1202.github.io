---
layout: single
title: "#22. [디자인 패턴-행위 패턴] Template Method"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Template Method](https://tango1202.github.io/pattern/pattern-template-method/)는 부모 개체에서 대략의 뼈대를 구현하여 뼈대 동작을 정규화하고, 자식 개체에서는 최소의 구현만 하게 합니다. 

# 설명

특정 OS에서 기본적인 기능들은 동일한 방식으로 동작합니다. 예를 들어 Windows에서 불러오기시 기존에 수정한 내용이 아직 저장되지 않았다면, 저장할지 물어봅니다. 또한 만약 파일명이 지정되지 않았다면, 파일 대화상자를 표시하여 파일명을 입력받은 뒤 저장합니다.

이러한 일련의 동작들은 모든 App에서 공통적으로 필요하므로, 부모 개체에서 대략의 뼈대 기능을 구현하고, 자식 개체에서 꼭 필요한 것만 구현하는게 좋습니다. 

만약 모든 App이 그때 그때 뼈대 기능을 구현한다면, 불필요하게 코드가 중복되어 [코드 냄새](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BD%94%EB%93%9C-%EB%83%84%EC%83%88code-smells)가 날 뿐만 아니라, [복사 붙여넣기](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B3%B5%EC%82%AC-%EB%B6%99%EC%97%AC%EB%84%A3%EA%B8%B0copy-paste-programming)가 되기도 하며, 미세한 세부 동작이 달라질 수도 있거든요.

다음 그림에서 `TemplateMethod()`는 `ConcreteClass`에서 구체 구현한 `PrimitiveOperation1()`과 `PrimitiveOperation2()`를 이용합니다. 이때 자식 개체의 구체 구현이 최소화 되도록 `TemplateMethod()`를 작성하는게 좋습니다.

![Template Method](https://github.com/tango1202/tango1202.github.io/assets/133472501/c0237f33-94cb-4c1e-ae7d-346ee0d08a61)

|항목|내용|
|--|--|
|`AbstractClass`|`TemplateMethod()`에서 뼈대 기능을 구현하고, 자식 개체에서 구현이 필요한 것은 [순가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)로 정의합니다.|
|`ConCreteClass`|`AbstractClass`에서 정의한 [순가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 구현합니다.|

# 특징

대략의 뼈대 동작을 정규화할 수 있어 일련의 동작을 제공하는 기반 프레임워크에서 주로 사용합니다.

# 예제

다음은 `MyApp`개체를 구현한 예입니다. `App`에서 `Load()`의 대략적인 뼈대 기능을 구현했으며, 자식 개체인 `MyApp`에서 `Load()`시 필요한 세부 기능들을 재구현 했습니다.

1. #1 : `App`은 `Load()`의 뼈대를 제공합니다. 자식 개체에서 `DoSaveMessage()`, `DoPathNameMessage()`, `DoSave()`, `DoLoad()`를 구현해야 합니다.
2. #2 : `MyApp`은 `App`을 구체 구현한 개체입니다.
3. #3 : 내용이 수정되고 파일명이 지정되지 않았으므로, `Load()`시 `DoSaveMessage()`, `DoPathNameMessage()`를 실행한 후 `DoSave()`하고 `DoLoad()`합니다.

```cpp
// ----
// #1. Load()의 뼈대를 제공합니다. 자식 개체에서 DoSaveMessage(), DoPathNameMessage(), DoSave(), DoLoad()를 구현해야 합니다.
// ----
class App {
    const char* m_PathName;
    bool m_Dirty;
protected:
    explicit App(const char* pathName) : m_PathName{pathName}, m_Dirty{false} {} // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~App() = default; // 다형 소멸 하도록 public virtual
private:
    App(const App&) = delete;
    App(const App&&) = delete;
    App& operator =(const App&) = delete;
    App& operator =(App&&) = delete; 
public:
    void Load(const char* pathName) {
        if (IsDirty()) { // 수정되었으면 저장이 필요합니다.
            if (DoSaveMessage()) { // 사용자에게 저장할지 물어봅니다.
                if (m_PathName == nullptr) {
                    m_PathName = DoPathNameMessage(); // 사용자에게 파일명을 물어봅니다.

                    if (m_PathName == nullptr) { // 사용자가 여전히 파일명을 정해 주지 않았다면 저장할 수 없습니다.
                        std::cout << "Cannot Save." << std::endl;
                        return;
                    }
                }
                DoSave(m_PathName);
            }
            else {
                // 저장하지 않고 Load 합니다.
            }
        }

        DoLoad(pathName); // 불러옵니다.
        m_PathName = pathName;
        SetDirty(false);
    }
        
    bool IsDirty() const {return m_Dirty;}       
    void SetDirty(bool dirty) {m_Dirty = dirty;}
protected:
    // 기존 것을 저장할지 말지 물어봅니다.
    virtual bool DoSaveMessage() const = 0;

    // 파일명을 입력받습니다.
    virtual const char* DoPathNameMessage() const = 0;

    virtual void DoSave(const char * pathName) = 0;
    virtual void DoLoad(const char * pathName) = 0;
};

// ----
// #2. App을 구체 구현한 개체입니다.
// ----
class MyApp : public App {
public:
    explicit MyApp(const char* pathName) : App{pathName} {}
protected:
    
    virtual bool DoSaveMessage() const override {
        std::cout << "MyApp::DoSaveMessage()" << std::endl;
        return true;    
    }

    // 파일명을 입력받습니다.
    virtual const char* DoPathNameMessage() const override {
        std::cout << "MyApp::DoPathNameMessage()" << std::endl;
        return "C:/MyOldFile.txt";
    }

    virtual void DoSave(const char * pathName) {
        std::cout << "MyApp::DoSave()" << std::endl;
    }
    virtual void DoLoad(const char * pathName) {
        std::cout << "MyApp::DoLoad()" << std::endl;
    }        
};

// ----
// 테스트 코드
// ----
MyApp myApp(nullptr);
myApp.SetDirty(true);

myApp.Load("C:/MyFile.txt"); // #3. DoSaveMessage(), DoPathNameMessage()를 실행한 후 DoSave()하고 DoLoad()합니다.
```