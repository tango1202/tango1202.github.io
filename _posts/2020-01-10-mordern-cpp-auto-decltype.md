---
layout: single
title: "#10. [모던 C++] (C++11~) auto, decltype(), declval(), 후행 리턴 타입, (C++14~) decltype(auto), 리턴 타입 추론, (C++17~) auto의 중괄호 초기화 특수 추론 규칙 개선"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#1] [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)을 숙지하라.
>   * [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)은 제거된다.
>   * 최상위 const 는 무시된다.
>   * 배열은 포인터로 변경된다.
>   * 함수는 함수 포인터로 변경된다.
> * [MEC++#2] auto의 추론 규칙을 숙지하라.
>   * [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)을 사용한다.
>   * [auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)으로 `auto val= {10};`, `auto val{10};`은 initializer_list로 추론된다.
>   * 리턴 타입에는 [auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)이 적용되지 않는다.
> * [MEC++#3] decltype의 작동 방식을 숙지하라.
>   * 왼값 표현식에 대해 항상 T&로 추론한다.
> * [MEC++#4] 연역된 형식을 파악하는 방법을 알아둬라.
>   * IDE, 컴파일러 진단, 실행시점 출력(typeid 부정확)등이 있다.
> * [MEC++#5] 명시적 선언보다는 auto를 선호하라.([auto의 장점](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%9E%A5%EC%A0%90)참고)
> * [MEC++#6] auto가 원치 않은 형식으로 연역될 때에는 명시적 형식의 초기치를 사용하라.([암시적 형변환과 auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98%EA%B3%BC-auto) 참고)

> * (C++11~) [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)이 추가되어 값으로부터 타입을 추론하며, 코딩이 간편해 졌습니다.
> * (C++11~) [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)이 추가되어 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 의존하여 리턴 타입을 결정하며, 좀더 동적인 함수 설계가 가능해 졌습니다.
> * (C++14~)  [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)가 추가되어 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)의 `()`내 표현식이 복잡할 경우 좀더 간결하게 작성할 수 있습니다.
> * (C++14~) [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 대신 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)나 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)를 사용할 수 있습니다.
> * (C++17~) [비타입 템플릿 인자에서 auto를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%EB%B9%84%ED%83%80%EC%9E%85-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90%EC%9D%98-auto-%ED%97%88%EC%9A%A9)합니다. 
> * (C++17~) [auto의 중괄호 초기화 특수 추론 규칙 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c17-auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99-%EA%B0%9C%EC%84%A0)되어 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list) 로 추론되는 오류가 개선되었습니다.
> * (C++20~) [축약된 함수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%EC%B6%95%EC%95%BD%EB%90%9C-%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 있습니다. 사실상 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)의 간략한 표현입니다.

# auto

선언되는 변수의 타입을 초기값으로부터 추론하여 결정합니다. 

```cpp
auto a_11 = 10; // int
auto b_11 = 10L; // long
auto c_11 = 10UL; // unsigned long

std::vector<int> v;
auto d_11 = v.begin(); // std::vector<int>::iterator
```

하지만, [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 정의시에는 사용할 수 없습니다.(*[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 쓰면 오버로딩 결정이 좀 애매해 질 수 있거든요. [오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고*)

```cpp
// (X) 컴파일 오류. auto는 함수 인자로 사용할 수 없습니다.
double Func_11(int a, auto b) {
    return a + b;   
}
```

> *(C++17~) [비타입 템플릿 인자에서 auto를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%EB%B9%84%ED%83%80%EC%9E%85-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90%EC%9D%98-auto-%ED%97%88%EC%9A%A9)합니다.*<br/>
> *(C++20~) [축약된 함수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%EC%B6%95%EC%95%BD%EB%90%9C-%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 있습니다. 사실상 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)의 간략한 표현입니다.*

# auto 추론 규칙

[auto의 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)은 [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따릅니다.

* [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 포인터로 추론되고, 

    ```cpp
    // 배열은 포인터로 추론됩니다.
    int arr[] = {1, 2, 3};
    auto a_11 = arr; // int*
    ```

* [최상위 const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)는 무시됩니다. 

    ```cpp
    // 최상위 const는 무시됩니다.
    const int constVal = 0;
    auto a_11 = constVal; // int
    a_11 = 10; // const가 아니여서 값을 대입받을 수 있습니다. 
    ```

* 특히 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)은 제거됩니다.

    ```cpp
    int a = 0;
    const int b = 0;

    // 참조성은 제거됩니다.
    int x = 10;
    int& ref = x;
    auto a_11 = ref; // int. 참조성이 제거됩니다.
    auto& b_11 = ref; // auto&을 이용하여 억지로 참조자로 받을 수 있습니다.
    ```

# auto의 중괄호 초기화 특수 추론 규칙

[auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 는 초기값으로 타입을 추론하고, [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)는 대입하는 타입에 맞게 초기값을 추론합니다.(*`int a = {10}`이면 `{10}`은 `int{T}`의 축약입니다.*)

따라서 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)는 서로 의존적이라 올바르게 추론할 수 없습니다. 

그래서, [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)를 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94) 할때 [중괄호 직접 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-t-t) 와 [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)는 각각 다음과 같이 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list)로 추론하도록 특수 추론 규칙을 만들었습니다.

```cpp
// 중괄호 직접 초기화
auto a_11{1}; //(△) a는 initializer_list<int> 또는 int
auto b_11{1, 2}; // (X) 컴파일 오류. auto에서는 단일 개체 대입 필요  

// 중괄호 복사 초기화
auto c_11 = {1}; // c는 initializer_list<int>
auto d_11 = {1, 2}; // d는 initializer_list<int>  
```

상기에서 `auto a_11{1};`를 `initializer_list<int>` 또는 `int`라고 했는데요,
원래 C++11에서는 `initializer_list<int>`로 추론됩니다. 그런데, 2014년에 [auto의 중괄호 초기화 특수 추론 규칙 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c17-auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99-%EA%B0%9C%EC%84%A0)인 [N3922](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3922.html)가 받아들여졌고, 일부 컴파일러에서(*제 경우엔 GCC version 8.1.0 이상에서*) C++11 환경에서도 `int`로 추론됩니다.

> *(C++17~) [auto의 중괄호 초기화 특수 추론 규칙 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c17-auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99-%EA%B0%9C%EC%84%A0)되어 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list) 로 추론되는 오류가 개선되었습니다.*

# auto의 장점

타입을 명시적으로 선언하기 보다는 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)를 사용하는게 [생성 후 대입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1-%ED%9B%84-%EB%8C%80%EC%9E%85--%ED%95%98%EC%A7%80-%EB%A7%88%EB%9D%BC)을 방지하고, 코딩량도 줄어들고, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)도 차단하여 훨씬 좋습니다.

1. 초기화하지 않으면 생성할 수 없습니다.

    ```cpp
    int a; // 쓰레기값으로 생성합니다.
    auto a_11; // (X) 컴파일 오류. 초기값이 없어 타입을 추론하지 못해 컴파일 오류가 납니다.
    ```

2. 기나긴 타입명을 간단하게 표현합니다.

    ```cpp
    std::vector<int>::iterator itr = v.begin();
    std::vector<int>::iterator endItr = v.end();  

    auto itr_11 = v.begin(); // 템플릿 사용에 따른 긴 타입명을 간소하게 표현합니다.
    auto endItr_11 = v.end();
    ```

3. 타입 종속적으로 표현되므로 리팩토링이 편합니다.

    ```cpp
    int f() {...}

    int a = f(); // (△) 비권장. f()함수의 리턴 타입이 long으로 변경되면 수정해야 합니다.
    auto a_11 = f(); // (O) f()함수의 리턴 타입이 long으로 변경되더라도 수정할 필요가 없습니다.
    ```

4. 의도치 않은 타입 불일치로 발생하는 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단합니다.

    ```cpp
    int f() {...}

    long a = f(); // (△) 비권장. int가 long으로 암시젹 형변환됩니다.
    auto a_11 = f(); // (O) 형변환 되지 않습니다.
    ```

# 암시적 형변환과 auto

 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)은 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)과 궁합이 맞지 않습니다. 

