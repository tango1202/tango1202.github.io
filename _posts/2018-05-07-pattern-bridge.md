---
layout: single
title: "#7. [디자인 패턴-구조 패턴] Bridge"
categories: "pattern"
tag: ["디자인 패턴", "구조 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Bridge](https://tango1202.github.io/pattern/pattern-bridge/)는 추상과 구현을 분리하여, 종속적인 부분을 느슨하게 만들어 주거나, 구현을 다형적으로 만들 수 있게 합니다.

# 설명

[추상화](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/)는 공통적인 일반 개념을 만드는 작업입니다. 이를 잘 설계하면, 재활용성이 높아져 활용도가 높아지고, 특정 문제들을 해결하는데 있어서 공통된 접근을 하기 때문에 고민의 가지수가 적어집니다. 

[Bridge](https://tango1202.github.io/pattern/pattern-bridge/)는 [추상화](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/)와 이의 구체화를 분리함으로서 코드간 종속성을 해결하고, 확장성을 제공해 줍니다. 대부분의 디자인 패턴에서의 근간이 되는 구조라고도 할 수 있겠습니다.

간단하게는 [PImpl 이디엄](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-pimpl/)도 [Bridge](https://tango1202.github.io/pattern/pattern-bridge/)라 할 수 있습니다. 추상과 구현을 분리해서 코드간의 종속성이나 컴파일 종속성을 최소화 해줍니다.

![Bridge](https://github.com/tango1202/tango1202.github.io/assets/133472501/c9ae947f-d491-4b6d-8a49-8d2a16f68891)

복잡하게는 구현부를 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)로 만들어 다형적으로 만들수 있습니다. 이때에는 `m_Impl`을 런타임에 교체할 수도 있어 확장성이 좋아집니다.

다음 그림에서 `Client`는 추상부인 `Abstraction`만 이용합니다.  `Client` 코드는 `Abstraction`의 `Implementor`가 내부적으로 `ConcreteImplementorA`로 변경되던 `ConcreteImplementorB`로 변경되던 아무런 영향을 받지 않습니다.

![Bridge](https://github.com/tango1202/tango1202.github.io/assets/133472501/7522dff5-3aae-4066-b512-1fa21dc0e461)

|항목|내용|
|--|--|
|`Abstraction`|추상부 입니다.|
|`Implementor`|구현부 입니다. 구현부를 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)로 만들어 다형적으로 확장할 수 있습니다.|
|`ConcreteImplementorA, ConcreteImplementorB`|`Implementor`를 구체화한 개체입니다.|
|`Client`|`Abstraction`을 사용하는 개체입니다. `Implementor`는 `Client`로부터 완전히 은닉되어 코드간의 종속성이 없습니다.|

# 특징

구현부를 은닉하여 코드간의 종속성이나 컴파일 종속성을 최소화 할 수 있으며, 구현부를 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)로 만들어 런타임 다형성을 구현할 수 있습니다.

# 예제

렌더링 엔진은 운영체제에 따라 다를 수 있고, 선호하는 방식에 따라 다를 수도 있습니다.(*예를 들어 Windows 에서도 GDI, GDI+, WPF가 번갈아 사용될 수도 있습니다.*)

이런 경우 다음처럼 렌더링을 할때마다 조건 검사를 하면, 

```cpp
void Draw() {
    if (IsGDI()) {
        //...
    }
    else {
        // ...
    }
}
```

조건 검사 코드가 여기저기 흩어져 많이 지저분해질 수 있고, 본연의 출력 로직 개발에 방해가 될 수 있습니다.

이런 경우 [Bridge](https://tango1202.github.io/pattern/pattern-bridge/)를 이용하여 추상부인 `Render`와 구현부인 `RenderImpl`을 나누면 좀더 간결하게 문제를 해결할 수 있습니다.

전체적인 구조는 다음과 같습니다.

1. `Rectangle`은 추상화된 `Render`만 사용하며, `RenderImpl`에 독립적입니다.
2. `RenderImpl`은 GDI 버전과 WPF 버전이 있으며, `Render::SetImpl()` 함수로 런타임에 설정할 수 있습니다. 
3. 외부에서 GDI와 WPF를 결정해서 전달해 주므로, `Rectangle`은 GDI인지, WPF인지 고민없이 본연의 출력 로직에만 집중하여 작성하면 됩니다.

![Bridge](https://github.com/tango1202/tango1202.github.io/assets/133472501/2766dcb1-e756-4ffb-8fcf-d0a4edea0f7b)

1. #1 : `Render` 추상부 입니다. 출력에 필요한 기본적인 함수들을 추상화합니다.
2. #2 : `RenderImpl`과의 컴파일 종속성을 없애기 위해 `Render`의 선언과 정의를 분리했습니다.
3. #3 : `RenderImpl`은 `Render`에서 필요로 하는 인터페이스 함수들을 제공합니다.
4. #4 : `RenderImpl`을 GDI 또는 WPF로 구체화합니다.
5. #5 : `SetImpl()`함수로 구현부를 런타임에 변경할 수 있습니다.
6. #6 : `Rectangle`(*Client*)에서는 어떤 렌더링 엔진을 사용하는지 고민하지 않고 본연의 드로잉 코드만 작성하면 됩니다.

```cpp
class RenderImpl; // #2

// ----
// #1. 추상부 입니다. m_Impl을 이용합니다.
// ----
class Render {
    std::unique_ptr<RenderImpl> m_Impl;
public:
    explicit Render(std::unique_ptr<RenderImpl> impl) : m_Impl{std::move(impl)} {}
private:
    Render(const Render&) = delete; 
    Render(Render&&) = delete; 
    Render& operator =(const Render&) = delete; 
    Render& operator =(Render&&) = delete;   
public:
    // #5. 구현부를 변경합니다.
    void SetImpl(std::unique_ptr<RenderImpl> impl) {
        m_Impl = std::move(impl);
    }

    // #2. RenderImpl에 컴파일 종속성이 있어 선언과 정의를 분리했습니다. 
    void DrawLine(int x1, int y1, int x2, int y2);
    void DrawImage(int l, int t, const char* filename);
};

// ----
// Client 입니다. 추상부인 Render만 사용하며, RenderImpl에 독립적입니다.
// ----
class Rectangle {
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : m_Left{l}, m_Top{t}, m_Width{w}, m_Height{h} {}

    // #6. 어떤 렌더링 엔진을 사용하는지 고민하지 않고 본연의 드로잉 코드만 작성합니다.
    void Draw(Render& render) const { 
        int right{m_Left + m_Width};
        int bottom{m_Top + m_Height};

        render.DrawLine(m_Left, m_Top, right, m_Top);
        render.DrawLine(right, m_Top, right, bottom);
        render.DrawLine(right, bottom, m_Left, bottom);
        render.DrawLine(m_Left, bottom, m_Left, m_Top);
    }
};
// ----
// #3. 구현부의 추상 클래스입니다.
// ----
class RenderImpl {
protected:
    RenderImpl() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected    
private:
    RenderImpl(const RenderImpl&) = delete; 
    RenderImpl(RenderImpl&&) = delete; 
    RenderImpl& operator =(const RenderImpl&) = delete; 
    RenderImpl& operator =(RenderImpl&&) = delete;   
public:
    virtual ~RenderImpl() = default; // 다형 소멸 하도록 public virtual

    virtual void DrawLineImpl(int x1, int y1, int x2, int y2) = 0;
    virtual void DrawImageImpl(int l, int t, const char* filename) = 0;
};

// ----
// #2. Render 정의부입니다. RenderImpl에 컴파일 종속성이 있어 선언과 정의를 분리했습니다. 
// ----
void Render::DrawLine(int x1, int y1, int x2, int y2) {
    m_Impl->DrawLineImpl(x1, y1, x2, y2);
}
void Render::DrawImage(int l, int t, const char* filename) {
    m_Impl->DrawImageImpl(l, t, filename);
}

// ----
// #4. RenderImpl의 실제 구현부 입니다.
// ----
class GDIRenderImpl : public RenderImpl {
public:
    virtual void DrawLineImpl(int x1, int y1, int x2, int y2) override {
        std::cout << "GDIRenderImpl::DrawLineImpl()" << std::endl;
    }
    virtual void DrawImageImpl(int l, int t, const char* filename) override {
        std::cout << "GDIRenderImpl::DrawImageImpl()" << std::endl;
    }
};
class WPFRenderImpl : public RenderImpl {
public:
    virtual void DrawLineImpl(int x1, int y1, int x2, int y2) override {
        std::cout << "WPFRenderImpl::DrawLineImpl()" << std::endl;
    }
    virtual void DrawImageImpl(int l, int t, const char* filename) override {
        std::cout << "WPFRenderImpl::DrawImageImpl()" << std::endl;
    }
};  

// ----
// 테스트 코드
// ----
Render render{std::unique_ptr<RenderImpl>{new GDIRenderImpl{}}};
Rectangle rect{0, 0, 10, 20};

// GDIRenderImpl로 실행합니다.
rect.Draw(render);

// #5. 런타임에 구현부를 WPFRenderImpl로 변경하여 실행할 수 있습니다.
render.SetImpl(std::unique_ptr<RenderImpl>{new WPFRenderImpl{}});
rect.Draw(render);
```
