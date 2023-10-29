---
layout: single
title: "#14. [모던 C++] (C++11~) constexpr, constexpr 함수, constexpr 생성자, (C++17~) if constexpr"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다.
> * (C++14~) [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98) 제약이 완화되어 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), 2개 이상의 리턴문, `if()`, `for()`, `while()` 등을 사용할 수 있습니다.
> * (C++17~) [if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr)을 이용하면 조건에 맞는 부분만 컴파일하고, 그렇지 않으면 컴파일 하지 않습니다.

# 개요

[템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 에서 언급한 것처럼 컴파일 타임에 여러가지 프로그래밍이 가능합니다. 하지만, 템플릿으로 우회하며 작성하다보니 상당히 난해했는데요, 

C++11 부터는 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr) 이용해 **컴파일 타임 상수 표현식**을 지정할 수 있어 컴파일 타임 프로그래밍 환경이 좀더 쉬워졌습니다.

# constexpr

컴파일 타임 상수로 지정합니다. 

컴파일 타임 상수는 읽기 전용 메모리인 [코드 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%BD%94%EB%93%9C-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)에 할당되므로 예외에 안전합니다. 할 수 있다면 최대한 컴파일 타임 상수로 만드는게 좋습니다.

컴파일 타임 상수는 다음과 같이 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 값지정이나 비 템플릿 개체 인수 전달을 통해 확인 할 수 있습니다.

`const int` 를 사용하면 컴파일 타임 상수로 취급되는되요,

```cpp
template<int val>
class T {
public:
    int operator ()() {return val;} 
};

const int size{20}; // 상수 입니다.

enum class MyEnum_11 {Val = size}; // (O) size는 컴파일 타임 상수 입니다.
T<size> t; // (O) size는 컴파일 타임 상수 입니다.
```

하지만, 변수로부터 `const int`를 초기화 하면, 런타임 상수이지, 컴파일 타임 상수가 아니어서 컴파일 오류가 납니다.

```cpp
int a{20};
const int size{a}; // 변수로부터 const int를 초기화 해서 런타임 상수 입니다.

enum class MyEnum_11 {Val = size}; // (X) 컴파일 오류. size는 런타임 상수 입니다.
T<size> t; // (X) 컴파일 오류. size는 런타임 상수 입니다.
```

[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)은 좀더 명시적으로 컴파일 타임 상수임을 알려 줍니다.

```cpp
constexpr int size_11{20}; // 컴파일 타임 상수 입니다.

enum class MyEnum_11 {Val = size_11}; // (O)
T<size_11> t; // (O) 
```

[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)은  `const int`와 달리 변수를 대입하면 컴파일 오류가 발생합니다.

```cpp
int a{20};
constexpr int size_11{a}; // (X) 컴파일 오류. 상수를 대입해야 합니다.
```

# constexpr 함수

기존에는 컴파일 타임 함수가 없었고, 함수처럼 동작하도록 다음처럼 템플릿을 활용했는데요([템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 참고),

```cpp
// Factorial n -1 을 재귀 호출합니다.
template<unsigned int n>
struct Factorial { 
    enum {
        Val = n * Factorial<n-1>::Val
    };  
};
// 0일때 특수화 버전. 더이상 재귀 호출을 안합니다.
template<>
struct Factorial<0> { 
    enum {
        Val = 1
    }; 
};
// 컴파일 타임에 계산된 120이 Val에 대입됩니다.
enum class MyEnum {Val = Factorial<5>::Val};
EXPECT_TRUE(static_cast<int>(MyEnum::Val) == 1 * 2 * 3 * 4 * 5);
```

C++11 부터는 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)을 이용하여 암시적으로 [인라인 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-inline/)인 컴파일 타임 함수를 만들 수 있습니다.

1. 컴파일 타임 상수를 전달하면 컴파일 타임 함수로 동작하고,
2. 일반 변수를 전달하면, 일반 함수들처럼 런타임 함수로 동작합니다.

```cpp
constexpr int Factorial_11(int val) {
    // 마지막으로 1에 도달하면 재귀호출 안함
    return val == 1 ? val : val * Factorial_11(val - 1);
}

// 컴파일 타임에 계산된 120이 Val에 대입됩니다.
enum class MyEnum_11 {Val = Factorial_11(5)};
EXPECT_TRUE(static_cast<int>(MyEnum_11::Val) == 1 * 2 * 3 * 4 * 5);       

// 변수를 전달하면, 일반 함수처럼 동작합니다.
int val{5};
int result{Factorial_11(5)};
```
런타임에 했던 작업을 최대한 컴파일 타임으로 전환하여 런타임 성능을 향상시킬 수 있는 획기적인 기능이지만 함수 구현에 제약이 좀 많습니다.

