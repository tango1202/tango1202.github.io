---
layout: single
title: "#20. [고전 C++ 가이드] 함수, 함수 포인터, 리턴, 인자(매개변수, Parameter)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)을 사용하지 마라.
> * [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 대신 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 나 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하라.
> * 멤버 개체의 참조가 아니라면, 컴파일러 최적화가 쉽도록, [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)가 가능하도록, [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 은 값 타입으로 리턴하라.
> * 다형적인 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)에서 부모 개체와 자식 개체의 기본값을 다르게 하지 마라.
> * [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 타입과 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입은 값을 사용할 것인지, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용할 것인지, [상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 사용할 것인지, 비 상수를 사용할 것인지 신중하게 결정하라.
> * 함수 오버로딩시 [함수 인자의 유효 범위에서 탐색(Argument-dependent lookup, ADL 또는 Koenig 검색)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%83%90%EC%83%89-%EA%B7%9C%EC%B9%99argument-dependent-lookup-adl)하는 원리를 이해하라.
> * 함수에 전달하는 인수는 순서대로 호출되지 않는다. 컴파일러 마음이다.

> **모던 C++**
> * (C++11~) [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 의존하여 리턴 타입을 결정하는 [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)이 추가되어 좀더 동적인 함수 설계가 가능해 졌습니다. 
> * (C++11~) [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)로 함수 인수 전달, 리턴문 작성을 간소화할 수 있습니다.
> * (C++11~) [중괄호 초기화시 인자의 암시적 형변환을 일부 차단](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98-%EC%B0%A8%EB%8B%A8)하여, 코딩 계약이 개선되었습니다.
> * (C++11~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 함수의 예외 방출 여부를 보증합니다.
> * (C++11~) [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)이 추가되어 1회용 익명 함수를 만들 수 있습니다. 
> * (C++11~) [가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 활용한 [가변 매크로](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EA%B0%80%EB%B3%80-%EB%A7%A4%ED%81%AC%EB%A1%9C)가 추가되어 C언어와의 호환성이 높아졌습니다.
> * (C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept)로 예외를 방출하느냐 안하느냐만 관심을 둡니다.
> * (C++14~) [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)이 가능하여 [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 대신 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)나 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)를 사용할 수 있습니다.
> * (C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.
> * (C++17~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 관련해서 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)가 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 이제 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)만 사용해야 합니다.

# 개요

함수는 다음 목적을 위해 만듭니다.

1. 함수 코드 재활용(*코드 중복 제거*) 
2. [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)와의 타입에 기반한 **코딩 계약**
3. 디버깅 편의성

함수 정의의 일반적인 형태는 하기와 같습니다.(*`[]`인 부분은 옵션입니다.*)

```cpp
return_type function_name(parameter_list) [const] [throw(exception_list)] {}
```

|항목|내용|
|--|--|
|[return_type](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)|함수 결과인 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)의 타입입니다. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 사용할 수 없습니다.|
|[parameter_list](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)|[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 목록입니다.|
|[const](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)|멤버 함수인 경우 개체를 수정하지 않습니다.(*[상수 한정자(const), 변경 가능 지정자(mutable), 최적화 제한 한정자(volatile)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 참고*)
|[throw(exception-list)](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)|함수가 방출하는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)입니다.<br/>나열된 예외 이외에는 `unexpected_handler` 로 분기하는데요, 사용하지 마세요. 이유는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)을 참고하기 바랍니다.|

함수를 만드는데 있어서 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)과 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 타입을 어떻게 하느냐에 따라서 불필요한 복사 부하를 최소화 하여 성능을 개선시킬 수 있으니, [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)과 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 타입은 신중하게 결정해야 합니다. 

# 함수 포인터

[함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)로 함수 자체를 변수처럼 사용할 수 있습니다.

다음 예에서 `void (*p)(int);`로 `void`를 리턴하고, `int`를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받는 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) `p`를 선언합니다.

`p = f;` 또는 `p = &f;`로 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) `p`가 `f()`함수를 가리키게 할 수 있고, `p()` 나 `(*p)()`로 `f()`함수를 호출할 수 있습니다.

```cpp
void (*p)(int); // void 를 리턴하고 int형을 인수로 전달받는 함수의 함수 포인터 p 선언

void f(int) {} // f 함수 정의
p = f; // 함수 포인터에 f 함수 대입. p = &f; 와 동일
p(10); // f 함수 실행. (*p)(10); 과 동일
```

[타입 재정의(typdef)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%B3%84%EC%B9%AD)를 사용하면 좀더 변수스럽게 사용할 수 있습니다.

```cpp
typedef void (*Func)(int); // void 를 리턴하고 int형을 인수로 전달받는 함수의 함수 포인터 타입 정의

Func p; // 함수 포인터 p 정의
p = f; // 함수 포인터에 f 함수 대입. p = &f; 와 동일
p(10); // f 함수 실행. (*p)(10); 과 동일
```

[함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)는 **의존성 주입(*[의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고*)** 을 활용하여, 원하는 알고리즘으로 손쉽게 변경하거나, [제어의 역전 원칙](https://tango1202.github.io/principle/principle-inversion-of-control/) 에 따라 프레임워크에서 제어를 통제하고자 할때 사용할 수 있습니다.

예를 들어 계산기 프로그램을 만들기 위해 `Button`을 만든다고 가정해 봅시다. `Button`은 `Click()`시 버튼에 할당된 연산(*더하기, 빼기*)을 수행하는 역할만 하려고 합니다. 이럴 경우 연산 행위를 수행하는 함수를 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)로 `Button`에 전달하고, `Button`은 `Click()`시 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 호출해 주면 됩니다.

다음 예에서 `plus()` 함수와 `minus()` 함수를 `Button` 생성자에 전달하여 `m_Func`에 저장한 뒤, `Click()`시 이를 호출하여, `Button`에 따라 더하기나 빼기를 수행합니다.

```cpp
typedef int (*Func)(int, int); // 함수 포인터 typedef

class Button {
private: 
    Func m_Func; // 버튼이 클릭될때 실행될 함수
public:
    explicit Button(Func func) : // 생성시 실행할 함수 포인터 전달
        m_Func(func) {}
    int Click(int a, int b) { 
        return m_Func(a, b); // 함수 포인터 실행
    }
};

int plus(int a, int b) {
    return a + b;
}
int minus(int a, int b) {
    return a - b;
}

// 버튼 생성시 어떤 연산을 수행할지 함수를 전달해 둡니다.
Button plusButton(plus); // 클릭시 더하기
Button minusButton(minus); // 클릭시 빼기

EXPECT_TRUE(plusButton.Click(10, 20) == 30); 
EXPECT_TRUE(minusButton.Click(10, 20) == -10); 
```

상기의 사례는 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 대신 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 나 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하거나 **의존성 주입(*[의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고*)** 을 활용해서도 구현 할 수 있습니다.


**멤버 함수 포인터**

개체의 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)도 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)로 접근 할 수 있습니다.

다음 예에서 `typedef int (Data::*Func)() const;`는 `Data`클래스의 [상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)중 `int`를 리턴하고, [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 없는 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) `Func`을 선언합니다.

`Func func = &Data::Print;` 와 같이 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) `Func`이 `Print()`함수를 가리키게 할 수 있고, `(data.*func)();`와 같이 개체로부터 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 호출할 수 있습니다.

