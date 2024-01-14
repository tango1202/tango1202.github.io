---
layout: single
title: "#2. [레거시 C++ 개체 지향] 복사 대입 연산자와 nothrow Swap"
categories: "legacy-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 1개라면, [암시적 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 정상 동작하도록 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/) 정의시 스마트 포인터(*[shared_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 사용하고, 필요없다면 못쓰게 만들어라.
> * [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 2개 이상이라면, [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 [예외에 안전](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)하도록 `swap()`으로 구현하고, 필요없다면 못쓰게 만들어라.

> **모던 C++**
> * (C++11~) [이동 연산](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 위해 [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다.
> * (C++11~) [default, delete](https://tango1202.github.io/cpp/modern-cpp-class/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.

# 복사 대입 연산자

[복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 개체의 내용을 복제하는 역할을 합니다. 기본 형태는 다음과 같습니다.

|항목|내용|
|--|--|
|`T& operator =(const T& other)`|[복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|

[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달되는 `other`는 수정할 수 없도록 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 사용하며, 복사 부하가 없도록 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)(`&`)를 사용합니다. 리턴값은 `a = b = c;`의 표현이 가능하도록 `T&`나 `const T&`를 리턴하며, `a = b = c;`의 표현을 사용하지 않을 것이라면 `void`를 리턴해도 됩니다.(*`T`를 리턴하는 것은 불필요하게 복사 연산을 하기 때문에 사용하지 않습니다.*)

# 암시적 복사 대입 연산자

[복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 마찬가지로, [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 정의하지 않으면, 컴파일러가 암시적으로 [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 정의해 줍니다. 기본 동작은 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 유사하게 멤버별 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)입니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) : 
        m_X(x), 
        m_Y(y) {} 
    // 암시적 복사 생성자의 기본 동작은 멤버별 복사 생성자 호출입니다.    
    // T(const T& other) :
    //     m_X(other.m_X),
    //     m_Y(other.m_Y) {}

    // 암시적 복사 대입 연산자의 기본 동작은 멤버별 복사 대입입니다.    
    // T& operator =(const T& other) {
    //     m_X = other.m_X;
    //     m_Y = other.m_Y;
    // }

    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};
T t1(10, 20);
T t2(1, 2); 
t2 = t1; // (O) 암시적 복사 대입 연산자 호출

EXPECT_TRUE(t2.GetX() == 10 && t2.GetY() == 20);
```

# swap을 이용한 예외 보증 복사 대입 연산자

[예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, [스택 풀기](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 언급된 것처럼 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하기 전의 상태를 [복원](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)해야 합니다.(*이를 [예외 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/)이라 합니다.*)

[암시적 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 각 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)별로 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 하는데요, 그러다 보니 중간에 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했을 경우, 이전에 이미 수정한 개체를 복원할 수 없어 [예외 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/)이 안됩니다.

```cpp
T& operator =(const T& other) {
    m_X = other.m_X;
    m_Y = other.m_Y; // 여기서 예외가 발생했다면 m_X를 되돌려야 합니다.
}
```

이러한 문제를 해결하기 위해, 

1. [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 만든 뒤,
2. `swap()`을 이용해 `this`와 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 바꿔치기하여,

[예외를 보증하는 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 구현할 수 있습니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) : 
        m_X(x), 
        m_Y(y) {} 
    // 암시적 복사 생성자의 기본 동작은 멤버별 복사 생성자 호출입니다.    
    // T(const T& other) :
    //     m_X(other.m_X),
    //     m_Y(other.m_Y) {}
    
    T& operator =(const T& other) {

        // other를 복제한 임시 개체를 만듭니다.
        T temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

        // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
        // this는 이제 other를 복제한 값을 가집니다.
        Swap(temp); 

        return *this;
        
    } // temp는 지역 변수여서 자동으로 소멸됩니다.

    // 멤버 변수들의 값을 바꿔치기 합니다.
    void Swap(T& other) {
        // (△) 비권장. int 형이라 복사 부하가 크지는 않습니다만, 
        // 조금 큰 개체라면 복사 부하가 있고 예외를 발생할 수 있습니다.
        std::swap(this->m_X, other.m_X); 
        std::swap(this->m_Y, other.m_Y);
    }

    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};
T t1(10, 20);
T t2(1, 2); 
t2 = t1; // (O) swap 버전 복사 대입 연산자 호출

EXPECT_TRUE(t2.GetX() == 10 && t2.GetY() == 20);
```

# swap의 복사 부하

[swap을 이용한 예외 보증 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 [예외 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/)이 되어 좋습니다만, 심각한 복사 부하가 있습니다.

다음 코드를 테스트 해보면,

```cpp
class T {
public:
    T() {}
    T(const T& other) {
        std::cout << "T::T(const T& other)" << std::endl;   
    }
    T& operator =(const T& other) {
        std::cout << "T::operator =()" << std::endl;
        return *this; 
    } 
};
T t1;
T t2;

t1 = t2; // 복사 대입 1회
std::swap(t1, t2); // 복사 생성 1회 복사 대입 2회
```

1. [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)별 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 방식을 사용하면, [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산이 1회 일어나지만, 
2. `swap()`을 이용하면, [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 1회와 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산 2회가 발생하는 걸 알 수 있습니다. 

보통 `swap()`은 다음과 같이 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 만들고, 각각 값을 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하기 때문에 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산에서 복사 부하가 있을 수 밖에 없습니다. 또한 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 과정에서 또다른 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 수도 있죠.

```cpp
swap(T& left, T& right) {
    T temp(right); // 복사 생성 1회, 멤버별 복사 대입 연산과 거의 동등한 부하
    right = left;  // 복사 대입 연산 1회 - swap에 따른 추가 복사 부하
    left = temp; // 복사 대입 연산 1회 - swap에 따른 추가 복사 부하
}
```


[임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 만들고 버리는 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)은 미세한 차이는 있겠으나 멤버별 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산과 동등한 부하라고 볼 수 있습니다. 하지만, `swap()` 과정에서 발생하는 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)은 `swap()` 때문에 추가된 부하입니다. 무려 2번이나 되죠. 

따라서, [nothrow swap](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94) 기법을 이용하여 `swap()`은 복사 부하도 없고, [예외도 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키기 않도록 최적화해야 합니다.

# nothrow swap - 포인터 멤버 변수를 이용한 swap 최적화

개체가 `int`형과 같은 기본 자료형 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 1~2개 사용하고 있다면, [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산 부하도 적고, [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 확률도 낮습니다. 그냥 [swap으로 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 구현하더라도 [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)이 없는 `nothrow swap`으로 취급해도 무방합니다. 

그러나, 아주 많은 기본 자료형을 사용하거나 동적으로 할당하는 거대한 데이터를 가지고 있다면, [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)시 복사 부하도 크고, 예외 발생 확률도 높습니다. `swap()`으로 구현해서 [예외 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/)은 되지만, 복사 부하나 [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 확률이 높다면 안되겠죠.

이런 문제를 해결하기 위해 비교적 복사 부하가 적고, [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 확률도 낮은 [포인터 멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 활용하여 [복사 대입 연산자를 swap](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 구현할 수 있습니다.(*포인터 복사는 8byte끼리의 복사이므로 복사 부하가 적고, [예외 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 확률도 낮습니다.*)

다음 예제에서

1. #1 : `Big`은 임의의 큰 데이터를 처리하는 클래스로 가정합니다.
2. #2 : `Big`의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에 메시지를 출력해서 복사 부하를 확인합니다.
3. #3 : `T`는 `Big`을 [포인터 멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)로 관리합니다.
4. #4 : [포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 없도록 `T`의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)에서 `Big`을 복제하고 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 합니다.
5. #5 : [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 만들고 `Swap()`으로 구현합니다.
6. #6 : `Swap()`은 [포인터 멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)들끼리 바꿔치기합니다. 실제 `Big`을 복사하는 것이 아니라 포인터만 복사하여 복사 부하를 줄입니다.

```cpp
class Big {
    int m_Val; // #1. 실제로는 복사 부하가 큰 데이터라고 생각해 주세요.
public:
    explicit Big(int val) : 
        m_Val(val) {}
    Big(const Big& other) : 
        m_Val(other.m_Val) {
        std::cout << "Big::Big(const Big& other)" << std::endl; // #2 
    }
    Big& operator =(const Big& other) {
        m_Val = other.m_Val;
        std::cout << "Big::operator =(const Big& other)" << std::endl;  // #2
        return *this;
    }    
    int GetVal() const {return m_Val;}
    void SetVal(int val) {m_Val = val;}
};
class T {
    Big* m_Big; // #3. 복사 부하가 큰 데이터는 포인터로 관리합니다.
public:
    explicit T(Big* big) : 
        m_Big(big) {} 
    // NULL 포인터가 아니라면 복제합니다.
    T(const T& other) :
        m_Big(other.m_Big != NULL ? new Big(*other.m_Big) : NULL) { // #4
    }
    // 힙 개체를 메모리에서 제거 합니다.
    ~T() {
        delete m_Big; // #4
    }
    
    T& operator =(const T& other) { // #5

        // other를 복제한 임시 개체를 만듭니다.
        T temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

        // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
        // this는 이제 other를 복제한 값을 가집니다.
        Swap(temp); 

        return *this;
        
    } // temp는 지역 변수여서 자동으로 소멸됩니다.

    // 멤버 변수들의 값을 바꿔치기 합니다.
    void Swap(T& other) { // #6
        // (O) 포인터 변수끼리의 복사/대입이라 복사 부하가 크지 않습니다.
        // 예외가 발생할 확률도 낮습니다.
        std::swap(this->m_Big, other.m_Big); 
    }

    const Big* GetBig() const {return m_Big;}
};
T t1(new Big(10));
T t2(new Big(1)); 
t2 = t1; // (O) swap 버전 복사 대입 연산자 호출

EXPECT_TRUE(t2.GetBig()->GetVal() == 10);
```
하기는 실행 결과 입니다. 

[복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산시 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(`temp`)를 생성하느라 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)(`T(const T& other)`)에서 `Big` 개체 1개를 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)한 것(*멤버별 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에서와 거의 동등한 부하입니다.*) 외에는 다른 복사 부하가 없습니다.

```cpp
Big::Big(const Big& other)
```

 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(`temp`)에서 [new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)된 `Big`개체들은 [this](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)에 포인터 복사되고, [this](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)가 관리하던 `Big`개체들은 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)에 전달된 후 버려집니다. 따라서 `Swap()`으로 인한 복사는 포인터 복사(*8byte 복사*) 뿐이므로, 복사 부하는 거의 없다고 보셔도 무방합니다.

즉, [포인터 멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)로 정의한 개체의 [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 `swap()`으로 구현하면,

1. [예외에 안전](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)하고,
2. 복사 부하는 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)별 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)과 거의 동등합니다.

# 복사 대입 연산자까지 지원하는 스마트 포인터

[복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 지원을 위해 [복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)에 [swap을 이용한 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 지원 기능을 추가하면, 

1. 복사 생성시 스마트 포인터에서 복제를 해주고,
2. 소멸시 스마트 포인터에서 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 해주고,
3. [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산시 스마트 포인터에서 `swap()`해 주므로,

[암시적 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [암시적 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90), [암시적 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)와 호환되어 별도로 구현할 필요가 없어집니다. 따라서 다음처럼 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/), [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 정의 없이 간소하게 클래스를 작성할 수 있습니다.

```cpp
class T {
    IntPtr m_Val;
public:
    explicit T(int* val) :
        m_Val(val) {}
    int GetVal() const {return *m_Val;}
};
```

다음은 전체 코드입니다.

```cpp
// 복사 생성시 m_Ptr을 복제하고, 소멸시 delete 합니다.
// 복사 대입 연산은 임시 개체 생성 후 swap 합니다.
class IntPtr {
private:
    int* m_Ptr; // new로 생성된 개체입니다.
public: 
    explicit IntPtr(int* ptr) :
        m_Ptr(ptr) {}

    // (O) NULL 포인터가 아니라면 복제합니다.    
    IntPtr(const IntPtr& other) :
        m_Ptr(other.IsValid() ? new int(*other.m_Ptr) : NULL) {}

    // 힙 개체를 메모리에서 제거 합니다.
    ~IntPtr() {delete m_Ptr;}

    IntPtr& operator =(const IntPtr& other) {

        // other의 힙 개체를 복제한 임시 개체를 만듭니다.
        IntPtr temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

        // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
        // this는 이제 other의 힙 개체를 복제한 값을 가집니다.
        Swap(temp); // (O) 포인터 끼리의 값 변경이므로 복사 부하가 없고, 예외가 발생하지 않습니다.

        return *this;
        // temp는 지역 변수여서 자동으로 소멸됩니다.
        // 소멸되면서 this가 이전에 가졌던 힙 개체를 소멸합니다.
    }
    // 멤버 변수들의 값을 바꿔치기 합니다.
    void Swap(IntPtr& other) {
        std::swap(this->m_Ptr, other.m_Ptr); // (O) 포인터 끼리의 값 변경이므로 복사 부하도 없고, 예외가 발생하지 않습니다.   
    }

    // 포인터 연산자 호출시 m_Ptr에 접근할 수 있게 합니다.
    const int* operator ->() const {return m_Ptr;}
    int* operator ->() {return m_Ptr;}

    const int& operator *() const {return *m_Ptr;}
    int& operator *() {return *m_Ptr;}

    // 유효한지 검사합니다.
    bool IsValid() const {return m_Ptr != NULL ? true : false;}    
};

class T {
    // (O) IntPtr로 복사 생성과 복사 대입시 포인터의 복제본을 만들고, 소멸시 IntPtr에서 delete 합니다.
    // (O) 암시적 복사 생성자에서 정상 동작하므로, 명시적으로 복사 생성자를 구현할 필요가 없습니다.
    // (O) 포인터 멤버 변수가 1개 있고, 내부적으로 복사 대입 연산시 swap하므로 복사 대입 연산자를 구현할 필요가 없습니다.
    IntPtr m_Val;
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}
    int GetVal() const {return *m_Val;}
};
// (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(t1); // 새로운 int형 개체를 만들고 10을 복제합니다.

    EXPECT_TRUE(t2.GetVal() == 10);
} 
// (O) 복사 대입 연산 시에도 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(new int(20));
    t2 = t1; // (O) swap 버전 복사 대입 연산자 호출
    EXPECT_TRUE(t2.GetVal() == 10);
}
```

# 멤버 변수가 2개 이상인 경우 스마트 포인터와 복사 대입 연산자와의 호환성

[복사 대입 연산자를 지원하는 스마트 포인터](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 사용하더라도, 만약 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 2개 이상이라면, [암시적 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)와 기본적인 호환은 되나, 기존에 수정된 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 되돌릴 수 없으므로 [예외 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/)은 지원하지 않습니다.

```cpp
class T {
    IntPtr m_Val1;
    IntPtr m_Val2;
public:
    // 암시적 복사 대입 연산자의 기본 동작은 멤버별 복사 대입입니다.    
    T& operator =(const T& other) {
        m_X = other.m_X;
        m_Y = other.m_Y; // (△) 비권장. 여기서 예외가 발생했다면 m_X를 되돌려야 합니다.
    }    
};
```

따라서, 스마트 포인터를 사용했더라도, [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 2개 이상이라면, [swap으로 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 구현해야 합니다.

```cpp
class T {
    // (O) IntPtr로 복사 생성과 복사 대입시 포인터의 복제본을 만들고, 소멸시 IntPtr에서 delete 합니다.
    // (O) 암시적 복사 생성자에서 정상 동작하므로, 명시적으로 복사 생성자를 구현할 필요가 없습니다.
    // (O) 포인터 멤버 변수가 2개 있어, 예외에 안전하지 않으므로 swap으로 복사 대입 연산자를 구현합니다.
    IntPtr m_Val1;
    IntPtr m_Val2;
public:
    // val1, val2 : new 로 생성된 것을 전달하세요.
    T(int* val1, int* val2) :
        m_Val1(val1),
        m_Val2(val2) {}
    T& operator =(const T& other) {
        T temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.
        Swap(temp); // (O) 포인터 끼리의 값 변경이므로 복사 부하가 없고, 예외가 발생하지 않습니다.
        return *this;
    } 
    void Swap(T& other) {
        m_Val1.Swap(other.m_Val1); // 포인터 끼리의 값 변경이므로 복사 부하도 없고, 예외가 발생하지 않습니다. 
        m_Val2.Swap(other.m_Val2);
    }

    int GetVal1() const {return *m_Val1;}
    int GetVal2() const {return *m_Val2;}
};
// (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10), new int(20));
    T t2(t1); // 새로운 int형 개체를 만들고 10, 20을 복제합니다.

    EXPECT_TRUE(t2.GetVal1() == 10 && t2.GetVal2() == 20);
} 
// (O) 복사 대입 연산 시에도 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10), new int(20));
    T t2(new int(1), new int (2));
    t2 = t1; // (O) swap 버전 복사 대입 연산자 호출
    EXPECT_TRUE(t2.GetVal1() == 10 && t2.GetVal2() == 20);
}
```

혹은 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 무조건 1개로 유지하는 방법도 있습니다.(*[PImpl 이디엄](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-pimpl/) 참고*)

# 복사 대입 연산자 사용 제한

[복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)의 경우와 마찬가지로, 만약 [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 필요없다면, [암시적 복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)도 사용할 수 없도록 `private` 로 만드는게 좋습니다. 어짜피 사용하지 않을거라 내버려 뒀는데, 누군가가 유지보수 하면서 무심결에 [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 사용하게 된다면, 오동작을 할 수 있거든요. ***의도하지 않았다면 동작하지 않게 해야 합니다.***

> *(C++11~) [default, delete](https://tango1202.github.io/cpp/modern-cpp-class/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.*