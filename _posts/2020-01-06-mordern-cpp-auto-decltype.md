---
layout: single
title: "#6. [모던 C++] (C++11~) auto, decltype(), declval(), 후행 리턴 타입, (C++14~) decltype(auto), 리턴 타입 추론"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 값으로부터 타입을 추론하는 `auto` 타입과 `decltype()`이 추가되어 코딩이 간편해 졌습니다.
> * 함수 인자에 의존하여 리턴 타입을 결정하는 후행 리턴이 추가되어 좀더 동적인 함수 설계가 가능해 졌습니다.

# auto

선언되는 변수의 타입을 초기값으로부터 추론하여 결정합니다. 특히, 템플릿 사용에 따른 긴 타입명을 간소하게 표현할 수 있습니다.

추론 규칙은 [템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따릅니다.



```cpp
int a = 0;
const int b = 0;

auto c = a; // int로 추론됨
auto d = b; // 최상위 const는 무시되므로 const int 가 아닌 int로 추론됨
d = 10;

std::vector<int> v;

// std::vector<int>::iterator itr = v.begin(); 
// std::vector<int>::iterator itrEnd = v.end();

auto itr = v.begin(); // 템플릿 사용에 따른 긴 타입명 간소화
auto itrEnd = v.end();
```

함수 인자 정의시에는 사용할 수 없습니다.(인자에 `auto`를 쓰면 오버로딩 결정이 좀 애매해 질 수 있거든요. [오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고)

```cpp
// (X) 컴파일 오류
double Func(int a, auto b) {
    return a + b;   
}
```

# decltype()

`()` 안의 개체나 표현식에 따라 타입을 추론하여 결정합니다.

```cpp
int a = 0;
const int b = 0;

decltype(a) c = a; // a와 동일한 int로 추론됨
decltype(b) d = a; // auto와는 다르게 b와 동일한 const int로 추론됨

d = 10; // (X) 컴파일 오류. const int이므로 값대입 안됨
```
`decltype()`은 괄호안의 개체 자체를 평가하지만, 괄호를 추가하면 좌측값 표현식으로 평가하여 특별히 `T&`로 추론합니다.

```cpp
class T {
public:
    double m_Val;
};
const T* t;

decltype(t->m_Val) a = 10; // 멤버 엑세스로 평가됩니다. double
decltype((t->m_Val)) b = 10; // 괄호를 추가하면 좌측값 표현식으로 처리합니다. t가 const 이므로 const double&
```

# decltype()과 값 카테고리

`xvalue`, `lvalue`, `prvalue` 인지에 따라 다음과 같이 타입이 결정됩니다.

|항목|내용|
|--|--|
|`decltype(xvalue)`|`T&&`, `std::move()`등으로 변환된 임시 개체. `rvalue`|
|`decltype(lvalue)`|`T&`, 이름이 부여된 개체|
|`decltype((lvalue))`|`T&`, 괄호가 추가되면 표현식으로 평가됨|
|`decltype(prvalue)`|`T`, 이름 없는 임시 개체|

# auto 와 decltype()의 차이점

1. 추론 방식
   
    `auto`의 경우 [템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따르기 때문에 배열을 포인터로 추론하고, 최상위 `const` 는 무시하지만, `decltype()`은 괄호안의 개체가 정의된 그대로 추론합니다.

2. 함수 인자
   
   `auto`는 함수 인자로 사용할 수 없지만, `decltype()`은 인자로 사용할 수 있습니다.

    ```cpp
    template<typename T, typename U>
    void Func(T a, U b, decltype(a + b)* result) { // decltype(a + b) 은 int
        *result = a + b;
    }
    int result = 0;
    Func(10, 20, &result);
    EXPECT_TRUE(result == 30);     
    ```

# declval() 

`decltype()`을 이용하면, 다음처럼 함수의 리턴 타입도 추론할 수 있습니다.

```cpp
class T {
public:
    int Func(int) {return 1;}
};

T t;
// T::Func(int) 함수의 리턴 타입
decltype(T().Func(10)) val = t.Func(10); 
```
`T().Func(10)`은 런타임에 동작하는 것은 아니며, 컴파일 타임에 리턴 타입을 추론하기 위해서 사용합니다.

그런데 만약 `T`에 기본 생성자가 없다면, `T().Func(10)`은 잘못된 표현이므로 컴파일 오류가 발생합니다. 따라서, 대충 아무 상수값이나 넣어줘야 합니다.

```cpp
class T {
public:
    explicit T(int) {} // 기본 생성자가 없습니다.
    int Func(int) {return 1;}
};

T t(10);

decltype(T().Func(10)) val = t.Func(10); // (X) 컴파일 오류. T에 기본 생성자가 없습니다.
decltype(T(10).Func(10)) val = t.Func(10); // (O)
``````

하지만, 상기 방법도 생성자 인자가 많거나 복잡하면, 사용하기 어렵습니다. 

`declval()`은 주어진 타입을 참조 타입으로 변환하여 멤버 함수 호출식을 표현해 줍니다. 따라서 굳이 `T()`나 `T(10)`과 같이 생성자 표현식을 사용하지 않아도 됩니다. 

상기 코드를 `decltype()`으로 작성하면 다음과 같습니다.

```cpp
// T::Func(int) 함수의 리턴 타입
decltype(std::declval<T>().Func(10)) val = t.Func(10); 
```

# 후행 리턴 타입

C++11 에서는 `auto`로 리턴 타입을 정의하면, 정의된 위치에서는 리턴 타입을 모르기 때문에 컴파일 오류가 납니다.(C++14에서 개선되었습니다.)

```cpp
// (X) 컴파일 오류. auto가 기재된 위치에서 a + b를 모름
template<typename T, typename U>
auto Add(T a, U b) { 
    return a + b;
}
auto result = Func(10, 20);
EXPECT_TRUE(result == 30);     
```

대신, 다음처럼 후행 리턴 타입 표현인 `-> decltype()`을 사용하여 리턴 타입을 정의할 수 있습니다.

```cpp
// C++11
template<typename T, typename U>
auto Add(T a, U b) -> decltype(a + b) { 
    return a + b;
} 
```

# (C++14~) decltype(auto) 

C++14 부터 `decltype()`의 표현식이 복잡할 경우 `auto`를 이용하여 좀더 간결하게 작성할 수 있습니다.

```cpp
int Func(int a, int b) {
    return a + b;
}

// Func(10, 20) 함수 결과 타입
decltype(Func(10, 20)) c = Func(10, 20); // C++11
decltype(auto) d = Func(10, 20); // C++14
```

# (C++14~) 리턴 타입 추론

C++14부터 `auto`와 `decltype(auto)`을 이용한 리턴 타입 추론이 가능하며, 후행 리턴 타입 표현식은 생략될 수 있습니다.

1. 만약 `auto` 만 사용했다면, [템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따릅니다.
2. `decltype(auto)` 사용시 
   1. `return T;` 는 `T` 타입 그대로를 사용하고, 
   2. `return (T);` 는 `(T)`가 좌측값 표현식이어서 `T&`로 처리합니다.
3. 함수내에 리턴문이 여러개 있는 경우, 리턴 타입이 다르면 리턴 타입을 추론할 수 없습니다.
4. 가상 함수는 리턴 타입을 추론할 수 없습니다.

```cpp
auto Add1(int a, int b) {
    return a + b;
}
auto Add2(int a, int b) {

    const int result = a + b;

    // 템플릿 함수 인수 추론에 의해 const int 대신 int가 사용됩니다.
    return result;
}

decltype(auto) Add3(int a, int b) {
    const int result = a + b;

    // 개체 엑세스로 평가. result 타입 그대로 평가
    return result; 
}
decltype(auto) Add4(int a, int b) {
    const int result = a + b; // (X) 예외 발생. Func4의 지역 변수 참조를 전달하기 때문

    // 좌측값 표현식의 결과로 평가. T&형태로 평가
    return (result); 
}
// (X) 컴파일 오류. 리턴 타입은 동일해야 합니다.
auto Add5(int a, int b) {
    if (a < 10) {
        return 10; // int
    }
    else {
        return 10.0F; // float
    }
}
class T {
public:
    // (X) 컴파일 오류. 가상 함수는 리턴 타입 추론을 할 수 없습니다.
    virtual auto Add(int a) {return a;}
};
auto result1 = Add1(10, 20); // int를 리턴
auto result2 = Add2(10, 20); // const int를 리턴했지만 템플릿 함수 인수 추론 규칙에 따라 int를 리턴
auto result3 = Add3(10, 20); // const int 리턴. 리턴하는 result 타입과 동일
auto result4 = Add4(10, 20); // const int& 리턴. 리턴하는 (result) 표현식과 동일
```