다음은 정수의 최대/최소를 0 ~ 10 으로 한정하는 `MyInt` 클래스 입니다. `int`로의 변환을 쉽게 하기 위해 `operator int()`로 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 했다고 합시다.(*사실 나쁜 설계죠. 최대/최소를 0 ~ 10 으로 한정한 클래스를 만들고, `int` 복제본을 리턴받아 마음껏 수정할 수 있게 하니까요. `const int&`를 리턴하는 `Getter`를 구현하는게 좋은 설계입니다.*)

`int val = MyInt(11);`와 같이 `int`타입에 대입할때는 별다른 문제없이 대입됩니다.

하지만, `auto val_11 = MyInt(11);`와 같이 표현하면, `val_11`은 `MyInt`타입입니다. 따라서, `val_11`를 `int&`로 변환할 수 없습니다. 이덕에 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)이 있다는 걸 감지하고, `MyInt` 클래스의 미세한 헛점을 파악할 수 있겠네요.

물론 억지로 `static_cast<int>`로 명시적으로 변환할 수는 있습니다. 하지만, [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/)에서 말씀드렸듯, 근본적으로 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)이던 [명시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)이던 최선을 다하여 [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/)하지 마세요.

```cpp
class MyInt {
    int m_Val;
public:
    explicit MyInt(int val) : m_Val(Clamp(val, 0, 10)) {}
    operator int() {return m_Val;} // int로 형변환 됩니다.

private:
    int Clamp(int val, int min, int max) { // 최대/최소값으로 변경합니다.
        if (val < min) {return min;}
        if (max < val) {return max;}

        return val;
    }
};

int val = MyInt(11); // (△) 비권장. 암시적으로 int로 변경됩니다. 

EXPECT_TRUE(val == 10); // 최대 / 최소가 잘 한정되어 있습니다.
EXPECT_TRUE(val + 1 == 11); // (△) 비권장. 값 한정한게 엉망이 되버렸습니다. 이게 다 암시적 형변환으로 int 복제본을 만들었기 때문이에요. 사실 const int&를 리턴하는게 좋습니다.

int& ref = val; // (△) 비권장. int&로 변환될 수 있습니다.

auto val_11 = MyInt(11); // MyInt입니다.
int& ref = val_11; // (X) 컴파일 오류. MyInt는 int&로 변환될 수 없습니다.

auto val_11 = static_cast<int>(MyInt(11)); // (△) 비권장. 명시적으로 형변환하여 int 입니다.
int& ref = val_11; // (△) 비권장. int&로 변환될 수 있습니다.
```

