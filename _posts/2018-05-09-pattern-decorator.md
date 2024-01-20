---
layout: single
title: "#9. [디자인 패턴-구조 패턴] Decorator"
categories: "pattern"
tag: ["디자인 패턴", "구조 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Decorator](https://tango1202.github.io/pattern/pattern-decorator/)는 개체에 동적으로 새로운 외형을 추가하거나 기능을 추가할 때 포함을 통해 처리합니다. 클래스를 직접 수정하거나 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)을 이용하여 추가하는 것보다 코드가 간결해 집니다.

# 설명

보통은 기존 클래스에 새로운 것을 추가할 경우 클래스를 직접 수정하던지, [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)을 해서 추가하는데요,

```cpp
class Old {
public:
    void A() {}
    void B() {}
};
class New : public Old {
public:
    void C() {} // Old에서 새로운 기능을 추가합니다.    
};
```

다음은 UI 컨트롤들에 테두리나 스크롤을 지원하기 위해 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)을 사용한 예입니다. 겨우 2개 기능을 추가했는데, 너무 많은 클래스들이 만들어 지네요.

```cpp
class Button {};
class Radio {};
class Check {};

class BorderButton : public Button {};
class BorderRadio : public Radio {};
class BorderCheck : public Check {};

class ScrollableButton : public Button {};
class ScrollableRadio : public Radio {};
class ScrollableCheck : public Check {};

class ScrollableBorderButton : public Button {};
class ScrollableBorderRadio : public Radio {};
class ScrollableBorderCheck : public Check {};
```

이런 점이 싫어 다음과 같이 각 컨트롤에 `Border`와 `Scroll`의 기능을 구현하고 on/off 시키는 방법을 사용할 수도 있습니다.

```cpp
class Button {
public:
    void SetEnableBorder(bool val);
    void SetEnableScroll(bool val);   
};
```

하지만 이 방법도 많은 기능을 포함하다 보면 개체가 점점 커지게 되고, 결국 [블롭](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B8%94%EB%A1%ADthe-blob)이 되어 많은 개체들에게 종속된 괴물로 변신하게 됩니다.

