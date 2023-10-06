---
layout: single
title: "#26. [모던 C++] (C++11~) 가변 템플릿, 파라메터(인자) 팩, sizeof...()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 가변 인자를 활용한 가변 템플릿이 추가되어 코딩 자유도가 높아졌습니다.
> * `sizeof...()`가 추가되어 가변 템플릿의 가변 인자인 파라메터 팩의 인자수를 구할 수 있습니다.

# 개요

[인자(매개변수, Parameter)](
https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 에서 `...`을 이용한 가변 인자를 소개해 드렸는데요, 

C++11 부터는 템플릿에서도 가변 인자를 사용할 수 있고, 이를 가변 템플릿 이라 합니다. 가변 템플릿은 `...`으로 표기되는 파라메터 팩을 가져야 합니다.

가변 템플릿을 이용해서 `Sum` 을 구현하면 다음처럼 할 수 있습니다.

```cpp
template<typename Type>
Type Sum(Type param) {
    return param; // 재귀 호출하다가 마지막에 도달하면 호출됩니다.
}
template<typename Type, typename... Types>
Type Sum(Type param, Types... params) {
    return param + Sum(params...); // 재귀 호출시 params의 첫번째 인자는 param으로 전달되고, 나머지는 params에 전달됩니다.
}

int val{Sum(1, 3, 5)};
EXPECT_TRUE(val == 1 + 3 + 5); 
```

또한, 다음처럼 포워딩 함수를 손쉽게 만들 수 있습니다.

```cpp

// func(params...) 를 호출합니다.
template<typename Func, typename... Params>
int Forwarding(Func func, Params... params) {
    return func(params...);
}

int MySum(int a, int b, int c) {
    return a + b + c;
}

EXPECT_TRUE(Forwarding(MySum, 1, 2, 3) == 1 + 2 + 3);
```

다만 `Forwarding()` 함수에서 인자를 전달할때 참조자 타입은 참조성이 제거되어 전달되기 때문에 [reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper)를 이용해야 합니다.([reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper) 참고)

|항목|내용|
|--|--|
|`template<typename... Types>`|여러 타입들로 구성된 매개변수 팩입니다.|
|`template<int... Values>`|비 템플릿 개체(예를 들어 `int`)로 구성된 매개변수 팩입니다.|

# 파라메터 팩 배포 및 확장

인자로 전달된 파라메터 팩은 `params...`와 같이 파라메터명 뒤에 `...`을 붙여 사용할 수 있습니다. 또한 다음과 같이 표현식을 이용한 패턴으로 확장하여 배포할 수 있습니다.(패턴에는 파라메터 팩 이름이 포함되어야 합니다.)

```cpp
f(params...); // f(param1, param2, param3) 로 전개되어 배포됩니다.
f(++params...); // f(++param1, ++param2, ++param3) 로 전개되어 배포됩니다.
f((params + 1)...); // f((param1 + 1), (param2 + 1), (param3 + 3)) 로 전개되어 배포됩니다.
f(&params...); // f(&param1, &param2, &param3) 로 전개되어 배포됩니다.
f(vector[params]...); // f((vector[param1], vector[param2] vector[param3]) 로 전개됩니다.
f(g(params)...); // f(g(param1), g(&param2), g(&param3)) 로 전개되어 배포됩니다.
f (g(params...)  + params...); // f(g(param1, param2, param3) + param1, f(g(param1, param2, param3) + param2, f(g(param1, param2, param3) + param3) 로 전개되어 배포됩니다. 
```

다음은 파라메터 팩을 확장하여 배포한 예입니다. `Func()` 함수는 인자로 전달받은 `params`를 전개할 때 `(params + 1)`을 하여 1씩 더해서 배포합니다.

```cpp
int Sum(int a, int b, int c) {
    return a + b + c;
}
template<typename... Params>
int Func(Params... params) {
    return Sum((params + 1)...); // 파라메터 팩의 각 요소에 1을 더해 배포합니다.
} 

EXPECT_TRUE(Func(1, 2, 3) == 2 + 3 + 4);
```

# sizeof...() 연산자

C++11 부터는 `sizeof...()` 연산자가 추가되었습니다.

[sizeof() 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)는 개체의 크기를 리턴하는데요, `sizeof...()`은 파라메터 팩의 인자수를 리턴합니다.

```cpp
template<typename... Types>
int Func(Types... params) {
    return sizeof...(params);
}

EXPECT_TRUE(Func(1, 2, 3) == 3);
```