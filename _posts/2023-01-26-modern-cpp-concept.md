---
layout: single
title: "#26. [모던 C++] 컨셉(concept)과 요구사항(requires)(C++20)"
categories: "cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [컨셉(concept)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항(requires)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 추가되어 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 줄 수 있습니다.
> * (C++20~) [컨셉 설계](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89-%EC%84%A4%EA%B3%84)를 활용하여 마치 [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)처럼 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)에 의한 코딩 계약을 만들 수 있습니다. 

# 개요

[템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)이나 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)는 너무 일반화되는 경향이 있습니다. 

예를들어 정수 타입이던 실수 타입이던 상관없이 더해줄 수 있도록 [함수 템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)으로 `Add()`함수를 만들면,

```cpp
template<typename T> 
T Add(T a, T b) {
    return a + b;
}

EXPECT_TRUE(Add(1, 2) == 3);
EXPECT_TRUE(Add(1.0, 2.0) == 3.0); 
```

의도치 않게 `+`연산자를 지원하는 다른 타입도 실행됩니다.

```cpp
EXPECT_TRUE(Add(std::string{"Hello"}, std::string{"World"}) == std::string{"HelloWorld"}); // (△) 비권장. 의도하지 않았는데, 되네요.
```

저절로 된다고 좋아할 수 있으나, ***의도치 않은 동작은 모두 잠재적으로 사이드 이펙트를 유발하는 코드 결함***입니다. 최선을 다해서 막아야 하는데요,

코드가 직관적이지 못해 가독성은 좀 떨어지지만, 다행히 C++11부터 [enable_if](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/#enable_if)를 이용하는 방법이 있었습니다.(*[SFINAE](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)와 [enable_if](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/#enable_if) 참고*) 

```cpp
template<
    typename T, 
    typename U = typename std::enable_if< 
        std::is_integral<T>::value || // T가 정수 타입이면 true 입니다.
        std::is_floating_point<T>::value // T가 실수 타입이면 true 입니다.
    >::type // 조건이 true인 경우에만 enable_if<>::type이 정의됩니다. 따라서 조건이 false면 템플릿 인스턴스화가 되지 않습니다.
> 
T Add_11(T a, T b) {
    return a + b;
}

EXPECT_TRUE(Add_11(1, 2) == 3);
EXPECT_TRUE(Add_11(1.0, 2.0) == 3.0);
EXPECT_TRUE(Add_11(std::string{"Hello"}, std::string{"World"}) == std::string{"HelloWorld"}); // (X) 컴파일 오류. std::is_integral<T>::value나 std::is_floating_point<T>::value가 true 인 것만 가능합니다.
```

`Add_11(std::string, std::string)`로 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)하면 [enable_if](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/#enable_if)에 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)으로 `type`이 없어 컴파일 오류가 발생하게 되죠. 컴파일 오류 메시지는 다음과 같습니다. 

```cpp
no matching function for call to 'Add_11(std::string, std::string)'
no type named 'type' in 'struct std::enable_if<false, void>'
```

C++20 부터는 [컨셉(concept)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항(requires)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 추가되어 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 줄 수 있습니다.

|항목|내용|
|--|--|
|[제약 조건(constraint)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)|타입이 준수해야할 조건이나 표현.|
|[컨셉(concept)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)|타입의 의미 체계에 관한 모델링. [제약 조건(constraint)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)들로 구성함.|
|[요구사항(requires)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)|[템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)가 준수해야할 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)들|

다음은 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)을 이용한 예입니다. `std::integral<T>`과 `std::floating_point<T>` [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) `T`의 [요구사항](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)으로 정의했습니다. [요구사항](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)의 [제약 조건](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 만족하지 않으면, 컴파일 오류가 발생합니다.(*`std::integral<T>` 와 `std::floating_point<T>`은 STL에서 기본으로 제공하는 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)입니다. 자세한 내용은 [concepts](https://tango1202.github.io/cpp-stl/modern-cpp-stl-concepts/)를 참고하세요.*) [enable_if](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/#enable_if)보다는 훨씬 직관적입니다.

```cpp
template<typename T>
requires std::integral<T> || std::floating_point<T> // T는 정수 타입이나 실수 타입이어야 합니다.
T Add_20(T a, T b) {
    return a + b;
}

EXPECT_TRUE(Add_20(1, 2) == 3);
EXPECT_TRUE(Add_20(1.0, 2.0) == 3.0);
EXPECT_TRUE(Add_20(std::string{"Hello"}, std::string{"World"}) == std::string{"HelloWorld"}); // (X) 컴파일 오류. std::integral 이거나 std::floating_point인 것만 가능합니다.
```

컴파일 오류 메시지는 다음과 같습니다. [제약 조건](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 만족하지 않는다고 좀더 직관적으로 표시됩니다.

```cpp
no matching function for call to 'Add_20(std::string, std::string)'
template argument deduction/substitution failed:
note: constraints not satisfied
In substitution of 'template<class T>  requires (integral<T>) || (floating_point<T>) T Add_20(T, T) [with T = std::__cxx11::basic_string<char>]':
```

# 컨셉(concept)과 제약 조건

[컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)은 컴파일 타임에 평가할 수 있는 표현식으로서 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)할때 감지되며, [제약 조건](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 만족하는 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)와 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)에 사용할 수 있습니다. 만족하지 않는 타입은 컴파일 오류를 발생시키고요. 

[컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)은 다음과 같이 정의 할 수 있습니다.

```cpp
template<템플릿 인자>
concept 컨셉명 = 제약 조건;
```

또한, 여러 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)들을 합성해서 만들 수도 있습니다.

