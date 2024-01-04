---
layout: single
title: "#3. [디자인 패턴-생성 패턴] Factory Method"
categories: "pattern"
tag: ["디자인 패턴", "생성 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Factory Method](https://tango1202.github.io/pattern/pattern-factory-method/)는 생성에 대한 구체적인 정보를 자식 클래스에서 알고 있을때 부모 클래스에서 자식 클래스에게 생성을 요청하기 위해 사용합니다.

다음 그림에서 `Creator`의 `Operation()`는 구체화된 자식 개체인 `ConcreteProduct`의 생성을 필요로 하는데요, 부모 레이어에서 자식 레이어의 클래스를 직접 참조하면 종속의 방향성이 깨져 [상호 참조](??)될 수 있으므로, `FactoryMethod()`함수를 [오버라이딩](??)한 자식 클래스에서 생성할 수 있게 합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/869a533b-ef65-4b6b-8bd1-17178c1f5deb)

|항목|내용|
|--|--|
|`Creator`|`FactoryMethod()` 함수를 제공하는 부모 개체입니다. 자식 개체에서 `FactoryMethod()`를 구체화 해야 합니다. `Operation()`등에서 `FactoryMethod()`를 사용합니다.|
|`Product`|`FactoryMethod()`에서 생성하는 개체의 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)입니다.|
|`ConcreteCreator`|`FactoryMethod()` 함수를 구체화하여 `ConcreteProduct`를 생성합니다.|
|`ConcreteProduct`|실제 사용할 구체화된 개체입니다.|

# 특징

일련의 동작을 제공하는 기반 프레임워크에서 프레임워크를 상속한 자식 개체의 정보를 필요로 할때 사용할 수 있습니다.

# 예제

다음 예제는 어플리케이션 프레임워크입니다. App의 `LoadDoc(), SaveDoc()`에서 `IsDirty()`함수의 상태에 따라 여러 메시지를 표시합니다. 

1. #1 : 프레임워크에서 제공하는 부모 클래스입니다.
2. #2 : `CreateDoc()`이 생성하는 `Doc`은 자식 개체에서 정의한 구체화된 개체인 `MyDoc`이어야 합니다.
3. #3 : 프레임워크에서는 `MyDoc`을 알 수 없으므로, `MyApp`에서 이를 상속하여 구현합니다.

```cpp
// ----
// #1. 프레임워크에서 제공하는 부모 클래스입니다.
// ----
class Doc {
protected:
    Doc() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
private:
    Doc(const Doc&) = delete; 
    Doc(Doc&&) = delete; 
    Doc& operator =(const Doc&) = delete; 
    Doc& operator =(Doc&&) = delete;   
public:
    virtual ~Doc() = default; // 다형 소멸 하도록 public virtual

    bool IsDirty() const {return false;} // 테스트용으로 그냥 false를 리턴합니다.
    virtual void Load() = 0;
    virtual void Save() = 0;
};

class App {
private:
    std::unique_ptr<Doc> m_Doc;
protected:
    App() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
private:
    App(const App&) = delete;
    App(const App&&) = delete;
    App& operator =(const App&) = delete;
    App& operator =(App&&) = delete;   
public:
    virtual ~App() = default; // 다형 소멸 하도록 public virtual

protected:
    // 자식 개체에서만 호출 가능하도록 protected입니다.
    // #2. 자식 개체에서 어떤 Doc을 생성할지 결정합니다.
    virtual std::unique_ptr<Doc> CreateDoc() = 0;

public:   
    void LoadDoc() {
        
        // 더럽혀 졌다면, 불러오지 않습니다.
        if (m_Doc && m_Doc->IsDirty()) {
            std::cout << "Please. Save Doc." << std::endl;
            return;
        }

        std::unique_ptr<Doc> doc{CreateDoc()};
        
        doc->Load();

        m_Doc = std::move(doc);
    }
    
    void SaveDoc() {
        if (!m_Doc) {
            m_Doc = CreateDoc(); // Doc이 만들어지지 않았자면 지연 생성합니다.
        }

        // 더렵혀 지지 않았다면 저장하지 않습니다.
        if (!m_Doc->IsDirty()) {
            std::cout << "Already Saved." << std::endl;
            return;
        }

        m_Doc->Save();
    }
};
// ----
// 자식 클래스입니다.
// ----
class MyDoc : public Doc { // #2
public:
    MyDoc() = default;

    virtual void Load() override {
        std::cout << "MyDoc : Load()" << std::endl;
    }
    virtual void Save() override {
        std::cout << "MyDoc : Save()" << std::endl;
    }    
};

class MyApp : public App { // #3
public:
    MyApp() = default;

protected:    
    // #3. 자식 개체에서 어떤 Doc을 생성할지 결정합니다.
    virtual std::unique_ptr<Doc> CreateDoc() override {
        return std::unique_ptr<Doc>(new MyDoc{});
    }
};

MyApp app;

app.LoadDoc();
app.SaveDoc();
```


