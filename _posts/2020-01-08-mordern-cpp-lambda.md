---
layout: single
title: "#8. [모던 C++] (C++11~) 람다 표현식"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


`[캡쳐](인자들) -> 리턴 타입 {표현식}`

```cpp
[](int a, int b) -> int {return a + b;}
```

이는 다음의 함수 정의와 같습니다.

```cpp
int f(int a, int b) {
    return a + b;
}
```

함수를 호출하려면 f(10, 20) 와 같이 호출하는데요, 람다 표현식으로부터 생성된 개체(이를 **클로저**라 합니다.)를 

1. 변수에 저장한뒤 호출할 수 있고,
    ```cpp
    auto f = [](int a, int b) -> int {return a + b;};

    f(10, 20);
    ```

2. `()`을 붙여 람다 표현식으로부터 생성된 클로저 개체에서 바로 호출할 수 있습니다.
    ```cpp
    [](int a, int b) -> int {return a + b;}(10, 20);
    ```

인자나 리턴값이 없다면 다음과 같이 생략 할 수 있습니다.

[] {std::cout<<"lambda"<<endl;}();

# 캡처

람다 표현식 바깥 부분의 개체 정보들을 람다식 내부에 전달합니다.

[=] 외부의 모든 변수를 값으로 가져옵니다.
[&] 외부의 모든 변수의 참조자를 가져옵니다.
[=, &x, &y] 외부의 모든 변수를 값으로 가져오되 x, y 만 참조로 가져옵니다.
[&, x, y] 외부의 모든 변수의 참조자를 가져오되 x, y만 값으로 가져옵니다.

```cpp
int sum = 0;
std::vector<int> v{1, 2, 3};

for_each(
    v.begin(), 
    v.end(),
    [&sum](int val) {sum += val;}
);
EXPECT_TRUE(sum == 1 + 2 + 3);
```

**캡처 시기**

클로저 개체가 생성될 때 캡쳐합니다.

```cpp
int val = 1;
auto f = [=]() -> int {return val;};
val = 2;

EXPECT_TRUE(f() == 1); // 실행했을때가 아닌 캡쳐할 때의 값
```

다음은 참조를 이용한 캡처 입니다. 값의 결과가 2로 달라지는데요, 이는 캡처시기에 값을 복사한게 아니라 참조만 했기 때문입니다.

```cpp
int val = 1;
auto f = [&]() -> int {return val;};
val = 2;

EXPECT_TRUE(f() == 2); // 실행했을때가 아닌 캡쳐할 때의 참조 개체
```

**mutable**

mutable 속성을 추가하면, 값으로 받더라도 내용을 변경할 수 있습니다.

```cpp
int val = 1;
auto f = [=]() {val = 10}; // (X) 컴파일 오류

EXPECT_TRUE(f() == 10); 
```

```cpp
int val = 1;
auto f = [=]() mutable {val = 10}; // (O) 

EXPECT_TRUE(f() == 10); 
```

**개체의 멤버 변수 캡처**

암묵적으로 this를 캡쳐합니다.

```cpp
class T {
    int m_Member = 1;
public:
    int Func() {
        int local = 2;

        auto f = [=]() -> int {return m_Member + local}; 

        return f();
    }
};

T t;
EXPECT_TRUE(t.Func() == 3);
```

**클로저 개체 복사**

테스트할것