[Decorator](https://tango1202.github.io/pattern/pattern-decorator/)는 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)이 아닌 포함을 통해 개체의 기능을 확장함으로서 이런 문제를 해결합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c9655a19-842a-4fb1-911e-c7ec0e45ec57)

다음 그림은 [Decorator](https://tango1202.github.io/pattern/pattern-decorator/)의 일반적인 구조입니다. 

`Decorator`는 내부적으로 `m_Component`를 포함하며, `Operation()` 호출시 `AddedBehavior()`를 호출하여 추가 기능을 실행합니다. `Operation()` 함수 내에서 살짝 덧칠해서 꾸미는 거죠.

![Decorator](https://github.com/tango1202/tango1202.github.io/assets/133472501/7d93a482-aca5-430b-89b0-ccbf3676dd29)

|항목|내용|
|--|--|
|`Component`|개체의 기본 인터페이스입니다.|
|`ConcreteComponent`|`Component`를 구체화한 개체입니다.|
|`Decorator`|`Operation()`실행시 추가 기능을 실행합니다.|

`Component` 관리를 공통화 하기 위해 `Decorator`를 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)로 만들어 다음과 같이 확장할 수도 있습니다.

![Decorator](https://github.com/tango1202/tango1202.github.io/assets/133472501/a0b04b15-0dd3-45ee-9316-6082bd32048b)

# 특징

 런타임에 동적으로 새로운 외형을 추가하거나 기능을 추가할 수 있게 합니다. 개체를 포함한다는 측면에서 [Adapter](https://tango1202.github.io/pattern/pattern-adapter/), [Proxy](https://tango1202.github.io/pattern/pattern-proxy/), [Chain of Responsibility](https://tango1202.github.io/pattern/pattern-chain-of-responsibility/) 와 비슷하지만 목적이 다릅니다. 
 
 * [Adapter](https://tango1202.github.io/pattern/pattern-adapter/)는 인터페이스를 맞추기 위한 것이고, 
 * [Proxy](https://tango1202.github.io/pattern/pattern-proxy/)는 포함한 개체를 대리하기 위한 것이고, 
 * [Chain of Responsibility](https://tango1202.github.io/pattern/pattern-chain-of-responsibility/)는 요청을 전달하기 위해 사용하며, 
 * [Decorator](https://tango1202.github.io/pattern/pattern-decorator/)는 기능을 추가하기 위해 사용합니다.
 
 또한 [Decorator](https://tango1202.github.io/pattern/pattern-decorator/)는 다음처럼 중첩해서 사용할 수 있기 때문에 여러 기능을 추가할 수도 있습니다.

![Decorator](https://github.com/tango1202/tango1202.github.io/assets/133472501/8aa05a82-a48d-4ce6-a7db-9e6ced98a0c9)

# 예제

새롭게 추가된 기능의 UI에 `New` 아이콘을 추가로 출력하는 기능을 만든다고 가정해 봅시다.

다음 예의 `NewIconDecorator`에서는 컨트롤을 `Draw()`할 때 `DrawNewIcon()`함수를 추가로 호출하여 `New` 아이콘을 출력합니다.

1. #1 : `Control`은 `Component`입니다. `Draw()` 함수를 재구현해야 합니다.
2. #2 : 개체 구현입니다. `Draw()` 함수를 재구현 하였습니다.
3. #3 : `NewIconDecorator`는 `Decorator`입니다. `Draw()` 함수 호출시 `DrawNewIcon()`을 추가로 호출합니다. 이때 런타임에 동적으로 `NewIconDecorator`로 감쌀지 결정할 수 있습니다.

```cpp
// ----
// #1. Component 입니다. Draw() 함수를 재구현해야 합니다.
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
    virtual void Draw() const = 0;
};  
// ----
//  #2. 개체 구현입니다. Draw() 함수를 재구현 하였습니다.
// ----    
class Label : public Control {
public:
    Label() = default;

        virtual void Draw() const override {
        std::cout << "Label::Draw()" << std::endl;
    }      
};
class Edit : public Control {
public:
    Edit() = default;

    virtual void Draw() const override {
        std::cout << "Edit::Draw()" << std::endl;
    }  
};
// ----
//  #3. Decorator 입니다. Draw() 함수 호출시 DrawNewIcon()을 추가로 호출합니다.
// ----
class NewIconDecorator : public Control {
    std::unique_ptr<Control> m_Control;
public:
    explicit NewIconDecorator(std::unique_ptr<Control> control) : m_Control{std::move(control)} {
        assert(m_Control);  
    }

    virtual void Draw() const override {
        assert(m_Control); 

        m_Control->Draw();
        DrawNewIcon(); // #3
    }
private:
    void DrawNewIcon() const {
        std::cout << "NewBehaviorDecorator::DrawBehavior()" << std::endl; // #3
    }
};

// ----
// 테스트 코드
// ----           
// Draw 호출시 NewIconDecorator인 것은 NewIcon을 추가로 출력합니다.
std::vector<std::shared_ptr<Control>> v;
v.emplace_back(std::unique_ptr<Control>{new Label});
v.emplace_back(std::unique_ptr<Control>{new NewIconDecorator{std::unique_ptr<Control>{new Edit}}}); // #3. 런타임에 NewIcon을 출력할지 결정할 수 있습니다.

for (auto& control : v) {
    control->Draw();
}
```

또한 `Decorator`는 `Control`을 상속했으므로, 다른 `Decorator`로 중첩해서 사용할 수 있습니다.

다음의 `LogDecorator`는 로그를 출력합니다. 만약 `NewIconDecorator`를 전달하면, `Draw()`호출시 `New` 아이콘을 출력한 뒤 로그를 출력하게 됩니다.

```cpp
class LogDecorator : public Control {
    std::unique_ptr<Control> m_Control;
public:
    explicit LogDecorator(std::unique_ptr<Control> control) : m_Control{std::move(control)} {
        assert(m_Control); 
    }

    virtual void Draw() const override {
        assert(m_Control); 

        m_Control->Draw();
        Log();
    }
private:
    void Log() const {
        std::cout << "LogDecorator::Log()" << std::endl;
    }
};

// ----
// 테스트 코드
// ----

// Draw 호출시 로그를 기록합니다.
std::vector<std::shared_ptr<Control>> v;
v.emplace_back(std::unique_ptr<Control>{new LogDecorator{std::unique_ptr<Control>{new Label}}});
v.emplace_back(
    std::unique_ptr<Control>{new LogDecorator{
        std::unique_ptr<Control>{new NewIconDecorator{
            std::unique_ptr<Control>{new Edit}
        }}
    }}
);

for (auto& control : v) {
    control->Draw();
}    
```