다만 1개의 명령문으로 구성해야 하기 때문에 구현 방법이 까다롭습니다.(*제약 조건은 점진적으로 완화되고 있습니다. [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)를 참고하세요.*)

1. 1개의 리턴문을 사용합니다.
2. 조건문 대신 [조건 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A1%B0%EA%B1%B4-%EC%97%B0%EC%82%B0%EC%9E%90)(`a ? b : c`)를 사용합니다. 
3. 반복문은 재귀 호출을 이용하여 구현합니다.

# constexpr 생성자 

[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)은 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)만 사용할 수 있는데요, 그러다 보니, 구조체/클래스는 사용자 정의 생성자, 소멸자가 없으며 모든 멤버 변수가 `public`인 [집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)인 경우에만 사용할 수 있습니다.

하지만, [constexpr 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)를 이용하여 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)인 구조체/클래스를 직접 만들 수 있습니다.

다음 `Area_11` 클래스는 `private` 멤버 변수와 생성자를 갖고 있지만, [constexpr 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용하여 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)으로 동작합니다.

1. `Area_11(int x, int y)`에 컴파일 상수를 전달하면, `Area_11`도 컴파일 상수로 동작합니다.

2. `Area_11`이 컴파일 타임 상수로 생성되었다면, `GetVal_11()`은 컴파일 타임 상수끼리의 연산이므로 컴파일 타임 함수가 될 수 있습니다.

```cpp
class Area_11 {
private:
    int m_X;
    int m_Y;
public:
    constexpr Area_11(int x, int y) : // 컴파일 타임 상수로 사용 가능
        m_X(x),
        m_Y(y) {} 
    constexpr int GetVal_11() const {return m_X * m_Y;}
};

constexpr Area_11 area(2, 5); // 컴파일 타임 상수로 정의

// 컴파일 타임에 계산된 면적이 Val에 대입됩니다.
enum class MyEnum_11 {Val = area.GetVal_11()}; // constexpr 함수 호출
EXPECT_TRUE(static_cast<int>(MyEnum_11::Val) == 2 * 5);     
```

만약 다음의 `x`와 같이 변수를 전달한다면 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)을 사용할 수 없습니다. 런타임 함수로 사용해야 합니다.

```cpp
int x = 10;
// constexpr Area_11 area(x, 5); // (X) 컴파일 오류. x는 컴파일 타임 상수가 아닙니다.
Area_11 area(x, 5); // 런타임 함수로 동작합니다.
```

`GetVal_11()`와 같은 `Setter` 계열 함수들은 다음 제약 조건 때문에 만들기 어렵습니다.(*제약 조건은 점진적으로 완화되고 있습니다. [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)를 참고하세요.*)

1. [constexpr 함수](
https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)만 리턴합니다. 그런데 `void`가 리터럴이 아닙니다.(C++14 부터는 `void`도 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)이 되어 가능합니다.)

