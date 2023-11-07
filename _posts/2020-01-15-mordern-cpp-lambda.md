---
layout: single
title: "#15. [모던 C++] (C++11~) 람다 표현식, 클로저, (C++14~) 람다 캡쳐 초기화, 일반화된 람다 표현식, (C++17~) *this 람다 캡쳐, constexpr 람다 표현식"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#31] 기본 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90) 모드를 피하라.
>   * [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)하는 항목을 나열하는게 직관적이다.
>   * `[=]`는 `this`를 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)한다.
> * [MEC++#32] 객체를 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure) 안으로 이동하려면 초기화 갈무리를 사용하라.(*[클로저에 이동 연산 전달](??) 참고*)
> * [MEC++#33] forward()를 통해서 전달할 auto&& 인자는 decltype을 사용하라.(*[일반화된 람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 참고*)

> * (C++11~) [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)이 추가되어 1회용 익명 함수를 만들 수 있습니다. 
> * (C++14~) [람다 캡쳐 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90-%EC%B4%88%EA%B8%B0%ED%99%94)로 람다내에서 사용하는 임의 변수를 정의하여 사용할 수 있습니다.
> * (C++14~) [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받아 마치 [함수 템플릿](??)처럼 동작하는 [일반화된 람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)가 추가되었습니다.
> * (C++17~) [람다 캡쳐시 *this 를 이용](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90)하여 개체 자체를 복제하여 사용합니다.
> * (C++17~) [constexpr 람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c17-constexpr-%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)가 추가되어 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)도 컴파일 타임 함수로 만들 수 있습니다.
 
# 개요
기존에는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 이용하여 함수를 개체화 했는데요,

C++11 부터는 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)을 추가하여 함수 지향 프로그래밍이 좀 더 간편해 졌습니다.

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)은 `prvalue` 타입(*[값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC) 참고*)의 1회용 익명 함수자를 만들며, 이를 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure) 라고 합니다. 

# 람다 표현식

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)은 STL의 [알고리즘](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-algorithm/)과 잘 호환될 뿐만 아니라, [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90) 기능등의 활용도도 높아서 복잡한 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 만들기 보다는 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)을 사용하는게 훨씬 좋습니다.

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)의 기본 문법은 다음과 같습니다.

`[람다 캡쳐](인자들) -> 리턴 타입 {표현식}`

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

# 클로저(Closure)

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)으로 런타임에 생성된 개체입니다. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 정의시 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)로 만들어 집니다.

# 람다 표현식 리턴 생략

[후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)이 생략되면, 함수의 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)에 의해 추론됩니다.(*[리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0) 참고*)
[리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 없으면 `void`로 추론됩니다.

```cpp
[](int a, int b) {return a + b;} // a + b의 결과 타입으로 리턴 타입이 추론됨
```

# 클로저 호출(람다 표현식 실행)

일반 함수를 호출하려면 `f(10, 20)` 와 같이 호출하는데요, [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)으로 작성된 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)를 호출하려면 다음과 같이 합니다.

1. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)으로부터 생성된 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)를 변수에 저장한뒤 호출할 수 있고,

    ```cpp
    auto f_11{
        [](int a, int b) -> int {return a + b;}
    };
    int val{f_11(10, 20)}; // 함수 호출하듯이 f_11(10, 20) 로 호출합니다.
    EXPECT_TRUE(val == 30);
    ```

2. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)에 `()`을 붙여 바로 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)를 호출할 수 있습니다.

    ```cpp
    int val_11{
        [](int a, int b) -> int {return a + b;}(10, 20) // 람다 표현식에 (10, 20)을 붙여 호출합니다.
    }; 
    EXPECT_TRUE(val_11 == 30);
    ```

# 람다 캡쳐

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 바깥 부분의 개체 정보들을 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 내부에 전달합니다.

