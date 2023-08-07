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
> * 클래스, 함수, 멤버 함수, 중첩 클래스를 템플릿으로 만들 수 있다.

# 템플릿 클래스

템플릿 클래스는 하기와 같이 정의되고 인스턴스화 됩니다.

```cpp
// 템플릿 클래스 정의부 - 코드가 생성되지 않은 상태
template<typename T> 
class MyClass {
public:
    T Plus(T left, T right) const {return left + right;}    
}; 

// 템플릿 인스턴스부 - 이때 코드가 생성됨
MyClass<int> myClass;
EXPECT_TRUE(myClass.Plus(10, 10) == 20); 
```

1. `typename` : `class` 키워드를 사용해도 됩니다. 클래스로 한정하지 않고 타입의 이름이란 의미로 `typename`을 더 선호합니다.
2. `T` : 템플릿 인자(Parameter)입니다. 향후 인스턴스화시 구체 타입으로 대체 됩니다.
3. `int` : 템플릿 인자 `T`가 대체될 인수(Argument)입니다.  

# 템플릿 정의부와 인스턴스부

템플릿은 개체 타입만 변경하여 클래스나 함수를 생성할 수 있는 틀이고, 인스턴스화를 거쳐 클래스나 함수 코드를 생성합니다.

템플릿은 템플릿 정의부와 템플릿 인스턴스화 부분으로 나뉩니다. 

1. 템플릿 정의부 : 아직 타입이 구체화 되지 않은 상태(코드가 생성되지 않은 상태) 
2. 템플릿 인스턴스부 : 지정한 타입과 바인딩되어 구체적인 클래스를 생성(코드가 생성된 상태)

다음 코드는 클래스 `B`에서 클래스 `T`를 상속받고, `T::TType`으로 멤버 변수를 정의합니다. `TType`이 `private`이기 때문에 컴파일 오류가 나와야 하나 아직 인스턴스 부가 없어 컴파일 오류가 없습니다.

```cpp
// 멤버 엑세스 지정
template<typename T> 
class A {
private:
    typedef T TType;
};  

template<typename T> 
class B : public T { // T로 A<int> 를 전달할 예정임. A<int>::TType은 int이나 private여서 접근 불가
    typename T::TType m_Member; // typename은 종속 타입 참고
};
```

다음처럼 인스턴스화 하면 그제야 코드를 생성하면서 컴파일 오류가 발생합니다.

```cpp
B<A<int>> b; // (X) 컴파일 오류. 템플릿 인스턴스화시 private 멤버 접근 오류 발생 
```

# 템플릿 멤버의 불완전성

템플릿은 인스턴스화 될때 코드를 생성합니다. 따라서, 템플릿 정의 시점에는 멤버 함수가 불완전해도 컴파일이 됩니다. 멤버 함수가 사용되지 않는한, 꼭 정의할 필요는 없습니다.

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

# 템플릿 클래스 별칭

템플릿 클래스는 다음과 같이 별칭을 사용할 수 있습니다.

```cpp
template<typename T>
class A {};

template<typename T>
using Alias = A<T>; // A의 별칭

Alias<int> alias;
```

# 템플릿 함수

템플릿 함수는 다음처럼 정의하고 인스턴스화 합니다.

```cpp
template <typename T>
T Plus(T left, T right) {
    return left + right;
}

EXPECT_TRUE(Plus<int>(10, 10) == 20);
EXPECT_TRUE(Plus<char>('a', 1) == 'b');
```

명시적인 타입을 생략하고 인수의 타입에 따라 추론이 가능합니다. 

```cpp
EXPECT_TRUE(Plus(10, 10) == 20); // (O) 인수로부터 int가 추론됨
EXPECT_TRUE(Plus<>(10, 10) == 20); // (O) <>를 기재하여 템플릿 함수를 명시하고, 인수로부터 추론

EXPECT_TRUE(Plus('a', 1) == 'b'); // (X) 인수가 int, char로 각각 다르므로 추론이 어려움 
EXPECT_TRUE(Plus('a', static_cast<char>(1)) == 'b'); // (O) 인수로부터 char 가 추론됨
```

템플릿 인수 추론의 좀더 상세한 내용은 [템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)을 참고하시기 바랍니다.

# 템플릿 멤버 함수와 템플릿 중첩 클래스

멤버 함수, 중첩 클래스도 템플릿으로 만들 수 있습니다.

단,

1. 소멸자, 복사 생성자는 만들 수 없습니다.
2. 가상 함수는 지원하지 않습니다.

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

선언과 정의를 분리할 때, 바깥쪽 템플릿과 안쪽 템플릿이 있다면, 여러개의 인자 집합이 필요합니다.

```cpp
template<typename T> 
class A {
public:    
    void f(); // 멤버 함수 선언

    template<typename U>
    U g(U val); // 템플릿 멤버 함수 선언
};

// 템플릿 클래스의 멤버 함수 정의
template<typename T> // 템플릿 클래스
void A<T>::f() {} 

// 템플릿 클래스의 템플릿 멤버 함수 정의 - 인자 집합 2개 필요
template<typename T> // 바깥쪽 인자
template<typename U> // 안쪽 인자
U A<T>::g(U val) {return val + val;} 

A<int> a;

a.f(); // 일반 함수 호출
a.g<int>(10); // 템플릿 멤버 함수 호출
```

# 함수 오버로딩

일반 멤버 함수와 동일하게 템플릿 멤버 함수도 오버로딩 할 수 있습니다. 단, 인자 타입이 동일하면, 일반 멤버 함수가 호출됩니다.

```cpp
template<typename T>
class A {
public:    
    int f(int) {return 1;} // #1. 멤버 함수
 
    template<typename U>
    int f(U) {return 2;} // #2. 템플릿 멤버 함수
};

A<int> a;

EXPECT_TRUE(a.f(10) == 1); // f(int) 호출
EXPECT_TRUE(a.f<>(10) == 2); // f<int>(int) 호출
EXPECT_TRUE(a.f('a') == 2); // f<char>(char) 호출
```
