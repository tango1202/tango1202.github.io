---
layout: single
title: "#2. [디자인 패턴-생성 패턴] Builder"
categories: "pattern"
tag: ["디자인 패턴", "생성 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Builder](https://tango1202.github.io/pattern/pattern-builder/)는 여러 요소가 합성된 개체일 경우 요소를 합성하는 방법과 요소를 생성하는 방법을 분리하여 확장성을 향상시켜 줍니다.

# 설명

다음 그림은 [Builder](https://tango1202.github.io/pattern/pattern-builder/)의 일반적인 구조입니다. 

`Director`는 `Builder`에서 제공하는 `BuildPart1()`, `BuildPart2()`, `BuildPart3()`을 조합하여 개체를 생성합니다. 향후 `Builder`를 교체하여 다른 기능을 수행할 수 있도록 확장할 수 있습니다.

![Builder](https://github.com/tango1202/tango1202.github.io/assets/133472501/48a5d651-fdec-4f77-9021-deb15a9a7586)


|항목|내용|
|--|--|
|`Director`|요소를 합성하는 방법을 정의합니다.|
|`Builder`|요소를 생성하는 방법을 제공합니다.|
|`ConcreteBuilder`|`Builder`를 구체화 합니다.|

# 특징

요소 생성을 구체화한 `ConcreteBuilder`를 다형적으로 만들어 기능을 확장할 수 있습니다.

# 예제

다음과 같은 컨트롤 구조가 있다고 가정합시다. `Panel`은 여러 `Control`들의 집합인데요, 이런 `Panel`을 만들어 두면, 사용자와 상호작용하는 여러 컨트롤들을 `Panel` 1개에 배치하여 만들 수 있습니다.

```cpp
class Control {
public:
    virtual ~Control() = default; // 다형 소멸 하도록 public virtual
};
class Label : public Control {
public:
    explicit Label(const char* caption) {}
};
class Edit : public Control {};
class Ok : public Control {};
class Cancel : public Control {};
class Panel : public Control {
    std::vector<std::shared_ptr<Control>> m_Controls;
public:
    void Add(std::unique_ptr<Control> control) {
        assert(control);
        m_Controls.emplace_back(control.release());
    }
};
```

다음은 [Builder](https://tango1202.github.io/pattern/pattern-builder/)를 이용하여 아이디와 암호를 입력받는 `Panel`을 생성하는 예입니다.

1. #1 : 각 컨트롤들을 생성할 수 있는 인터페이스를 제공합니다.
2. #2 : `PanelBuilder`에서 `Panel`을 생성하고, `AddLabel(), AddEdit(), AddOk(), AddCancel()`호출시 `m_Panel`에 추가합니다.
3. #3 : `IdPasswordDirector`에서 `IControlBuilder`를 이용(*사실은 `PanelBuilder`*)하여 아이디와 암호를 입력받을 수 있는 UI를 합성합니다.
4. #4 : 생성된 `m_Panel`을 외부에서 사용합니다.

```cpp
class Control {
public:
    virtual ~Control() = default; // 다형 소멸 하도록 public virtual
};
class Label : public Control {
public:
    explicit Label(const char* caption) {}
};
class Edit : public Control {};
class Ok : public Control {};
class Cancel : public Control {};
class Panel : public Control {
    std::vector<std::shared_ptr<Control>> m_Controls;
public:
    void Add(std::unique_ptr<Control> control) {
        assert(control);
        m_Controls.emplace_back(control.release());
    }
};

// ----
// #1. 각 컨트롤들을 생성할 수 있는 인터페이스를 제공합니다.
// ----
class IControlBuilder { 
protected:
    IControlBuilder() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~IControlBuilder() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    IControlBuilder(const IControlBuilder&) = delete;
    IControlBuilder(IControlBuilder&&) = delete;
    IControlBuilder& operator =(const IControlBuilder&) = delete;
    IControlBuilder& operator =(IControlBuilder&&) = delete;
public:
    virtual void AddLabel(const char* caption) = 0;
    virtual void AddEdit() = 0;
    virtual void AddOk() = 0;
    virtual void AddCancel() = 0;
};
// ----
// #2. Panel을 생성하고, AddLabel(), AddEdit(), AddOk(), AddCancel()호출시 m_Panel에 추가합니다.
// ----
class PanelBuilder : public IControlBuilder { 
private:
    std::unique_ptr<Panel> m_Panel;
public:
    PanelBuilder() : m_Panel{new Panel{}} {}

    virtual void AddLabel(const char* caption) override {
        m_Panel->Add(std::unique_ptr<Control>{new Label{caption}});
    }
    virtual void AddEdit() override {
        m_Panel->Add(std::unique_ptr<Control>{new Edit{}});
    }
    virtual void AddOk() override {
        m_Panel->Add(std::unique_ptr<Control>{new Ok{}});   
    }
    virtual void AddCancel() override {
        m_Panel->Add(std::unique_ptr<Control>{new Cancel{}});   
    }  

    // #4. 생성된 패널을 리턴하고 내부 멤버 변수는 초기화 합니다.
    std::unique_ptr<Panel> Release() {
        assert(m_Panel);
        return std::move(m_Panel);
    }  
};

// ----
// #3. Builder를 이용하여 요소를 합성합니다.
// ----
class IdPasswordDirector { 
private:
    IControlBuilder& m_Builder;
public:
    explicit IdPasswordDirector(IControlBuilder& builder) : m_Builder{builder} {}

    // ID, Password를 입력받는 Panel을 만듭니다.
    void Construct() {
        m_Builder.AddLabel("ID");
        m_Builder.AddEdit();

        m_Builder.AddLabel("Password");
        m_Builder.AddEdit();

        m_Builder.AddOk();
        m_Builder.AddCancel();
    }    
};  

// ----
// 테스트 코드
// ----   
PanelBuilder panelBuilder;
IdPasswordDirector director{panelBuilder};
director.Construct();
std::unique_ptr<Panel> panel{panelBuilder.Release()}; // #4. 생성된 panel을 구합니다.
```

아직까지는 굳이 `Director`와 `Builder`를 나눌 필요가 있을까 싶은데요, `ConcreteBuilder`를 다형적으로 만들어 기능을 확장할 수 있습니다.

`IControlBuilder`를 다음의 `PanelWriter`처럼 구현하면, `IdPasswordDirector`를 이용하여 아이디와 암호를 입력받는 UI를 Xml 형태로 출력하도록 만들 수 있습니다.

```cpp
class PanelWriter : public IControlBuilder {
public:
    PanelWriter() {
        std::cout << "<Panel>" << std::endl;
    }
    ~PanelWriter() {
        std::cout << "</Panel>" << std::endl;
    } 
    virtual void AddLabel(const char* caption) override {
        assert(caption);
        std::cout << "    <Label caption = \"" << caption << "\"/>" << std::endl; 
    }
    virtual void AddEdit() override {
        std::cout << "    <Edit/>" << std::endl;
    }
    virtual void AddOk() override {
        std::cout << "    <Ok/>" << std::endl;  
    }
    virtual void AddCancel() override {
        std::cout << "    <Cancel/>" << std::endl; 
    } 
};

// ----
// 테스트 코드
// ----           
PanelWriter panelWriter;
IdPasswordDirector director{panelWriter};
director.Construct(); // 생성하는 정보를 cout으로 출력합니다.
```

또한 다음의 `PanelCounter`처럼 구현하면, `IdPasswordDirector`를 이용하여 아이디와 암호를 입력받는 UI에서 사용된 컨트롤의 갯수를 구할 수 있습니다.

```cpp
class PanelCounter : public IControlBuilder {
    size_t m_Count;
public:
    PanelCounter() : m_Count{0} {}
    virtual void AddLabel(const char* caption) override {
        ++m_Count;
    }
    virtual void AddEdit() override {
        ++m_Count;
    }
    virtual void AddOk() override {
        ++m_Count;
    }
    virtual void AddCancel() override {
        ++m_Count;
    } 

    size_t GetCount() const {return m_Count;}
};

// ----
// 테스트 코드
// ----           
PanelCounter panelCounter;
IdPasswordDirector director{panelCounter};
director.Construct();
EXPECT_TRUE(panelCounter.GetCount() == 6); // panel 내에 사용된 control 갯수를 출력합니다.       
```