```cpp
class Data { 
public: 
    int Print() const {return 1;} 
};

typedef int (Data::*Func)() const; // Data 클래스 멤버 함수 typedef
Func func = &Data::Print;  // 멤버 함수 포인터 전달

Data data;
EXPECT_TRUE((data.*func)() == 1); // data 개체로 부터 멤버 함수 포인터 실행
```

예를 들어 인쇄 버튼이 상황에 따라 프린터에 출력하거나 화면 미리보기를 수행한다고 해봅시다. 동적으로 호출되는 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 달라지는데요, [멤버 함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 전달해서 구현할 수 있습니다.

다음 예에서는 `Button`의 `Click()` 함수를 호출할때 `m_Data`의 어떤 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 실행할지 동적으로 전달하고 있습니다.

```cpp
class Data { 
public: 
    int Print() const {return 1;}
    int Preview() const {return 2;}
};
typedef int (Data::*Func)() const; // Data 클래스 멤버 함수 typedef

class Button {
private: 
    const Data& m_Data; // 버튼이 관리하는 Data
public:
    explicit Button(const Data& data) :
        m_Data(data) {}
    int Click(Func func) { 
        return (m_Data.*func)(); // 전달된 멤버 함수 포인터 실행
    }
};

Data data;
Button button(data);

EXPECT_TRUE(button.Click(&Data::Print) == 1); // data 개체로 부터 Print 함수 실행
EXPECT_TRUE(button.Click(&Data::Preview) == 2); // data 개체로 부터 Preview 함수 실행
```

# 리턴값

함수가 리턴문을 통해 전달하는 결과값을 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이라 합니다.

void, 값 타입, 포인터, const 포인터, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), const [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴할 수 있으며, 특별히 함수의 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 리턴하면 오류가 발생하니 주의하시기 바랍니다.(*[Dangling 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#dangling-%EC%B0%B8%EC%A1%B0%EC%9E%90) 참고*)

```cpp
class T {
    int m_Val;

    void f() {} // 아무것도 리턴 안함
    int g() {return 0;} // 정수값을 리턴함
    const int& h() const {return m_Val;} // 멤버 변수의 참조자를 리턴함
};
```

[리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)의 타입은

1. 값을 리턴할 것인지, 포인터를 리턴할 것인지, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴할 것인지
2. [상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 리턴할 것인지, 비 상수를 리턴할 것인지

신중하게 결정해야 복사 부하를 줄이고, 타입에 기반한 **코딩 계약** 을 수립할 수 있습니다. 해당 방법에 대해서는 [Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)를 참고하기 바랍니다.

# 리턴값 최적화(Return Value Optimization, RVO)

일반적으로 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)은 다른 변수에 복사 생성 혹은 복사 대입됩니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    // 값 생성자
    T(int x, int y) :
        m_X(x),
        m_Y(y) {
        std::cout << "RVO -> T::T()" << std::endl;
    }

    // 복사 생성자
    T(const T& other) {
        std::cout << "RVO -> T(const T& other)" << std::endl;    
    }
    
    T f() {
        T result(0, 0);
        return result;
    }
};
T t1(0, 0);
T t2(t1.f()); // T t2 = t1.f(); 와 동일
```

상기에서 `T` 개체는 `t1`생성시 1회, `f()`에서 `result`를 생성하면서 2회, `f()`에서 리턴하면서 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 3회, `t2`를 생성하면서 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 전달받는 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하여 4회 생성될 것 같지만, 실제 확인해 보면 그렇지 않습니다.

GCC에서는 하기 2개만 실행됩니다. 심지어 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)는 호출되지도 않습니다.(*컴파일러 최적화 방식에 따라 다를 수 있습니다.*)

```cpp
RVO -> T::T() // t1 생성
RVO -> T::T() // t2 생성
```

이는 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)인 `result`가 `t2`에 전달되므로, 괜히 생성하고 전달하는게 아니라 리턴할 개체를 그냥 `t2`로 사용하기 때문입니다. 이를 [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)(*Return Value Optimization, RVO*) 라고 합니다. 

[리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)는 다음 조건에서 수행됩니다.

1. [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 값 타입이어야 합니다.([참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 타입이 아닙니다.)

2. [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 타입이 리턴하려는 개체와 같은 타입이어야 합니다.

상기의 `f()`함수는 이 두가지 조건을 모두 충족하기 때문에 컴파일러가 [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)를 해줍니다.

```cpp
T f() { // 리턴값은 T 로 값 타입입니다.
    T result(0, 0); // 리턴값과 리턴하려는 개체가 타입이 같습니다.
    return result;
}
```

또는 컴파일러가 최적화를 쉽게 할 수 있도록 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 명시적으로 변수로 만들기 보다는,

```cpp
T result(0, 0);
.
. // 복잡한 제어문들. 
.
return result; // (△) 비권장. 컴파일러가 최적화를 못할 수도 있습니다.
```

다음처럼 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 사용하는게 좋을 수 있습니다.

```cpp
return result(0, 0); // (O) 임시 개체를 생성하는게 컴파일러가 최적화하기 편합니다.
```
> *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.*

# 값 타입 리턴값

함수의 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 이용하지 않고 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 통해 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)에 포인터나 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 전달하여 함수의 결과값을 대입받을 수 있습니다만, 심각한 복사 부하가 있습니다.

다음 예에서는

1. `T Create()`로 값 타입을 리턴하는 경우
2. `T* CreatePtr()`로 포인터 타입을 리턴하는 경우
3. `Create(T* ptr)`로 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 통해 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 대입 받는 경우
3. `Create(T& ref)`로 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 통해 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 대입 받는 경우

를 비교한 예입니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T() {
        std::cout << "T : Default Constructor" << std::endl;
    }
    // 값 생성자
    T(int x, int y) :
        m_X(x),
        m_Y(y) {
        std::cout << "T : Value Constructor" << std::endl;
    }

    // 복사 생성자
    T(const T& other) {
        std::cout << "T : Copy Constructor" << std::endl;    
    }
    // 복사 대입 연산자
    T& operator =(const T& other) {
        std::cout << "T : Assign" << std::endl;    
        return *this;
    }    
    
    // 값 타입으로 생성합니다. RVO 가 적용됩니다.
    static T Create() { 
        T result(0, 0);
        return result;
    }

    // 포인터를 생성해서 리턴합니다. 호출한 곳에서 delete 해줘야 합니다.
    static T* CreatePtr() {
        return new T(0, 0);
    }

    // 생성된 개체에 복사 대입 합니다. 포인터라서 널검사가 필요합니다. NULL 이면 예외를 방출합니다.
    static void Create(T* ptr) {
        if (ptr == NULL) {
            throw std::invalid_argument("NULL"); 
        }

        *ptr = T(0, 0);
    }

    // 생성된 개체에 복사 대입 합니다.
    static void Create(T& ref) {
        ref = T(0, 0);
    }
};

T a = T::Create(); // 리턴값 최적화로 복사하지 않습니다.

T* b = T::CreatePtr(); // (△) 비권장. 생성한 포인터만 복사합니다. 포인터이기 때문에 나중에 delete 해야 합니다.
delete b;

T c; 
T::Create(&c); // (△) 비권장. 생성 후 대입 받으면, 기본 생성자, 값 생성자, 복사 대입 연산자가 호출됩니다.

T d;
T::Create(d); // (△) 비권장. 생성 후 대입 받으면, 기본 생성자, 값 생성자, 복사 대입 연산자가 호출됩니다.
```

실행 결과는 다음과 같습니다.

1. [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 값 타입으로 사용한 경우가 복사 부하도 가장 적고, 인터페이스도 깔끔합니다.

2. 포인터 타입을 리턴하면 복사 부하는 없으나, [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)의 부담이 있고, 

3. 포인터를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달하는건 2회의 생성과 1회 대입이 있을 뿐만아니라 예외 방출까지 합니다.

4. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달하는 것도 2회의 생성과 1회 대입이 있습니다.

```cpp
// T a = Create();
T : Value Constructor

// T* b = CreatePtr();
T : Value Constructor

// T c;
// Create(&c);
T : Default Constructor
T : Value Constructor
T : Assign

// T d;
// Create(d);
T : Default Constructor
T : Value Constructor
T : Assign
```

복사 부하도 없고 사용법도 깔끔하니, [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)은 값 타입을 사용하시기 바랍니다.

단, 클래스 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)인 경우 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴하셔야 합니다. [Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)를 참고하세요.


# 인자(매개변수, Parameter)

**명명된 인자 선언**

[함수의 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 타입과 이름을 지정하여 선언합니다.

```cpp
void f(int a, int b); // (O)
```

**이름이 없는 인자**

함수 정의부에서 사용하지 않는다면 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)명을 생략할 수 있습니다.

다음 2가지 경우입니다.

* [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 선언 용도라면 생략할 수 있습니다.

    ```cpp
    void f(int, int); // (O)
    ```

* 템플릿 함수 구현시 오버로딩을 위해 생략할 수 있습니다.(*[타입 처리 방법 공통화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-traits/#%ED%83%80%EC%9E%85-%EC%B2%98%EB%A6%AC-%EB%B0%A9%EB%B2%95-%EA%B3%B5%ED%86%B5%ED%99%94) 참고*)

이것 외에는 사용하지 않는 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 억지로 작성해야 하는 경우입니다. 

설계가 잘못된 겁니다. 악취가 난다는 뜻입니다. [인터페이스 분리 원칙](https://tango1202.github.io/principle/principle-interface-segregation/) 위반입니다. 설계 변경을 추천합니다. 그럼에도 불구하고 꼭 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 생략을 해야 한다면, 하기 작성 방법을 고려해 보세요.

다음과 같이 함수 본문에서 컴파일러 경고를 막기 위해 억지로 사용할 수는 있지만, 명시적으로 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)명을 적었기 때문에 컴파일러 최적화가 힘들수 있고요,

```cpp
void f(int a, int b) {
    // 명시적으로 사용한 것이기에 컴파일러가 최적화 하기 힘듭니다.
    a; // (△) 사용하지 않는 인자에 대한 warning 제거. 
    b; // (△) 사용하지 않는 인자에 대한 warning 제거. 
}
```

다음은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)명이 없어 컴파일러 최적화의 여지는 있으나, 어떤 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 필요한 건지 가독성이 좀 떨어집니다.

```cpp
void f(int, int) {
    // (△) 비권장. 어떤 인자가 필요했던 것인지 알기 힘듭니다.
}
```

따라서, 컴파일러 최적화 여지도 있고, 가독성도 있도록 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)명만 주석으로 만드는 걸 추천합니다.

```cpp
void f(int /*a*/, int /*b*/) {
    // (O) 인자명이 없어 컴파일러가 최적화 할 수 있습니다.
}
```

**인자 없음**

[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 없으면 생략하거나 `void`를 작성할 수 있습니다.

```cpp
void f(); // (O)
void f(void); // (O)
```

`void`를 다른 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)와 함께 섞어 쓰거나, [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)와 함께 쓰는건 컴파일 오류가 납니다.(*`void*`는 포인터형이기에 `const void*`는 괜찮습니다.*)

```cpp
int f(void, int); // (X) 컴파일 오류
int f(const void); // (X) 컴파일 오류
```

# 가변 인자(...)

[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 1개 이상인 경우 `...`으로 [가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 처리할 수 있습니다.

|항목|내용|
|--|--|
|`va_start()`|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 액세스 시작 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|`va_arg()`|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 추출 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|`va_end()`|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 사용 종료 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|`va_list`|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)에 대한 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)|

[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 사용하려면 `<cstdarg>`를 [include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)해야 합니다.

```cpp
#include <cstdarg>
 
class T {
public:
    static int Sum(int count, ...) {
        int result = 0;
        std::va_list paramList; // 가변 인자
        va_start(paramList, count); // 가변 인자 처리 시작
        for (int i = 0; i < count; ++i) {
            result += va_arg(paramList, int); // 가변 인자 추출
        }
        va_end(paramList); // 가변 인자 처리 끝
        return result;       
    }
};
EXPECT_TRUE(T::Sum(3, 1, 2, 3) == 1 + 2 + 3);
```

# 기본값 인자

함수 선언시 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 기본값을 줄 수 있습니다.

```cpp
int f1(int a = 10) {  // 인수를 전달하지 않으면 기본값 10
    return a;
}

EXPECT_TRUE(f1() == 10); // 아무값도 주지 않으면 a 는 10
EXPECT_TRUE(f1(20) == 20);
```

[기본값 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90)를 사용하면, 그 뒤로는 다 기본값을 사용해야 합니다.(*단 `...`은 가능합니다. `int g(int n = 0, ...);`*)

```cpp
int f2(int a, int b = 20, int c = 30); // (O)
int f2(int a, int b = 20, int c); // (X) 컴파일 오류. c에도 기본값을 줘야 합니다.
```

함수 선언과 정의가 분리되어 있으면, 선언부에만 기본값을 작성해야 합니다.

```cpp
// 선언부
int f2(int a, int b = 20, int c = 30); // (O)

// 정의부
int f2(int a, int b /*= 20*/, int c /*= 30*/) { // (O) 선언과 정의 분리시, 정의부는 기본값 작성 안함.
    return 0;
}
```

[this 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)는 기본값으로 사용할 수 없습니다.
   
```cpp
class T {
    void f3(T* p = this) {} // (X) 컴파일 오류. this는 기본값을 사용할 수 없음
};
```

[전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)를 기본값으로 사용하면, 런타임 호출 시점의 값을 반영합니다.

```cpp
int g_Val = 10;
int f4(int val = g_Val) { // g_Val의 런타임 호출 시점의 값이 사용됩니다.
    return val;
}
EXPECT_TRUE(g_Val == 10);
EXPECT_TRUE(f4() == 10);
g_Val = 20;
EXPECT_TRUE(f4() == 20);
```

[전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)의 경우와 마찬가지로 함수 호출을 기본값으로 사용하면, 런타임 호출 시점의 값을 반영합니다.

```cpp
int g_Val = 10;
int g() {return g_Val;}
int f5(int val = g()) { // g()의  런타임 호출 시점의 값이 사용됩니다.
    return val;
}
g_Val = 10;
EXPECT_TRUE(f5() == 10);
g_Val = 20;
EXPECT_TRUE(f5() == 20);
```

상속 관계에서 [인자의 기본값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90)을 재정의하면, 혼란을 야기할 수 있습니다. 

다음과 같이 `Base`와 `Derived`의 인스턴스를 생성하고 호출하면, 각자의 기본값대로 처리됩니다.

```cpp
class Base {
public:
    virtual int f6(int val = 10) const {
        return val;
    }
};
class Derived : public Base {
public:
    virtual int f6(int val = 20) const { // (△) 비권장. Base의 디폴트 값과 다르게 했습니다. 혼란스러워 질 수 있습니다.
        return val;
    }
};
```

실행 결과를 보면, `Base` 개체로 호출할 때와 `Derived` 개체로 호출할때 각자의 값으로 실행되는 걸 알 수 있습니다.

```cpp
Base b;
Derived d;
EXPECT_TRUE(b.f6() == 10);   
EXPECT_TRUE(d.f6() == 20);    
```

하지만 `Derived`개체를 부모 개체인 `Base*` 통해 접근하면 [가상 함수 테이블](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)을 참조하여 호출하므로, `Base`의 기본값으로 처리됩니다. 

```cpp
Derived d;
Base* p = &d;
EXPECT_TRUE(p->f6() == 10); // 가상 함수 테이블을 참조하여 Base 의 기본값인 10을 사용합니다.   
```

이러한 차이가 생기므로, 부모 개체와 자식 개체의 기본값은 같은 값으로 하는게 좋습니다.

**인자 타입**

[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입은 

1. 값을 전달받을 것인지, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 전달받을 것인지
2. [상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 전달받을 것인지, 비 상수를 전달받을 것인지

신중하게 결정해야 복사 부하를 줄이고, 타입에 기반한 **코딩 계약** 을 수립할 수 있습니다. 해당 방법에 대해서는 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)를 참고하기 바랍니다.

# 오버로딩된 함수 결정 규칙

C++언어에서는 이름이 동일한 함수를 여러개 정의하여 사용할 수 있습니다. 이렇게 함수명이 동일한 경우 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입에 따라 알맞은 함수를 호출하게 됩니다. 

**암시적 형변환**

기본적으로 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입이 일치하는 것을 선택하지만, 해당 타입의 것이 없다면 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 적용하여 최대한 매핑되는 것을 사용합니다.

```cpp
class T {
public:
    int f(int) {return 1;}
    int f(double) {return 2;}
};
T t;
EXPECT_TRUE(t.f(1) == 1); // (O) 타입 일치. int 버전 호출
EXPECT_TRUE(t.f(1.) == 2); // (O) 타입 일치. double 버전 호출
EXPECT_TRUE(t.f(1L) == 1); // (X) 컴파일 오류. long 버전이 없음
EXPECT_TRUE(t.f(1.F) == 2); // (△) 비권장. float 버전이 없지만, double로 암시적 형변환 되므로 double 버전 호출
```

**동일 함수 취급**

[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 타입을 검사할때 동일 타입으로 취급하는 규칙이 있습니다.

1. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 포인터로 취급됩니다.

    즉, 

    ```cpp
    int f(int a[3]);
    int f(int a[]);
    int f(int* a);
    ```
    는 모두 동일합니다.

    이는

    ```cpp
    int a[3] = {0, 1, 2};
    int* p = a;
    ```

    와 같이 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 포인터로 다룰 수 있기 때문입니다.

2. 최상위 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)는 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입에서 제거하고 취급합니다.

    즉,

    ```cpp
    int f(int a);
    int f(const int a);
    ```
    
    는 동일합니다. `int`로 받던, `const int`로 받던 최상위 데이터가 함수쪽으로 복제되는건 똑같으니까요.

    [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/) 관점에서 보면,

    ```cpp
    int f(int* a);
    int f(int* const a);
    ```

    가 동일합니다.

    하지만,

    ```cpp
    int f(int* a);
    int f(const int* a);
    ```

    는 다릅니다. `int* a` 는 `a`의 실제값을 수정할 수 있고, `const int* a`는 수정할 수 없으니까요.

3. 함수의 [상수성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)은 다른 타입으로 취급합니다.

    즉,

    ```cpp
    int f(int a);
    int f(int a) const;
    ```

    는 다릅니다.

4. 리턴 타입은 오버로딩 함수를 취급하는데 사용하지 않습니다.

    즉,

    ```cpp
    int f(int) {return 1;}
    double f(int) {return 9.};
    ```

    는 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 같으니, 오버로딩 후보 목록에서 1개만 사용됩니다.

```cpp
class T {
public:
    
    int f(int) {return 1;}
    int f(int* a) {return 2;} 

    // (X) 컴파일 오류. 배열은 f(int* a)와 동일해서 오버로딩 안됨.
    // int f(int a[3]) {return 3;}
    // int f(int a[]) {return 4;}

    // (X) 컴파일 오류. int f(int) 와 int f(const int) 는 동일해서 오버로딩 안됨.
    // int f(const int) {return 5;}

    // (X) 컴파일 오류. f(int* a)와 f(int* const a)는 동일해서 오버로딩 안됨.
    // int f(int* const a) {return 6;}

    // (O) f(int)와 f(const int) 는 동일해서 오버로딩 안되지만, 
    // f(int* a)와 f(const int* a)는 다르므로(포인터 자체가 const가 아니라 가리키는 곳이 const임) 오버로딩 됨
    int f(const int* a) {return 7;}

    // (O) 함수 상수성에 따라 선택됨
    int f(int) const {return 8;} 

    // (X) 컴파일 오류. 리턴 타입은 오버로딩 함수를 취급하는데 사용하지 않습니다.
    // double f(int) {return 9.};
};

T t;
EXPECT_TRUE(t.f(1) == 1); // (O) 타입 일치. int 버전 호출
int a = 10;
EXPECT_TRUE(t.f(&a) == 2); // (O) 타입 일치. int* 버전 호출
int arr[3] = {1, 2, 3};
EXPECT_TRUE(t.f(arr) == 2); // (O) 배열은 int* 버전 호출
int* const p = &a;
EXPECT_TRUE(t.f(p) == 2); // (O) int* const는 int* 버전 호출
EXPECT_TRUE(t.f(const_cast<const int*>(&a)) == 7); // (O) const int* 는 const int* 버전 호출
EXPECT_TRUE(const_cast<const T&>(t).f(1) == 8); // (O) 개체 상수성에 따라 상수 함수 선택됨
```

# 함수 인자의 유효 범위 탐색 규칙(Argument-dependent lookup, ADL)

오버로딩한 함수를 탐색할때 해당 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) 에서도 동일한 이름의 함수를 탐색한다는 규칙입니다. 
Argument-dependent lookup(*ADL*) 또는 Koenig 검색이라고 합니다. 

구체적인 사례는 [오버로딩 함수 탐색 규칙]()을 참고하세요.

# 오버로딩 함수 탐색 규칙

오버로딩 함수의 후보군은 하기 단계에 따라 수집되고 선정됩니다.
 
1. 자신의 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)에서 탐색
   
2. [함수 인자의 유효 범위에서 탐색(Argument-dependent lookup, ADL 또는 Koenig 검색)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%83%90%EC%83%89-%EA%B7%9C%EC%B9%99argument-dependent-lookup-adl)

3. [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 포함하여 실행 가능 함수 결정
   
   1. 타입 완전 일치
   2. 경미한 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)
   3. 승격 일치(*[bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)을 `int`로, `char`를 `int`로*) 
   4. 표준 변환(*자식 개체 포인터를 부모 개체 포인터로, `T*`를 `void*`로, `int`를 `double`로, `double`을 `int`로*) 
   5. 사용자 정의 형변환(*[형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고*)


다음 경우를 보면 `g()` 에서 `MyFunc()`을 호출하면, 같은 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)에서 `MyFunc(double)`을 찾고, [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) `1`을 `double`로 암시적으로 변환해서 사용하게 됩니다.

```cpp
namespace A {
    class Date {};
    int MyFunc(int) {return 1;}
} 
namespace B { 
    int MyFunc(double) {return 2;}
    int g() {
        A::Date d;
        // 현 유효 범위에서 MyFunc(double)을 찾음. 1을 double로 암시적으로 변환함
        return MyFunc(1); 
    }
}

EXPECT_TRUE(B::g() == 2); // B::MyFunc 이 채택됨
```

하지만 `MyFunc()`함수의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 다음처럼 특정 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)의 것을 참조한다면, 

```cpp
int MyFunc(const C::Date&, double) {return 2;}
```

[함수 인자의 유효 범위에서 탐색(Argument-dependent lookup, ADL 또는 Koenig 검색)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%9C%A0%ED%9A%A8-%EB%B2%94%EC%9C%84-%ED%83%90%EC%83%89-%EA%B7%9C%EC%B9%99argument-dependent-lookup-adl)에 의해 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) `C`의 함수들에서도 검색하게 됩니다.

그래서 함수 후보군은

* [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) `C`에서는 `int MyFunc(const Date&, int)`
* [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) `D`에서는 `int MyFunc(const C::Date&, double)`

이 찾아지게 되고, 타입이 완전 일치하는 [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) `C`의 `int MyFunc(const Date&, int)`이 사용됩니다.

좀더 넓은 범위에서 타입이 일치하는 함수를 찾도록 도와주는 역할입니다만, 의도한 동작일 수도 있고, 아닐 수도 있고, 분석이 어려워질 수도 있으니, 원리를 이해하고 사용하시기 바랍니다.

```cpp
namespace C {
    class Date {};
    int MyFunc(const Date&, int) {return 1;}
} 
namespace D { 
    int MyFunc(const C::Date&, double) {return 2;}
    int g() {
        C::Date d;
        // Koenig 검색이 활용됨. 
        // 인자로 전달한 d 가 네임스페이스 C에 있기 때문에, C의 함수들중 MyFunc(const Date&, int)를 찾아냄
        // MyFunc(const Date&, int) 과 MyFunc(const C::Date&, double) 중 타입이 일치하는 MyFunc(const Date&, int)이 채택됨
        return MyFunc(d, 1); 
    }  
}

EXPECT_TRUE(D::g() == 1); // C::MyFunc 이 채택됨
```
# 평가 순서

코드를 위에서부터 아래로 읽고, 왼쪽에서 오른쪽으로 읽기 때문에 읽는 순서대로 실행될 것이다 예측할 수 있는데요,

보통의 경우엔 그렇습니다만, 공식적으로는 [연산자 우선 순위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%9A%B0%EC%84%A0-%EC%88%9C%EC%9C%84)를 제외한 모든 실행 순서는 컴파일러 마음입니다.

심지어 속도 향상을 위해 컴파일러가 최적화를 위해 캐쉬된 내용을 활용하기 위해 임의로 순서를 바꿀 수도 있습니다.(*[순차적 일관성](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-atomic/#%EC%88%9C%EC%B0%A8%EC%A0%81-%EC%9D%BC%EA%B4%80%EC%84%B1sequential-consistency) 참고*)

다음은 `a(a_sub())`, `b()`, `c()` 함수의 결과를 `f()` 에 전달하는 예인데요, `a->b->c`의 순서로 실행된다고 보증하지 않습니다. `a->b->c`, `a->c->b`, `b->a->c`, `b->c->a`, `c->a->b`, `c->b->a`의 6가지 경우의 수중 하나로 실행됩니다. 이점 유의해야 예외 안전 프로그래밍(*[스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80) 참고*)과 쓰레드 프로그래밍(*[쓰레드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/) 참고*)을 할 수 있습니다. 

`a()` 함수는 `a_sub()`의 결과를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받으므로, `a_sub()`보다 나중에 실행됨을 보증합니다.(*예를 들어 `b->a_sub->c->a`가 될 수도 있습니다.*)

```cpp
f(a(a_sub()), b(), c());
```