|항목|내용|
|--|--|
|`[]` (C++11~)|아무것도 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)하지 않습니다.|
|`[=]` (C++11~)|외부의 모든 변수를 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)형 값으로 가져옵니다.|
|`[&]` (C++11~)|외부의 모든 변수의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 가져옵니다.|
|`[=, &x, &y]` (C++11~)|외부의 모든 변수를 값으로 가져오되 `x`, `y` 만 참조로 가져옵니다.|
|`[&, x, y]` (C++11~)|외부의 모든 변수의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 가져오되 `x`, `y` 만 값으로 가져옵니다.|
|[this](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0) (C++11~)|[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)을 사용한 개체의 [this 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 값으로 가져옵니다.|
|`*this` (C++17~)|[this](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)는 포인터를 가져오지만, `*this`는 개체 자체를 복제하여 가져옵니다.|

다음 코드에서는 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 외부에 정의된 `sum`을 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)하고, [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 내에서 `v`의 각 요소의 값을 전달받아 `sum`에 누적합니다.

```cpp
int sum{0};
std::vector<int> v_11{1, 2, 3};

std::for_each( // 시퀀스 안의 요소들에 대해 f(요소)를 실행합니다. f는 람다 표현식입니다.
    v_11.begin(), 
    v_11.end(),
    [&sum](int val) {sum += val;} // 람다 캡쳐된 sum에 시퀀스 요소의 값(val)을 누적합니다.
);

EXPECT_TRUE(sum == 1 + 2 + 3);
```

`[=]`나 `[&]` 는 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)되는 대상이 무엇인지 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 본문을 확인해야 알 수 있으므로 `[&var1, &var2]`와 같이 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)하는 항목을 나열하는게 좀 더 직관적이여서 좋습니다.

# 람다 캡쳐 시점

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)은 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)가 생성될 때 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)를 합니다. 따라서, [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)를 생성한 시점과 호출한 시점이 다르면, 값이 다를 수 있습니다.

```cpp
int val{1};
auto f_11{
    [=]() -> int {return val;} // 클로저가 생성되는 시점에 val을 람다 캡쳐합니다.
}; 
val = 2;

EXPECT_TRUE(f_11() == 1); // 람다 캡쳐할 때의 값을 사용하므로 1입니다.  
```

# 값 캡쳐

값으로 개체를 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)하면 복제본을 만들어 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)로 사용합니다. 따라서, 그 값을 수정할 수 없습니다. 다만, 포인터 변수는 `int* const`여서 `ptr`은 수정할 수 없지만, `*ptr`은 수정할 수 있습니다.

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

이런 경우 [mutable](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)을 사용하면 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)한 개체를 수정할 수 있으나, 원본이 아닌 복제본만 수정됩니다.(*원본을 수정하려면 [참조 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%B0%B8%EC%A1%B0-%EC%BA%A1%EC%B3%90)를 이용하셔야 합니다.*)

```cpp
int a{1};
int b{2};
int c{3};
int* ptr{&b};
int& ref{c};
[=]() mutable { // 값 캡쳐했지만 수정할 수 있습니다.
    a = 10; // (△) 비권장. 복제본이 수정될 뿐, 원본이 수정되는 건 아닙니다.
    *ptr = 20;
    ref = 30; // (△) 비권장. 복제본이 수정될 뿐, 원본이 수정되는 건 아닙니다.
}();

EXPECT_TRUE( a == 1 && b == 20 && c == 3);         
```

개체의 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 내에서 사용할 때에는 [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)시 [this 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)가 복제되어 개체의 내부 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 수정할 수 있습니다.

```cpp
class T {
    int m_Member{1};
public:
    int GetMember() const {return m_Member;}
    int Func() {
        int local{2};

        auto f_11{
            [=]() -> int {
                // this->m_Member = 10; 
                m_Member = 10; // this->m_Member = 10; 과 동일. 멤버 변수의 값을 수정합니다.
                return m_Member + local;
            }
        };  

        return f_11();
    }
};

T t;
EXPECT_TRUE(t.Func() == 12);
EXPECT_TRUE(t.GetMember() == 10); // 멤버 변수가 수정되어 있습니다.
```

# 참조 캡쳐

[참조 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%B0%B8%EC%A1%B0-%EC%BA%A1%EC%B3%90)를 이용하면, [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)한 개체를 수정할 수 있습니다.

```cpp
int a{1};
int b{2};
int c{3};
int* ptr{&b};
int& ref{c};
[&]() { // 람다 캡쳐한 개체를 수정할 있습니다.
    a = 10; 
    *ptr = 20;
    ref = 30;
}();

EXPECT_TRUE( a == 10 && b == 20 && c == 30);     
```

# 클로저에 이동 연산 전달

[람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)는 [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)를 이용한 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)과, [참조 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%B0%B8%EC%A1%B0-%EC%BA%A1%EC%B3%90)를 이용한 참조로 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 내부에 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 바깥 부분의 개체 정보들을 전달합니다. 그런데, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 전달하려면 어떻게 할까요?

다음과 같이 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)만 지원하는 개체 `A_11`이 있다고 합시다.

