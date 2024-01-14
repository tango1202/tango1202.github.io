---
layout: single
title: "#8. [개체지향 원칙] 명시적 의존성 원칙(Explicit Dependencies Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

명시적 의존성 원칙은 **개체나 함수는 제대로 작동하는데 필요한 요소를 명시적으로 요청하라** 라는 원칙입니다.

조금 풀어 쓰면,

1. 클래스 생성시 필요한 요소를 명시적으로 요청하고,
2. 함수 호출시 전역이나 인프라 요소를 몰래 사용하지 말고, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 요청하라.

라는 뜻입니다. 

보통은 개체지향 세계에서 은닉을 하는게 일반적이지만, 명시적 의존성은 오히려 명시하라 합니다. 이는 개체나 함수가 의존하는 요소를 명시함으로서, 호출자가 필요한 요소를 파악할 수 있도록 돕기 위함입니다. 이에 따라 **코딩 계약** 의 신뢰성이 향상되고, 코드의 자체 문서화가 가능하며, 가독성이 좋아집니다.

**준수 방법 : 완전한 생성자**

`Rectangle`을 구현한다고 생각해 봅시다.

```cpp
class Rectangle {
private:
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    void SetLeft(int val) {m_Left = val;}
    void SetTop(int val) {m_Top = val;}
    void SetWidth(int val) {m_Width = val;}
    void SetHeight(int val) {m_Height = val;}
};
```

상기 `Rectangle`은 생성후 값을 세팅해 주어야만 사용할 수 있습니다.

```cpp
Rectangle rect;
rect.SetLeft(0);
rect.SetTop(0);
rect.SetWidth(10);
rect.SetHeight(10);
```

이렇게 필요한 요소를 생성 후 일일이 세팅하는건 번거롭습니다. 또한 번거로움은 둘째치고, 사용이 어렵고, 클래스 내부 구조를 완전히 파악해야만 하는 부담이 있습니다.(내부 구조 파악을 필요로 하니 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)도 위반이군요.)

다음과 같이 생성시에 필요한 값을 모두 전달하는 [완전한 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90)로 해결할 수 있습니다.

```cpp
class Rectangle {
private:
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    // 생성시에 필요한 값을 모두 전달해야 합니다.
    Rectangle(int l, int t, int w, int h) : 
        m_Left(l), m_Top(t), m_Width(w), m_Height(h) {}
  ...
};
```

이제 `Rectangle`은 필요한 모든 요소를 제공해야만 생성할 수 있습니다.

```cpp
Rectangle rect(0, 0, 10, 20);
```

**준수 방법 : 완전한 함수**

화면에 표시되는 view의 중심에서 `Rectangle`을 회전 시켜 봅시다. 다음과 같이 `RotateAtViewCenter()` 함수를 만들면 어떨까요?

```cpp
void RotateAtViewCenter(const Degree& delta) {
    // view 중심점을 구합니다.
    const View* view = GetOwnerView();
    float x = view->GetCenterX();
    float y = view->GetCenterY();
    
    // x, y에 맞춰 회전시킵니다.
    RotateAt(delta, x, y);
} 
```
복잡하게 view를 다루는 코드가 함수내에 은닉되어 있으니, 좋은것 같아 보이지만, 하기 그림처럼 의존성이 [상호 참조](https://tango1202.github.io/cpp-stl/modern-cpp-stl-shared_ptr-weak_ptr/#%EC%83%81%ED%98%B8-%EC%B0%B8%EC%A1%B0)되어 **의존성 부패** 가 발생합니다.(결합도가 증가하니 이또한 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 위반이군요.)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2f02246b-fd3f-4669-8f1c-0272fcdda58f)

위의 예에 슬쩍 써보긴 했습니다만, `RotateAt()` 함수 하나만 구현하고, 외부에서 필요한 정보를 명시적으로 인수로 전달할 수 있게 강제하여, 의존성 관계를 끊어주시는게 좋습니다. 

```cpp
class Rectangle {
    ...
    // 함수 호출시에 필요한 인수를 모두 전달해야 합니다.
    void RotateAt(const Degree& delta, float x, float y);
};
```

이젠 외부에서 `view` 중심점을 구해 `RotateAt()`에 전달하여야 합니다. 함수가 필요로 하는 모든 요소를 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 제공했기 때문에([완전한 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%ED%95%A8%EC%88%98) 참고), **코딩 계약** 이 좀더 투명해 졌습니다. 

```cpp
// view 중심점을 구합니다.
float x = view->GetCenterX();
float y = view->GetCenterY();
    
// view 중심점에 맞춰 회전시킵니다.
rect.RotateAt(delta, x, y); 
```
