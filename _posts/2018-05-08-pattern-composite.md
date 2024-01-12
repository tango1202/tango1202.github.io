---
layout: single
title: "#8. [디자인 패턴-구조 패턴] Composite"
categories: "pattern"
tag: ["디자인 패턴", "구조 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Composite](https://tango1202.github.io/pattern/pattern-composite/)은 단일 개체와 복합 개체를 [추상화](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/)하여 모두 동일한 방식으로 다루게 해줍니다. 

# 설명

`Tree`와 같은 개체 구조를 다룰 때 개별 개체에 접근하려면 하위 개체가 있는지 검사한 뒤, 있다면 하위 개체를 탐색해 주어야 합니다. 약간 번거롭고 코드가 지저분해질 수 있는데요,

![Composite](https://github.com/tango1202/tango1202.github.io/assets/133472501/c8d13e09-77ef-43a1-a568-59b762627a04)

[Composite](https://tango1202.github.io/pattern/pattern-composite/)은 단일 개체와 복합 개체를 모두 동일한 방식으로 다루게 해줍니다. 코드를 단순하게 유지시켜 주는 장점이 있지만, 단일 개체에서도 복합 개체용 인터페이스들을 구현해야 하는 단점이 있습니다.

다음 그림에서 `Composite`는 `Operation()` 호출시 자신의 하위 `m_Childeren`들의 `Operation()`을 모두 호출해 주어, `Client`가 하위 개체를 일일이 탐색하는 번거로움을 대신해 주고 있습니다. 다만, 단일 개체인 `Leaf`도 `Add(), Remove(), GetChild()`와 같은 복합 개체용 인터페이스를 구현해 주어야 합니다.

![Composite](https://github.com/tango1202/tango1202.github.io/assets/133472501/03b8f342-1381-4bbb-a239-c7f2239d3cf9)

|항목|내용|
|--|--|
|`Component`|단일 개체와 복합 개체를 동일하게 다룰 수 있는 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)입니다.|
|`Leaf`|단일 개체입니다. 복합 개체용 인터페이스도 구현해야 합니다.|
|`Composite`|복합 개체입니다. 하위 개체 추가/삭제/탐색 인터페이스를 제공하며, `Operation()`실행시 모든 하위 개체에 적용합니다.|
|`Client`|`Composite`를 이용하여 각 개체의 `Operation()`을 실행합니다.|

# 특징

하위 개체를 탐색해야 하는 번거로움은 줄어들지만, `Leaf`입장에선 억지로 복합 개체용 인터페이스를 구현해야 하므로 [인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/)과 [제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/)을 위반합니다. 이는 하위 개체 접근시에만 [다운 캐스팅](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#runtime-type-infortti%EC%99%80-%ED%98%95%EB%B3%80%ED%99%98)하여 어느 정도 보완 할 수도 있습니다.

# 예제

UI 컨트롤들은 여러개의 컨트롤을 하위 개체로 사용하는 `Panel`을 사용하여 관리하면 좋습니다. 이 `Panel`을 대화상자에 붙이거나, 메인 메뉴에 붙이거나, 작업창에 붙여서 동일한 UI를 손쉽게 재활용 할 수 있습니다.

다음은 각 컨트롤들을 [Composite 패턴](https://tango1202.github.io/pattern/pattern-composite/)을 이용하여 `Panel`로 관리하는 예입니다. `Panel`은 하위에 일반 컨트롤들이나 또다른 `Panel`을 추가할 수 있으며, `Panel`에 `SetEnable()`을 호출하면, 모든 하위 컨트롤들의 `SetEnable()`이 호출됩니다.

1. #1 : `Control`은 `Component`입니다. 단일 개체와 복합 개체용 인터페이스를 함께 정의합니다.
2. #2 : `Label`과 `Edit`는 단일 개체의 구현입니다. 이때 복합 개체용 인터페이스 함수들은 사용하지 못하도록 예외를 발생시킵니다.
3. #3 : `Panel`은 `Composite` 입니다. 이때 `SetEnable()` 실행시 모든 하위 개체에도 `SetEnable()`을 호출합니다.
4. #4 : `rootPanel`에서 `SetEnable()`을 호출하면 모든 하위 개체들의 `SetEnable()`을 실행합니다.

```cpp
// ----
// #1. Component 입니다. 단일 개체와 복합 개체용 인터페이스를 함께 정의합니다.
// ----
class Control {
protected:
    Control() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Control() = default; // 다형 소멸 하도록 public virtual
private:
    Control(const Control&) = delete;
    Control(Control&&) = delete;
    Control& operator =(const Control&) = delete;
    Control& operator =(Control&&) = delete;          
public:
    // #1. 단일 개체용 인터페이스입니다.
    virtual void SetEnable(bool val) = 0;

    // #2. 복합 개체용 인터페이스입니다.
    virtual void Add(std::unique_ptr<Control> child) = 0;
    virtual void Remove(int index) = 0;
    virtual Control& GetChild(int index) = 0;
};

// ----
// #2. 단일 개체의 구현입니다. 복합 개체용 함수들은 예외를 발생시킵니다.
// ----
class Label : public Control {
public:
    Label() = default;

        virtual void SetEnable(bool val) override {
        std::cout << "Label::SetEnable()" << std::endl;
    }      
    virtual void Add(std::unique_ptr<Control> child) override {
        throw "Can not support"; // #2
    }
    virtual void Remove(int index) override {
        throw "Can not support"; // #2
    }
    virtual Control& GetChild(int index) override {
        throw "Can not support"; // #2
    }
};
class Edit : public Control {
public:
    Edit() = default;

    virtual void SetEnable(bool val) override {
        std::cout << "Edit::SetEnable()" << std::endl;
    }  
    virtual void Add(std::unique_ptr<Control> child) override {
        throw "Can not support"; // #2
    }
    virtual void Remove(int index) override {
        throw "Can not support"; // #2
    }
    virtual Control& GetChild(int index) override {
        throw "Can not support"; // #2
    }
};

// ----
// #3. 복합 개체인 Composite 입니다. SetEnable() 실행시 모든 하위 개체에 적용합니다.
// ----
class Panel : public Control {
    std::vector<std::shared_ptr<Control>> m_Children;
public:
    Panel() = default;

    // Child들의 SetEnable들을 설정합니다.
    virtual void SetEnable(bool val) override {
        for (auto& child : m_Children) { // #3
            child->SetEnable(val);
        }
    } 
    virtual void Add(std::unique_ptr<Control> child) override {
        m_Children.emplace_back(child.release());
    }
    virtual void Remove(int index) override {
        m_Children.erase(m_Children.begin() + index);
    }
    virtual Control& GetChild(int index) override {
        return *m_Children[index];
    }
};

// ----
// 테스트 코드
// ----
std::unique_ptr<Control> subPanel{new Panel};
subPanel->Add(std::unique_ptr<Control>{new Label});
subPanel->Add(std::unique_ptr<Control>{new Edit});   

Panel rootPanel;
rootPanel.Add(std::unique_ptr<Control>{new Label});
rootPanel.Add(std::unique_ptr<Control>{new Edit});
rootPanel.Add(std::move(subPanel)); // 하위 Panel을 추가합니다.

rootPanel.SetEnable(true); // #4. 모든 하위 개체들의 SetEnable()을 실행합니다.
```