# decltype()

`()` 안의 개체나 표현식의 타입으로 추론합니다. 이때 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)처럼 [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따르지 않고, 선언된 타입 그대로 추론합니다.

```cpp
int a = 0;
const int b = 0;

decltype(a) c_11 = a; // int. a와 동일한 int로 추론됨
decltype(b) d_11 = a; // const int. auto와는 다르게 b와 동일한 const int로 추론됨
d_11 = 10; // (X) 컴파일 오류. const int이므로 값대입 안됨
```

주의할 부분은 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)은 괄호안의 개체 자체를 평가하지만, 좌측값 표현식은 `T&`로 추론한다는 점입니다.

따라서, `decltype(t->m_Val)`는 `t->m_Val`의 타입으로 추론하지만, 괄호를 추가하여 `decltype((t->m_Val))`로 하면 `(t->m_Val)`는 좌측값 표현식이므로 `T&`로 추론합니다.

```cpp
class T {
public:
    double m_Val;
};
const T* t;

decltype(t->m_Val) a_11 = 10; // 멤버 엑세스로 평가됩니다. double
decltype((t->m_Val)) b_11 = 10; // 괄호를 추가하면 좌측값 표현식으로 처리합니다. t가 const 이므로 const double&
```

# decltype()과 값 카테고리

[값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)에 따라 다음과 같이 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)의 타입이 결정됩니다.(*[값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC) 참고*)

|항목|내용|
|--|--|
|`decltype(xvalue)`|`T&&`<br/>[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)등으로 변환된 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4). `rvalue`|
|`decltype(lvalue)`|`T&`<br/>이름이 부여된 개체|
|`decltype((lvalue))`|`T&`<br/>괄호가 추가되면 표현식으로 평가됨|
|`decltype(prvalue)`|`T`<br/>이름 없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)|

# auto 와 decltype()의 차이점

