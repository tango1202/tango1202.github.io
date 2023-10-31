---
layout: single
title: "#15. [모던 C++] (C++11~) 람다 표현식, 클로저, (C++14~) 람다 캡쳐 초기화, 일반화된 람다 함수, (C++17~) *this 캡쳐, constexpr 람다 함수"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)이 추가되어 1회용 익명 함수를 만들 수 있습니다. 
> * (C++14~) [람다 캡쳐 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90-%EC%B4%88%EA%B8%B0%ED%99%94)로 람다내에서 사용하는 임의 변수를 정의하여 사용할 수 있습니다.
> * (C++14~) [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받아 마치 템플릿 함수처럼 동작하는 [일반화된 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98)가 추가되었습니다.
> * (C++17~) [람다 캡쳐시 *this 를 이용](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90)하여 개체 자체를 복제하여 사용합니다.
> * (C++17~) [constexpr 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c17-constexpr-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98)가 추가되어 람다 함수도 컴파일 타임 함수로 만들 수 있습니다.
 
# 개요
기존에는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 이용하여 함수를 개체화 했는데요,

C++11 부터는 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)을 추가하여 함수 지향 프로그래밍이 좀 더 간편해 졌습니다.

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)은 `prvalue` 타입([값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC) 참고)의 1회용 익명 함수를 만들며, 이를 **클로저(Closure)** 라고 합니다. 

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)은 STL의 [알고리즘](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-algorithm/)과 잘 호환될 뿐만 아니라, [캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90) 기능등의 활용도도 높아서 복잡한 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 만들기 보다는 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)을 사용하는게 훨씬 좋습니다.

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)의 기본 문법은 다음과 같습니다.

`[캡쳐](인자들) -> 리턴 타입 {표현식}`

예를 들면,

```cpp
[](int a, int b) -> int {return a + b;}
```

이는 다음의 함수 정의와 같습니다.

```cpp
int f(int a, int b) {
    return a + b;
}
```

[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)나 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 없다면 다음과 같이 생략하여 `[] {}` 로 작성할 수 있습니다.

```cpp
[] {std::cout << "lambda" << std::endl;}
```

# 람다 리턴 생략

[후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)이 생략되면, 함수의 리턴문에 의해 추론됩니다.([리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0) 참고)
리턴문이 없으면 `void`로 추론됩니다.

```cpp
[](int a, int b) {return a + b;} // a + b의 결과 타입으로 리턴 타입이 추론됨
```

# 람다 함수 호출

일반 함수를 호출하려면 `f(10, 20)` 와 같이 호출하는데요, [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)으로 작성된 람다 함수를 호출하려면 다음과 같이 합니다.

1. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)으로부터 생성된 개체(클로저)를 변수에 저장한뒤 호출할 수 있고,

    ```cpp
    auto f_11{[](int a, int b) -> int {return a + b;}};
    int val{f_11(10, 20)}; // 함수 호출하듯이 f_11(10, 20) 로 호출합니다.
    EXPECT_TRUE(val == 30);
    ```

2. 클로저 개체에 `()`을 붙여 바로 호출할 수 있습니다.

    ```cpp
    int val_11{[](int a, int b) -> int {return a + b;}(10, 20)}; // 클로저 개체에 (10, 20)을 붙여 호출합니다.
    EXPECT_TRUE(val_11 == 30);
    ```

# 캡쳐

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) 바깥 부분의 개체 정보들을 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) 내부에 전달합니다.

|항목|내용|
|--|--|
|`[=]` (C++11~)|외부의 모든 변수를 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)형 값으로 가져옵니다.|
|`[&]` (C++11~)|외부의 모든 변수의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 가져옵니다.|
|`[=, &x, &y]` (C++11~)|외부의 모든 변수를 값으로 가져오되 `x`, `y` 만 참조로 가져옵니다.|
|`[&, x, y]` (C++11~)|외부의 모든 변수의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 가져오되 `x`, `y` 만 값으로 가져옵니다.|
|`this` (C++11~)|[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)을 사용한 개체의 [this 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 값으로 가져옵니다.|
|`*this` (C++17~)|`this`는 포인터를 가져오지만, `*this`는 개체 자체를 복제하여 가져옵니다.|

다음 코드에서는 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) 외부에 정의된 `sum`을 캡쳐하고, [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) 내에서 `v`의 각 요소의 값을 전달받아 `sum`에 누적합니다.

