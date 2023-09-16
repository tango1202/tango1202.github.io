---
layout: single
title: "#7. [모던 C++] (C++11~) 중괄호 초기화"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `{}` 초기화가 추가되어 클래스, 배열, 구조체를 일관성 있게 초기화 할 수 있습니다.
> * `{}` 초기화시 인자의 암시적 형변환을 차단하여, 코딩 계약을 더 단단하게 할 수 있습니다.
> * `{}` 초기화로 리턴문 작성을 간소화할 수 있습니다.
> * `std::initializer_list` 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다.

# 중괄호 초기화

[기본 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B8%B0%EB%B3%B8-%EC%B4%88%EA%B8%B0%ED%99%94) 에서도 언급했듯 `()`을 사용하는 초기화 방법은 컴파일러가 다르게 해석(기본 생성자 호출을 함수 선언으로 오해)할 소지가 있습니다. 그리고, 클래스인지, 배열인지, 구조체 인지에 따라 때로는 괄호를 생략하거나, 괄호를 넣거나, 중괄호를 사용해야 하거나 뒤죽 박죽입니다.

```cpp
class T {
    int m_A;
    char m_B;
public:
    T() {}
    T(int a, char b) : m_A(a), m_B(b) {}    
};
T obj(); //(△) 비권장. T를 리턴하는 obj 함수 선언

T obj1; // 기본 생성자로 T 개체 생성
T obj2(T()); // 기본 생성자인 T()로 생성한 개체를 obj의 복사 생성자로 복사 생성
T obj3 = T(); // T obj(T());와 동일
T obj4(10, 'b'); // // m_A == 10, m_B == `b`인 T 개체 생성

T arr[] = {T(), T(10, 'b')}; // T 요소 2개인 배열 생성

struct U {
    int m_A;
    char m_B;
};
U objs = {10, 'b'}; // m_A == 10, m_B == `b`인 U 개체 생성
```

C++11 부터는 중괄호 초기화를 제공하여 클래스인지, 배열인지, 구조체의 구분없이 `{}`을 이용하여 일관성 있게 초기화 할 수 있습니다.

```cpp
class T {
    int m_A;
    char m_B;
public:
    T() {}
    T(int a, char b) : m_A(a), m_B(b) {}    
};

T obj1{}; // 기본 생성자로 T 개체 생성
T obj2(T{}); // 기본 생성자인 T()로 생성한 개체를 obj의 복사 생성자로 복사 생성
T obj3 = T{}; // T obj = T();와 동일
T obj4{10, 'b'}; // // m_A == 10, m_B == `b`인 T 개체 생성

T arr[]{T{}, T{10, 'b'}}; // T 요소 2개인 배열 생성

struct U {
    int m_A;
    char m_B;
};
U objs{10, 'b'}; // m_A == 10, m_B == `b`인 U 개체 생성   
```

# T t{}; 과 T t = {};

|항목|내용|
|--|--|
|`T t{};`|생성자를 이용하여 초기값을 전달합니다.|
|`T t = {};`|`T t = T{};` 표현과 동일합니다. 즉, `T{}`로 개체를 생성하고, 복사 생성자를 이용하여 `t`에 대입하는 표현입니다. 하지만 컴파일러 최적화에 의해 `T t{};`와 동일하게 작동합니다.|
|`T t;`<br/>`t = {};`|`T t;`<br/>`t = T{};`<br/>표현과 동일합니다. 즉, `T{}`로 개체를 생성하고, 대입 연산자를 이용하여 `t`에 대입하는 표현입니다.|

# 인자의 암시적 형변환 차단

중괄호 초기화는 생성자 인자의 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하기 때문에 코딩 계약이 더 단단합니다.

`T obj1(3.14);`은 `double`형이 `int`로 암시적으로 형변환되어 호출됩니다만([오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고), `T obj2{3.14};` 은 컴파일 오류를 발생시킵니다.

```cpp
class T {
public:
explicit T(int) {};
};

T obj1(3.14); // (△) 비권장. double 이 int로 암시적으로 변환되어 초기화 됩니다.
T obj2{3.14}; // (X) 컴파일 오류. 암시적 변환을 차단합니다.
```

# 리턴문 작성 간소화

중괄호식만 작성해도 개체를 생성하여 리턴합니다.

```cpp
class T {
    int m_A;
    char m_B;
public:
    T() {}
    T(int a, char b) : m_A(a), m_B(b) {}    
};

T Func() {
    return {10, 'b'}; // return T{10, 'b'}; 와 동일
}
```

# std::initializer_list

`std::initializer_list`개체를 이용하여 중괄호 초기화 식을 이용한 초기화가 가능합니다.

`vector`의 경우에도 요소를 입력할때 `push_back()`을 사용하여 코드 작성이 번거로웠는데([vector 의 삽입과 삭제](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C) 참고), `std::initializer_list` 생성자를 구현하여 좀 더 간편하게 요소 값을 초기화 할 수 있습니다.

```cpp
// 이전 방식
std::vector<int> v1;
v1.push_back(1);
v1.push_back(2);
EXPECT_TRUE(v1[0] == 1 && v1[1] == 2);

// {} 를 이용하여 initializer_list로 초기화
std::vector<int> v2{1, 2};
EXPECT_TRUE(v2[0] == 1 && v2[1] == 2);       

// = {} 형태도 지원
std::vector<int> v3 = {1, 2};
EXPECT_TRUE(v1[0] == 1 && v1[1] == 2); 
```

# 기존 생성자와 std::initializer_list 생성자와의 충돌

오버로딩 선택시 `std::initializer_list`를 사용한 버전을 최우선으로 선택합니다. 그러다 보니 기존 생성자들과 충돌할 수도 있는데요, `vector(size_t count);` 를 호출하기 위해 `std::vector<int> v2{2};` 와 같이 한다면, 요소가 2개인 `vector` 를 생성하는게 아니라, `2`값인 요소 1인 `vector`를 생성합니다.

```cpp
// 요소가 2개인 vector를 생성합니다.
std::vector<int> v1(2);
EXPECT_TRUE(v1.size() == 2 && v1[0] == 0 && v1[1] == 0);

// 요소값이 2인 vector를 생성합니다.
std::vector<int> v2{2};
EXPECT_TRUE(v2.size() == 1 && v2[0] == 2);
```

# auto와 std::initializer_list

`auto`와 함께 중괄호 초기화를 사용하면 다음처럼 `std::initializer_list` 개체로 추론되는데요, `{}` 만 사용할때와 `= {}` 로 사용할 때가 다르므로 주의해서 사용해야 합니다.

* `auto val{}` : 인자가 1개면 인자 타입으로 추론되고, 여러개면 컴파일 오류를 발생합니다.
* `auto val = {}` : 인자들의 타입이 동일하면 `std::initializer_list`로 추론됩니다.

```cpp
int a{1}; // a는 int
auto b{1}; // b는 int
auto c = {1}; // c는 initializer_list<int>
// auto d{1, 2}; // (X) 컴파일 오류. auto에서는 단일 개체 대입 필요    
auto e = {1, 2}; // e는 initializer_list<int>  
```