1. 추론 방식
   
    [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)의 경우 [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따르기 때문에 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 포인터로 추론하고, [최상위 const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85%EC%8B%9C-%EC%B5%9C%EC%83%81%EC%9C%84-const-%EC%A0%9C%EA%B1%B0)는 무시하고, [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)은 제거되지만, [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)은 괄호안의 개체를 정의된 그대로 추론합니다.

    ```cpp
    // 배열
    {
        int arr[] = {1, 2, 3};
        auto a_11 = arr; // int*
        decltype(arr) d_11 = {1, 2, 3}; // int d_11[3] 
    }
    // 최상위 const
    {
        const int constInt = 10;
        auto a_11 = constInt; // int
        decltype(constInt) d_11 = constInt; // const int
    }
    // 참조성
    {
        int x = 10;
        int& ref = x;

        auto a_11 = ref; // int
        decltype(ref) d_11 = ref; // int&
    }
    ```

2. 함수 인자
   
   [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)는 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용할 수 없지만, [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용할 수 있습니다.

    ```cpp
    template<typename T, typename U>
    void Func_11(T a, U b, decltype(a + b)* result) { // decltype(a + b) 은 int
        *result = a + b;
    }
    int result = 0;
    Func_11(10, 20, &result);
    EXPECT_TRUE(result == 30);     
    ```

# declval() 

[decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)을 이용하면, 다음처럼 함수의 리턴 타입도 추론할 수 있습니다.

```cpp
class T {
public:
    int Func(int) {return 1;}
};

T t;
// T::Func(int) 함수의 리턴 타입
decltype(T().Func(10)) val_11 = t.Func(10); 
```
`T().Func(10)`은 런타임에 동작하는 것은 아니며, 컴파일 타임에 리턴 타입을 추론하기 위해서 사용합니다.

그런데 만약 `T`에 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 없다면, `T().Func(10)`은 잘못된 표현이므로 컴파일 오류가 발생합니다. 따라서, 대충 아무 상수값이나 넣어줘야 합니다.

```cpp
class T {
public:
    explicit T(int) {} // 기본 생성자가 없습니다.
    int Func(int) {return 1;}
};

T t(10);

decltype(T().Func(10)) val_11 = t.Func(10); // (X) 컴파일 오류. T에 기본 생성자가 없습니다.
decltype(T(10).Func(10)) val_11 = t.Func(10); // (O)
```

하지만, 상기 방법도 생성자 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 많거나 복잡하면, 사용하기 어렵습니다. 

`declval()`은 주어진 타입을 참조 타입으로 변환하여 멤버 함수 호출식을 표현해 줍니다. 따라서 굳이 `T()`나 `T(10)`과 같이 생성자 표현식을 사용하지 않아도 됩니다. 

상기 코드를 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)으로 작성하면 다음과 같습니다.

```cpp
// T::Func(int) 함수의 리턴 타입
decltype(std::declval<T>().Func(10)) val_11 = t.Func(10); 
```

# 후행 리턴 타입

C++11 에서는 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)로 리턴 타입을 정의하면, 정의된 위치에서는 리턴 타입을 모르기 때문에 컴파일 오류가 납니다.

```cpp
// (X) 컴파일 오류. auto가 기재된 위치에서 a + b를 모름
template<typename T, typename U>
auto Add_11(T a, U b) { 
    return a + b;
}
auto result_11 = Add_11(10, 20);
EXPECT_TRUE(result_11 == 30);     
```

대신, 다음처럼 [후행 리턴 타입 표현](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 인 `-> decltype()`을 사용하여 리턴 타입을 정의할 수 있습니다.

```cpp
// C++11
template<typename T, typename U>
auto Add_11(T a, U b) -> decltype(a + b) { 
    return a + b;
} 
```

>*(C++14~) [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)이 추가되어 [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 대신 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)나 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)를 사용할 수 있습니다.*

# (C++14~) decltype(auto) 

C++14 부터 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)의 표현식이 복잡할 경우 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 이용하여 좀더 간결하게 작성할 수 있습니다.

```cpp
int Func(int a, int b) {
    return a + b;
}

// Func(10, 20) 함수 결과 타입
decltype(Func(10, 20)) c_11 = Func(10, 20); // C++11
decltype(auto) d_14 = Func(10, 20); // C++14
```

[auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)의 경우 [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따르기 때문에 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 포인터로 추론하고, 최상위 `const` 는 무시하고, [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)은 제거되지만, `decltype(auto)`는 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype) 처럼 괄호안의 개체를 정의된 그대로 추론합니다.

다음은 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)과 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)의 타입 추론을 비교한 예입니다.

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 경우 `decltype(auto) d_14 = {1, 2, 3};`은 [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)로 배열 요소의 타입까지는 추론하지 못해 컴파일 오류가 발생합니다.

```cpp
// 배열
{
    int arr[] = {1, 2, 3};
    auto a_11 = arr; // int*
    decltype(arr) d_11 = {1, 2, 3}; // int d_11[3] 
    // decltype(auto) d_14 = {1, 2, 3}; // (X) 컴파일 오류. 중괄호 복사 초기화로 배열 요소의 타입까지는 추론하지 못합니다.
}
// 최상위 const
{
    const int constInt = 10;
    auto a_11 = constInt; // int
    decltype(constInt) d_11 = constInt; // const int
    decltype(auto) d_14 = constInt; // const int
}
// 참조성
{
    int x = 10;
    int& ref = x;

    auto a_11 = ref; // int
    decltype(ref) d_11 = ref; // int&
    decltype(auto) d_14 = ref; // int&
}
```

