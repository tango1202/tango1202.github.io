---
layout: single
title: "#20. [모던 C++] 가변 템플릿과 파라메터 팩(C++11, C++17)"
categories: "cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [가변 템플릿](https://tango1202.github.io/cpp/modern-cpp-variadic-template/)과 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)이 추가되어, [가변 인자(…)](
https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)와 같이 갯수와 타입이 정해 지지 않은 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 사용할 수 있습니다.
> * (C++11~) [sizeof...() 연산자](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [가변 템플릿](https://tango1202.github.io/cpp/modern-cpp-variadic-template/)에서 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)의 인자수를 구할 수 있습니다.
> * (C++17~) [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)이 추가되어 [가변 템플릿](https://tango1202.github.io/cpp/modern-cpp-variadic-template/)에서 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)을 재귀적으로 반복하여 전개할 수 있습니다.

# 개요

[가변 인자(…)](
https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)에서 `...`을 이용한 [가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 소개해 드렸는데요(*[가변 인자(…)](
https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 참고*), 

C++11 부터는 [가변 템플릿](https://tango1202.github.io/cpp/modern-cpp-variadic-template/)과 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)이 추가되어, [가변 인자(…)](
https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)와 같이 갯수와 타입이 정해 지지 않은 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 사용할 수 있습니다.

[가변 템플릿](https://tango1202.github.io/cpp/modern-cpp-variadic-template/)을 이용하면 다음처럼 재귀적으로 호출하여 합계를 구현할 수 있습니다.

```cpp
template<typename Type>
Type Sum_11(Type param) {
    return param; // 재귀 호출하다가 마지막에 도달하면 호출됩니다.
}

// Types는 파라메터 팩입니다.
template<typename Type, typename... Types>
Type Sum_11(Type param, Types... params) {
    // 재귀 호출시 params의 첫번째 인자인 params[1]은 param으로 전달되고, 
    // 나머지 params[2] ~ params[N] 은 params에 전달됩니다.
    return param + Sum_11(params...); 
} 

int val{Sum_11(1, 3, 5)};
EXPECT_TRUE(val == 1 + 3 + 5);  
```

# 파라메터 팩 배포 및 확장

[함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달된 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)은 `params...`와 같이 파라메터명 뒤에 `...`을 붙여 사용할 수 있습니다. 

|항목|내용|
|--|--|
|`template<typename... Types>`|여러 타입들로 구성된 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)입니다.|
|`template<int... Values>`|[비타입 템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)(예를 들어 `int`)로 구성된[파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)입니다.|

다음과 같이 표현식을 이용한 패턴으로 확장하여 배포할 수 있습니다.(*패턴에는 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5) 이름이 포함되어야 합니다.*)

```cpp
f(params...); // f(params[1], params[2], params[3]) 로 전개됩니다.
f(++params...); // f(++params[1], ++params[2], ++params[3]) 로 전개됩니다.
f((params + 1)...); // f((params[1] + 1), (params[2] + 1), (params[3] + 3)) 로 전개됩니다.
f(&params...); // f(&params[1], &params[2], &params[3]) 로 전개됩니다.
f(vector[params]...); // f((vector[params[1]], vector[params[2]] vector[params[3]]) 로 전개됩니다.
f(g(params)...); // f(g(params[1]), g(&params[2]), g(&params[3])) 로 전개됩니다.
f(g(params...) + params...); // f(g(params[1], params[2], params[3]) + params[1], 
                             //   g(params[1], params[2], params[3]) + params[2], 
                             //   g(params[1], params[2], params[3]) + params[3]) 로 전개됩니다.
```

다음은 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)을 확장하여 배포한 예입니다. `Func_11()` 함수는 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달받은 `params`를 전개할 때 `(params + 1)`을 하여 1씩 더해서 배포합니다.

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

> *(C++17~) [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)이 추가되어 [가변 템플릿](https://tango1202.github.io/cpp/modern-cpp-variadic-template/)에서 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)을 재귀적으로 반복하여 전개할 수 있습니다.*

# 가변 템플릿을 이용한 포워딩 함수

또한, 다음처럼 [전달 참조](https://tango1202.github.io/cpp/modern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)와 [forward()](https://tango1202.github.io/cpp/modern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)를 이용하면 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 갯수나 타입이 가변적인 포워딩 함수도 손쉽게 만들 수 있습니다.(*[forward() 원리](https://tango1202.github.io/cpp/modern-cpp-forwarding-reference/#forward-%EC%9B%90%EB%A6%AC) 참고*)

```cpp
// func(params...)를 호출합니다.
template<typename Func, typename... Params>
int Forwarding_11(Func func, Params&&... params) {
    return func(std::forward<Params>(params)...);
}

int MySum(int a, int b, int c) {
    return a + b + c;
}

EXPECT_TRUE(Forwarding_11(MySum, 1, 2, 3) == 1 + 2 + 3);   
```

> *(C++17~) [invoke()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c17-invoke)가 추가되어 일반 함수와 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 동일한 방식으로 호출할 수 있습니다. 상기 `Forwarding_11()`과 유사합니다.*

# sizeof...() 연산자

C++11 부터는 [sizeof...() 연산자](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되었습니다.

[sizeof() 연산자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)는 개체의 크기를 리턴하는데요(*[sizeof() 연산자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90) 참고*), [sizeof...() 연산자](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)는 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)의 인자수를 리턴합니다.

```cpp
template<typename... Types>
int Func_11(Types... params) {
    return sizeof...(params);
}

EXPECT_TRUE(Func_11(1, 2, 3) == 3);
```

# (C++17~) Fold 표현식

기존에는 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)에서 패턴을 활용하여 확장하여 전개할 수 있었는데요(*[파라메터 팩 배포 및 확장](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5) 참고*), 패턴이 단순히 반복되는 형태였죠.

```cpp
int Sum(int a, int b, int c) {
    return a + b + c;
}
template<typename... Params>
int Func_11(Params... params) {
    return Sum((params + 1)...); // Sum(params[1] + 1, params[2] + 1, params[3] + 1) 으로 전개됩니다.
} 

EXPECT_TRUE(Func(1, 2, 3) == 2 + 3 + 4);
```

C++17 부터는 [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)을 이용하여 [파라메터 팩](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)을 재귀적으로 반복하여 전개할 수 있습니다.

|항목|내용|
|--|--|
|`(params op...)`|단항 오른쪽 [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)<br/>`(params[1] op (... op (params[N-1] op params[N])))` 로 전개|
|`(params op...op init)`|이항 오른쪽 [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)<br/>`(params[1] op (... op (params[N] op init)))` 로 전개|
|`(...op params)`|단항 왼쪽 [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)<br/>`(((params[1] op params[2]) op ...) op params[N])` 로 전개|
|`(init op...op params)`|이항 왼쪽 [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)<br/>`(((init op params[1]) op ...) op params[N])` 로 전개|

`op`는 다음 연산자들을 사용할 수 있습니다.

`+` `-` `*` `/` `%` `^` `&` `|` `=` `<` `>` `<<` `>>` `+=` `-=` `*=` `/=` `%=` `^=` `&=` `|=` `<<=` `>>=` `==` `!=` `<=` `>=` `&&` `||` `,` `.*` `->*`

`(params +...)`에서 괄호 자체도 [Fold 표현식](https://tango1202.github.io/cpp/modern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)의 일부 입니다. 따라서 `return params +...;`와 같이 괄호를 생략하면 컴파일 오류가 발생합니다.

```cpp
template<typename... Params>
int UnaryRightFold_17(Params... params) {
    // (params[1] + (나머지 전개)) 
    // (params[1] + (params[2] + (나머지 전개)))
    // (params[1] + (params[2] + (params[3] + (나머지 전개))))
    // 즉, (params[1] op (... op (params[N-1] op params[N]))) 으로 전개 됩니다.        
    return (params +...); 
}  
// UnaryRightFold_17 과 동일하게 전개되나 마지막에 init 을 추가합니다.
template<typename... Params>
int BinaryRightFold_17(int init, Params... params) {
    // (params[1] + (나머지 전개 + val)) 
    // (params[1] + (params[2] + (나머지 전개 + val)))
    // (params[1] + (params[2] + (params[3] + (나머지 전개))))
    // 즉, (params[1] op (... op (params[N] op init))) 으로 전개 됩니다.        
    return (params +...+ init); 
} 
template<typename... Params>
int UnaryLeftFold_17(Params... params) {
    // ((나머지 전개) + params[N]) 
    // (((나머지 전개) + params[N-1]) + params[N]) 
    // ((((나머지 전개) + params[N-2]) + params[N-1]) + params[N])         
    // 즉, (((params[1] op params[2]) op ...) op params[N]) 으로 전개 됩니다.        
    return (...+ params); 
}   
// UnaryLeftFold_17 과 동일하게 전개되나 마지막에 init 을 추가합니다.
template<typename... Params>
int BinaryLeftFold_17(int init, Params... params) {
    // ((나머지 전개) + params[N]) 
    // (((나머지 전개) + params[N-1]) + params[N]) 
    // ((((나머지 전개) + params[N-2]) + params[N-1]) + params[N])         
    // 즉, (((init op params[1]) op ...) op params[N]) 으로 전개 됩니다.        
    return (init +...+ params); 
}

EXPECT_TRUE(UnaryRightFold_17(1, 2, 3) == 1 + 2 + 3);
EXPECT_TRUE(BinaryRightFold_17(10, 1, 2, 3) == 1 + 2 + 3 + 10);  

EXPECT_TRUE(UnaryLeftFold_17(1, 2, 3) == 1 + 2 + 3);
EXPECT_TRUE(BinaryLeftFold_17(10, 1, 2, 3) == 10 + 1 + 2 + 3); 
```