```cpp
class A_11 {
public:
    A_11() {}
    A_11(const A_11& other) = delete;
    A_11(A_11&& other) {
        std::cout << "A : Lambda Move" << std::endl;
    }
    A_11& operator =(const A_11& other) = delete;
    A_11& operator =(A_11&& other) = delete; 
};
```

다음과 같이 [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)는 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 없으므로 사용할 수 없습니다.

```cpp
A_11 a; // 기본 생성과 이동 생성만 가능한 개체입니다.

[a]() {a;}(); // (X) 컴파일 오류. 복사 생성자가 delete 되었습니다.
```

물론 [참조 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%B0%B8%EC%A1%B0-%EC%BA%A1%EC%B3%90)는 이용할 수 있으나, 이동된 것은 아니죠.

```cpp
A_11 a; // 기본 생성과 이동 생성만 가능한 개체입니다.

[&a]() {a;}(); // (O) 참조로 사용해서 컴파일 오류는 없습니다. 그러나 이동된 것은 아니죠.
```

이동을 시키려면 다음과 같이 [bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)를 이용하여 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 만들면 됩니다.

1. `func_11`은 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 저장합니다. 추후 `func_11()`와 같이 호출해 주어야 합니다.
2. [bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)로 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 생성합니다. 

    이때 첫번째 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 함수처럼 호출될 수 있는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 입니다. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)으로 만들어진 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure) 처럼요.

    두번째 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 첫번째 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)인 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)에 인수로 전달될 개체입니다. 

    결과적으로 [bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)는 `Closure(param);`의 형태로 호출하는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 생성합니다.
3. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)으로 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)를 전달합니다. 여기서 `const A_11& param`를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용하는데요, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)로부터 생성된 개체(*#4에서 생성된 개체*)를 참조로 전달받습니다. 
4. `std::move(a)`로 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 전달합니다. [bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)는 이값을 개체로 저장(*`변수 = 우측값 참조;`이다 보니, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출됩니다.*)하고 있다가 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 호출하는 #5 시점에 사용합니다.
5. [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 실행합니다. 즉, 함수를 호출합니다.

```cpp
A_11 a; // 기본 생성과 이동 생성만 가능한 개체입니다.

auto func_11 { // #1. bind 개체를 저장합니다. func_11() 형태로 호출할 수 있습니다.
    std::bind( // #2. bind 개체를 생성합니다. lambda(obj) 를 호출하는 함수자를 만듭니다. 이시점에 이동 생성자가 호출됩니다. 
        [](const A_11& param) {param;}, // #3. 이동 생성자로부터 생성된 개체(#4에서 생성된 개체)를 참조합니다. 
        std::move(a) // #4. 우측값 참조. bind 내에서 이동 생성자로부터 생성된 개체가 만들어 집니다.
    )
};
func_11(); // #5. 함수를 호출합니다.
```

> *(C++14~) [람다 캡쳐 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90-%EC%B4%88%EA%B8%B0%ED%99%94)로 람다내에서 사용하는 임의 변수를 정의하여 사용할 수 있습니다. 이를 이용하면 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 전달하는게 훨씬 간단해 집니다.*

```cpp
A_11 a; // 기본 생성과 이동 생성만 가능한 개체입니다.

auto f_14{
    [b = std::move(a)](){b;} // a를 이동 생성한 b를 사용합니다.
}; 
f_14();
```

# 클로저 대입

[클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)는 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)와 [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)을 이용하여 변수에 대입할 수 있습니다.

1. [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)(*단, [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)이기 때문에 [함수의 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용할 수 없습니다.*)
    
    ```cpp
    auto f_11{
        [](int a, int b) -> int {return a + b;}
    };
    EXPECT_TRUE(f_11(10, 20) == 30);
    ```

2. 함수 포인터(*단, [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)를 지원하지 않습니다.*)
   
    ```cpp
    typedef int (*Func)(int, int); // 함수 포인터 typedef
    Func f_11{
        [](int a, int b) -> int {return a + b;}
    };
    EXPECT_TRUE(f_11(10, 20) == 30);
    ```

3. [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)(*[람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)도 지원하고, [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용할 수도 있습니다.*)
    
    ```cpp
    std::function<int(int, int)> f_11;
    int g(const std::function<int(int, int)>& lambda_11, int a, int b) { // 함수의 인자로 람다를 지정합니다.
        return lambda_11(a, b);
    }
    int c{30};
    f_11 = [=](int a, int b) -> int {return a + b + c;}; // 람다 캡쳐를 사용하는 람다 표현식도 사용할 수 있습니다.      
    EXPECT_TRUE(g(f_11, 10, 20) == 60); // g() 함수에 클로저를 저장한 f_11을 전달합니다.       
    ```

# 클로저 복사 부하

[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)에서 [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)를 사용하면, 

1. [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)시에 복사 부하가 있으며, 
2. [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)간 복사시에도 복사 부하가 있으므로,

[참조 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%B0%B8%EC%A1%B0-%EC%BA%A1%EC%B3%90)를 이용하는게 좋습니다.

다음 `T`클래스는 생성자와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출을 탐지하기 위한 테스트 클래스입니다.

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

1. [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)던, [참조 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%B0%B8%EC%A1%B0-%EC%BA%A1%EC%B3%90)던, 해당 개체를 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)에서 사용하지 않으면 복사하지 않습니다.

    ```cpp
    T t;
    [=]() {std::cout << "Run Lambda" << std::endl;}(); // t를 사용하지 않았습니다.
    ```
    ```cpp
    T::Default Constructor
    Run Lambda
    T::Destructor
    ```

2. [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)를 사용하면, 대상 개체가 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)에 사용될때, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하여, [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 복제본을 만듭니다.

    ```cpp
    T t;
    [=]() {
        t; // t;로 사용합니다. 람다 캡쳐시 복사 부하가 있습니다.      
        std::cout << "Run Lambda" << std::endl;
    }();  
    ```

    ```cpp
    T::Default Constructor
    T::Copy Constructor // 람다 캡쳐시 복사 생성자를 호출하여 const 복제본을 만듭니다.
    Run Lambda
    T::Destructor // 복제본을 삭제합니다.
    T::Destructor    
    ```

3. [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)를 사용하고, [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure)를 변수에 저장하고 호출하는 것은 추가의 복사 부하가 없습니다. 즉, 2와 동일합니다.

    ```cpp
    T t;
    auto f_11{
            [=]() { // 변수에 저장하고, 호출합니다.   
            t; 
            std::cout << "Run Lambda" << std::endl;
        }
    };      
    f_11();
    ```
    ```cpp
    T::Default Constructor
    T::Copy Constructor // 추가 복사 부하가 없습니다.
    Run Lambda
    T::Destructor
    T::Destructor
    ```

4. [값 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EA%B0%92-%EC%BA%A1%EC%B3%90)를 사용할때 [클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure) 끼리 복제하면 추가의 복사 부하가 발생합니다.

    ```cpp
    T t;
    auto f1_11{
        [=]() {
            t; 
            std::cout << "Run Lambda" << std::endl;
        }
    }; 
    auto f2_11{f1_11}; // 클로저를 복제합니다. 복사 부하가 발생합니다.
    f1_11();
    f2_11();
    ```

    ```cpp
    T::Default Constructor
    T::Copy Constructor // 람다 캡쳐시 복사 생성자를 호출하여 const 복제본을 만듭니다.
    T::Copy Constructor // f2{f1}시 클로저 복제본을 만듭니다. 추가 복사 부하가 있습니다.
    Run Lambda
    Run Lambda
    T::Destructor
    T::Destructor
    T::Destructor
    ```

5. [참조 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%B0%B8%EC%A1%B0-%EC%BA%A1%EC%B3%90)를 사용하면 복사 부하가 없습니다.

    ```cpp
    T t;
    auto f1_11{
        [&]() { // 람다 캡쳐시 복사 부하가 없습니다.
        t; 
            std::cout << "Run Lambda" << std::endl;
        }
    }; 
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

C++14 부터는 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)시에 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)내에서 사용하는 임의 변수를 정의하여 사용할 수 있습니다.
정의한 변수는 초기값이 지정되어야 하며, [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D) 내에서만 사용할 수 있습니다. 

[클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%ED%81%B4%EB%A1%9C%EC%A0%80closure) 생성시에 [람다 캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90)하므로, [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)을 여러번 호출하더라도 기존 값이 유지됩니다. 

```cpp
// 클로저 생성시 람다 내에서 사용할 수 있는 val_14 변수를 만들어 람다 캡쳐 합니다.
// 람다 캡쳐된 val_14 을 수정하기 위해 mutable로 만듭니다.
auto f_14{
    [val_14 = 0]() mutable -> int {return ++val_14;}
}; 

// 호출시마다 람다 캡쳐된 val이 증가합니다.
EXPECT_TRUE(f_14() == 1);
EXPECT_TRUE(f_14() == 2);
EXPECT_TRUE(f_14() == 3);
```

# (C++14~) 일반화된 람다 표현식

C++14 부터는 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 있어, 마치 [함수 템플릿](??)처럼 동작하게 할 수 있습니다. 다만 [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)에서와 같이 [auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)은 적용되지 않습니다.

```cpp
auto add_14{
    [](auto a, auto b) {return a + b;}
}; 

EXPECT_TRUE(add_14(1, 2) == 3);
EXPECT_TRUE(add_14(std::string{"hello"}, std::string{"world"}) == "helloworld");
```

마치 [함수 템플릿](??)과 유사하죠.

```cpp
template<typename T, typename U> 
auto add_14(T a, U b) {
    return a + b;
} 

EXPECT_TRUE(add_14(1, 2) == 3);
EXPECT_TRUE(add_14(std::string{"hello"}, std::string{"world"}) == "helloworld");
```

하지만 템플릿 표현이 아니다 보니 [완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 위한 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 구문을 사용하기 어렵습니다.

```cpp
int f_11(int&) {return 1;}
int f_11(int&&) {return 2;}

auto Forwarding_14{
    [](auto&& param) {
        return f_11(std::forward<??>(param)); // (X) 컴파일 오류. 템플릿이라면 템플릿 인자 T를 전달하면 되는데, ??에 무얼 전달해야 할까요? 
    }
}; 

int val;
int& ref = val;
EXPECT_TRUE(Forwarding_14(val) == 1);
EXPECT_TRUE(Forwarding_14(ref) == 1);
EXPECT_TRUE(Forwarding_14(std::move(val)) == 2);
```

다행히 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 인 변수를 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)을 해보면, [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 나오고, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 나옵니다. 즉, [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)을 사용하면, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수의 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)은 [forward() 원리](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%9B%90%EB%A6%AC)에서와 살펴본 것과 동일해 집니다.

|항목|[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)|[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수의 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)|[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수의 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)|
|--|--|--|--|
|값 타입|`decltype<param> == int&, param == int&`|`T == int&, param == int&`|`int&`|
|[좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`decltype(param) == int&, param == int&`|`T == int&, param == int&`|`int&`|
|[함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)처럼 [우측값을 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하는 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`decltype(param) == int&&, param == int&`|`T == int&&, param == int&`|`int&&`|

따라서 [일반화된 람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)에서는 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)을 이용하여 [완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 할 수 있습니다.

```cpp
int f_11(int&) {return 1;}
int f_11(int&&) {return 2;}

auto Forwarding_14{
    [](auto&& param) {
        // 전달 참조일때 decltype(param)은 
        // param이 좌측값 참조이면 T&, param이 우측값 참조이면 T&& 입니다.
        return f_11(std::forward<decltype(param)>(param)); 
    }
}; 

int val;
int& ref = val;
EXPECT_TRUE(Forwarding_14(val) == 1); // 전달 참조에서 값 타입은 좌측값 참조입니다.
EXPECT_TRUE(Forwarding_14(ref) == 1); // 전달 참조에서 좌측값 참조는 그대로 좌측값 참조입니다.
EXPECT_TRUE(Forwarding_14(std::move(val)) == 2); // 전달 참조에서 우측값 참조는 그대로 우측값 참조입니다.       
```

# (C++17~) constexpr 람다 표현식

C++17 부터 [constexpr 람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c17-constexpr-%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)이 추가되어 요구사항이 충족되면 암시적으로 컴파일 타임 함수로 만들어지고, [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr) 을 지정하여 명시적으로 컴파일 타임 함수로 만들 수 있습니다.

```cpp
// 명시적 constexpr 람다 표현식 입니다.
auto id_17{
    [](int n) constexpr {return n;}
};
static_assert(id_17(10) == 10);

// 암시적 constexpr 람다 표현식 입니다.
auto add_17{
    [](int a, int b) {return a + b;}
};
static_assert(add_17(1, 2) == 3);

// 인자로 전달한 a_11, b_11는 컴파일 타임 상수이기 때문에 요구사항이 맞아 암시적 constexpr 람다 표현식 입니다.
constexpr int a_11{1};
constexpr int b_11{2};
static_assert(add_17(a_11, b_11) == 3);

// 인자로 전달한 c, d는 변수이기 때문에 요구사항이 맞지 않아 런타임 람다  표현식 입니다.
int c{1};
int d{2};
EXPECT_TRUE(add_17(c, d) == 3);
```