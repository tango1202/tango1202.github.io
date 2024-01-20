---
layout: single
title: "#18. [디자인 패턴-행위 패턴] Memento"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Memento](https://tango1202.github.io/pattern/pattern-memento/)는 내부 상태를 저장해 두고 나중에 복원할 수 있게 해줍니다. [Command 패턴](https://tango1202.github.io/pattern/pattern-command/)과 함께 사용되어 `Undo`를 구현할 수 있습니다.

# 설명

개체를 이동시키는 기능을 구현하고자 할때 다음과 같이 미리 속성을 저장할 수 있습니다. 

```cpp
Rectangle rectangle;
int oldLeft = rectangle.GetLeft(); // 수정하기 전에 이전값을 저장합니다.
int oldTop = rectangle.GetTop();

rectangle.Move(left, top);
```

상기와 같이 저장된 값이 있다면 다음과 같이 복원할 수 있습니다.

```cpp
rectangle.Move(oldLeft, oldTop);
```

하지만 위의 방법은 `Move()`시에 어떠한 속성이 변경되는지 일일이 알아야 하는 문제가 있습니다. 기껏 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 해놨지만 무의미해 지죠. 개체도 속성 정보만 노출하는 [빈혈 모델](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EB%B9%88%ED%98%88-%EB%AA%A8%EB%8D%B8anemic-model)이 될 수 있고요.

[Memento 패턴](https://tango1202.github.io/pattern/pattern-memento/)은 속성이 수정된 원본 개체가 직접 복원에 필요한 정보를 제공하고, 해당 정보로 복원해 줌으로서 이런 문제를 해결합니다.

다음 그림은 [Memento](https://tango1202.github.io/pattern/pattern-memento/)의 일반적인 구조입니다. 

`Caretaker`는 `Originator`의 `CreateMemento()`를 호출하여 복원 정보를 저장하고, 복원이 필요할 경우 `SetMemento()`를 호출하여 정보를 복원합니다.

![Memento](https://github.com/tango1202/tango1202.github.io/assets/133472501/bd5de27f-6a43-4257-a23e-d2e108ec594e)

|항목|내용|
|--|--|
|`Caretaker`|`Memento`를 관리하는 개체입니다.|
|`Memento`|원본 개체를 복원하기 위한 정보입니다.|
|`Originator`|원본 개체입니다. `Memento`를 생성하고, 원본 개체를 복원합니다.|

# 특징

[Command 패턴](https://tango1202.github.io/pattern/pattern-command/)에서 `Undo()`(*`Unexecute()`*)를 구현하기 위해 사용합니다.

![Memento](https://github.com/tango1202/tango1202.github.io/assets/133472501/77a989d4-1e71-4409-821e-ff9b02d34933)

# 예제

다음은 [Command 패턴](https://tango1202.github.io/pattern/pattern-command/)으로 사각형을 이동시키고  [Memento 패턴](https://github.com/tango1202/tango1202.github.io/assets/133472501/d3d25908-d11c-4516-897b-63ff82e312cc)으로 복원하는 예입니다. `MoveCommand`의 `Execute()`시 `Memento`를 생성하고, `Unexecute()`시 수정한 값을 복원합니다.

1. #1 : `Rectangle`은 `Originator`입니다. `CreateMoveMemento()`에서 `Move()`를 복원할 할 수 있는 정보를 제공하며, `SetMemento()`에서 복원합니다.
2. #2 : `RectangleMoveMemento`는 `Rectangle`을 `Move()`후 복원할 수 있는 정보를 저장합니다.
3. #3 : `MoveCommand`는 `Execute()`시 `Memento`를 생성하고, `Unexecute()`시 수정한 값을 복원합니다.
4. #4 : `Execute()`로 값을 수정하고, `Unexecute()`로 값을 복원합니다.

```cpp
// ----
// #2. Rectangle을 Move()후 복원할 수 있는 정보를 저장합니다.
// ----
class RectangleMoveMemento {
    int m_Left;
    int m_Top;
public:
    RectangleMoveMemento(int left, int top) : m_Left{left}, m_Top{top} {}

    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}
};    

// ----
// #1. Originator 입니다. CreateMoveMemento()에서 Move()를 복원할 할 수 있는 정보를 제공하며, SetMemento()에서 복원합니다.
// ----
class Rectangle {
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : m_Left{l}, m_Top{t}, m_Width{w}, m_Height{h} {}

    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}

    void Move(int left, int top) {
        m_Left = left;
        m_Top = top;
    }

    // #1. Move() 시 수정된 속성을 복원할 수 있는 정보를 리턴합니다.
    std::unique_ptr<RectangleMoveMemento> CreateMoveMemento() {
        return std::unique_ptr<RectangleMoveMemento>{new RectangleMoveMemento{m_Left, m_Top}};
    }

    // #1. Move() 시 수정된 속성을 복원합니다.
    void SetMemento(const RectangleMoveMemento& memento) {
        m_Left = memento.GetLeft();
        m_Top = memento.GetTop();
    }
}; 

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
    virtual Rectangle& GetSelected() = 0;

    virtual void Move(int left, int top) = 0;
};

class MyApp : public ICommandReceiver {
    std::vector<std::unique_ptr<Rectangle>> m_Rectangles;
    size_t m_SelectedIndex;
public:
    MyApp() : m_SelectedIndex{0} {}

    virtual Rectangle& GetSelected() override { // 선택된 개체를 리턴합니다.
        assert(m_SelectedIndex < m_Rectangles.size());

        return *m_Rectangles[m_SelectedIndex];
    }
    virtual void Move(int left, int top) {
        GetSelected().Move(left, top);
    }

    void Add(std::unique_ptr<Rectangle> rectangle) { // 개체를 추가합니다.
        assert(rectangle);

        m_Rectangles.emplace_back(rectangle.release());
    } 
    void Select(size_t index) { // 관리중인 개체를 선택합니다.
        assert(index < m_Rectangles.size());
        
        m_SelectedIndex = index;
    }
};

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
    virtual void Unexecute() = 0;   
};

// ----
// #3. Execute()시 Memento를 생성하고, Unexecute()시 수정한 값을 복원합니다.
// ----
class MoveCommand : public Command {
    ICommandReceiver& m_Receiver;
    int m_Left;
    int m_Top;
    std::unique_ptr<RectangleMoveMemento> m_Memento;
public:
    explicit MoveCommand(ICommandReceiver& receiver, int left, int top) : m_Receiver{receiver}, m_Left{left}, m_Top{top} {}

    virtual void Execute() override {
        Rectangle& rectangle{m_Receiver.GetSelected()};
        m_Memento = rectangle.CreateMoveMemento();
        assert(m_Memento);

        m_Receiver.Move(m_Left, m_Top);
    }
    virtual void Unexecute() override {
        if (m_Memento) {
            Rectangle& rectangle{m_Receiver.GetSelected()};
            rectangle.SetMemento(*m_Memento);
        }
    }
};

// ----
// 테스트 코드
// ----  
MyApp myApp;

myApp.Add(std::unique_ptr<Rectangle>{new Rectangle{1, 2, 10, 20}});
myApp.Select(0);
const Rectangle& rectangle{myApp.GetSelected()};

MoveCommand moveCommand{myApp, 3, 4};

moveCommand.Execute(); // #4. 왼쪽 상단값을 수정합니다.
EXPECT_TRUE(rectangle.GetLeft() == 3 && rectangle.GetTop() == 4);

moveCommand.Unexecute(); // #4. 이전 값으로 되돌립니다.
EXPECT_TRUE(rectangle.GetLeft() == 1 && rectangle.GetTop() == 2);
```
