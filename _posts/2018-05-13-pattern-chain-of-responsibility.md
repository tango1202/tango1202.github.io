---
layout: single
title: "#13. [디자인 패턴-행위 패턴] Chain of Responsibility"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Chain of Responsibility](https://tango1202.github.io/pattern/pattern-chain-of-responsibility/)는 요청을 여러 개체에 전달하여 함께 처리할 수 있게 해줍니다. 한군데에서 모든 요청을 처리하지 않고 책임에 따라 여러 개체에 분산하므로, 해당 개체 구현시 고민할 것들이 줄어듭니다. 

# 설명

UI 컨트롤의 테두리 속성을 살펴봅시다. 컨트롤은 테두리 속성을 가지고 있고, 이 속성을 이용하여 테두리를 그립니다.

```cpp
class Control {
    Border m_Border; // 테두리 속성
public:
    void Draw() const {
        DrawBorder(left, top, width, height, m_Border); // m_Border 속성으로 그립니다.
    }
private:
    void DrawBorder(int l, int t, int w, int h, const Border& border) {
        // 테두리를 그립니다.
    }
};
```

또다른 방법으로는 스타일링을 이용하는 방법이 있습니다. 내부적으로 `Border` 속성이 있으면 해당 속성으로 그리고, 없다면 상위 개체의 속성으로 그리는 거죠. 이 방법을 사용하면 상위 개체의 속성을 변경했을때 하위 개체의 속성이 모두 변경되는 효과를 볼 수 있습니다.

```cpp
class Control {
    std::unique_ptr<Border> m_Border; // 테두리 속성. nullptr이면 상위 개체의 속성을 이용합니다.
    const char* m_Parent;
public:
    void Draw() const {
        const Border* border{m_Border.get()};
        if (!border) { // Border 속성이 없다면
            border = m_Parent->GetBorder(); // 상위 개체의 테두리 속성을 구합니다.
        }
        DrawBorder(left, top, width, height, *border); // border 속성으로 그립니다.
    };
    const char* GetParent() const {return m_Parent;}
    const Border* GetBorder() const {return m_Border.get();}
private:
    void DrawBorder(int l, int t, int w, int h, const Border& border) {
        // 테두리를 그립니다.
    }    
};
```

하지만 `m_Parent`에도 테두리 속성이 없다면, 더 상위 개체에 테두리 속성을 요청해야 합니다. 그렇다고, `m_Parent->m_Parent->GetBorder()`라고 호출하는건 좀 그렇죠. `m_Parent->m_Parent->m_Parent->GetBorder()`와 같이 계속 상위의 상위의 상위 개체에 요청할 수는 없는 노릇이잖아요?

[Chain of Responsibility](https://tango1202.github.io/pattern/pattern-chain-of-responsibility/)는 이러한 요청을 Chain 처럼 연결하여 문제를 해결합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/042b6fec-a617-49e6-b4de-5391152ebf69)

다음 그림처럼 `Handler`는 요청을 처리할 다음 개체인 `m_Successor`와 연결됩니다. 따라서, `Client`가 `HandleRequest()`를 호출하면, `Handler`에서 직접 처리할 것인지, 다음 개체인 `m_Successor`에 요청할 것인지 판단하여 처리하고, 다음 개체에서도 동일한 반복을 하게 되어 Chain으로 연결된 개체들이 함께 처리를 할 수 있도록 해줍니다.  

![Chain of Responsibility](https://github.com/tango1202/tango1202.github.io/assets/133472501/c483fe70-d68e-49cb-b629-1d6a5663b04f)

|항목|내용|
|--|--|
|`Handler`|요청을 처리할 다음 개체인 `m_Successor`를 관리합니다. `HandleRequest()`시 직접 처리할 것인지, 다음 개체인 `m_Successor`에 요청할 것인지 판단하여 처리합니다.|
|`ConcreteHandler1, ConcreteHandler2`|`Handler`를 구체화합니다.|
|`Client`|`Handler`의 `HandleRequest()`를 호출합니다.|

# 특징

`HandleRequest()`실행시 다음 개체로 요청할 수도 있고, 요청하지 않을 수도 있습니다. 이는 로직에 따라 달라집니다.

# 예제

다음은 테두리의 스타일링을 지원하는 `Control`의 구현 예입니다. 기본적으로 상위 `Panel`의 스타일을 따르며, 특정 속성을 직접 설정했다면 해당 속성을 사용합니다.

예제에서는 다음 그림과 같이 속성을 설정했는데요,

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/6a9b06e1-15b0-4872-8ebd-a0a493af8b1a)

`rootPanel`에 색상을 설정했으므로, 별도로 색상을 변경하지 않은 한 하위의 모든 컨트롤들은 `0x000000FF`색을 사용합니다.

`subPanel`에서 굵기를 `3`으로 사용했으므로 별도로 굵기를 변경하지 않는한 `subPanel`과 하위 컨트롤들은 `3` 굵기를 사용합니다.

`subPanel`내의 `Label`은 굵기를 `1`로 변경했으므로 `1`굵기를 사용합니다.

이를 구현하기 위해 컨트롤에서는 상위 개체인 `Panel`을 [Chain of Responsibility](https://tango1202.github.io/pattern/pattern-chain-of-responsibility/)로 연결하며, `Border`의 속성을 수집하기 위해 `MergeBorder()`를 호출합니다. 실제로 그릴 때는 `MergeBorder()`한 속성을 사용하면 됩니다.

1. #1 : [Composite 패턴](https://tango1202.github.io/pattern/pattern-composite/)으로 `Control`, `Label`, `Edit`, `Panel`을 구현합니다.
2. #2 : `IStyledBorderHandler`는 `Handler` 입니다. `MergeBorder()` 호출시 연결된 다음 개체에 요청합니다.
3. #3 : `Control`이 `IStyledBorderHandler`를 상속하여 `MergeBorder()`시 연결된 다음 개체에 요청할 수 있도록 합니다.
4. #4 : `Panel`에 `Child`가 추가되면 `SetSuccessor()`를 이용하여 다음 개체를 연결합니다.
5. #5 : `MergeBorder()` 호출시 자기의 속성과 다음 개체의 속성을 누적합니다.
6. #6 : `Border`의 속성을 합칩니다. 

```cpp
enum BorderStyle {Width, Color};
class Border {
    std::unique_ptr<unsigned int> m_Width;
    std::unique_ptr<unsigned int> m_Color;
public:
    Border() = default; // 속성이 세팅되지 않은 기본 Border를 생성합니다.
    ~Border() = default;
private:
    Border(const Border&) = delete; 
    Border(Border&&) = delete; 
    Border& operator =(const Border&) = delete; 
    Border& operator =(Border&&) = delete; 
public:
    unsigned int GetWidth() const {
        if (!m_Width) {
            return 0; // 설정되지 않으면 기본적으로 0 입니다.
        } 
        return *m_Width;
    }
    unsigned int GetColor() const {
        if (!m_Color) {
            return 0x00000000; // 설정되지 않으면 기본적으로 0입니다.
        }
        return *m_Color;
    }
    void SetWidth(unsigned int val) {
        m_Width = std::unique_ptr<unsigned int>{new unsigned int{val}};
    }
    void SetColor(unsigned int val) {
        m_Color = std::unique_ptr<unsigned int>{new unsigned int{val}};
    }      
    // ----
    // #6. this에 없는 속성은 other의 것을 사용하여 합칩니다.
    // ----
    void Merge(const Border& other) {
        if (!m_Width && other.m_Width) {
            SetWidth(*other.m_Width);
        }
        if (!m_Color && other.m_Color) {
            SetColor(*other.m_Color);
        }
    }
};

// ----
// #2. Handler 입니다. MergeBorder()를 연결된 다음 개체에 요청합니다.
// ----
class IStyledBorderHandler {
protected:
    IStyledBorderHandler() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~IStyledBorderHandler() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    IStyledBorderHandler(const IStyledBorderHandler&) = delete;
    IStyledBorderHandler(IStyledBorderHandler&&) = delete;
    IStyledBorderHandler& operator =(const IStyledBorderHandler&) = delete;
    IStyledBorderHandler& operator =(IStyledBorderHandler&&) = delete;   
public:
    // border에 스타일을 처리를 누적합니다.
    virtual void MergeBorder(Border& border) const = 0;
};

// ----
// #1. Composite 패턴으로 Control, Label, Edit, Panel을 구현합니다.
// ----
class Control : public IStyledBorderHandler { // #3
    std::unique_ptr<Border> m_Border;
    const IStyledBorderHandler* m_Successor; // #4
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
    void SetBorder(BorderStyle style, unsigned int val) {
        if (!m_Border) {
            m_Border = std::unique_ptr<Border>{new Border};
        }
        switch (style) {
        case BorderStyle::Width:
            m_Border->SetWidth(val);
            break;
        case BorderStyle::Color:
            m_Border->SetColor(val);
            break;
        }
    }
    // ----
    // #5. 자기의 속성과 다음 개체의 속성을 누적합니다.
    // ----
    virtual void MergeBorder(Border& border) const override { 
        
        if (m_Border) { // 자기 자신의 Border를 누적합니다.
            border.Merge(*m_Border); 
        }
        if (m_Successor) { // 다음 개체의 속성을 누적합니다.
            m_Successor->MergeBorder(border);
        }
    }
    void SetSuccessor(const IStyledBorderHandler* successor) { // #4
        m_Successor = successor;
    }

    // 복합 개체용 인터페이스입니다.
    virtual void Add(std::unique_ptr<Control> child) = 0;
    virtual Control& GetChild(int index) = 0;
};

class Label : public Control {
public:
    Label() = default;

    virtual void Add(std::unique_ptr<Control> child) override {
        throw "Can not support"; 
    }
    virtual Control& GetChild(int index) override {
        throw "Can not support";
    }
};
class Edit : public Control {
public:
    Edit() = default;

    virtual void Add(std::unique_ptr<Control> child) override {
        throw "Can not support"; 
    }
    virtual Control& GetChild(int index) override {
        throw "Can not support";
    }
};

class Panel : public Control {
    std::vector<std::shared_ptr<Control>> m_Children;
public:
    Panel() = default;

    virtual void Add(std::unique_ptr<Control> child) override {
        assert(child);

        child->SetSuccessor(this); // #4
        m_Children.emplace_back(child.release());
    }
    virtual Control& GetChild(int index) override {
        assert(index < m_Children.size());
        
        return *m_Children[index];
    }
};

// ----
// 테스트 코드
// ----
std::unique_ptr<Control> subPanel{new Panel};
subPanel->SetBorder(BorderStyle::Width, 3); // subPanel의 테두리의 굵기는 3으로 설정합니다.

std::unique_ptr<Control> subPanelLabel{new Label};
subPanelLabel->SetBorder(BorderStyle::Width, 1);
subPanel->Add(std::move(subPanelLabel)); // 라벨의 두께는 직접 설정합니다.

subPanel->Add(std::unique_ptr<Control>{new Edit}); // 상위 속성을 따릅니다.  

Panel rootPanel;
rootPanel.SetBorder(BorderStyle::Color, 0x000000FF); // 0x0000 00FF 색으로 설정합니다.
rootPanel.Add(std::unique_ptr<Control>{new Label});
rootPanel.Add(std::unique_ptr<Control>{new Edit});
rootPanel.Add(std::move(subPanel)); // 하위 Panel을 추가합니다.

Border border1;
rootPanel.GetChild(0).MergeBorder(border1); // rootPanel의 Label 속성입니다.
EXPECT_TRUE(border1.GetWidth() == 0); // 디폴트 속성을 따릅니다.
EXPECT_TRUE(border1.GetColor() == 0x000000FF); // root에 설정된 색상을 따릅니다.

Border border2;
rootPanel.GetChild(2).GetChild(0).MergeBorder(border2); // subPanel의 Label 속성입니다.
EXPECT_TRUE(border2.GetWidth() == 1); // 직접 설정한 속성을 따릅니다.
EXPECT_TRUE(border2.GetColor() == 0x000000FF); // root에 설정된 색상을 따릅니다.

Border border3;
rootPanel.GetChild(2).GetChild(1).MergeBorder(border3); // subPanel의 Edit 속성입니다.
EXPECT_TRUE(border3.GetWidth() == 3); // subPanel에 설정한 속성을 따릅니다.
EXPECT_TRUE(border3.GetColor() == 0x000000FF); // root에 설정된 색상을 따릅니다.
```