# (C++14~) 리턴 타입 추론

C++14부터 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)을 이용한 리턴 타입 추론이 가능하여, [후행 리턴 타입 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 을 대체합니다.

1. 만약 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 만 사용했다면, [함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 규칙을 따릅니다.
2. [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto) 사용시 
   1. `return T;` 는 `T` 타입 그대로를 사용하고, 
   2. `return (T);` 는 `(T)`가 좌측값 표현식이어서 `T&`로 처리합니다.
3. 함수내에 리턴문이 여러개 있는 경우, 리턴 타입이 다르면 리턴 타입을 추론할 수 없습니다.
4. 가상 함수는 리턴 타입을 추론할 수 없습니다.

```cpp
auto Add1_14(int a, int b) {
    return a + b;
}
int result1 = Add1_14(10, 20); // int를 리턴

auto Add2_14(int a, int b) {

    const int result = a + b;

    // 함수 템플릿 인수 추론에 의해 const int 대신 int가 사용됩니다.
    return result;
}
int result2 = Add2_14(10, 20); // const int를 리턴했지만 함수 템플릿 인수 추론 규칙에 따라 int를 리턴

decltype(auto) Add3_14(int a, int b) {
    const int result = a + b;

    // 개체 엑세스로 평가. result 타입 그대로 평가
    return result; 
}
const int result3 = Add3_14(10, 20); // const int 리턴. 리턴하는 result 타입과 동일

decltype(auto) Add4_14(int a, int b) {
    const int result = a + b; // (X) 예외 발생. Func4의 지역 변수 참조를 전달하기 때문

    // 좌측값 표현식의 결과로 평가. T&형태로 평가
    return (result); 
}
const int& result4 = Add4_14(10, 20); // const int& 리턴. 리턴하는 (result) 표현식과 동일

// (X) 컴파일 오류. 리턴 타입은 동일해야 합니다.
auto Add5_14(int a, int b) {
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
    virtual auto Add_14(int a) {return a;}
};
```

하지만, [auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)은 [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)에 적용되지 않습니다. 그래서 다음과 같이 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 리턴 타입으로 사용하면, [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)를 할 수 없습니다. 

```cpp
int FuncInt_14() {
    return {10};
}
auto FuncAuto_14() {
    return {10}; // (X) 컴파일 오류. auto의 중괄호 초기화 특수 추론 규칙은 리턴 타입 추론에서 제공하지 않습니다.
}

auto a = FuncInt_14();
auto b = FuncAuto_14(); // (X) 컴파일 오류.
```

# (C++17~) auto의 중괄호 초기화 특수 추론 규칙 개선

C++17 부터 [auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)에서 언급한 [auto의 중괄호 초기화 특수 추론 규칙 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c17-auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99-%EA%B0%9C%EC%84%A0)인 [N3922](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3922.html)이 공식적으로 적용되었습니다.(*[N3922](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3922.html)과 [N3681](https://open-std.org/JTC1/SC22/WG21/docs/papers/2013/n3681.html) 참고*)

기존에는 `auto x{10};`는 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list)로 추론되었으나(*[auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99) 참고*), C++17 부터는 `int`로 추론됩니다.

전체적으로 다음과 같은 규칙으로 추론됩니다.

* [중괄호 직접 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-t-t) `auto val{}` : [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 1개면 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입으로 추론되고, 여러개면 컴파일 오류를 발생합니다.
* [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-) `auto val = {}` : [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들의 타입이 동일하면 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list)로 추론됩니다.

```cpp
// 중괄호 직접 초기화
auto a_11{1}; //(△) a는 initializer_list<int> 또는 int
auto b_11{1, 2}; // (X) 컴파일 오류. auto에서는 단일 개체 대입 필요  

// 중괄호 복사 초기화
auto c_11 = {1}; // c는 initializer_list<int>
auto d_11 = {1, 2}; // d는 initializer_list<int>

// 중괄호 직접 초기화
auto a_17{1}; // b는 int. 기존에는 initializer_list<int> 일 수 있었음
auto b_17{1, 2}; // (X) 컴파일 오류. auto에서는 단일 개체 대입 필요  

// 중괄호 복사 초기화
auto c_17 = {1}; // c는 initializer_list<int>
auto d_17 = {1, 2}; // d는 initializer_list<int>  
```


