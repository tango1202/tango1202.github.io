---
layout: single
title: "#11. [Legacy C++ 가이드] 상수 한정자(const), 변경 가능 지정자(mutable), 최적화 제한 한정자(volatile)"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/) 정확성을 지켜라.
> * 무조건 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 들이대라.
> * [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)와 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 메모리를 수정하지 않기 때문에 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않는다.

> **Mordern C++**
> * (C++11~) [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다.
> * (C++20~) [volatile의 일부가 deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c20-volatile-%EC%9D%BC%EB%B6%80-deprecate)되었습니다.

# 개요

[const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)인 개체나 함수를 사용하면, 메모리의 수정이 없으므로 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않습니다. [예외에 안전](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)한 프로그램을 위해, ***할 수 있는 한 최대한 많이 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)로 작성***하는 것이 좋습니다.

또한, **[상수성 계약](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)** 을 준수하세요. 계약 위반시 대부분 컴파일 오류가 발생하니, 계약을 지키기 까다롭지도 않습니다.

|항목|내용|
|--|--|
|[상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)|개체 정의시 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/) 한정자를 붙여 [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)를 만들 수 있습니다. [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)는 값을 변경할 수 없습니다.<br/>생성시 초기화 해야 합니다.|
|[상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)|[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)의 뒤에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/) 한정자를 붙여 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 만들 수 있습니다.<br/>[상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 개체의 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 변경할 수 없습니다.<br/>단, [mutable](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)로 정의된 개체는 수정 가능합니다.|

# 상수성 계약

[상수성 계약](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)은 값을 변경하지 않는다는 계약입니다. 메모리를 읽기만 하고 수정하지 않는다는 것이죠. 그러다 보니 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키지 않습니다. [논리적 상수성](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-logical-const/)인 경우만 빼고요.

1. [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)는 생성과 함께 [초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/) 되어야 합니다.(*컴파일 오류*)
2. [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)는 변경할 수 없습니다.(*컴파일 오류*)
3. [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 수정하지 않습니다.(*컴파일 오류, 단 [mutable](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)로 수정 가능합니다.*)
4. [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 몰래 수정할 수 있는 [포인터나 참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/)를 리턴하지 않습니다.(*컴파일 오류. 단, [const_cast](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)로 억지로 구현 가능하나 하지 마세요.*)
5. [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 내부 구현에서 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)만을 호출합니다.(*컴파일 오류*)
6. [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않습니다.(*일반적으로 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않습니다만, 복잡한 연산들이 있다면, 노력해서 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않도록 구현해야 합니다.*)
7. [mutable](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)로 [논리적 상수성](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-logical-const/)을 구현한 경우 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수 있습니다.(*이때에는 [예외 처리](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/)를 해야 합니다.*)

한마디로 ***상수는 상수만 접근한다***로 생각하면 됩니다. 한편, 비 상수는 바이러스처럼 전파됩니다. [비 상수 멤버 함수의 비 상수성 전파](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%B9%84-%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98%EC%9D%98-%EB%B9%84-%EC%83%81%EC%88%98%EC%84%B1-%EC%A0%84%ED%8C%8C)를 참고하세요.

# 상수 개체

[상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)는 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 붙여 정의합니다. 반드시 초기값을 설정해야 합니다. 

```cpp
const int x; // (X) 컴파일 오류. 초기값 없음
const int x = 10; // (O) x의 값은 이제 변경될 수 없음
```

포인터(*혹은 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)*)형 변수의 경우 `const*`와 같이 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)의 오른쪽에 `*`이 있으면 포인터가 참조하는 실제 데이터가 상수이고, 왼쪽에 `*`이 있으면 포인터형 변수가 상수입니다.(*[포인터(Pointer)와 참조자(Reference)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/) 언급*)

```cpp
int obj = 10;
int* p1 = &obj; // *p1 수정 가능. p1 수정 가능
*p1 = 20;

const int* p2 = &obj; // *p2 수정 불가. p2 수정 가능
*p2 = 20; // (X) 컴파일 오류
p2 = p1;

int* const p3 = &obj; // *p3 수정 가능. p3 수정 불가
*p3 = 20;
p3 = p1; // (X) 컴파일 오류

const int* const p4 = &obj; // *p4 수정 불가. p4 수정 불가
*p4 = 20; // (X) 컴파일 오류
p4 = p1; // (X) 컴파일 오류
```

# 복사 대입시 최상위 const 제거

[최상위 const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)란 포인터나 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 아닌 개체 타입의 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 말합니다. `const int*`나 `const int&` 말고 `const int`처럼요.

[최상위 const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)인 [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)는 그 값을 수정할 수 없지만, [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하여 복제본을 만들면 [상수성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)을 제거할 수 있습니다.

```cpp
const int constVal = 10;
constVal = 20; // (X) 컴파일 오류. 상수 개체는 수정할 수 없습니다.

const int a = constVal;
a = 20; // (X) 컴파일 오류. 상수 개체를 상수 개체로 복사 대입하면 수정할 수 없습니다.

int b = constVal;
b = 20; // (O) 상수 개체를 복사 대입하면 복제본은 수정할 수 있습니다.
EXPECT_TRUE(b == 20 && constVal == 10);
```

하지만, 포인터나 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 대입받는 것은 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 아니므로 [상수성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)을 유지해야 합니다.(*[const_cast](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)로 억지로 [상수성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)을 땔 수는 있습니다만 하지 마세요.*)

```cpp
int data = 10;
const int* constPtr = &data;
const int& constRef = data;

int* a = constPtr; // (X) 컴파일 오류. 상수 개체 포인터는 상수 개체 포인터로 받아야 합니다.
const int* b = constPtr; // (O)

int& c = *constPtr; // (X) 컴파일 오류. 상수 개체 포인터는 상수 개체 참조자로 받아야 합니다.
const int& d = *constPtr; // (O)

int& e = constRef; // (X) 컴파일 오류. 상수 개체 참조자는 상수 개체 참조자로 받아야 합니다.
const int& f = constRef; // (O) 

int* g = &constRef; // (X) 컴파일 오류. 상수 개체 참조자는 상수 개체 포인터로 받아야 합니다.
const int* h = &constRef; // (O)
```

[최상위 const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)인 [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)의 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)시 복제본의 [상수성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)이 제거될 수 있는 것은 참 당연한 것입니다. 복제본이니까요. 그런데, 이 당연한 규칙 때문에 복잡한 규칙들이 파생됩니다.

1. [오버로딩된 함수 결정 규칙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)에서 [최상위 const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)는 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입에서 제거하고 취급합니다.

    예를 들어 다음의 함수를 보면 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 `int`와 `const int`로 동일하지만, 둘다 `int` 타입의 인수나 `const int` 타입의 인수를 대입받을 수 있습니다. [오버로딩된 함수 결정](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)이 모호해지므로 아예 이둘을 동일 함수로 취급합니다.

    ```cpp
    int f(int a);
    int f(const int a);
    ```

2. [함수 템플릿 인수 추론](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)에서 [최상위 const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)는 무시됩니다.

    [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 할때 `int`와 `const int`가 구분된다면, 상기 1번 문제가 똑같이 발생하게 됩니다. 따라서, `const int`를 전달하더라도 [최상위 const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)는 무시하고 `int`로 취급합니다.

    ```cpp
    template<typename T>
    void f(T) {}

    const int a = 0;
    f(a); // f<int>(int)
    ```

# 리턴값의 상수성

[리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)에 무의미하게 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 붙일 필요는 없습니다. 의미에 맞게 붙이거나 떼야 합니다.

[리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 [기본 타입](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/)이라면 어짜피 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 복제되므로, [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 붙일 필요가 없거든요.

```cpp
// (O) 멤버 변수의 값을 리턴하는 const 함수
int GetX1() const {return m_X;} 

// (△) 비권장. 리턴값을 쓸데없이 const로 리턴하는 const 함수. 
const int GetX2() const {return m_X;} 
```

[멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)의 포인터를 리턴하는 경우, [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)인지 아닌지에 따라 포인터 [상수성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)을 맞춰서 리턴해야 합니다. 다음의 `GetX4()`처럼 맞지 않게 리턴하면, **상수성 계약** 위반입니다.(*억지로 [const_cast](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)를 하지 마세요.*)

```cpp
// (O) 멤버 변수의 값을 수정하지 않는 const 함수
const int* GetX3() const {return &m_X;}    

// (△) 비권장. 멤버 변수의 값을 몰래 수정할 수 있는 const 함수
int* GetX4() const {return const_cast<int*>(&m_X);}

// (O) 맴버 변수의 값을 수정하는 non-const 함수      
int* GetX5() {return &m_X;} 	                        
```

# 인자(함수 선언에 작성된 Parameter)의 상수성

인수가 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 복사된다면, [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)는 무의미 하므로 사용할 필요가 없습니다.(*`void f(const int x);`에서 `x`는 인수를 복사하므로, [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)는 무의미합니다. 또한 [오버로딩된 함수 결정 규칙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)을 참고하면, [오버로딩된 함수 결정](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)시 `f(const int x)` 는 `f(int x)`로 취급되는걸 알 수 있습니다.*)

```cpp
void f(int x); // (O) 인수를 x에 복사해서 사용함.
void f(const int x); // (△) 비권장. 인수를 x에 복사해서 쓰되 f에서 수정하지 않음. 호출하는 쪽에선 무의미
```

[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 포인터나 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 타입이라면, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 사용하여 전달된 인수(*함수에 전달하는 개체. Argument*)를 함수가 수정하는지, 수정하지 않는지 **코딩 계약**을 만들어 주는 게 좋습니다. 이런 정보들이 프로그래밍 환경을 좀더 쾌적하게 해주거든요.

```cpp
void f(int* x); // (O) x가 가리키는 값을 f에서 수정함.
void f(int& x); 

void f(const int* x); // (O) x가 가리키는 값을 f에서 수정하지 않음.
void f(const int& x);   
```
# 상수 멤버 함수

[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)의 뒤에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 붙여 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 만들 수 있습니다. [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 개체의 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 변경할 수 없습니다. 자세한 내용은 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 참고 하세요.

```cpp
class T {
private:
    int m_Val;
public:
    void Func() const { // 상수 멤버 함수입니다.
        m_Val = 10; // (X) 컴파일 오류. 상수 멤버 함수에서 멤버 변수를 수정할 수 없습니다.
    }
};
```

# 변경 가능 지정자(mutable)

지연 생성이나 캐쉬등 [논리적 상수성](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-logical-const/)인 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)에서 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 수정해야 할 때 사용합니다. [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키지 않습니다만, 이 경우엔 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킵니다. 따라서 꼭 [try-catch()로 예외 처리](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/)를 해주시기 바랍니다.

```cpp
TEST(TestClassicCpp, Mutable) {
    class T {
    public:
        // 메모리를 절약하기 위해 GetString() 으로 실제 데이터를 요청할때 문자열을 채울겁니다.
        mutable std::wstring m_Lazy; // const 함수에서 수정할 수 있습니다.
        
        // 개념적으로 내부 String을 리턴하므로 const 함수
        // 하지만 내부에서 m_Lazy를 세팅하기 때문에 mutable을 사용합니다.
        const std::wstring& GetString() const {
            if (m_Lazy.empty()) {
                m_Lazy = L"Lazy String"; // 예외가 발생할 수 있습니다.
            }
            return m_Lazy;
        }
    };
    T t;
    EXPECT_TRUE(t.GetString() == L"Lazy String");
}
```

# 최적화 제한 한정자(volatile)

컴파일러 최적화 옵션을 비활성 시킵니다.

디바이스에 명령을 내리기 위해, 특정 메모리 공간에 쓰기 명령을 주어야 한다고 가정해 보죠.

```cpp
unsigned int *p = 0x1234;
*p = 0x0001; // 0x1234에 0x0001이 써질 때의 명령 실행
*p = 0x0002; // 0x1234에 0x0002가 써질 때의 명령 실행
*p = 0x0003; // 0x1234에 0x0003이 써질 때의 명령 실행
```

`*p` 에 쓰기 명령을 주어 3개의 명령을 수행하는 건데요, 어차피 `*p`에 값을 덮어쓰다가 결국 `0x0003`이 되니, 컴파일러는 최종값만 저장하는 것으로 최적화 할 수 있습니다. 다음처럼요.

```cpp
unsigned int *p = 0x1234;
*p = 0x0003; // 최종값만 저장합니다. 즉, 0x1234에 0x0003이 써질 때의 명령만 실행됩니다.
```

최적화 때문에 `*p = 0x0001;` 과 `*p = 0x0002;`가 무시되어 버렸는데요, 이런 최적화를 막기 위해 [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile)을 사용합니다. [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile)을 사용하면 컴파일러 최적화를 하지 않습니다.

```cpp
volatile unsigned int *p = 0x1234; // 컴파일러 최적화를 하지 않습니다.
*p = 0x0001;
*p = 0x0002;
*p = 0x0003;
```



