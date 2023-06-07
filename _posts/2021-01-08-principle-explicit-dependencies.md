---
layout: single
title: "#8. [개체지향 원칙] 명시적 의존성 원칙(Explicit Dependencies Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

명시적 의존성 원칙은 **클래스나 함수는 옳바르게 작동하는데 필요한 요소를 명시적으로 요청하라** 는 원칙입니다.

조금 풀어 쓰면,

1. 클래스 생성시 필요한 요소를 명시적으로 요청하고,
2. 함수 호출시 전역이나 인프라 요소를 몰래 사용하지 말고 인자로 요청하라.

라는 뜻입니다.

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
  void SetLeft(int val) { left = val;}
  void SetTop(int val) { top = val;}
  void SetWidth(int val) { width = val;}
  void SetHeight(int val) {height = val;}
};
```

상기 `Rectangle`응 하기와 같이 생성후 값을 세팅해 주어야만 사용할 수 있습니다.

```cpp
Rectangle rect;
rect.SetLeft(0);
rect.SetTop(0);
rect.SetWidth(10);
rect.SetHeight(10);
```

이렇게 필요한 요소를 생성 후 일일이 세팅하는건 번거로운건 둘째치고, 사용이 어렵고, 클래스 내부 구조를 완전히 파악해야만 하는 부담이 있습니다.(내부 구조 파악을 필요로 하니 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)도 위반이군요.)

하기와 같이 완전한 생성자를 만들어 해결할 수 있습니다.

```cpp
class Rectangle {
private:
  int left;
  int top;
  int width;
  int height;
public:
  Rectangle(int l, int t, int h, int h) : 
      left(l), top(t), width(w), height(h) {}
  
  ...
};
```

이제 `Rectangle`은 필요한 모드 요소를 제공해야만 생성할 수 있습니다.

```cpp
Rectangle rect(0, 0, 10, 20);
```

**준수 방법 : 완전한 인자**

완전한 생성자 : 나중에 Setter를 실행해야만 하는.

완전한 인자 : 전역요소, 인프라요소