```cpp
int sum{0};
std::vector<int> v_11{1, 2, 3};

std::for_each( // 시퀀스 안의 요소들에 대해 f(요소)를 실행합니다. f는 람다 함수입니다.
    v_11.begin(), 
    v_11.end(),
    [&sum](int val) {sum += val;} // 캡처된 sum에 시퀀스 요소의 값(val)을 누적합니다.
);

EXPECT_TRUE(sum == 1 + 2 + 3);
```

**캡쳐 시기**

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)은 클로저 개체가 생성될 때 캡쳐를 합니다. 따라서, 클로저 개체를 생성한 시점과 호출한 시점이 다르면, 값이 다를 수 있습니다.

```cpp
int val{1};
auto f_11{[=]() -> int {return val;}}; // 클로저 개체가 생성되는 시점에 val을 캡쳐합니다.
val = 2;

EXPECT_TRUE(f_11() == 1); // 캡쳐할 때의 값을 사용하므로 1입니다.  
```

**값 캡쳐**

값으로 개체를 캡쳐하면 복제본을 만들어 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)로 사용합니다. 따라서, 그 값을 수정할 수 없습니다. 다만, 포인터 변수는 `int* const`여서 `ptr`은 수정할 수 없지만, `*ptr`은 수정할 수 있습니다.

```cpp
int a{1};
int b{2};
int c{3};
int* ptr{&b};
int& ref{c};
[=]() { // 값으로 복제하며 `const` 입니다.
    // a = 10; // (X) 컴파일 오류. const 이므로 수정할 수 없습니다.
    // ptr = &a; // (X) 컴파일 오류. const 이므로 수정할 수 없습니다.
    *ptr = 20; // int* const 여서 ptr 은 수정할 수 없지만, ptr이 가리키는 개체는 수정할 수 있습니다.
    // ref = 30; // (X) 컴파일 오류. const 이므로 수정할 수 없습니다.
}();

EXPECT_TRUE(b == 20);         
```

이런 경우 [mutable](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)을 사용하면 캡쳐한 개체를 수정할 수 있으나, 원본이 아닌 복제본만 수정됩니다.(원본을 수정하려면 참조 캡쳐를 이용하셔야 합니다.)

```cpp
int a{1};
int b{2};
int c{3};
int* ptr{&b};
int& ref{c};
[=]() mutable { // 값으로 캡쳐했지만 수정할 수 있습니다.
    a = 10; // (△) 비권장. 복제본이 수정될 뿐, 원본이 수정되는 건 아닙니다.
    *ptr = 20;
    ref = 30; // (△) 비권장. 복제본이 수정될 뿐, 원본이 수정되는 건 아닙니다.
}();

EXPECT_TRUE( a == 1 && b == 20 && c == 3);         
```

개체의 멤버 함수 내에서 사용할 때에는 값 캡쳐시 [this 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)가 복제되어 개체의 내부 멤버 변수를 수정할 수 있습니다.

```cpp
class T {
    int m_Member{1};
public:
    int GetMember() const {return m_Member;}
    int Func() {
        int local{2};

        auto f_11{[=]() -> int {
            // this->m_Member = 10; 
            m_Member = 10; // this->m_Member = 10; 과 동일. 멤버 변수의 값을 수정합니다.
            return m_Member + local;
        }};  

        return f_11();
    }
};

T t;
EXPECT_TRUE(t.Func() == 12);
EXPECT_TRUE(t.GetMember() == 10); // 멤버 변수가 수정되어 있습니다.
```

**참조 캡쳐**

참조 캡쳐를 이용하면, 캡쳐한 개체를 수정할 수 있습니다.

