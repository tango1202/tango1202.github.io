---
layout: single
title: "#19. [고전 C++ 가이드] 함수"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 동적 예외 사양을 사용하지 마라.
> * 함수 포인터 대신 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 나 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하라.
> * 다형적인 가상 함수에서 부모 개체와 자식 개체의 기본값을 다르게 하지 마라.
> * 함수 오버로딩시 함수 인자의 유효 공간에서도 탐색(ADL(Argument-dependent lookup) 또는 Keonig 검색)하는 원리를 이해하라.

# 개요

함수는 다음 목적을 위해 만듭니다.

1. 함수 코드 재활용(코드 중복 제거) 
2. 함수 인자와의 **코딩 계약**
3. 디버깅 편의성

함수 정의의 일반적인 형태는 하기와 같습니다.(`[]`인 부분은 옵션입니다.)

```cpp
return-type function-name(parameter-list) [const] [throw(exception-list)] {}
```

|항목|내용|
|--|--|
|`return-type`|함수 결과의 타입. 단, 배열은 안됨|
|`[const]`|멤버 함수인 경우 개체를 수정하지 않음([상수(const), 변경 가능 지정자(mutable), 최적화 제한 지정자(volatile)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable/) 참고)
|`[throw(exception-list)]`|함수가 발생하는 예외 사양.<br/>나열된 예외 이외에는 `unexpected`로 강제 변환됨. 사용하지 말 것.([동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-dynamic-exception-specification) 참고)|

# 함수 포인터

함수 포인터를 이용하여 함수 자체를 변수처럼 사용할 수 있습니다.

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
p = f; // 함수 포인터에 f 함수 대입.
p(10); // f 함수 실행
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
Button minusButton(minus); //클릭시 빼기

EXPECT_TRUE(plusButton.Click(10, 20) == 30); 
EXPECT_TRUE(minusButton.Click(10, 20) == -10); 

```

상기의 사례는 함수 포인터 대신 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 나 [Strategy 패턴](https://tango1202.github.io/pattern/pattern-strategy/)을 이용하거나 **의존성 주입([의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고)** 을 활용해서도 구현 할 수 있습니다.


**멤버 함수 포인터**

개체의 멤버 함수도 함수 포인터로 접근 할 수 있습니다.

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

# 인자(매개변수, Parameter) 작성법

**명명된 인자 선언**

함수의 인자는 하기와 같이 인자 이름과 함께 선언합니다.

```cpp
void f(int a, int b); // (O)
```

**이름이 없는 인자**

함수 정의부에서 사용하지 않는다면, 그리고 함수 포인터 선언 용도라면 인자명을 생략할 수 있습니다.

```cpp
void f(int, int); // (O)
```

사용하지 않는 인자를 억지로 작성해야 한다는건 악취가 난다는 뜻입니다. 설계 변경을 추천합니다. 그럼에도 불구하고 꼭 인자 생략을 해야 한다면, 하기 작성 방법을 고려해 보세요.

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

인자가 없으면 하기와 같이 생략하거나 `void`를 작성할 수 있습니다.

```cpp
void f(); // (O)
void f(void); // (O)
```

하기와 같이 `void`를 다른 인자와 섞어 쓰거나(`void*`는 포인터형이기에 괜찮습니다.), `const`와 함께 쓰는건 컴파일 오류가 납니다.

```cpp
int f(void, int); // (X) 컴파일 오류
int f(const void); // (X) 컴파일 오류
```

**가변 인자(...)**

인자가 1개 이상인 경우 `...`을 이용하여 가변 인자를 처리할 수 있습니다.

|항목|내용|
|--|--|
|`va_start`|가변 인자 액세스 시작 매크로 함수|
|`va_arg`|가변인자 추출 매크로 함수|
|`va_end`|가변 인자 사용 종료 매크로 함수|
|`va_list`|가변 인자에 대한 `typedef`|

가변 인자를 사용하려면 `cstdarg`를 `include`해야 합니다.

```cpp
#include <cstdarg>
 
int sum(int count, ...) {
    int result = 0;
    std::va_list argList; // 가변 인자
    va_start(argList, count); // 가변 인자 처리 시작
    for (int i = 0; i < count; ++i) {
        result += va_arg(argList, int); // 가변 인자 추출
    }
    va_end(argList); // 가변인자 처리 끝
    return result;       
}
 
EXPECT_TRUE(sum(3, 1, 2, 3) == 1 + 2 + 3);
```

# 기본값 인자

함수 선언시 인자에 기본값을 줄 수 있습니다.

```cpp
int f1(int a = 10) { 
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

하기와 같이 `Base`와 `Derived`의 인스턴스를 생성하고 호출하면, 각자의 기본값대로 처리됩니다.

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
EXPECT_TRUE(p->f6() == 10); // vtable을 참조하여 Base 의 기본값인 10을 사용합니다.   
```

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

    포인터나 참조자 관점에서 보면,

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

3. 함수의 상수성은 다른 타입으로 취급합니다.

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
    double f(int) {return 1.};
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
    // double f(int) {return 1.};
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
 
1. 자신의 유효 공간에서 탐색
   
2. 함수 인자의 유효 공간에서 탐색(ADL(Argument-dependent lookup) 또는 Keonig 검색)

3. 암시적 형변환을 포함하여 실행 가능 함수 결정
   
   1. 타입 완전 일치
   2. 경미한 암시적 형변환
   3. 승격 일치(`bool`을 `int`로, `char`를 `int`로) 
   4. 표준 변환(자식 개체 포인터를 부모 개체 포인터로, `T*`를 `void*`로, `int`를 `double`로, `double`을 `int`로) 
   5. 사용자 정의 형변환([형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고)


하기의 경우를 보면 `g()` 에서 `MyFunc()`을 호출하면, 같은 네임스페이스에서 `MyFunc(double)`을 찾고, 인자 `1`을 `double`로 암시적으로 변환해서 사용하게 됩니다.

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

하지만 `MyFunc()`함수의 인자가 네임스페이스 `C`의 것을 참조한다면, ADL(Argument-dependent lookup) 또는 Keonig 검색에 의해 네임스페이스 `C`의 함수들에서도 검색하게 됩니다.

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
        // (△) 비권장. Koenig 검색이 활용됨. 
        // 인자로 전달한 d 가 네임스페이스 C에 있기 때문에, C의 함수들중 MyFunc(const Date&, int)를 찾아냄
        // MyFunc(const Date&, int) 과 MyFunc(const C::Date&, double) 중 타입이 일치하는 MyFunc(const Date&, int)이 채택됨
        // 명시적으로 C::MyFunc 을 호출하는게 분석에 좋을 수도 있음
        return MyFunc(d, 1); 
    }  
}

EXPECT_TRUE(D::g() == 1); // C::MyFunc 이 채택됨
```
