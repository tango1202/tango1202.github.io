---
layout: single
title: "#1. [모던 C++] auto(C++ 11~), decltype(C++ 11~)(작성중)"
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
`decltype()`은 괄호안의 개체 자체를 평가하지만, 괄호를 추가하면 표현식으로 평가하여 결과가 다를 수 있습니다.

```cpp
class T {
public:
    double m_Val;
};
const T* t;

decltype(t->m_Val) a = 10; // 멤버 엑세스로 평가됩니다. double
decltype((t->m_Val)) b = 10; // 괄호를 추가하면 표현식으로 평가됩니다. t가 const 이므로 const double
```

# decltype에서 Value 카테고리에 따른 평가

xvalue, lvalue, prvalue에 따라 처리 방식이 다름.
decltype(xvalue) : T&&
decltype(lvalue) : T&
decltype(prvalue) : T
(작성중)

# decltype(auto)

C++ 14 부터 `decltype()`내에 `auto`를 이용하여 좀더 간결하게 작성할 수 있습니다.

```cpp
int Func(int a, int b) {
    return a + b;
}

// Func(10, 20) 함수 결과 타입
decltype(Func(10, 20)) c = Func(10, 20); // C++ 11
decltype(auto) d = Func(10, 20); // C++ 14
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

# 리턴값 추론

C++ 11에서는 템플릿 함수에서 `auto`를 사용하여 리턴값을 추론하면 `a + b`를 `auto`가 기재된 위치에서는 모르기 때문에 컴파일 오류가 납니다.

```cpp
// (X) 컴파일 오류. auto가 기재된 위치에서 a + b를 모름
template<typename T, typename U>
auto Add(T a, U b) { 
    return a + b;
}
auto result = Func(10, 20);
EXPECT_TRUE(result == 30);     
```

따라서 다음 처럼 후행 반환 형식을 사용하여 `-> decltype()`을 작성해야 합니다.

```cpp
// C++ 11
template<typename T, typename U>
auto Add(T a, U b) -> decltype(a + b) { 
    return a + b;
} 
```

C++ 14 부터는 하기 2개가 모두 지원됩니다.

```cpp
// C++ 14
template<typename T, typename U>
auto Add(T a, U b) { 
    return a + b;
}

template<typename T, typename U>
decltype(auto) Add(T a, U b) { 
    return a + b;
}
```


