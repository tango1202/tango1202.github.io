---
layout: single
title: "#21. [디자인 패턴-행위 패턴] Strategy"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Strategy](https://tango1202.github.io/pattern/pattern-strategy/)는 알고리즘이나 기능등의 전략을 캡슐화하고 런타임에 변경할 수 있게 만들어 줍니다. 

# 설명

보통은 어떠한 기능을 실행할 때 함수를 호출하므로, 컴파일 타임에 실행이 결정됩니다.

```cpp
Rectangle rectangle{0, 0, 10, 20};

rectangle.SaveXml(); // Xml로 저장합니다.
```

만약 사용자의 선택에 따라 런타임에 저장하는 방식을 변경하고 싶다면, 다음과 같이 `if()`문을 사용할 수 있습니다.

```cpp
Rectangle rectangle{0, 0, 10, 20};

if (사용자가 Xml 저장을 선택했다면) {
    rectangle.SaveXml(); // Xml로 저장합니다.
}
else if (사용자가 Json 저장을 선택했다면) {
    rectangle.SaveJson();
}
```

하지만, 상기의 방식은 컴파일 타임에 `SaveXml()`과 `SaveJson()`을 모두 제공해야 하므로 `Rectangle` 개체가 덩치가 커지는 문제가 있습니다. 이러한 경우 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하여 런타임에 선택적으로 저장 전략을 변경할 수 있습니다.

다음 그림에서 `ConcreteStrategy1`와 `ConcreteStrategy2`는 주어진 전략을 구현합니다. `Context`는 전략이 바뀔 때마다 `ConcreteStrategy1`이나 `ConcreteStrategy2`를 참조하게 되며, `ContextInterface()` 호출시 설정된 `Strategy`를 실행하게 됩니다.

![Strategy](https://github.com/tango1202/tango1202.github.io/assets/133472501/629da927-a327-4ef2-8208-347813493c42)

|항목|내용|
|--|--|
|`Context`|기능이나 알고리즘 실행시 `Startegy`를 사용합니다.|
|`Strategy`|기능이나 알고리즘의 공통 인터페이스를 정의합니다.|
|`ConcreteStrategy1, ConcreteStrategy2`|주어진 전략에 따라 기능이나 알고리즘을 구현합니다.|

# 특징

외부에서 특정 전략을 주입(***의존성 주입***)하여 런타임에 유연하게 알고리즘이나 동작을 변경할 수 있습니다.

# 예제

다음은 `Rectangle`개체를 저장하는 예입니다. `Save()`시 어떤 전략을 설정했느냐에 따라 `XmlWriter`나 `JsonWriter`가 실행됩니다.

1. #1 : `IWriter`는 `Strategy`입니다. `WriteRectangle()` 함수로 `Rectangle` 정보를 저장합니다.
2. #2 : `XmlWriter`, `JsonWriter`는 `IWriter`를 구체화 합니다. 각각 Xml 또는 Json으로 저장합니다.
3. #3 : `Rectangle`은 `Save()`시 설정된 `IWriter`에 따라 다른 전략으로 저장합니다.
4. #4 : `SetWriter()`를 이용하여 런타임에 전략을 변경합니다.

```cpp
// ----
// #1. WriteRectangle() 함수로 Rectangle 정보를 저장합니다.
// ----
class IWriter {
protected:
    IWriter() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~IWriter() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    IWriter(const IWriter&) = delete;
    IWriter(IWriter&&) = delete;
    IWriter& operator =(const IWriter&) = delete;
    IWriter& operator =(IWriter&&) = delete;
public:
    virtual int WriteRectangle(int left, int top, int width, int height) const = 0;
};

// ----
// #2. Xml 또는 Json으로 저장합니다.
// ----
class XmlWriter : public IWriter {
public:
    XmlWriter() = default;

    virtual int WriteRectangle(int left, int top, int width, int height) const override {
        return 0;
    }
};
class JsonWriter : public IWriter {
public:
    JsonWriter() = default;

    virtual int WriteRectangle(int left, int top, int width, int height) const override {
        return 1;
    }
};    

// ---
// #3. Save()시 설정된 IWriter에 따라 다른 전략으로 저장합니다.
// ---
class Rectangle {
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;

    const IWriter* m_Writer;
public:
    Rectangle(int l, int t, int w, int h) : m_Left{l}, m_Top{t}, m_Width{w}, m_Height{h} {}

    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetWriter(const IWriter* writer) {m_Writer = writer;} // #3
    int Save() {
        if (m_Writer) {
            return m_Writer->WriteRectangle(m_Left, m_Top, m_Width, m_Height); // #3
        }
        return -1;
    }
};

// ----
// 테스트 코드
// ----
Rectangle rectangle{0, 0, 10, 20};

XmlWriter xmlWriter;
rectangle.SetWriter(&xmlWriter);
EXPECT_TRUE(rectangle.Save() == 0); // XmlWriter::WriteRectangle()이 실행됩니다.

JsonWriter jsonWriter;
rectangle.SetWriter(&jsonWriter); // #4. 런타임에 전략을 변경합니다.
EXPECT_TRUE(rectangle.Save() == 1); // JsonWriter::WriteRectangle()이 실행됩니다.
```


