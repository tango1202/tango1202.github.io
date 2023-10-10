---
layout: single
title: "#24. [모던 C++] (C++11~) 가변 템플릿, 파라메터(인자) 팩, sizeof...() (C++17~) Fold 표현식"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) 가변 인자를 활용한 가변 템플릿이 추가되어 코딩 자유도가 높아졌습니다.
> * (C++11~) `sizeof...()`가 추가되어 가변 템플릿의 가변 인자인 파라메터 팩의 인자수를 구할 수 있습니다.
> * (C++17~) Fold 표현식을 이용하여 가변 템플릿에서 파라메터 팩을 재귀적으로 반복하여 전개할 수 있습니다.

# 개요

[인자(매개변수, Parameter)](
https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 에서 `...`을 이용한 가변 인자를 소개해 드렸는데요, 

C++11 부터는 템플릿에서도 가변 인자를 사용할 수 있고, 이를 가변 템플릿 이라 합니다. 가변 템플릿은 `...`으로 표기되는 파라메터 팩을 가져야 합니다.

가변 템플릿을 이용해서 `Sum` 을 구현하면 다음처럼 할 수 있습니다.

```cpp
template<typename Type>
Type Sum_11(Type param) {
    return param; // 재귀 호출하다가 마지막에 도달하면 호출됩니다.
}
template<typename Type, typename... Types>
Type Sum_11(Type param, Types... params) {
    return param + Sum_11(params...); // 재귀 호출시 params의 첫번째 인자는 param으로 전달되고, 나머지는 params에 전달됩니다.
} 

int val{Sum_11(1, 3, 5)};
EXPECT_TRUE(val == 1 + 3 + 5);  
```

또한, 다음처럼 포워딩 함수를 손쉽게 만들 수 있습니다.

```cpp
// func(params...) 를 호출합니다.
template<typename Func, typename... Params>
int Forwarding_11(Func func, Params... params) {
    return func(params...);
}

int MySum(int a, int b, int c) {
    return a + b + c;
}

EXPECT_TRUE(Forwarding_11(MySum, 1, 2, 3) == 1 + 2 + 3);   
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
int Func_11(Params... params) {
    return Sum((params + 1)...); // 파라메터 팩의 각 요소에 1을 더해 배포합니다.
}

EXPECT_TRUE(Func_11(1, 2, 3) == 2 + 3 + 4);
```

# sizeof...() 연산자

C++11 부터는 `sizeof...()` 연산자가 추가되었습니다.

[sizeof() 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)는 개체의 크기를 리턴하는데요, `sizeof...()`은 파라메터 팩의 인자수를 리턴합니다.

```cpp
template<typename... Types>
int Func_11(Types... params) {
    return sizeof...(params);
}

EXPECT_TRUE(Func_11(1, 2, 3) == 3);
```

# (C++17~) Fold 표현식

기존에는 파라메터 팩에서 패턴을 활용하여 확장하여 전개할 수 있었는데요, 패턴이 단순히 반복되는 형태입니다.

```cpp
int Sum(int a, int b, int c) {
    return a + b + c;
}
template<typename... Params>
int Func_11(Params... params) {
    return Sum((params + 1)...); // Sum(param1 + 1, param2 + 1, param2 + 1) 으로 전개됩니다.
} 

EXPECT_TRUE(Func(1, 2, 3) == 2 + 3 + 4);
```

C++17 부터는 Fold 표현식을 이용하여 파라메터 팩을 재귀적으로 반복하여 전개할 수 있습니다.

|항목|내용|
|--|--|
|`(params op...)`|단항 오른쪽 Fold<br/>`(param[1] op (... op (param[N-1] op param[N])))` 로 전개|
|`(params op...op init)`|이항 오른쪽 Fold<br/>`(param[1] op (... op (param[N] op init)))` 로 전개|
|`(...op params)`|단항 왼쪽 Fold<br/>`(((param[1] op param[2]) op ...) op param[N])` 로 전개|
|`(init op...op params)`|이항 왼쪽 Fold<br/>`(((init op param[1]) op ...) op param[N])` 로 전개|

`op`는 다음 연산자들을 사용할 수 있습니다.

`+` `-` `*` `/` `%` `^` `&` `|` `=` `<` `>` `<<` `>>` `+=` `-=` `*=` `/=` `%=` `^=` `&=` `|=` `<<=` `>>=` `==` `!=` `<=` `>=` `&&` `||` `,` `.*` `->*`

`(params +...)`에서 괄호 자체도 Fold 표현식의 일부 입니다. 따라서 `return params +...;`와 같이 괄호를 생략하면 컴파일 오류가 발생합니다.

```cpp
template<typename... Params>
int UnaryRightFold_17(Params... params) {
    // (param[1] + (나머지 전개)) 
    // (param[1] + (param[2] + (나머지 전개)))
    // (param[1] + (param[2] + (param[3] + (나머지 전개))))
    // 즉, (param[1] op (... op (param[N-1] op param[N]))) 으로 전개 됩니다.        
    return (params +...); 
}  
// UnaryRightFold_17 과 동일하게 전개되나 마지막에 init 을 추가합니다.
template<typename... Params>
int BinaryRightFold_17(int init, Params... params) {
    // (param[1] + (나머지 전개 + val)) 
    // (param[1] + (param[2] + (나머지 전개 + val)))
    // (param[1] + (param[2] + (param[3] + (나머지 전개))))
    // 즉, (param[1] op (... op (param[N] op init))) 으로 전개 됩니다.        
    return (params +...+ init); 
} 
template<typename... Params>
int UnaryLeftFold_17(Params... params) {
    // ((나머지 전개) + param[N]) 
    // (((나머지 전개) + param[N-1]) + param[N]) 
    // ((((나머지 전개) + param[N-2]) + param[N-1]) + param[N])         
    // 즉, (((param[1] op param[2]) op ...) op param[N]) 으로 전개 됩니다.        
    return (...+ params); 
}   
// UnaryLeftFold_17 과 동일하게 전개되나 마지막에 init 을 추가합니다.
template<typename... Params>
int BinaryLeftFold_17(int init, Params... params) {
    // ((나머지 전개) + param[N]) 
    // (((나머지 전개) + param[N-1]) + param[N]) 
    // ((((나머지 전개) + param[N-2]) + param[N-1]) + param[N])         
    // 즉, (((init op param[1]) op ...) op param[N]) 으로 전개 됩니다.        
    return (init +...+ params); 
}

using namespace VariadicTemplate_3;
EXPECT_TRUE(UnaryRightFold_17(1, 2, 3) == 1 + 2 + 3);
EXPECT_TRUE(BinaryRightFold_17(10, 1, 2, 3) == 1 + 2 + 3 + 10);  

EXPECT_TRUE(UnaryLeftFold_17(1, 2, 3) == 1 + 2 + 3);
EXPECT_TRUE(BinaryLeftFold_17(10, 1, 2, 3) == 10 + 1 + 2 + 3); 
```