```cpp
template<typename T>
concept Number_20 = std::integral<T> || std::floating_point<T>; // T는 정수 타입이나 실수 타입이어야 합니다.
```

[컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)의 [제약 조건](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)은 여러가지 방법을 이용하여 만들 수 있습니다.

1. [bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/)을 리턴하는 [컴파일 타임 상수](https://tango1202.github.io/cpp/modern-cpp-constexpr/#%EC%BB%B4%ED%8C%8C%EC%9D%BC-%ED%83%80%EC%9E%84-%EC%83%81%EC%88%98) 표현식

    [컴파일 타임 타입 특성](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/)을 보면, 컴파일 타임에 사용할 수 있는 다양한 [타입 특성](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/)이 제공됩니다. `integral` [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)도 사실은 [is_integral](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/#%EA%B8%B0%EB%B3%B8-%ED%83%80%EC%9E%85-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC) [타입 특성](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/)으로 구현되어 있습니다. 

    ```cpp
    template<typename T>
    concept Integral_20 = std::is_integral<T>::value; // T는 정수 타입이어야 합니다.
    ```

2. `requires`를 이용한 표현식

    특정한 표현식이 가능한지 정의합니다.

    ```cpp
    template<typename T>
    concept Addable_20 = requires(T a, T b) {
        a + b; // a + b 가 유효해야 합니다.
    };
    ```

3. [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85) 

    개체 내부에 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)이 제공되는지 정의합니다.
    ```cpp
    class A_11 {
    public:
        using Ref = A_11&; // 종속 타입
    };
    template<typename T> 
    concept MyType_20 = requires {
        typename T::Ref; // 종속 타입 Ref가 제공되어야 합니다.
    };
    ```

4. 복합 제약 조건

    `{표현식} [noexcept] 후행 리턴 타입;`의 형태로 표현식의 결과 타입을 정의합니다. `[noexcept]`는 옵션입니다.

    ```cpp
    template<typename T>
    concept Equal_20 = requires(T a, T b) {
        {a == b} -> std::convertible_to<bool>; // T 는 ==을 제공하고 결과는 bool로 변환되어야 합니다.
        {a != b} -> std::convertible_to<bool>; // T 는 !=을 제공하고 결과는 bool로 변환되어야 합니다.
    };
    ```

5. 중첩 제약 조건

    `requires`문 내부에 추가 [요구사항](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 있으면 중첩하여 정의합니다.

    ```cpp
    template<typename T>
    concept Addable_20 = requires(T a, T b) {
        a + b; // a + b 가 유효해야 합니다.
    };

    template<typename T>
    concept AddableEqual_20 = requires(T a, T b) {
        
        {a == b} -> std::convertible_to<bool>; // T 는 ==을 제공하고 결과는 bool로 변환되어야 합니다.
        {a != b} -> std::convertible_to<bool>; // T 는 !=을 제공하고 결과는 bool로 변환되어야 합니다.
        
        requires Addable_20<T>; // requires 로 추가 제약 조건을 작성합니다.
    };  
    ```
# 컨셉 테스트

특정 타입이 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 만족하는지 확인하려면 다음과 같이 [static_assert()](https://tango1202.github.io/cpp/modern-cpp-static-assert/)를 이용하면 됩니다.

```cpp
template<typename T>
concept Integral_20 = std::is_integral<T>::value; // T는 정수 타입이어야 합니다.

static_assert(Integral_20<int>); // (O)
static_assert(Integral_20<char>); // (O)
static_assert(Integral_20<float>); // (X) 컴파일 오류. 정수 타입이 아닙니다.
static_assert(Integral_20<double>); // (X) 컴파일 오류. 정수 타입이 아닙니다.
```
# 컨셉 설계

[컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)은 작은 단위의 [제약 조건](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)으로 작성하고, 잘 설계된 이름을 부여한 뒤, 이를 [논리 연산자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EB%85%BC%EB%A6%AC-%EC%97%B0%EC%82%B0%EC%9E%90)로 합성하여 사용합니다. [단일 책임 원칙(Single Responsibility Principle)](https://tango1202.github.io/principle/principle-single-responsibility/)과 [인터페이스 분리 원칙(Interface Segregation Principle)](https://tango1202.github.io/principle/principle-interface-segregation/)에 따라서요. 잘 작성하면 의미 체계를 부여한 모델링이 가능합니다.  

앞서 예시했듯이 수치 타입을 더하는 함수를 다시 살펴 봅시다.

```cpp
template<typename T>
requires std::integral<T> || std::floating_point<T> 
T Add_20(T a, T b) {
    return a + b;
}
```

정수 타입과 실수 타입만 허용하겠다는 의미로, `requires std::integral<T> || std::floating_point<T>` 만 작성했는데요, 

사실,

1. 값 타입으로 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 인수를 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하고, 값 타입으로 리턴하므로 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이 필요하며, 
2. [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 자동 소멸 되어야 하고,
3. `+`연산자로 더할 수 있어야 합니다.

따라서 Add_20의 [요구사항](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)을 좀더 고도화하여 `NumberAddable_20` [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 다음과 같이 모델링 할 수 있습니다. 

```cpp
template<typename T>
concept Number_20 = std::integral<T> || std::floating_point<T>; // T는 정수 타입이나 실수 타입이어야 합니다.

template<typename T>
concept Addable_20 = requires (T a, T b) {  
    a + b; // a + b 가 유효해야 합니다.
};

template<typename T>
concept NumberAddable_20 = // 숫자를 더할 수 있습니다.
    Number_20<T> && // 정수 타입이나 실수 타입이고,
    std::copyable<T> && // 복사 생성, 이동 생성, 복사 대입, 이동 대입, 소멸이 가능해야 합니다.
    Addable_20<T>; //  a + b 가 유효해야 합니다.

template<typename T>
requires NumberAddable_20<T>
T Add_20(T a, T b) {
    return a + b;
}

EXPECT_TRUE(Add_20(1, 2) == 3);
EXPECT_TRUE(Add_20(1.0, 2.0) == 3.0);
EXPECT_TRUE(Add_20(std::string{"Hello"}, std::string{"World"}) == std::string{"HelloWorld"}); // (X) 컴파일 오류. NumberAddable_20인 것만 가능합니다.
```

또한 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)에 의한 코딩 계약을 만들 수 있습니다. 마치 [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)를 이용한 코딩 계약처럼요.

기존 [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4) 방식은 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)을 통해 다음처럼 구현합니다. `IDrawable_11` [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)를 구체화한 `Rectangle_11`과 `Circle_11`을 `Draw()`함수에서 사용할 수 있죠.

```cpp
class IDrawable_11 {
public:
    // 인터페이스이므로 사용 못하게 막음.
    IDrawable_11(const IDrawable_11&) = delete; 
    IDrawable_11(IDrawable_11&&) noexcept = delete;
    IDrawable_11& operator =(const IDrawable_11&) = delete;
    IDrawable_11& operator =(IDrawable_11&&) noexcept = delete;
protected:
    // 인터페이스여서 상속한 개체에서만 사용할 수 있게 함
    IDrawable_11() = default;
    ~IDrawable_11() = default;
public:
    // 자식 클래스에서 구체화 해야 합니다.
    virtual void Draw() const = 0;
};
class Rectangle_11 : public IDrawable_11 {
public:
    virtual void Draw() const override {
        std::cout << "Rectangle_11::Draw()" << std::endl;
    }
}; 
class Circle_11 : public IDrawable_11 {
public:
    virtual void Draw() const override {
        std::cout << "Circle_11::Draw()" << std::endl;
    }
};  

// IDrawable_11 인터페이스를 사용한 개체를 Draw() 합니다.
void Draw(const IDrawable_11& drawable) {
    drawable.Draw();
}    

Rectangle_11 rect;
Circle_11 circle;

// rect와 circle의 가상 함수인 Draw가 호출됩니다.
Draw(rect); 
Draw(circle);
```

[컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 이용하면 다음처럼 구현됩니다. `IDrawable_11` [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)가 생략되었고, [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)도 없어졌으며, [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 호출도 없어졌습니다. 단지 `Drawable_20` [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)으로 해당 개체에 `Draw()` [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 호출 가능한지만 컴파일 타임에 검사합니다. [가상 함수 테이블(Virtual Function Table, vTable)](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)을 위한 추가 공간도 필요 없어지고, 런타임 호출 부하도 적어집니다. 미세하겠지만 성능 향상이 되겠네요.

```cpp
template<typename T>
concept Drawable_20 = requires(T obj) {
    obj.Draw(); // Draw() 멤버 함수를 호출할 수 있어야 합니다.
};

// Drawable_20 컨셉을 준수하는 개체를 Draw() 합니다.
template<typename T>
requires Drawable_20<T> // Draw() 함수를 호출할 수 있어야 합니다.
void Draw_20(const T& drawable) {
    drawable.Draw();
}

class Rectangle {
public:
    void Draw() const {
        std::cout << "Rectangle::Draw()" << std::endl;
    }
}; 
class Circle {
public:
    void Draw() const {
        std::cout << "Circle::Draw()" << std::endl;
    }
};

Rectangle rect;
Circle circle;

// Drawable_20 컨셉을 준수하는 개체만 호출 가능합니다.
Draw_20(rect);
Draw_20(circle);
```

# 요구사항(requires)

[요구사항(requires)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)은 [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)에 적용합니다.

 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)의 목록 끝이나 [함수 선언](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%84%A0%EC%96%B8)의 끝에 작성하며, [컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 [논리 연산자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EB%85%BC%EB%A6%AC-%EC%97%B0%EC%82%B0%EC%9E%90)로 합성할 수도 있습니다.

```cpp
template<typename T>
requires std::integral<T> || std::floating_point<T> // 논리 연산자로 합성합니다.
auto Func_20(T val) {
    return val;
}

Func_20(1); // int;
Func_20(1.0); // double
Func_20(std::string("Hello")); // (X) 컴파일 오류.
```

다음과 같이 [요구사항](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)을 적용할 수 있습니다.

```cpp
// requires로 템플릿 인자에 적용
template<typename T> 
requires std::integral<T> 
auto Func1_20(T val) {
    return val;
}

// 후행 requires로 함수 선언의 끝에 템플릿 인자에 적용
template<typename T> 
auto Func2_20(T val) requires std::integral<T> {
    return val;
}

// 템플릿 인자에 적용
template<std::integral T> 
auto Func3_20(T val) {
    return val;
}  

// 비타입 템플릿 인자에 적용
template<std::integral auto v>
auto Func4_20 () {
    return v;
}  

// 리턴값과 함수 인자에 적용
std::integral auto Add_20(std::integral auto val) {
    return val;
}
   
// 타입 추론에 적용
int Func() {return 1;}
std::integral auto val_20 = Func();
```

[람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)에도 사용할 수 있습니다.

```cpp
// 람다 표현식의 템플릿 인자에 적용
auto val1_20 = [] <std::integral T> (T val) {
    return val;
};  
auto val2_20 = [] <typename T> requires std::integral<T> (T val) {
    return val;
};  
auto val3_20 = [] <typename T> (T val) requires std::integral<T> {
    return val;
};     

// 람다 표현식의 비타입 템플릿 인자에 적용
auto val4_20 = [] <std::integral auto v> () {
    return v;
}; 

// 람다 표현식의 함수 인자에 적용
auto val5_20 = [] (std::integral auto val) {
    return val;
};  
```


[구조화된 바인딩](https://tango1202.github.io/cpp/modern-cpp-structured-binding/)은 지원하지 않습니다.

```cpp
std::integral auto [x_17, y_17] = std::pair{1, 2}; // (X) 컴파일 오류
```

# 컨셉 활용

[컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)
은 함수외에도 [클래스 템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF), [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98), [템플릿 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94), [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)에도 사용할 수 있습니다.


```cpp
// 클래스 템플릿
template<std::integral T> 
class A_20 {};

A_20<int> a1;
A_20<double> a2; // (X) 컴파일 오류

// 멤버 함수
template<typename T>
class B_20 {
public:
    void Func(T other) requires std::integral<T> {} 
};

B_20<int> b1;
b1.Func(10);

B_20<double> b2;
b2.Func(10.0); // (X) 컴파일 오류


// 템플릿 특수화
template<typename T>
class C_20 {
 public:
    int Func() {return 1;}    
};

template<std::integral T>
class C_20<T> {
 public:
    int Func() {return 2;}    
};

EXPECT_TRUE(C_20<double>{}.Func() == 1);
EXPECT_TRUE(C_20<int>{}.Func() == 2); // integral 컨셉 버전

// 오버로딩
int Func_20(auto val) {
    return 1;
}
int Func_20(std::integral auto val) {
    return 2;
}
int Func_20(std::floating_point auto val) {
    return 3;
}
int Func_20(long val) {
    return 4;
}

EXPECT_TRUE(Func_20("Hello") == 1); // 일반 버전  
EXPECT_TRUE(Func_20(1) == 2); // integral 컨셉 버전
EXPECT_TRUE(Func_20(1.0) == 3); // floating_point 컨셉 버전
EXPECT_TRUE(Func_20(1L) == 4); // long 버전
```

# 익명 컨셉

[요구사항](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires) 적용은 `requires 제약 조건`이고, [제약 조건](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)은 `requires {}`로 작성되므로, [익명 컨셉](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9D%B5%EB%AA%85-%EC%BB%A8%EC%85%89) 적용은 `requires requires {}`의 형태로 작성됩니다. 모양도 이상할 뿐더러 재활용도 되지 않으므로 사용하지 않는게 좋습니다. 

```cpp
template<typename T>
requires requires(T a, T b) { // requires(T a, T b) {}은 익명 컨셉입니다.
    a + b; 
}
T Add_20(T a, T b) {return a + b;}

Add_20(1, 2); // + 가 제공되어 사용 가능합니다.

class A {}; // + 가 제공되지 않습니다.
A a, b;
Add_20(a, b); // (X) 컴파일 오류. + 가 제공되지 않습니다.
```

