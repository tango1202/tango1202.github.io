---
layout: single
title: "#19. [고전 C++ 가이드] 함수, 함수 포인터, 리턴, 인자(매개변수, Parameter)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 동적 예외 사양을 사용하지 마라.
> * 함수 포인터 대신 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 나 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하라.
> * 컴파일러 최적화가 쉽도록, RVO가 쉽도록, [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 사용하라.
> * 다형적인 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)에서 부모 개체와 자식 개체의 기본값을 다르게 하지 마라.
> * 리턴 타입과 인자 타입은 값을 사용할 것인지, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)를 사용할 것인지, [상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 사용할 것인지, 비 상수를 사용할 것인지 신중하게 결정하라.
> * 함수 오버로딩시 함수 인자의 유효 공간에서도 탐색(ADL(Argument-dependent lookup) 또는 Koenig 검색)하는 원리를 이해하라.

> **모던 C++**
> * 함수 인자에 의존하여 리턴 타입을 결정하는 후행 리턴이 추가되어 좀더 동적인 함수 설계가 가능해 졌습니다.([후행 리턴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)과 [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0-c14) 참고) 
> * `{}` 복사 초기화로 함수 인수 전달, 리턴문 작성을 간소화할 수 있습니다.([중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94) 참고)
> * `{}` 초기화시 인자의 암시적 형변환을 일부 차단하여, 코딩 계약이 개선되었습니다.([중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94) 참고)
> * `noexcept` 를 이용하여 예외 발생이 없는 함수인 nothrow 보증을 할 수 있습니다.([noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept) 참고)
> * 람다 표현식이 추가되어 1회용 익명 함수를 만들 수 있습니다.([람다 표현식, 클로져](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda) 참고) 
> * 가변 인자를 활용한 가변 매크로가 추가되어 C언어와의 호환성이 높아졌습니다.([가변 매크로](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-macro/) 참고)

# 개요

함수는 다음 목적을 위해 만듭니다.

1. 함수 코드 재활용(코드 중복 제거) 
2. 함수 인자와의 타입에 기반한 **코딩 계약**
3. 디버깅 편의성

함수 정의의 일반적인 형태는 하기와 같습니다.(`[]`인 부분은 옵션입니다.)

```cpp
return_type function_name(parameter_list) [const] [throw(exception_list)] {}
```

|항목|내용|
|--|--|
|`return_type`|함수 결과의 타입. 배열은 안됨|
|`parameter_list`|인자 목록|
|`[const]`|멤버 함수인 경우 개체를 수정하지 않음([상수 한정자(const), 변경 가능 지정자(mutable), 최적화 제한 한정자(volatile)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 참고)
|`[throw(exception-list)]`|함수가 발생하는 예외 사양.<br/>나열된 예외 이외에는 `unexpected()` 핸들러로 분기함. 사용하지 말 것.([동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 참고)|

# 함수 포인터

함수 포인터로 함수 자체를 변수처럼 사용할 수 있습니다.

```cpp
void (*p)(int); // void 를 리턴하고 int형을 인수로 전달받는 함수의 함수 포인터 p 정의

void f(int) {} // f 함수 정의
p = f; // 함수 포인터에 f 함수 대입. p = &f; 와 동일
p(10); // f 함수 실행. (*p)(10); 과 동일
```

`typdef`를 사용하면 좀더 변수스럽게 사용할 수 있습니다.

```cpp
typedef void (*Func)(int); // void 를 리턴하고 int형을 인수로 전달받는 함수의 함수 포인터 타입 정의

Func p; // 함수 포인터 p 정의
p = f; // 함수 포인터에 f 함수 대입. p = &f; 와 동일
p(10); // f 함수 실행. (*p)(10); 과 동일
```

함수 포인터는 **의존성 주입([의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고)** 을 활용하여, 원하는 알고리즘으로 손쉽게 변경하거나, [제어의 역전 원칙](https://tango1202.github.io/principle/principle-inversion-of-control/) 에 따라 프레임워크에서 제어를 통제하고자 할때 사용할 수 있습니다.

예를 들어 계산기 프로그램을 만들기 위해 `Button`을 만든다고 합시다. `Button`은 `Click`시 버튼에 할당된 연산(더하기, 빼기)을 수행하는 역할만 하려고 합니다. 이럴 경우 연산 행위를 수행하는 함수를 함수 포인터로 `Button`에 전달하고, `Button`은 `Click`시 함수 포인터를 호출해 주면 됩니다.

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

상기의 사례는 함수 포인터 대신 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 나 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하거나 **의존성 주입([의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고)** 을 활용해서도 구현 할 수 있습니다.


**멤버 함수 포인터**

개체의 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)도 함수 포인터로 접근 할 수 있습니다.

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

예를 들어 인쇄 버튼이 상황에 따라 프린터에 출력하거나 화면 미리보기를 수행한다고 해봅시다. 동적으로 호출되는 멤버 함수가 달라지는데요, 멤버 함수 포인터를 전달해서 구현할 수 있습니다.

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

함수는 리턴문을 통해 결과값을 전달합니다.

```cpp
class T {
    int m_Val;

    void f() {} // 아무것도 리턴 안함
    int g() {return 0;} // 정수값을 리턴함
    const int& h() const {return m_Val;} // 멤버 변수의 참조자를 리턴함
};
```

또한 리턴문은 특정 조건에서 강제 종료시 사용할 수 있습니다.

```cpp
class T {
    void f() {
        if () {
            return;
        }
    }
};
```

특별히 함수의 지역 변수를 참조자로 리턴하면 오류가 발생하니 주의하시기 바랍니다.([Dangling 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#dangling-%EC%B0%B8%EC%A1%B0%EC%9E%90) 참고)

**Return Value Optimization(RVO)**

일반적으로 리턴되는 값은 다른 변수에 대입됩니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    // 값 생성자
    T(int x, int y) :
        m_X(x),
        m_Y(y) {
        std::cout<<"RVO -> T::T()"<<std::endl;
    }

    // 복사 생성자
    T(const T& other) {
        std::cout<<"RVO -> T(const T& other)"<<std::endl;    
    }
    
    T f() {
        T result(0, 0);
        return result;
    }
};
T t1(0, 0);
T t2(t1.f()); // T t2 = t1.f(); 와 동일
```

상기에서 `T` 개체는 `t1`생성시 1회, `f()`에서 `result`를 생성하면서 2회, `f()`에서 리턴하면서 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 3회, `t2`를 생성하면서 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 전달받는 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하여 4회 생성될 것 같지만, 실제 확인해 보면 그렇지 않습니다.

GCC에서는 하기 2개만 실행됩니다.(컴파일러마다 다를 수 있습니다.)

```
RVO -> T::T()
RVO -> T::T()
```

이는 리턴값인 `result`가 `t2`에 전달되므로, 괜히 생성하고 전달하는게 아니라 리턴할 개체를 그냥 `t2`로 사용하기 때문입니다. 이를 Return Value Optimization(RVO) 라고 합니다. 

컴파일러마다 최적화하는 방법이 다를 수 있기 때문에, 컴파일러가 최적화를 쉽게 할 수 있도록 리턴값을 명시적으로 변수로 만들기 보다는,

```cpp
T result(0, 0);
return result; // (△) 비권장. 컴파일러가 최적화를 못할 수도 있습니다.
```

다음처럼 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 사용하는게 좋습니다.

```cpp
return result(0, 0); // (O) 임시 개체를 생성하는게 컴파일러가 최적화하기 편합니다.
```

**리턴 타입**

리턴 타입은 

1. 값을 리턴할 것인지, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)를 리턴할 것인지
2. [상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 리턴할 것인지, 비 상수를 리턴할 것인지

신중하게 결정해야 복사 부하를 줄이고, 타입에 기반한 **코딩 계약** 을 수립할 수 있습니다. 해당 방법에 대해서는 [Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)를 참고하기 바랍니다.

# 인자(매개변수, Parameter)

**명명된 인자 선언**

함수의 인자는 인자 타입과 이름을 지정하여 선언합니다.

```cpp
void f(int a, int b); // (O)
```

**이름이 없는 인자**

함수 정의부에서 사용하지 않는다면, 그리고 함수 포인터 선언 용도라면 인자명을 생략할 수 있습니다. 또한 템플릿 함수 구현시 오버로딩을 위해 생략할 수 있습니다.([타입 처리 방법 공통화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-traits/#%ED%83%80%EC%9E%85-%EC%B2%98%EB%A6%AC-%EB%B0%A9%EB%B2%95-%EA%B3%B5%ED%86%B5%ED%99%94) 참고)

```cpp
void f(int, int); // (O)
```

이외에, 사용하지 않는 인자를 억지로 작성해야 한다는건 악취가 난다는 뜻입니다. 설계 변경을 추천합니다. 그럼에도 불구하고 꼭 인자 생략을 해야 한다면, 하기 작성 방법을 고려해 보세요.

```cpp
void f(int, int) {
    // (△) 비권장. 어떤 인자가 필요했던 것인지 알기 힘듭니다.
}
```

보다는

```cpp
void f(int /*a*/, int /*b*/) {
    // (△) 비권장. 주석 처리하는 부분이 귀찮아 집니다.
}
```

보다는

```cpp
void f(int a, int b) {
    a; // (O) 사용하지 않는 인자에 대한 warning 제거
    b; // (O) 사용하지 않는 인자에 대한 warning 제거
}
```

가 낫습니다.

**인자 없음**

인자가 없으면 생략하거나 `void`를 작성할 수 있습니다.

```cpp
void f(); // (O)
void f(void); // (O)
```

`void`를 다른 인자와 함께 섞어 쓰거나(`void*`는 포인터형이기에 괜찮습니다.), `const`와 함께 쓰는건 컴파일 오류가 납니다.

```cpp
int f(void, int); // (X) 컴파일 오류
int f(const void); // (X) 컴파일 오류
```

**가변 인자(...)**

인자가 1개 이상인 경우 `...`으로 가변 인자를 처리할 수 있습니다.

|항목|내용|
|--|--|
|`va_start`|가변 인자 액세스 시작 매크로 함수|
|`va_arg`|가변인자 추출 매크로 함수|
|`va_end`|가변 인자 사용 종료 매크로 함수|
|`va_list`|가변 인자에 대한 `typedef`|

가변 인자를 사용하려면 `cstdarg`를 `include`해야 합니다.

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
        va_end(paramList); // 가변인자 처리 끝
        return result;       
    }
};
EXPECT_TRUE(T::Sum(3, 1, 2, 3) == 1 + 2 + 3);
```

# 기본값 인자

함수 선언시 인자에 기본값을 줄 수 있습니다.

```cpp
int f1(int a = 10) {  // 인수를 전달하지 않으면 기본값 10
    return a;
}

EXPECT_TRUE(f1() == 10); // 아무값도 주지 않으면 a 는 10
EXPECT_TRUE(f1(20) == 20);
```

기본값 인자를 사용하면, 그 뒤로는 다 기본값을 사용해야 합니다.(단 `...`은 가능합니다. `int g(int n = 0, ...);`)

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

`this` 는 기본값으로 사용할 수 없습니다.
   
```cpp
class T {
    void f3(T* p = this) {} // (X) 컴파일 오류. this는 기본값을 사용할 수 없음
};
```

전역 변수를 기본값으로 사용하면, 런타임 호출 시점의 값을 반영합니다.

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

전역 변수의 경우와 마찬가지로 함수 호출을 기본값으로 사용하면, 런타임 호출 시점의 값을 반영합니다.

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

상속 관계에서 인자의 기본값을 재정의하면, 혼란을 야기할 수 있습니다.

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

다음과 같이 `Base`와 `Derived`의 인스턴스를 생성하고 호출하면, 각자의 기본값대로 처리됩니다.

```cpp
Base b;
Derived d;
EXPECT_TRUE(b.f6() == 10);   
EXPECT_TRUE(d.f6() == 20);    
```

하지만 부모 개체인 `Base*` 통해 접근하면 `Base`의 기본값으로 처리됩니다. 이러한 차이가 생기므로, 부모 개체와 자식 개체의 기본값은 같은 값으로 하는게 좋습니다.

```cpp
Derived d;
Base* p = &d;
EXPECT_TRUE(p->f6() == 10); // 가상 함수 테이블을 참조하여 Base 의 기본값인 10을 사용합니다.   
```

**인자 타입**

인자 타입은 

1. 값을 전달받을 것인지, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)를 전달받을 것인지
2. [상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 전달받을 것인지, 비 상수를 전달받을 것인지

신중하게 결정해야 복사 부하를 줄이고, 타입에 기반한 **코딩 계약** 을 수립할 수 있습니다. 해당 방법에 대해서는 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)를 참고하기 바랍니다.

# 오버로딩된 함수 결정 규칙

C++언어에서는 이름이 동일한 함수를 여러개 정의하여 사용할 수 있습니다. 이렇게 함수명이 동일한 경우 인자 타입에 따라 알맞은 함수를 호출하게 됩니다. 

**암시적 형변환**

기본적으로 인자 타입이 일치하는 것을 선택하지만, 해당 타입의 것이 없다면 암시적 형변환이 지원되는 것을 사용하게 됩니다.

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

인자의 타입을 검사할때 동일 타입으로 취급하는 규칙이 있습니다.

1. 배열은 포인터로 취급됩니다.

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

    와 같이 배열을 포인터로 다룰 수 있기 때문입니다.

2. 최상위 `const`는 인자 타입에서 제거하고 취급합니다.

    즉,

    ```cpp
    int f(int a);
    int f(const int a);
    ```
    
    는 동일합니다. `int`로 받던, `const int`로 받던 최상위 데이터가 함수쪽으로 복제되는건 똑같으니까요.

    [포인터나 참조자]((https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)) 관점에서 보면,

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

3. 함수의 [상수성]((https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/))은 다른 타입으로 취급합니다.

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

    는 인자가 같으니, 오버로딩 후보 목록에서 1개만 사용됩니다.

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

# 오버로딩 함수 탐색 규칙

오버로딩 함수의 후보군은 하기 단계에 따라 수집되고 선정됩니다.
 
1. 자신의 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)에서 탐색
   
2. 함수 인자의 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)에서 탐색(ADL(Argument-dependent lookup) 또는 Koenig 검색)

3. 암시적 형변환을 포함하여 실행 가능 함수 결정
   
   1. 타입 완전 일치
   2. 경미한 암시적 형변환
   3. 승격 일치(`bool`을 `int`로, `char`를 `int`로) 
   4. 표준 변환(자식 개체 포인터를 부모 개체 포인터로, `T*`를 `void*`로, `int`를 `double`로, `double`을 `int`로) 
   5. 사용자 정의 형변환([형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고)


다음 경우를 보면 `g()` 에서 `MyFunc()`을 호출하면, 같은 네임스페이스에서 `MyFunc(double)`을 찾고, 인자 `1`을 `double`로 암시적으로 변환해서 사용하게 됩니다.

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

하지만 `MyFunc()`함수의 인자가 다음처럼 특정 네임스페이스의 것을 참조한다면, 

```cpp
int MyFunc(const C::Date&, double) {return 2;}
```

ADL(Argument-dependent lookup) 또는 Koenig 검색에 의해 네임스페이스 `C`의 함수들에서도 검색하게 됩니다.

그래서 함수 후보군은

* 네임스페이스 `C`에서는 `int MyFunc(const Date&, int)`
* 네임스페이스 `D`에서는 `int MyFunc(const C::Date&, double)`

이 찾아지게 되고, 타입이 완전 일치하는 네임스페이스 `C`의 `int MyFunc(const Date&, int)`이 사용됩니다.

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
