---
layout: single
title: "#9. [개체지향 원칙] 묻지 말고 말하라 원칙(Tell, Don't Ask Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[묻지 말고 말하라 원칙](https://tango1202.github.io/principle/principle-tell-dont-ask/)은 ***개체의 내부 정보를 자꾸 묻지 말고 그냥 원하는 작업을 시켜라*** 라는 원칙입니다.

조금 풀어 쓰면,

1. 개체의 정보를 Getter로 획득해서 개체 외부에서 로직을 구현하지 말고,
2. 개체 정보가 은닉 되도록 내부에 로직을 구현하라.

라는 뜻입니다. 

하기 그림처럼 말이죠.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a48ece43-c49b-4f53-94ce-76ef2ff697e0)

이렇게 개체의 정보와 행동을 결합시켜 응집력을 강화하면 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)가 좋아지고, 은닉성도 좋아집니다. 정보가 은닉되어 있으면, 리팩토링시에 수정할 범위도 적어지고, 코드 확장도 용이 합니다.

복잡한 서브시스템을 가지고 있는 경우에는 [Facade](https://tango1202.github.io/pattern/pattern-facade/)로 구현하면 되고요.

**위반 사례**

예를 들어 `Rectangle`의 중심을 구한다고 생각해 봅시다.

```cpp
class Rectangle {
private:
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : 
        m_Left(l), 
        m_Top(t), 
        m_Width(w), 
        m_Height(h) {}
public:
    int GetLeft() const {return m_Left;}
    int GetTop() const {return m_Top;}
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}
};
```

왼쪽 상단 좌표와 너비와 높이로 부터 중심점을 구할 수 있습니다.

```cpp
Rectangle rect(0, 0, 10, 20);
int left = rect.GetLeft();
int top = rect.GetTop();
int width = rect.GetWidth();
int height = rect.GetHeight();

int centerX = left + width / 2;
int centerY = top + height / 2;
```
개체의 내부 정보인 `m_Left`, `m_Top`, `m_Width`, `m_Height`를 자꾸 물으므로 묻지 말고 말하라 원칙 위반입니다.

**준수 방법**

`Rectangle`에 내부 정보를 묻지 않고, `GetCenter()`함수를 멤버로 구현한다면, 묻지말고 말하라 원칙을 준수할 수 있습니다.

```cpp
class Rectangle {
private:
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : 
        m_Left(l),
        m_Top(t), 
        m_Width(w),
        m_Height(h) {}
public:
    std::pair<int, int> GetCenter() const { 
        return std::make_pair(m_Left + m_Width / 2, m_Top + m_Height / 2);
    }
};
```

다음과 같이 사용할 수 있습니다.

```cpp 
Rectangle rect(0, 0, 10, 20);
std::pair<int, int> center = rect.GetCenter();
```

이제 내부정보를 노출하는 Getter를 사용하지 않으므로, 별다른 영향도 없이 `Rectangle` 의 내부 정보를 취향껏 변경할 수 있습니다. 하기는 왼쪽/상단이 아닌 중심점을 멤버 변수로 사용합니다. 은닉성이 주는 이점입니다. 

```cpp
class Rectangle {
private:
    int m_CenterX; // 중심점을 멤버로 사용합니다.
    int m_CenterY;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : 
        m_CenterX(l + w / 2), 
        m_CenterY(t + h / 2),
        m_Width(w), 
        m_Height(h) {}
public:
    std::pair<int, int> GetCenter() const { 
        return std::make_pair(m_CenterX, m_CenterY);
    }
};
``` 