```cpp
int a{1};
int b{2};
int c{3};
int* ptr{&b};
int& ref{c};
[&]() { // 캡쳐한 개체를 수정할 있습니다.
    a = 10; 
    *ptr = 20;
    ref = 30;
}();

EXPECT_TRUE( a == 10 && b == 20 && c == 30);     
```

# 클로저 개체 대입

클로저 개체는 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)와 [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)을 이용하여 변수에 대입할 수 있습니다.

1. [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)(단, [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)이기 때문에 [함수의 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용할 수 없습니다.)
    
    ```cpp
    auto f_11{[](int a, int b) -> int {return a + b;}};
    EXPECT_TRUE(f_11(10, 20) == 30);
    ```

2. 함수 포인터(단, 캡쳐를 지원하지 않습니다.)
   
    ```cpp
    typedef int (*Func)(int, int); // 함수 포인터 typedef
    Func f_11{[](int a, int b) -> int {return a + b;}};
    EXPECT_TRUE(f_11(10, 20) == 30);
    ```

3. [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)(캡쳐도 지원하고, 함수 인자로 사용할 수도 있습니다.)
    
    ```cpp
    std::function<int(int, int)> f_11;
    int g(const std::function<int(int, int)>& lambda_11, int a, int b) { // 함수의 인자로 람다를 지정합니다.
        return lambda_11(a, b);
    }
    int c{30};
    f_11 = [=](int a, int b) -> int {return a + b + c;}; // 캡쳐를 사용하는 람다 표현식도 사용할 수 있습니다.      
    EXPECT_TRUE(g(f_11, 10, 20) == 60); // g() 함수에 클로저 개체를 저장한 f_11을 전달합니다.       
    ```

# 클로저 개체 복사 부하

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)에서 값 캡쳐를 사용하면, 

1. 캡쳐시에 복사 부하가 있으며, 
2. 클로저 개체간 복사시에도 복사 부하가 있으므로,

참조 캡쳐를 이용하는게 좋습니다.

다음 `T`클래스는 생성자와 소멸자 호출을 탐지하기 위한 테스트 클래스입니다.

```cpp
class T {
public: 
    T() {std::cout << "T::Default Constructor" << std::endl;}
    T(const T&) {std::cout << "T::Copy Constructor" << std::endl;}
    T(T&&) {std::cout << "T::Move Constructor" << std::endl;}
    ~T() {std::cout << "T::Destructor" << std::endl;}
    void operator =(const T&) {std::cout << "T::operator =()" << std::endl;}
};
```

1. 값 캡쳐던, 참조 캡쳐던, 해당 개체를 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)에서 사용하지 않으면 복사하지 않습니다.

    ```cpp
    T t;
    [=]() {std::cout << "Run Lambda" << std::endl;}(); // t를 사용하지 않았습니다.
    ```
    ```cpp
    T::Default Constructor
    Run Lambda
    T::Destructor
    ```

