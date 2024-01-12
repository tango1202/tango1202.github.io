---
layout: single
title: "#14. [디자인 패턴-행위 패턴] Command"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Command](https://tango1202.github.io/pattern/pattern-command/)는 기능의 요청과 실행을 분리하여, 실행 부분이 재활용될 수 있게 합니다.

# 설명

메뉴에 있는 버튼이 클릭되었을 때 해당 이벤트 핸들러에서 주어진 기능을 실행합니다.

```cpp
void OpenButton::Clicked() {
    MyApp::Open();
}
void SaveButton::Clicked() {
    MyApp::Save();
}
```

하지만 이러한 방식은 UI에 종속적이어서 UI가 변경되면 많은 수정이 필요합니다. 또한 만약 `Open()`과 관련된 UI가 메뉴, 버튼, 단축키 등 여러가지가 있을 경우 `MyApp::Open()`은 중복해서 작성되야 하죠.

이러한 경우 [Command 패턴](https://tango1202.github.io/pattern/pattern-command/)을 이용하여 기능 요청부와 실행부 분리해두면, 실행부를 재활용 할 수 있습니다.

다음 그림에서 `Invoker`는 버튼등으로서 기능을 요청하고, `Command`의 `Execute()`에서 실제 기능을 실행합니다. 이때 여러개의 `Invoker`에서 동일한 `Command`를 사용할 수도 있습니다. 

`ConcreteCommand`에서는 `Receiver`를 이용하여 기능을 수행하는 함수를 실행합니다. 이때 `m_State`에 상태값을 저장하여 `Undo`를 구현할 수도 있습니다.

![Command](https://github.com/tango1202/tango1202.github.io/assets/133472501/3262fd08-c3ac-480d-afb5-7c3f517a012f)

# 특징

`Execute()`시의 상태값을 저장하여 `Undo()`를 구현할 수 있으며, [Composite 패턴](https://tango1202.github.io/pattern/pattern-composite/)을 활용하여 여러 `Command`들을 매크로처럼 실행할 수 있습니다. 또한 UI를 사용자 정의하여 UI와 매칭되는 `Command`를 직접 매칭하는 기능을 구현할 수도 있습니다.

`Command`들의 갯수가 많다면, 자주 사용하는 것들만 먼저 생성하고, 자주 사용하지 않는 것들은 나중에 필요할 때 생성하여, 제품의 초기 실행 속도를 향상시킬 수도 있습니다.

# 예제

다음은 어플리케이션의 기능 실행을 [Command 패턴](?https://tango1202.github.io/pattern/pattern-command/)으로 구현한 예입니다. `Button`에 `Command`를 연결하고, `Button` 클릭시 `MyApp`의 각 기능들이 실행됩니다. 

1. #1 : `Command` 입니다. `Execute()`를 제공합니다.
2. #2 : `Invoker` 입니다. `Click()`시 연결된 `Command`를 `Execute()` 합니다.
3. #3 : `Receiver` 입니다. `Command`의 `Execute()`시 실행할 인터페이스입니다.
4. #4 : `MyApp`은 `Receiver` 를 구체화한 클래스 입니다.
5. #5 : `Command` 를 구체화한 클래스들입니다.
6. #6 : `CompositeCommand`은 여러개의 `Command` 들을 `Execute()` 합니다. 매크로 구현시 활용할 수 있습니다.
7. #7 : `Button`과 `Command`를 연결해 두면, `Button` `Click()`시 연결된 `Command`가 실행됩니다.

```cpp
// ----
// #1. Command 입니다. Execute()를 제공합니다.
// ----
class Command {
protected:
    Command() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Command() = default; // 다형 소멸 하도록 public virtual   
private:
    Command(const Command&) = delete; 
    Command(Command&&) = delete; 
    Command& operator =(const Command&) = delete; 
    Command& operator =(Command&&) = delete;   
public:
    virtual void Execute() = 0;     
};
// ----
// #2. Invoker 입니다. Click()시 연결된 Command를 Execute() 합니다.
// ----
class Button {
    std::shared_ptr<Command> m_Command;
public:
    explicit Button(std::shared_ptr<Command> command) : m_Command{command} {}
    ~Button() = default;
private:
    Button(const Button&) = delete; 
    Button(Button&&) = delete; 
    Button& operator =(const Button&) = delete; 
    Button& operator =(Button&&) = delete;   
public:
    void Click() {
        m_Command->Execute(); // #2
    }
};

// ----
// #3. Receiver 입니다. Command의 Execute()시 실행할 인터페이스입니다.
// ----
class ICommandReceiver {
protected:
    ICommandReceiver() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~ICommandReceiver() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    ICommandReceiver(const ICommandReceiver&) = delete;
    ICommandReceiver(ICommandReceiver&&) = delete;
    ICommandReceiver& operator =(const ICommandReceiver&) = delete;
    ICommandReceiver& operator =(ICommandReceiver&&) = delete;   
public:
    virtual void Open() = 0;
    virtual void Save() = 0;
    virtual void Copy() = 0;
    virtual void Paste() = 0;
};
// ----
// #4. Receiver 를 구체화한 클래스 입니다.
// ----
class MyApp : public ICommandReceiver {
    public:
    MyApp() = default; 
    ~MyApp() = default;
private:
    MyApp(const MyApp&) = delete; 
    MyApp(MyApp&&) = delete; 
    MyApp& operator =(const MyApp&) = delete; 
    MyApp& operator =(MyApp&&) = delete; 

    virtual void Open() override {std::cout << "MyApp::Open()" << std::endl;}
    virtual void Save() override {std::cout << "MyApp::Save()" << std::endl;}
    virtual void Copy() override {std::cout << "MyApp::Copy()" << std::endl;}
    virtual void Paste() override {std::cout << "MyApp::Paste()" << std::endl;}               
};
// ----
// #5. Command 를 구체화한 클래스 입니다.
// ----
class OpenCommand : public Command {
    ICommandReceiver& m_Receiver;
public:
    explicit OpenCommand(ICommandReceiver& receiver) : m_Receiver{receiver} {}

    virtual void Execute() override {m_Receiver.Open();}
};
class SaveCommand : public Command {
    ICommandReceiver& m_Receiver;
public:
    explicit SaveCommand(ICommandReceiver& receiver) : m_Receiver{receiver} {}

    virtual void Execute() override {m_Receiver.Save();}
};
class CopyCommand : public Command {
    ICommandReceiver& m_Receiver;
public:
    explicit CopyCommand(ICommandReceiver& receiver) : m_Receiver{receiver} {}

    virtual void Execute() override {m_Receiver.Copy();}
};
class PasteCommand : public Command {
    ICommandReceiver& m_Receiver;
public:
    explicit PasteCommand(ICommandReceiver& receiver) : m_Receiver{receiver} {}

    virtual void Execute() override {m_Receiver.Paste();}
}; 

// ----
// #6. 여러개의 Command 들을 Execute() 합니다. 매크로 구현시 활용할 수 있습니다.
// ----   
class CompositeCommand : public Command {
    std::vector<std::shared_ptr<Command>> m_Children;    

public:
    CompositeCommand() = default;

    virtual void Execute() override {
        for (auto& child : m_Children) { // 하위의 모든 Command를 실행합니다.
            child->Execute();
        }
    }
    void Add(std::shared_ptr<Command> child) {
        m_Children.push_back(child);
    }
};

// ----
// 테스트 코드
// ----
MyApp myApp;

// Command 들을 생성합니다.
std::shared_ptr<Command> openCommand{new OpenCommand{myApp}};
std::shared_ptr<Command> saveCommand{new SaveCommand{myApp}};
std::shared_ptr<Command> copyCommand{new CopyCommand{myApp}};
std::shared_ptr<Command> pasteCommand{new PasteCommand{myApp}};
std::shared_ptr<CompositeCommand> copyPasteCommand{new CompositeCommand{}};
copyPasteCommand->Add(copyCommand); // #6. 여러개의 Command를 추가합니다.
copyPasteCommand->Add(pasteCommand);   

// #7. 버튼과 연결합니다.
Button openButton{openCommand};
Button saveButton{saveCommand};
Button copyButton{copyCommand};
Button pasteButton{pasteCommand};
Button copyPasteButton{copyPasteCommand};

// #7. 버튼 클릭시 Command가 실행됩니다.
openButton.Click();
saveButton.Click();
copyButton.Click();
pasteButton.Click();
copyPasteButton.Click(); // #6. 여러개의 Command가 실행됩니다.
```