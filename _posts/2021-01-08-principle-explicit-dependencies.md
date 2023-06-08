---
layout: single
title: "#8. [개체지향 원칙] 명시적 의존성 원칙(Explicit Dependencies Principle)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

명시적 의존성 원칙은 **개체나 함수는 제대로 작동하는데 필요한 요소를 명시적으로 요청하라** 는 원칙입니다.

조금 풀어 쓰면,

1. 클래스 생성시 필요한 요소를 명시적으로 요청하고,
2. 함수 호출시 전역이나 인프라 요소를 몰래 사용하지 말고 인자로 요청하라.

라는 뜻입니다. 

보통은 개체지향 세계에서 은닉을 하는게 일반적이지만, 명시적 의존성은 오히려 명시하라 합니다. 이는 개체나 함수가 의존하는 요소를 명시함으로서, 호출자가 필요한 요소를 파악할 수 있도록 돕기 위함입니다. 이에 따라 코딩 계약의 신뢰성이 향상되고, 코드의 자체 문서화가 가능하며, 가독성이 좋아집니다.

**준수 방법 : 완전한 생성자**

`Rectangle`을 구현한다고 생각해 봅시다.

```cpp
class Rectangle {
private:
    int left;
    int top;
    int width;
    int height;
public:
    void SetLeft(int val) { left = val; }
    void SetTop(int val) { top = val; }
    void SetWidth(int val) { width = val; }
    void SetHeight(int val) { height = val; }
};
```

상기 `Rectangle`은 하기와 같이 생성후 값을 세팅해 주어야만 사용할 수 있습니다.

```cpp
Rectangle rect;
rect.SetLeft(0);
rect.SetTop(0);
rect.SetWidth(10);
rect.SetHeight(10);
```

이렇게 필요한 요소를 생성 후 일일이 세팅하는건 번거로움은 둘째치고, 사용이 어렵고, 클래스 내부 구조를 완전히 파악해야만 하는 부담이 있습니다.(내부 구조 파악을 필요로 하니 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)도 위반이군요.)

하기와 같이 완전한 생성자를 만들어 해결할 수 있습니다.

```cpp
class Rectangle {
private:
    int left;
    int top;
    int width;
    int height;
public:
    Rectangle(int l, int t, int w, int h) : 
        left(l), top(t), width(w), height(h) {
    }
  ...
};
```

이제 `Rectangle`은 하기와 같이 필요한 모든 요소를 제공해야만 생성할 수 있습니다.

```cpp
Rectangle rect(0, 0, 10, 20);
```

**준수 방법 : 완전한 인자**

화면에 표시되는 view의 중심에 `Rectangle`을 회전 시켜 봅시다. 하기와 같이 함수를 만들면 어떨까요?

```cpp
void RotateAtViewCenter(const Degree& delta) {
    // view 중심점을 찾습니다.
    const View* view = GetOwnerView();
    float x = view->GetCenterX();
    float y = view->GetCenterY();
    
    // view 중심점에 맞춰 회전시킵니다.
    RotateAt(delta, x, y);
} 
```
복잡하게 view를 다루는 코드가 함수내에 있으니, 좋은것 같아 보이지만, 하기 그림처럼 의존성이 상호 참조 될 수 있습니다.(결합도가 증가하니 이또한 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 위반이군요.)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/8c7fd60c-42c6-4c1f-8f09-bd8b47c24a9e)

위의 예에 슬쩍 써보긴 했습니다만, 하기와 같이 함수에서 필요한 정보를 명시적으로 인자로 요청하셔서 의존성 관계를 끊어주시는게 좋습니다. 

```cpp
void RotateAt(const Degree& delta, float x, float y);
```


