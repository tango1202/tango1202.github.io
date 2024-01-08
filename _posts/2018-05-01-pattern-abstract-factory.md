---
layout: single
title: "#1. [디자인 패턴-생성 패턴] Abstract Factory"
categories: "pattern"
tag: ["디자인 패턴", "생성 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Abstract Factory](https://tango1202.github.io/pattern/pattern-abstract-factory/)는 특정 그룹 계열에 속한 개체들을 생성합니다. 

# 설명

사용하는 디바이스(*데스크탑 또는 모바일*)에 따라 달라지는 컨트롤이 있다고 해봅시다. `Button`의 경우는 큰 차이가 없지만, `Check`의 경우 모바일에서 `Switch`처럼 구현될 수 있고, `RadioGroup`은 콤보박스의 형태로 구현될 수도 있습니다. 

이럴 경우 디바이스가 데스크탑인지 모바일용인지 코드 여기저기서 확인하고 분기하여 작성하는게 아니라, 한군데이서만 확인하고 처리하는게 좋습니다. 

[Abstract Factory](https://tango1202.github.io/pattern/pattern-abstract-factory/)는 이러한 경우 팩토리에서 일련의 개체를 생성해 줍니다.

다음 그림에서 `Client`는 `ConcreteFactory1`을 사용하느냐, `ConcreteFactory2`를 사용하느냐에 따라 다른 계열의 개체들을 생성하여 사용하게 됩니다.

![Abstract Factory](https://github.com/tango1202/tango1202.github.io/assets/133472501/d3a501b7-67a6-44c3-a86e-1f5dd4cebcad)

|항목|내용|
|--|--|
|`AbstractFactory`|주어진 그룹 계열의 개체들을 생성할 수 있는 인터페이스를 제공합니다.|
|`AbstractProductA, AbstractProductB`|개체의 인터페이스를 제공합니다.|
|`Client`|`AbstractFactory`를 이용하여 개체를 생성하고 사용합니다.|
|`ConcreteFactory1, ConcreteFactory2`|1계열과 2계열에 따라 구체화된 개체들을 생성합니다.|
|`ProductA1, ProductB1`, `ProductA2, ProductB2`|`AbstractProductA, AbstractProductB`를 구체화한 개체입니다.|

# 특징

개체 생성을 위해 필요한 조건 검사를 여기저기서 하지 않고 팩토리 선택시에만 하면 됩니다.

# 예제

다음 예제는 디바이스가 데스크탑인지 모바일용인지를 확인하고 팩토리를 사용하는 예입니다.

#4를 보면 `CreateControlFactory()`에서 모바일인지 검사하고, `MobileFactory`나 `NormalFactory`를 선택해서 팩토리를 만듭니다. 각 팩토리에서 디바이스에 따른 컨트롤들을 생성하기 때문에 코드 여기저기서 모바일인지 아닌지에 따라 분기해서 코드를 작성할 필요가 없습니다. 그저 `ControlFactory`에서 생성된 컨트롤을 믿고 사용하면 됩니다.

1. #1 : `Button`, `Check`, `RadioGroup`은 구체화된 클래스에서 상속해야 하는 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)입니다.
2. #2 : `ControlFactory`는 `Button`, `Check`, `RadioGroup`을 생성하는 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)입니다.
3. #3 : `Normal`용이나 `Mobile`용을 생성하는 `ControlFactory`의 구체 구현입니다.
4. #4 : 일반용이 필요한지, 모바일용이 필요한지 검사하여 `ControlFactory`를 선택해서 사용합니다.
5. #5 : `Normal`용과 `Mobile`용이 올바르게 생성되는지 테스트합니다.
6. #6 : 전달받은 `ControlFactory`에 따라 다른 계열의 컨트롤들을 생성하여 사용합니다.

```cpp
// ----
// 기본 인터페이스
// ----
class Button { // #1
protected:
    Button() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Button() = default; // 다형 소멸 하도록 public virtual
private:
    Button(const Button&) = delete;
    Button(Button&&) = delete;
    Button& operator =(const Button&) = delete;
    Button& operator =(Button&&) = delete;   
public:
    virtual void Click() = 0; // 버튼을 클릭합니다.
};

class Check { // #1
protected:
    Check() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Check() = default; // 다형 소멸 하도록 public virtual    
private:
    Check(const Check&) = delete;
    Check(Check&&) = delete;
    Check& operator =(const Check&) = delete;
    Check& operator =(Check&&) = delete;   
public:
    virtual void Toggle() = 0; // 체크 선택을 토글합니다.
};

class RadioGroup { // #1
protected:
    RadioGroup() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~RadioGroup() = default; // 다형 소멸 하도록 public virtual
private:
    RadioGroup(const RadioGroup&) = delete;
    RadioGroup(RadioGroup&&) = delete;
    RadioGroup& operator =(const RadioGroup&) = delete;
    RadioGroup& operator =(RadioGroup&&) = delete;   
public:
    virtual void Select(size_t index) = 0; // 주어진 인덱스를 선택합니다.
};

class ControlFactory { //#2
protected:
    ControlFactory() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~ControlFactory() = default; // 다형 소멸 하도록 public virtual
private:
    ControlFactory(const ControlFactory&) = delete;
    ControlFactory(ControlFactory&&) = delete;
    ControlFactory& operator =(const ControlFactory&) = delete;
    ControlFactory& operator =(ControlFactory&&) = delete;
public:
    virtual std::unique_ptr<Button> CreateButton() const = 0;
    virtual std::unique_ptr<Check> CreateCheck() const = 0;
    virtual std::unique_ptr<RadioGroup> CreateRadioGroup() const = 0;
};

// ----
// #3. Normal 계열 클래스
// ----
class NormalButton : public Button {
public: 
    NormalButton() = default;

    virtual void Click() override { // 버튼을 클릭합니다.
        std::cout << "NormalButton : Click()" << std::endl;
    }
};

class NormalCheck : public Check {
public: 
    NormalCheck() = default;

    virtual void Toggle() override { // 체크 선택을 토글합니다.
        std::cout << "NormalCheck : Toggle()" << std::endl;
    }
};  

class NormalRadioGroup : public RadioGroup {
public: 
    NormalRadioGroup() = default;

    virtual void Select(size_t index) override { // 주어진 인덱스를 선택합니다.
        std::cout << "NormalRadioGroup : Select()" << std::endl;
    }
};     

// Normal 계열로 생성합니다.
class NormalFactory : public ControlFactory { 
public:
    NormalFactory() = default;
public:
    virtual std::unique_ptr<Button> CreateButton() const override {
        return std::unique_ptr<Button>{new NormalButton{}};
    }
    virtual std::unique_ptr<Check> CreateCheck() const override {
        return std::unique_ptr<Check>{new NormalCheck{}};
    }
    virtual std::unique_ptr<RadioGroup> CreateRadioGroup() const override {
        return std::unique_ptr<RadioGroup>{new NormalRadioGroup{}};
    }
};

// ----
// #3. Mobile 계열 클래스
// ----
class MobileButton : public Button {
public: 
    MobileButton() = default;

    virtual void Click() override { // 버튼을 클릭합니다.
        std::cout << "MobileButton : Click()" << std::endl;
    }
};

class MobileCheck : public Check {
public: 
    MobileCheck() = default;

    virtual void Toggle() override { // 체크 선택을 토글합니다.
        std::cout << "MobileCheck : Toggle()" << std::endl;
    }
};  

class MobileRadioGroup : public RadioGroup {
public: 
    MobileRadioGroup() = default;

    virtual void Select(size_t index) override { // 주어진 인덱스를 선택합니다.
        std::cout << "MobileRadioGroup : Select()" << std::endl;
    }
};     

// Mobile 계열로 생성합니다.
class MobileFactory : public ControlFactory { 
public:
    MobileFactory() = default;
public:
    virtual std::unique_ptr<Button> CreateButton() const override {
        return std::unique_ptr<Button>{new MobileButton{}};
    }
    virtual std::unique_ptr<Check> CreateCheck() const override {
        return std::unique_ptr<Check>{new MobileCheck{}};
    }
    virtual std::unique_ptr<RadioGroup> CreateRadioGroup() const override {
        return std::unique_ptr<RadioGroup>{new MobileRadioGroup{}};
    }
};

// #4. 모바일 인지 검사합니다.
bool IsMobile() {
    return true; // 테스트 용으로 그냥 true를 리턴합니다.
} 

// #4. 모바일 인지 아닌지에 따라 ControlFactory를 생성합니다.
std::unique_ptr<ControlFactory> CreateControlFactory() {
    if (IsMobile()) { 
        // Mobile용 팩토리를 사용합니다.
        return std::unique_ptr<ControlFactory>{new MobileFactory{}};
    }
    else {
        // Normal용 팩토리를 사용합니다.
        return std::unique_ptr<ControlFactory>{new NormalFactory{}};
    }
}

// #6. 전달받은 factory에 따라 다른 컨트롤을 생성하여 사용합니다.
void TestAbstractFactory(ControlFactory& factory) {
    factory.CreateButton()->Click();
    factory.CreateCheck()->Toggle();
    factory.CreateRadioGroup()->Select(0);
}

// ----
// 테스트 코드
// ----
std::unique_ptr<ControlFactory> factory{CreateControlFactory()}; // #4

TestAbstractFactory(*factory); // #5
```