2. 값 캡쳐를 사용하면, 대상 개체가 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)에 사용될때, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하여, [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 복제본을 만듭니다.

    ```cpp
    T t;
    [=]() {
        t; // t;로 사용합니다. 캡쳐시 복사 부하가 있습니다.      
        std::cout << "Run Lambda" << std::endl;
    }();  
    ```

    ```cpp
    T::Default Constructor
    T::Copy Constructor // 캡쳐시 복사 생성자를 호출하여 const 복제본을 만듭니다.
    Run Lambda
    T::Destructor // 복제본을 삭제합니다.
    T::Destructor    
    ```

3. 값 캡쳐를 사용하더라도, [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 변수에 저장하고 호출하는 것은 추가의 복사 부하가 없습니다.

    ```cpp
    T t;
    auto f_11{[=]() { // auto 변수에 저장하고, 호출합니다.   
        t; 
        std::cout << "Run Lambda" << std::endl;
    }};      
    f_11();
    ```
    ```cpp
    T::Default Constructor
    T::Copy Constructor // 추가 복사 부하가 없습니다.
    Run Lambda
    T::Destructor
    T::Destructor
    ```

4. 값 캡쳐를 사용할때 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 변수들 끼리 복제하면 추가의 복사 부하가 발생합니다.

    ```cpp
    T t;
    auto f1_11{[=]() {
        t; 
        std::cout << "Run Lambda" << std::endl;
    }}; 
    auto f2_11{f1_11}; // 복사 부하가 발생합니다.
    f1_11();
    f2_11();
    ```

    ```cpp
    T::Default Constructor
    T::Copy Constructor // 캡쳐시 복사 생성자를 호출하여 const 복제본을 만듭니다.
    T::Copy Constructor // f2{f1}시 복제본을 만듭니다. 추가 복사 부하가 있습니다.
    Run Lambda
    Run Lambda
    T::Destructor
    T::Destructor
    T::Destructor
    ```

5. 참조 캡쳐를 사용하면 복사 부하가 없습니다.

    ```cpp
    T t;
    auto f1_11{[&]() { // 캡쳐시 복사 부하가 없습니다.
        t; 
        std::cout << "Run Lambda" << std::endl;
    }}; 
    auto f2_11{f1_11}; // 대입시 복사 부하가 없습니다.
    f1_11();
    f2_11();      
    ```
    ```cpp   
    T::Default Constructor
    Run Lambda
    Run Lambda
    T::Destructor
    ```

# (C++14~) 람다 캡쳐 초기화

C++14 부터는 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90)시에 람다내에서 사용하는 임의 변수를 정의하여 사용할 수 있습니다.
정의한 변수는 초기값이 지정되어야 하며, 람다 내에서만 사용할 수 있습니다. 

클로저 개체 생성시에 [캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90)하므로, 람다 함수를 여러번 호출하더라도 기존 값이 유지됩니다. 

```cpp
// 클로저 개체 생성시 람다 내에서 사용할 수 있는 val_14 변수를 만들어 캡쳐 합니다.
// 캡쳐된 val_14 을 수정하기 위해 mutable로 만듭니다.
auto f_14{[val_14 = 0]() mutable -> int {return ++val_14;}}; 

// 호출시마다  캡쳐된 val이 증가합니다.
EXPECT_TRUE(f_14() == 1);
EXPECT_TRUE(f_14() == 2);
EXPECT_TRUE(f_14() == 3);
```

# (C++14~) 일반화된 람다 함수

C++14 부터는 람다 함수 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 있어, 마치 템플릿 함수처럼 동작하게 할 수 있습니다. 다만 [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)에서와 같이 [auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)은 적용되지 않습니다.

```cpp
auto add_14{[](auto a, auto b) {return a + b;}}; 

EXPECT_TRUE(add_14(1, 2) == 3);
EXPECT_TRUE(add_14(std::string{"hello"}, std::string{"world"}) == "helloworld");
```

# (C++17~) constexpr 람다 함수

C++17 부터 [constexpr 람다 함수가 추가](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c17-constexpr-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98)되어 요구사항이 충족되면 암시적으로 컴파일 타임 함수로 만들어지고,[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr) 을 지정하여 명시적으로 컴파일 타임 함수로 만들 수 있습니다.

```cpp
// 명시적 constexpr 람다 함수 입니다.
auto id_17{[](int n) constexpr {return n;}};
static_assert(id_17(10) == 10);

// 암시적 constexpr 람다 함수 입니다.
auto add_17{[](int a, int b) {return a + b;}};
static_assert(add_17(1, 2) == 3);

// 인자로 전달한 a_11, b_11는 컴파일 타임 상수이기 때문에 요구사항이 맞아 암시적 constexpr 람다 함수 입니다.
constexpr int a_11{1};
constexpr int b_11{2};
static_assert(add_17(a_11, b_11) == 3);

// 인자로 전달한 c, d는 변수이기 때문에 요구사항이 맞지 않아 런타임 람다 함수 입니다.
int c{1};
int d{2};
EXPECT_TRUE(add_17(c, d) == 3);
```