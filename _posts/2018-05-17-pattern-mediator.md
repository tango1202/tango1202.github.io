---
layout: single
title: "#17. [디자인 패턴-행위 패턴] Mediator"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Mediator](https://tango1202.github.io/pattern/pattern-mediator/)는 개체들끼리의 상호작용을 캡슐화합니다. 개체들끼리 서로 직접 참조하지 않도록 결합도를 낮춰 유지보수 편의성의 향상됩니다.

# 설명

다음과 같이 리스트의 선택이 변경되면, 해당 선택 항목으로 다른 컨트롤을 변경하는 UI를 생각해 봅시다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/76eb603b-736b-47fa-8b15-bc12832bb220)

이벤트 핸들러를 이용한다면 다음과 같이 작성할 수 있습니다.

```cpp
void List::SelectChanged() {
    m_NameEdit.SetText(m_List.GetCurrentData().GetName());
    m_AddrEdit.SetText(m_List.GetCurrentData().GetAddr());
}
```

하지만 이 방식은 상호작용하는 컨트롤을 직접 접근하여 결합도가 높아 향후 유지보수가 어려워질 수 있습니다. 이럴 경우 개체들끼리의 상호작용을 담당하는 [Mediator 패턴](https://tango1202.github.io/pattern/pattern-mediator/)을 사용하면 결합도를 낮출 수 있습니다.

다음 그림에서 `Colleague`에서 변경이 발생한 경우 `Mediator`의 `Changed()`를 호출하여 상호작용하는 개체들을 처리합니다. 이애 따라 상호작용하는 `ConcreteColleague1`과 `ConcreteColleague2`는 서로 직접 참조하지 않으므로 결합도가 낮아집니다.

![Mediator](https://github.com/tango1202/tango1202.github.io/assets/133472501/1f45f85e-cf0f-4d7d-99da-53853184f655)

|항목|내용|
|--|--|
|`Colleague`|상호작용이 필요한 개체입니다. 상호작용이 필요한 경우 `m_Mediator`에 의뢰합니다.|
|`Mediator`|개체간의 상호작용을 처리합니다.|
|`ConcreteColleague1, ConcreteColleague2`|상호작용이 필요한 개체들 입니다.|
|`ConcreteMediator`|`Mediator`를 구체화한 개체로서 개체간의 상호작용을 처리합니다.|

# 특징

대화상자와 같은 UI에서는 컨트롤 변경에 따라 상호작용하는 컨트롤들이 많습니다. 이 경우 [Mediator 패턴](https://tango1202.github.io/pattern/pattern-mediator/)을 이용하면 결합도를 낮출 수 있습니다.

# 예제

다음은 리스트에 표시된 이름과 주소를 선택할 경우 에디트에 해당 내용을 출력하는 예입니다.

1. #1 : `IMediator`는 컨트롤의 변동사항을 통지받아 다른 컨트를들을 변경하는 `Changed()`인터페이스를 제공합니다.
2. #2 : `Control`은 컨트롤의 변동사항이 있을 경우, `IMediator`의 `Changed()`를 호출해 줍니다.
3. #3 : `List`와 `Edit`는 `Control`을 구체화한 개체입니다. 변경사항이 있으면, `IMediator`의 `Changed()`를 호출해 줍니다.  
4. #4 : `MyMediator`는 `IMediator`를 구체화한 개체입니다. `List`의 선택 항목이 변경되면, 각 `Edit`의 문자열을 선택한 항목으로 변경합니다.

```cpp
class Control;

// ----
// #1. 컨트롤의 변동사항을 통지받아 다른 컨트를들을 변경합니다.
// ----
class IMediator {
protected:
    IMediator() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~IMediator() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    IMediator(const IMediator&) = delete;
    IMediator(IMediator&&) = delete;
    IMediator& operator =(const IMediator&) = delete;
    IMediator& operator =(IMediator&&) = delete;
public:
    // 컨트롤에서 변경사항을 통지합니다.
    virtual void Changed(const Control& control) = 0;
}; 

// ----
// #2. 컨트롤의 변동사항이 있을 경우, IMediator의 Changed()를 호출해 줍니다.
// ----
class Control {
    IMediator m_Mediator;
protected:
    Control() : m_Mediator{nullptr} {} // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Control() = default; // 다형 소멸 하도록 public virtual   
private:
    Control(const Control&) = delete;
    Control(Control&&) = delete;
    Control& operator =(const Control&) = delete;
    Control& operator =(Control&&) = delete;

public:
    void SetMediator(IMediator* mediator) {m_Mediator = mediator;} 
protected:
    void Changed() {
        if (m_Madiator) {m_Mediator->Changed(*this);}
    }
};
// ----
// #3. Control의 구체한 개체입니다.
// ----

// 리스트의 선택 항목이 변경되면, Mediator의 Changed()를 호출해 줍니다.
class List : public Control {
    std::vector<std::string, std::string> m_Data;
    size_t m_SelectedIndex;
public: 
    List() : Control{}, m_SelectedIndex{0} {} 

    size_t GetSelectedIndex() const {return m_SelectedIndex;}

    void Add(const char* name, const char addr) {
        m_Data.emplace_back(name, addr);
    }
    // 이름-주소를 리턴합니다.
    std::pair<std::string, std::string> GetAt(int index) {
        return m_Data[index];
    }

    void Select(size_t index) {
        m_SelectedIndex = index;
        Changed();
    } 
};
// Edit의 문자열이 변경되면, Mediator의 Changed를 호출해 줍니다.
class Edit : public Control {
    std::string m_String;
public: 
    explicit Edit() : Control{} {} 

    const std::string& GetString() const {return m_String;}
    void SetString(const char* str) {
        m_String = str;
        Changed();
    } 
};

// ----
// #4. IMediator를 구체화한 개체입니다.
// List의 선택 항목이 변경되면, 각 Edit의 문자열을 선택한 항목으로 변경합니다. 
// ----
clsss MyMediator : public IMediator {
    List* m_List;
    Edit* m_NameEdit; 
    Edit* m_AddrEdit;
public:
    // 생성시 각 컨트롤의 Mediator를 설정합니다.
    MyMediator(List* list, Edit* nameEdit, Edit* AddrEdit) : m_List{list}, m_NameEdit{nameEdit}, m_AddrList{addrList} {
        m_List->SetMediator(this);
        m_NameEdit->SetMediator(this);
        m_AddrEdit->SetMediator(this);
    }

    // 컨트롤중 리스트가 변경되면 이름과 주소를 갱신합니다.
    virtual void Changed(const Control& control) override {
        if (dynamic_cast<const List*>(&control) == m_List) {
            size_t index{m_List->GetSelectedIndex()};
            std::pair<std::string, std::string> data{m_List->GetAt(index)};

            m_NameEdit->SetString(data.first);
            m_AddrEdit->SetString(data.second);
            }
    }
};
// ----
// 테스트 코드
// ----  
List list;
list.Add("Kim", "Seoul");
list.Add("Lee", "Pusan");
Edit nameEdit;
Edit addrEdit;

// 내부적으로 각 컨트롤의 Mediator를 설정합니다. 
MyMediator myMediator{list, nameEdit, addrEdit};

// 최초에는 nameEdit와 addrEdit가 비었습니다.
EXPECT_TRUE(nameEdit.GetString().empty() && addrEdit.GetString().empty());

// #4. 선택한 항목의 데이터로 nameEdit와 addrEdit가 변경됩니다.
list.Select(0);
EXPECT_TRUE(nameEdit.GetString() == "Kim" && addrEdit.GetString() == "Seoul");
list.Select(2);
EXPECT_TRUE(nameEdit.GetString() == "Lee" && addrEdit.GetString() == "Pusan");
```