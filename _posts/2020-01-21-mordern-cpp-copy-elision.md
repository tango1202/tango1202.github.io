---
layout: single
title: "#21. [모던 C++] (C++17~) 임시 구체화와 복사 생략 보증"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.

# 개요

기존에는 다양한 상황에서 생성된 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 컴파일러가 자체적으로 최적화 하여 불필요한 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이나 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 최소화 되도록 해줬습니다.

1. [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94)
   
   [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 그냥 `lvalue`(*[임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 전달받는 변수*)로 사용합니다.(*[생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94) 와 [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-) 참고*)

2. [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)

    리턴할 개체를 그냥 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 저장할 변수로 사용합니다.

컴파일러 종류에 따라 다르겠지만, gcc에서는 상기 최적화를 수행하여 불필요한 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)을 생략하고, 생성된 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 그냥 사용합니다.

```cpp
class A {};
A a{A{}}; // A{}를 기본 생성자로 생성하고 a에 이동 생성 합니다. 하지만 컴파일러에 따라 생성된 임시 개체를 그냥 a로 사용합니다.

void Func1(A param) {}
Func1(A{}); // A{}를 기본 생성자로 생성하고, param에 이동 생성 합니다. 하지만 컴파일러에 따라 전달된 임시 개체를 그냥 param으로 사용합니다.

A Func2() {return A{};}
A result = Func2(); // A{} 기본 생성자로 생성하고, result에 이동 생성합니다. 하지만 컴파일러에 따라 리턴된 임시 개체를 그냥 result로 사용합니다.
```

아쉬운 점은, 컴파일러 최적화에 따라 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용하지 않더라도, 문법적으로는 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)가 필요하므로, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하면 컴파일 오류가 난다는 점입니다.

```cpp
class A_11 {
public:
    int m_Val;
public:
    A_11() = default;
    A_11(const A_11& other) = delete;
    A_11(A_11&& other) = delete; // 이동 생성자 사용 안함
    A_11& operator =(const A_11& other) = delete; 
    A_11& operator =(A_11&& other) = delete;         
};

A_11 a{A_11{}}; // (X) 컴파일 오류. 이동 생성자가 없음    
```

그래서 억지로 다음처럼 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용했었습니다.

```cpp
    class A_11 {
    public:
        int m_Val;
    public:
        A_11() = default;
        A_11(const A_11& other) = delete;
        A_11(A_11&& other) = default; // 억지로 이동 생성자를 사용함
        A_11& operator =(const A_11& other) = delete; 
        A_11& operator =(A_11&& other) = delete;         
    };

A_11 a{A_11{}}; // 컴파일러 최적화로 이동 생성자를 사용하지 않지만, 컴파일을 위해 문법적으로는 이동 생성자가 필요합니다.
```

하지만, C++17 부터는 [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 불필요하게 복사나 이동되지 않음을 문법적으로 보증해 줍니다. 그덕에 상기 경우에 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 억지로 사용하지 않아도 컴파일 오류없이 잘 동작하게 됐습니다.

# 임시 구체화(Temporary materialization)

C++17 부터 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)인 `prvalue`는 다른 개체를 초기화 하는데에만 사용되며, 다음의 경우만 [임시적으로 구체화](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/#%EC%9E%84%EC%8B%9C-%EA%B5%AC%EC%B2%B4%ED%99%94temporary-materialization)되는 것으로 한정했습니다.

1. `prvalue`를 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 바인딩할때

    ```cpp
    T& t = T{}; // T{}는 임시 구체화 됩니다.
    ```
    
2. `prvalue` 개체 멤버에 접근할때

    ```cpp
    int val = T{}.m_Val; // T{} 는 임시 구체화 됩니다.
    ```

3. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 포인터로 변환하거나 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 첨자 연산(`[]`)을 할때

4. [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)에서 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list)를 초기화할때

    ```cpp
    class T_11 {
    public:
        explicit T_11(std::initializer_list<int>) {}
    };
    T_11 t({1, 2, 3}); // {1, 2, 3} 은 initializer_list로 임시 구체화됩니다.
    ```
5. [typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)와 `sizeof()`에서 사용할때

# 복사 생략(Copy elision)

C++17 부터 `prvalue`는 [임시 구체화](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/#%EC%9E%84%EC%8B%9C-%EA%B5%AC%EC%B2%B4%ED%99%94temporary-materialization) 기준에 따라 최대한 구체화를 하지 않으며, 최종 대상의 저장소에 직접 구현됩니다.

```cpp
A a{A{}}; // A{}는 a에 구현됩니다.

void Func1(A param) {}
Func1(A{}); // A{}는 param에 구현됩니다.

A Func2() {
    return A{}; // A{}는 result에 구현됩니다. return move(A{}); 하지 마세요.
}
A result = Func2(); // A{}는 result에 구현됩니다. move(Func2()) 하지 마세요.
```

즉, 다음 코드에서 `A_17 a{A_17{}};`는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)인 `A_17{}`가 `a`에 직접 구현되므로, 문법적으로 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)을 사용하지 않습니다. 따라서 C++17 에서는 다음과 같이 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 해도 정상적으로 컴파일됩니다.

```cpp
class A_17 {
public:
    int m_Val;
public:
    A_17() = default;
    A_17(const A_17& other) = delete;
    A_17(A_17&& other) = delete; // 이동 생성자 사용 안함
    A_17& operator =(const A_17& other) = delete; 
    A_17& operator =(A_17&& other) = delete;           
}; 

A_17 a{A_17{}}; // (O) 문법적으로 복사 생성자와 이동 생성자를 사용하지 않습니다. 
```
