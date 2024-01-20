---
layout: single
title: "#12. [디자인 패턴-구조 패턴] Proxy"
categories: "pattern"
tag: ["디자인 패턴", "구조 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Proxy](https://tango1202.github.io/pattern/pattern-proxy/)는 실제 개체의 동작을 대리해서 실행합니다. 최소한의 정보만을 이용하여 대리 실행하기 때문에, 생성이나 실행시 속도 부하가 있는 개체의 속도 개선을 위해 사용합니다.

# 설명

편집 프로그램은 처음 파일을 열면서 몇 페이지가 될지 미리 계산을 하고, 그에 따라 스크롤바를 표시합니다. 이때 문서내의 이미지를 모두 생성할 필요는 없습니다. 화면에 표시되는 현 페이지의 것만 생성하면 됩니다. 그외에 아직 화면에 표시되지 않는 이미지는 그려질 필요가 없으니 생성할 필요가 없습니다. 단지 스크롤바 계산을 위한 이미지의 크기만 알면 됩니다.

즉, 이미지의 생성은 비용이 비싸므로 최대한 미뤄두고, 이미지 크기 정보만 제공하는게 좋습니다. 미리 이미지를 생성해 두지 마세요.(*[제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/) 참고*)

이를 위해 [Proxy](https://tango1202.github.io/pattern/pattern-proxy/)를 이용할 수 있습니다.
크기 정보를 요청할때는 [Proxy](https://tango1202.github.io/pattern/pattern-proxy/)가 대리해서 처리하고, 실제 `Draw()`될때만 이미지를 생성합니다. 

다음 그림은 [Proxy](https://tango1202.github.io/pattern/pattern-proxy/)의 일반적인 구조입니다. 

`Client`가 `Request()`를 호출하면, `Proxy`가 이를 대리하여 처리하거나, `m_RealSubject.Request()`를 호출하여 실제 개체를 사용합니다.

![Proxy(](https://github.com/tango1202/tango1202.github.io/assets/133472501/8a166c74-65f3-480e-9548-e4044204dea8)

|항목|내용|
|--|--|
|`Subject`|`Client`에서 사용하는 개체의 인터페이스 입니다.|
|`Proxy`|`RealSubject`를 대리합니다.|
|`RealSubject`|`Subject`를 구체화한 개체로서 이미지등 지연 생성이 필요한 개체입니다.|
|`Client`|`Subject`를 사용합니다.|

# 특징

대리 실행은 `RealSubject`가 이미지처럼 개체 생성 비용이 비싸던가, 원격지의 개체여서 접근 비용이 비쌀때 유용합니다. 

# 예제

다음은 이미지를 [Proxy](https://tango1202.github.io/pattern/pattern-proxy/)를 이용하여 지연 생성하는 예입니다.

1. #1 : `Shape`은 `Subject`입니다. `Draw()`와 크기의 Get/Set 인터페이스를 제공합니다.
2. #2 : `Image`는 `RealSubject`입니다. 생성시 실제 이미지를 `Load()` 합니다.
3. #3: `ImageProxy`는 `Image` 생성 전에는 크기 정보 요청시 멤버 변수의 값으로 대리 실행하며, `Draw()` 시 실제 `Image`를 생성합니다.
4. #4 : `Draw()`한 뒤에는 `Image` 개체가 사용됩니다.

```cpp
// ----
// #1. Subject입니다. Draw()와 크기의 Get/Set 인터페이스를 제공합니다.
// ----
class Shape {
protected:
    Shape() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Shape() = default; // 다형 소멸 하도록 public virtual
private:
    Shape(const Shape&) = delete;
    Shape(Shape&&) = delete;
    Shape& operator =(const Shape&) = delete;
    Shape& operator =(Shape&&) = delete;          
public:
    virtual int GetWidth() const = 0;
    virtual int GetHeight() const = 0;

    virtual void SetWidth(int val) = 0;
    virtual void SetHeight(int val) = 0;    

    virtual void Draw() const = 0;   
};
// ----
// #2. RealSubject입니다. 생성시 실제 이미지를 Load() 합니다.
// ----
class Image : public Shape {
    int m_Width;
    int m_Height;
public:
    explicit Image(int w, int h, const char* filename) : m_Width{w}, m_Height{h} {
        Load(filename); // #2
    }
    virtual int GetWidth() const override {return m_Width;}
    virtual int GetHeight() const override {return m_Height;}

    virtual void SetWidth(int val) override {m_Width = val;}
    virtual void SetHeight(int val) override {m_Height = val;}    

    virtual void Draw() const {
        std::cout << "Image::Draw()" << std::endl;
    }
private:
    void Load(const char* filename) { // #2
        std::cout << "Image::Load()" << std::endl;
    }
};
// ----
// #3. Proxy입니다. Image 생성 전에는 크기 정보 요청시 멤버 변수의 값으로 대리 실행하며, Draw() 시 실제 Image를 생성합니다.
// ----
class ImageProxy : public Shape {
    int m_Width; // #3. m_Image가 만들어지지 않으면 사용합니다.
    int m_Height;   
    const char* m_Filename;     
    mutable std::unique_ptr<Image> m_Image; 
public:
    ImageProxy(int w, int h, const char* filename) :
        m_Width{w}, 
        m_Height{h}, 
        m_Filename{filename} {
        assert(filename);
    }

    virtual int GetWidth() const override {
        if (!m_Image) {
            return m_Width; // #3
        }
        return m_Image->GetWidth();
    }
    virtual int GetHeight() const override {
        if (!m_Image) {
            return m_Height; // #3  
        }
        return m_Image->GetHeight();
    }
    virtual void SetWidth(int val) override {
        if (!m_Image) {
            m_Width = val; // #3  
            return; 
        }
        m_Image->SetWidth(val);
    }
    virtual void SetHeight(int val) override {
        if (!m_Image) {
            m_Height = val; // #3 
            return; 
        }
        m_Image->SetHeight(val);
    } 

    // #3. m_Image가 없다면 생성합니다.      
    void Draw() const {
        if(!m_Image) {
            m_Image = std::unique_ptr<Image>{new Image{m_Width, m_Height, m_Filename}};
        }

        m_Image->Draw();
    }

    bool IsValid() const {
        return m_Image ? true : false;
    }
};

// ----
// 테스트 코드
// ----
ImageProxy image{10, 20, "MyImage.png"};

image.GetWidth(); // #3. Image를 생성하기 전에 Proxy를 이용하여 크기를 구합니다.
image.GetHeight();

EXPECT_TRUE(image.IsValid() == false);

image.Draw(); // 이미지를 로딩하여 그립니다.

EXPECT_TRUE(image.IsValid() == true); // #4  
```
