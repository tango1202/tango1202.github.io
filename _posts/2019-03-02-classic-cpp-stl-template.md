---
layout: single
title: "#2. [고전 C++ STL] 템플릿"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 템플릿은 인스턴스화 되기 전에는 코드를 생성하지 않는다.
> * 클래스, 함수, 멤버 함수, [중첩 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A4%91%EC%B2%A9-%ED%81%B4%EB%9E%98%EC%8A%A4)를 템플릿으로 만들 수 있다.

> **모던 C++**
> * (C++11~) [extern으로 템플릿 선언](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#extern-%ED%85%9C%ED%94%8C%EB%A6%BF)을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다.
> * (C++11~) [export 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#export-%ED%85%9C%ED%94%8C%EB%A6%BF)은 제대로 구현한 컴파일러는 드물고, 세부사항에 대한 의견이 일치하지 않아 remove 되었습니다.
> * (C++14~) [변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 변수도 템플릿으로 만들 수 있습니다.
> * (C++17~) [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)처럼 타입을 생략할 수 있습니다.

# 템플릿

타입에 따라 클래스나 함수를 생성하는 틀입니다.

# 클래스 템플릿

클래스 템플릿는 하기와 같이 정의되고 인스턴스화 됩니다.

```cpp
// 클래스 템플릿 정의부 - 코드가 생성되지 않은 상태
template<typename T> 
class MyClass {
public:
    T Plus(T left, T right) const {return left + right;}    
}; 

// 템플릿 인스턴스부 - 이때 코드가 생성됨
MyClass<int> myClass;
EXPECT_TRUE(myClass.Plus(10, 10) == 20); 
```

1. `typename` : [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 정의에 사용합니다.(`class` 키워드를 사용해도 됩니다. 클래스로 한정하지 않고 타입의 이름이란 의미로 `typename`을 더 선호합니다.)
2. `T` : [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)(Parameter)입니다. 향후 인스턴스화시 구체 타입으로 대체 됩니다.
3. `int` : [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) `T`가 대체될 인수(Argument)입니다.  

> *(C++14~) [변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 변수도 템플릿으로 만들 수 있습니다.*<br/>
> *(C++17~) [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)처럼 타입을 생략할 수 있습니다.*

# 템플릿 정의부와 템플릿 인스턴스화

템플릿은 개체 타입만 변경하여 클래스나 함수를 생성할 수 있는 틀이고, 인스턴스화를 거쳐 클래스나 함수 코드를 생성합니다.

템플릿은 템플릿 정의 부분과 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 부분으로 나뉩니다. 

1. 템플릿 정의 : 아직 타입이 구체화 되지 않은 상태(*코드가 생성되지 않은 상태*) 
2. [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) : [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)가 지정한 타입으로 대체되어 구체적인 클래스를 생성한 상태(*코드가 생성된 상태*)입니다.

    다음 클래스 템플릿는
    ```cpp
    template<typename T> 
    class MyClass {
    public:
        T Plus(T left, T right) const {return left + right;}    
    };
    ```
    
    `MyClass<int>`와 같이 인스턴스화 하면 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) `T`가 `int`로 대체되어 다음과 같아집니다.

    ```cpp
    class MyClass<int> {
    public:
        int Plus(int left, int right) const {return left + right;}    
    };
    ```

템플릿은 인스턴스화 될때 코드를 생성하므로, 템플릿 정의 시점에는 멤버 함수가 불완전해도 컴파일이 됩니다. 단, 인스턴스화 시점에는 컴파일에 필요한 정보를 모두 알고 있어야 합니다.

```cpp
template<typename T>
class A {
public:    
    void f() {}
    void g(); // 함수 선언만 됨. 불완전함. 함수 정의부가 없음
};

A<int> a;
a.f(); // A<int>::g() 를 사용하지 않았기에 컴파일 됨 
a.g(); // (X) 컴파일 오류. 함수 정의부가 없음
```

# 함수 템플릿

[함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)은 다음처럼 정의하고 인스턴스화 합니다.

```cpp
template<typename T>
T Plus(T left, T right) {
    return left + right;
}

EXPECT_TRUE(Plus<int>(10, 10) == 20);
EXPECT_TRUE(Plus<char>('a', 1) == 'b');
```

명시적인 타입을 생략하고 인수의 타입에 따라 추론이 가능합니다. 

```cpp
EXPECT_TRUE(Plus(10, 10) == 20); // (O) 인수로부터 int가 추론됨
EXPECT_TRUE(Plus<>(10, 10) == 20); // (O) <>를 기재하여 함수 템플릿임을 명시하고, 인수로부터 추론

EXPECT_TRUE(Plus('a', 1) == 'b'); // (X) 컴파일 오류. 인수가 int, char로 각각 다르므로 추론이 어려움 
EXPECT_TRUE(Plus('a', static_cast<char>(1)) == 'b'); // (O) 인수로부터 char 가 추론됨
```

템플릿 인수 추론의 좀더 상세한 내용은 [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)을 참고하시기 바랍니다.

# 템플릿 멤버 함수와 템플릿 중첩 클래스

멤버 함수, [중첩 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A4%91%EC%B2%A9-%ED%81%B4%EB%9E%98%EC%8A%A4)도 템플릿으로 만들 수 있습니다.

단,

1. 생성자는 타입을 명시적으로 지정할 수 없습니다. 인수로부터 추론됩니다.
2. [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/), [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)는 만들 수 없습니다.
3. 가상 함수는 지원하지 않습니다.

```cpp

class A {
public:
    // 템플릿 생성자
    template<typename T>
    explicit A(T val) {}

    // 일반 멤버 함수
    void f() {}

    // 템플릿 멤버 함수
    template<typename U> 
    U g(U left, U right) {return left + right;} 

    // 템플릿 중첩 클래스
    template<typename V> 
    class B {
    public:
        void h() {}
    };
};

int val;
A a(val); // 템플릿 생성자 호출, 타입을 명시적으로 지정 못함
a.f(); // 일반 함수 호출
a.g<int>(10, 10); // 템플릿 멤버 함수 호출

A::B<int> b; // 중첩 클래스 인스턴스화
b.h(); 
```

# 템플릿 선언과 정의 분리

선언과 정의를 분리할 때, 바깥쪽 템플릿과 안쪽 템플릿이 있다면, 여러개의 [템플릿 인자](?https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 집합이 필요합니다.

```cpp
template<typename T> 
class A {
public:    
    void f(); // 멤버 함수 선언

    template<typename U>
    U g(U val); // 템플릿 멤버 함수 선언
};

// 클래스 템플릿의 멤버 함수 정의
template<typename T> // 클래스 템플릿
void A<T>::f() {} 

// 클래스 템플릿의 템플릿 멤버 함수 정의 - 인자 집합 2개 필요
template<typename T> // 바깥쪽 인자
template<typename U> // 안쪽 인자
U A<T>::g(U val) {return val + val;} 

A<int> a;

a.f(); // 일반 함수 호출
a.g<int>(10); // 템플릿 멤버 함수 호출
```

# 템플릿 인스턴스 중복 생성

동일한 템플릿을 인스턴스화 할때 각각의 파일에서 템플릿 정의를 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)하면 각 파일별로 템플릿 인스턴스가 생성됩니다.(마치 템플릿을 [인라인화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-inline/) 하는 것 처럼요.) 이에 따라 프로그램 코드 용량이 커질 수 있습니다.

```cpp
// test.h 에서
template<typename T>
T Add(T a, T b) {
    return a + b;
}

// test1.cpp 에서
#include "test.h"
Add(1, 2); // Add<int>()가 정의 되어 포함됩니다.

// test2.cpp 에서
#include "test.h"
Add(10, 20); // Add<int>가 재정의 되어 포함됩니다.
```

> *(C++11~) [extern으로 템플릿 선언](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#extern-%ED%85%9C%ED%94%8C%EB%A6%BF)을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다.*

# export 템플릿

[템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 템플릿 정의가 필요하므로, 분할 컴파일이 되지 않으며, 외부에 모듈을 제공할 때 템플릿 정의 코드를 함께 제공해야 하는 문제가 있었습니다.([분할 컴파일을 위한 선언과 정의 분리](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EB%B6%84%ED%95%A0-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EC%9D%84-%EC%9C%84%ED%95%9C-%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 참고) 

이러한 문제를 해결하기 위해 템플릿 선언과 정의를 분리하는 `export`를 제공했는데요,

헤더 파일에서,

```cpp
// test.h 에서
export template<typename T> // 선언만 합니다.
T MyAdd(T a, T b);
```

cpp 파일에서,

```cpp
// test.cpp 에서
template<typename T>
T MyAdd(T a, T b) {
    return a + b; // 구현 코드 입니다.
}
```

사용하는 곳에서,

```cpp
// test1.cpp 에서
#include "test.h"
MyAdd(1, 2); 

// test2.cpp 에서
#include "test.h"
MyAdd(10, 20); 
```

하면 되도록요.

하지만, 제대로 구현한 컴파일러는 드물고, 세부사항에 대한 의견도 일치하지 않아 C++11 부터 완전 remove 되었습니다. 

> *(C++11~) [export 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#export-%ED%85%9C%ED%94%8C%EB%A6%BF)은 제대로 구현한 컴파일러는 드물고, 세부사항에 대한 의견이 일치하지 않아 remove 되었습니다.*
