---
layout: single
title: "#1. [모던 C++] auto(C++11~), decltype(C++11~), decltype(auto)(C++14~), 후행 반환(C++11~), 리턴 타입 추론(C++14~)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# auto

선언되는 변수의 타입을 대입하는 초기값으로부터 추론하여 결정합니다. 추론 규칙은 [템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따릅니다.

템플릿 사용에 따른 긴 타입명을 간소하게 표현할 수 있습니다.

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

인자 정의로는 사용할 수 없습니다.

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
`decltype()`은 괄호안의 개체 자체를 평가하지만, 괄호를 추가하면 왼값 표현식으로 평가하여 특별히 `T&`로 처리합니다.

```cpp
class T {
public:
    double m_Val;
};
const T* t;

decltype(t->m_Val) a = 10; // 멤버 엑세스로 평가됩니다. double
decltype((t->m_Val)) b = 10; // 괄호를 추가하면 왼값 표현식으로 처리합니다. t가 const 이므로 const double&
```

# decltype()에서 Value 카테고리에 따른 평가

`xvalue`, `lvalue`, `prvalue` 인지에 따라 다음과 같이 타입이 결정됩니다.

|항목|내용|
|--|--|
|`decltype(xvalue)`|`T&&`|
|`decltype(lvalue)`|`T&`|
|`decltype((lvalue))`|`T&`|
|`decltype(prvalue)`|`T`|

# decltype(auto)

C++14 부터 `decltype()`의 표현식이 복잡할 경우 `auto`를 이용하여 좀더 간결하게 작성할 수 있습니다.

```cpp
int Func(int a, int b) {
    return a + b;
}

// Func(10, 20) 함수 결과 타입
decltype(Func(10, 20)) c = Func(10, 20); // C++11
decltype(auto) d = Func(10, 20); // C++14
```

# auto 와 decltype의 차이점

1. 추론 방식
   
    `auto`의 경우 [템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따르기 때문에 배열을 포인터로 추론하고, 최상위 `const` 는 무시하지만, `decltype()`은 괄호안의 개체가 정의된 그대로 추론합니다.

2. 함수 인자
   
   `auto`는 함수 인자로 사용할 수 없지만, `decltype`은 템플릿 인수의 연산 결과로 추론할 수도 있습니다.

    ```cpp
    template<typename T, typename U>
    void Func(T a, U b, decltype(a + b)* result) { // decltype(a + b) 은 int
        *result = a + b;
    }
    int result = 0;
    Func(10, 20, &result);
    EXPECT_TRUE(result == 30);     
    ```

# 후행 반환

C++11에서는 `auto`로 리턴 타입을 정의하면, 정의된 위치에서는 리턴 타입을 모르기 때문에 컴파일 오류가 납니다.

```cpp
// (X) 컴파일 오류. auto가 기재된 위치에서 a + b를 모름
template<typename T, typename U>
auto Add(T a, U b) { 
    return a + b;
}
auto result = Func(10, 20);
EXPECT_TRUE(result == 30);     
```

하지만, 리턴 타입이 인자에 의존한다면, 다음처럼 후행 반환 형식인 `-> decltype()`을 사용하여 리턴 타입을 정의할 수 있습니다.

```cpp
// C++11
template<typename T, typename U>
auto Add(T a, U b) -> decltype(a + b) { 
    return a + b;
} 
```

C++14 부터는 리턴값 추론을 지원하여 하기 2개가 모두 가능합니다.

```cpp
// C++14
template<typename T, typename U>
auto Add(T a, U b) { 
    return a + b;
}

template<typename T, typename U>
decltype(auto) Add(T a, U b) { 
    return a + b;
}
```

# 리턴 타입 추론

C++14부터 `auto`와 `decltype(auto)`을 이용한 리턴값 추론이 가능합니다.

`decltype(auto)`를 사용한 경우 후행 반환은 생략될 수 있으며, 컴파일러는 리턴문으로부터 리턴 타입을 추론합니다. 

1. 만약 `auto` 만 사용했다면, [템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따릅니다.
2. `decltype(auto)` 사용시 `return result;` 시 `result` 타입 그대로를 사용하고, `return (result);`시 에는 `(result)` 왼값 표현식이어서 `T&`로 처리합니다.
3. 리턴 타입이 다르면 리턴 타입을 추론할 수 없습니다.
4. 가상 함수는 리턴 타입을 추론할 수 없습니다.

```cpp
auto Func1(int a, int b) {
    return a + b;
}
auto Func2(int a, int b) {

    const int result = a + b;

    // 템플릿 함수 인수 추론에 의해 const int 대신 int가 사용됩니다.
    return result;
}

decltype(auto) Func3(int a, int b) {
    const int result = a + b;

    // 개체 엑세스로 평가. result 타입 그대로 평가
    return result; 
}
decltype(auto) Func4(int a, int b) {
    const int result = a + b;

    // 왼값 표현식의 결과로 평가. T&형태로 평가
    return (result); 
}
// (X) 컴파일 오류. 리턴 타입은 동일해야 합니다.
auto Func5(int a, int b) {
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
    virtual auto Func(int a) {return a;}
};
auto result1 = Func1(10, 20); // int를 리턴
auto result2 = Func2(10, 20); // const int를 리턴했지만 템플릿 함수 인수 추론 규칙에 따라 int를 리턴
auto result3 = Func3(10, 20); // const int 리턴. 리턴하는 result 타입과 동일
auto result3 = Func4(10, 20); // const int& 리턴. 리턴하는 (result) 표현식과 동일
```