2. [constexpr 함수](
https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 암묵적으로 [상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)이고 `operator =`등이 아예 허용되지 않습니다. [mutable](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)도 못씁니다. 

다행히 C++14 부터는 상기 [제약이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)되어 다음처럼 `Setter`를 만들 수 있습니다.

```cpp
class Area_11 {
private:
    int m_X;
    int m_Y;
public:
    constexpr Area_11(int x, int y) : // 컴파일 타임 상수로 사용 가능
        m_X(x),
        m_Y(y) {} 
    constexpr int GetVal_11() const {return m_X * m_Y;}
             
    constexpr void SetX_14(int val) {m_X = val;}
    constexpr void SetY_14(int val) {m_Y = val;}           
}; 
```


# (C++14~) constexpr 함수 제약 완화

[constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 기본적으로 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)만 리턴할 수 있고, 그외 스펙들은 조금씩 개선되고 있습니다. 

C++11 에서는 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)나 제어문도 사용할 수 없어서 상당히 제한적이었으나, 점점 개선되고 있습니다. 자세한 내용은 [cppreference.com](https://en.cppreference.com/w/cpp/language/constexpr)을 참고하시기 바랍니다.

|항목|C++11|C++14|C++17|C++20|
|--|--|--|--|--|
|[리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85) 외의 리턴|X|X|X|X|
|[조건 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A1%B0%EA%B1%B4-%EC%97%B0%EC%82%B0%EC%9E%90)|O|O|O|O|
|[static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/)|O|O|O|O|
|[constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98) 호출|O|O|O|O|
|초기화된 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 정의|X|O|O|O|
|2개 이상의 리턴문|X|O|O|O|
|`if`, `for`, `while`|X|O|O|O|
|`try`|X|X|X|O|
|가상 함수|X|X|X|O|
|[void 사용](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)|X|O|O|O|
|[const 자유도](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)|X|O|O|O|O|

다음은 C++14 기준에 맞춰서 좀더 일반적인 형태로 `Factorial_14()`를 구현한 예입니다. [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), 2개 이상의 리턴문, `if()`, `for()`등을 사용할 수 있어서 코딩 자유도가 높아졌습니다.

```cpp
constexpr int Factorial_14(int val) {
    int result{1}; // 초기화된 지역 변수 정의

    if (val < 1) {
        return 1; // 2개 이상의 리턴문
    }

    for (int i{val}; 0 < i; --i) { // 제어문
        result *= i;
    }

    return result;
} 

// 컴파일 타임에 계산된 120이 Val에 대입됩니다.
enum class MyEnum {Val = Factorial_14(5)};
EXPECT_TRUE(static_cast<int>(MyEnum::Val) == 1 * 2 * 3 * 4 * 5);       
```

# (C++17~) if constexpr 

조건식에 따라 컴파일하거나 컴파일하지 않습니다.

[템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 에서 소개된 다음 코드는 `if()`을 통해 `new T(*ptr);`와 `ptr->Clone();`을 호출하기 때문에, 이 두가지가 모두 가능한 것만 컴파일할 수 있었습니다.

```cpp
class ICloneable {
private:
    ICloneable(const ICloneable& other) {}
    ICloneable& operator =(const ICloneable& other) {return *this;}
protected:
    ICloneable() {}  
    ~ICloneable() {} 
public:
    virtual ICloneable* Clone() const = 0; 
};

// D가 B를 상속하였는지 검사하는 템플릿
template<typename D, typename B> 
class IsDerivedFrom {
    class No {};
    class Yes {No no[2];};

    static Yes Test(B*);
    static No Test(...);
public:
    enum {Val = sizeof(Test(static_cast<D*>(0))) == sizeof(Yes)};
};

template<typename T>
class CloneTraits {
public:

    static T* Clone(const T* ptr) {
    	if (ptr == NULL) {
    		return  NULL;
    	}

        if (!IsDerivedFrom<T, ICloneable>::Val) {
            return new T(*ptr);
        }
        else {
            return ptr->Clone(); // (X) 컴파일 오류. int에 Clone() 함수가 없습니다.
        }
    } 
};

int val;
int* ptr{CloneTraits<int>::Clone(&val)}; // (X) 컴파일 오류. int에 Clone() 함수가 없습니다.
delete ptr;
```

따라서, `CloneTraits`를 구현할때 컴파일 타임 프로그래밍을 위해 `if()`문을 사용하지 말고, `CloneTag`를 이용하여 오버로딩하라 소개했는데요,

[if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr)을 이용하면 조건에 맞는 부분만 컴파일하고, 그렇지 않으면 컴파일 하지 않습니다. 

즉, 다음과 같이 작성하면,

```cpp
template<typename T>
class CloneTraits {
public:
    static T* Clone_17(const T* ptr) {
        if (ptr == NULL) {
            return  NULL;
        }

        // 조건에 맞는 부부만 컴파일 합니다.
        if constexpr (!IsDerivedFrom<T, ICloneable>::Val) {
            return new T(*ptr);
        }
        else {
            return ptr->Clone(); 
        }
    } 
};
int val;
int* ptr{CloneTraits<int>::Clone_17(&val)}; // (O)
delete ptr; 
```

다음과 동등하기 때문에 컴파일되고 잘 작동합니다. [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 에서 소개한 `CloneTag`를 이용한 오버로딩 기법 보다 훨씬 직관적입니다.

```cpp
static T* Clone_17(const T* ptr) {
    if (ptr == NULL) {
        return  NULL;
    }

    return new T(*ptr);
} 
int val;
int* ptr{CloneTraits<int>::Clone_17(&val)}; // (O)
delete ptr; 
```


