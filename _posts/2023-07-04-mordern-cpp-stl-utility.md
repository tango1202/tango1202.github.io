---
layout: single
title: "#4. [모던 C++ STL] 유틸리티"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)이 추가되어 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 좀 더 간편하게 만들 수 있습니다.
> * (C++11~) [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)가 추가되어 좌측값을 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 형변환할 수 있습니다.
> * (C++11~) [move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept)가 추가되어 [nothrow 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 되는 경우에만 `&&`로 형변환할 수 있습니다.
> * (C++11~) [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward)가 추가되어 함수가 전달받은 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수로 [완벽하게 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)할 수 있습니다.
> * (C++11~) [declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval)이 추가되어 참조 타입 표현식으로 변경할 수 있습니다.
> * (C++11~) [quick_exit(), _Exit(), at_quick_exit()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90)가 추가되었습니다.
> * (C++11~) [va_copy](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)가 추가되었습니다.
> * (C++14~) [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)가 추가되어 주어진 값을 바꾸는 작업이 간편해 졌습니다. 특히 [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.
> * (C++17~) [as_const()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c11-%ED%83%80%EC%9E%85-%EB%B3%80%ED%99%98)가 추가되어 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 좀더 간편하게 붙일 수 있습니다.
> * (C++17~) [optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/)이 추가되어 값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 성공 여부 처리를 좀더 단순하게 할 수 있습니다.
> * (C++17~) [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/)가 추가되어 타입의 변동 가능성이 있는 데이터를 비교적 안전하게 사용할 수 있습니다.
> * (C++17~) [variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/)가 추가되어 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다.
> * (C++17~) [inplace](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c17-%EB%AA%A8%ED%98%B8%EC%84%B1-%ED%95%B4%EC%86%8C)등이 추가되었습니다. 내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 내부 개체를 직접 생성하라는 의미로 [optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/), [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/), [variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/) 생성자에 더미(*Dummy*) 개체로 사용됩니다.
> * (C++17~) [timespec_get(), timespec](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%8B%9C%EA%B0%84-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)이 추가되었습니다.
> * (C++20~) [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [!=, >, <=, >=](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90)는 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.
> * (C++20~) [cmp_equal(), cmp_not_equal(), cmp_less(), cmp_greater(), cmp_less_equal(), cmp_greater_equal()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90)이 추가되어 음의 정수와 양의 정수를 정상적으로 비교할 수 있습니다.
> * (C++20~) [in_range()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90)가 추가되었습니다. 주어진 `value`가 주어진 `type`의 값 범위 내에 있는지 검사합니다.
> * (C++20~) [source_location](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c20-source_location)이 추가되어 파일명, 줄번호, 칼럼번호, 함수명등의 정보를 제공합니다.
> * (C++20~) [삼중 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90) 관련 유틸리티들이 추가되었습니다.
> * (C++20~) [유틸리티의 constexpr 지원이 개선](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c20-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0%EC%9D%98-constexpr-%EA%B0%9C%EC%84%A0)되어 `swap()`함수도 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)로 변경되었습니다.

# 일반 유틸리티

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`swap()`|바꿔치기 합니다.|(C++20~)|
|[exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/) (C++14~)|주어진 값을 바꾸고 이전값을 리턴합니다. [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.|(C++20~)|

# 간단한 데이터 개체

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|[pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)|`first`와 `second`로 2개의 요소를 관리합니다. `make_pair()`를 이용하여 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)를 쉽게 생성할 수 있습니다.|생성자에 따라,<br/>(C++11~)<br/>(C++14~)<br/>(C++20~)|
|[tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/) (C++11~)|다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.|생성자에 따라,<br/>(C++11~)<br/>(C++14~)<br/>(C++20~)<br/>(C++23~)|
|[optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/) (C++17~)|값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 성공 여부 처리를 좀더 단순하게 할 수 있습니다.|생성자에 따라,<br/>(C++17~)<br/>(C++20~)|
|[any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/) (C++17~)|타입의 변동 가능성이 있는 데이터를 비교적 안전하게 사용할 수 있습니다.|생성자에 따라,<br/>(C++17~)|
|[variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/) (C++17~)|타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다.|(C++17~)|

# 개체 비교

[대소 비교의 논리 조건](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EB%8C%80%EC%86%8C-%EB%B9%84%EA%B5%90%EC%9D%98-%EB%85%BC%EB%A6%AC-%EC%A1%B0%EA%B1%B4)에 따라 다음의 연산자가 제공됩니다.

|항목|내용|
|--|--|
|`!=` (~C++20)|`==`을 활용하여 구현되어 있습니다.|
|`>, <=, >=` (~C++20)|`<`을 활용하여 구현되어 있습니다.|

하지만, C++20 부터  [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 모두 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.

기존에는 `signed`와 `unsigned`를 비교하면, `signed`를 `unsigned`로 [암시적으로 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 하기 때문에, 음의 정수와 양의 정수를 정상적으로 비교하지 못했습니다.

```cpp
int x{-1}; // 0xFFFF FFFF(4294967295) 
unsigned int y{1};
EXPECT_TRUE(x < y); // (X) 런타임 오류. -1은 unsigned int로 형변환되어 4294967295입니다.
```

C++20 부터는 [cmp_equal(), cmp_not_equal(), cmp_less(), cmp_greater(), cmp_less_equal(), cmp_greater_equal()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90)이 추가되어 음의 정수와 양의 정수를 정상적으로 비교할 수 있습니다.

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|[cmp_equal()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90) (C++20~)<br/>[cmp_not_equal()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90) (C++20~)<br/>[cmp_less()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90) (C++20~)<br/>[cmp_greater()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90) (C++20~)<br/>[cmp_less_equal()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90) (C++20~)<br/>[cmp_greater_equal()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90) (C++20~)|음의 정수와 양의 정수를 정상적으로 비교 합니다.|(C++20~)|
|[in_range<type>(value)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90) (C++20~)|주어진 `value`가 주어진 `type`의 값 범위 내에 있는지 검사합니다.|(C++20~)|

다음은 [cmp_less()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90)를 이용하여 음의 정수와 양의 정수를 비교하는 예입니다.

```cpp
int x{-1}; 
unsigned int y{1};
EXPECT_TRUE(std::cmp_less(x, y)); // (O) 음수와 양수를 정상적으로 비교합니다.  
```

[in_range()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%9C%EC%B2%B4-%EB%B9%84%EA%B5%90)는 주어진 `value`가 주어진 `type`의 값 범위 내에 있는지 검사합니다.

```cpp
static_assert(std::in_range<unsigned int>(-1) == false); // unsigned int 범위 바깥입니다.
static_assert(std::in_range<unsigned int>(1) == true); // unsigned int 범위 입니다. 
```

# (C++11~) 타입 변환

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move) (C++11~)|좌측값을 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 형변환 합니다.|(C++14~)|
|[move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept) (C++11~)|[nothrow 보증](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 되는 경우에만 `&&`로 형변환 합니다.|(C++14~)|
|[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) (C++11~)|함수가 전달받은 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수로 [완벽하게 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)합니다.|(C++14~)|
|`forward_like()` (C++23)|(작성중)||
|[declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval) (C++11~)|주어진 타입을 참조 타입으로 변환하여, 참조 타입 표현식으로 변경해 줍니다.||
|`as_const(T& param)` (C++17~)|[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) `param`에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 붙여 `const T&`로 변환합니다.|(C++17~)|
|`to_underlying()` (C++23~)|(작성중)|(C++23~)|

# 프로그램 지원

|항목|내용|
|--|--|
|[abort()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90)|프로그램을 비정상 종료 시킵니다. [terminate()](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#terminate) 참고|
|`exit()`|프로그램을 정상 종료 시킵니다.|
|`quick_exit()` (C++11~)|(작성중)|
|`_Exit()` (C++11~)|(작성중)|
|`atexit()`|(작성중)|
|`at_quick_exit()` (C++11~)|(작성중)|
|`EXIT_SUCCESS`<br/>`EXIT_FALURE`|(작성중)|
|`system()`|(작성중)|
|`getenv()`|(작성중)|
|`signal()`|(작성중)|
|`raise()`|(작성중)|
|`sig_atomic_t`|(작성중)|
|`SIG_DFL`<br/>`SIG_IGN`|(작성중)|
|`SIG_ERR`|(작성중)|
|`SIGABRT`<br/>`SIGFPE`<br/>`SIGILL`<br/>`SIGINT`<br/>`SIGSEGV`<br/>`SIGTERM`|(작성중)|
|`setjmp()`|(작성중)|
|`longjmp()`|(작성중)|
|`jump_buf()`|(작성중)|
|`unreachable()` (C++23~)|도달할 수 없는 실행 지점을 마킹합니다.|

# 가변 인자

[가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 처리하는 예는 [가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 참고하시기 바랍니다.

|항목|내용|
|--|--|
|[va_start()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 액세스 시작 [매크로 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|[va_arg()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 추출 [매크로 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|[va_end()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 사용 종료 [매크로 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|[va_list](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)에 대한 [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)|
|`va_copy` (C++11)|(작성중)|

# C스타일 시간 유틸리티

|항목|내용|
|--|--|
|`difftime()`|(작성중)|
|`time()`|(작성중)|
|`clock()`|(작성중)|
|`asctime()`|(작성중)|
|`ctime()`|(작성중)|
|`strftime()`|(작성중)|
|`wcsftime()`|(작성중)|
|`gmtime()`|(작성중)|
|`localtime()`|(작성중)|
|`mktime()`|(작성중)|
|`CLOCKS_PER_SEC`|(작성중)|
|`tm`|(작성중)|
|`time_t`|(작성중)|
|`clock_t`|(작성중)|
|`timespec_get()` (C++17~)|`timespec` 개체를 구합니다.|
|`timespec` (C++17~)|초(`tv_sec`)와 나노초(`tv_nsec`)로 구분합니다.|

# (C++17~) 모호성 해소

|항목|내용|
|--|--|
|`in_place` (C++17~)<br/>`in_place_type` (C++17~)<br/>`in_place_index` (C++17~)<br/>`in_place_t` (C++17~)<br/>`in_place_index_t` (C++17~)|내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 내부 개체를 직접 생성하라는 의미로 [optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/), [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/), [variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/) 생성자에 전달되는 더미(*Dummy*) 개체 입니다.|

# (C++20~) source_location

기존에는 [`__LINE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)과 [`__FILE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)를 이용하여 줄번호와 파일명을 사용했는데요(*[`__LINE__`, `__FILE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line) 참고*),

```cpp
// Line Number:118 Filename:C:\XXX\XXX.cpp
std::cout << "Line Number:" << __LINE__ << " Filename:" << __FILE__ << std::endl; 
```

C++20 부터는 [source_location](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c20-source_location)이 추가되어 파일명, 줄번호, 칼럼번호, 함수명등의 정보를 제공합니다.

다음의 `Log()`함수는 메시지와 [source_location](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c20-source_location)을 이용한 부가 정보를 출력합니다.

```cpp
void Log(std::string message, const std::source_location& location = std::source_location::current()) {

    // Message.
    // source_location : F:\Data\language_test\test\Test_MordernCpp_Utility.cpp, virtual void TestMordern_Utility_Test::TestBody()(41, 12)
    std::cout 
        << message << std::endl
        << "source_location : " 
        << location.file_name() << ", " << location.function_name()  // 파일명, 함수명
        << "(" << location.line() << ", " << location.column() << ")"// 줄번호, 칼럼번호
    << std::endl;
}

Log("Message.");    
```

# (C++20~) 삼중 비교

C++20 부터 추가된 [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)를 지원하는 유틸리티입니다.

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|[strong_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) (C++20~)|`==`, `!=`, `<`, `>`, `<=`, `>=`의 비교 연산을 제공합니다. 여기서 `==`은 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)입니다. 즉 데이터들이 완전히 동일함을 의미합니다.| 
|[weak_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) (C++20~)|`==`, `!=`, `<`, `>`, `<=`, `>=`의 비교 연산을 제공합니다. 여기서 `==`은 [동등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 합니다. 즉, 개념적으로 동일함을 의미합니다. 예를들어 대소문자 구분없이 비교 할때 `A`와 `a`는 [아스키 코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%95%84%EC%8A%A4%ED%82%A4-%EC%BD%94%EB%93%9C)값이 다르므로 [상등](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)하지 않지만, 개념적으로 [동등](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)합니다.| 
|[partial_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) (C++20~)|`==`, `!=`, `<`, `>`, `<=`, `>=`의 비교 연산을 제공합니다. 실수 타입과 같이 대소 비교는 가능한데, `==`는 소수점 오차등으로 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 신뢰하기 애매한 경우입니다.|  
|`strong_order()` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)로 비교 하고 [strong_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)를 리턴합니다.|(C++20~)|
|`weak_order()` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)로 비교 하고 [weak_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)를 리턴합니다.|(C++20~)|
|`partial_order()` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)로 비교 하고 [partial_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)를 리턴합니다.|(C++20~)|
|`is_eq(), is_neq()` (C++20~)<br/>`is_lt(), is_lteq(), is_gt(), is_gteq()` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)로 비교합니다.|  (C++20~)|
|`compare_three_way` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)로 비교하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)입니다.||
|`compare_three_way_result` (C++20~)|주어진 타입의 [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90) 리턴 타입의 별칭입니다. [strong_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85), [weak_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85), [partial_ordering](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 중 하나입니다.||
|`common_comparison_category` (C++20~)|주어진 타입의 [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)를 리턴합니다.||
|`compare_strong_order_fallback()` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 없는 경우에도 삼중 비교를 합니다.|(C++20~)|
|`compare_weak_order_fallback()` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 없는 경우에도 삼중 비교를 합니다.|(C++20~)|
|`compare_partial_order_fallback()` (C++20~)|[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 없는 경우에도 삼중 비교를 합니다.|(C++20~)|

다음은 [삼중 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90) 관련 유틸리티들의 사용예입니다.

`compare_three_way`, `strong_order()`등은 [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 없으면 사용할 수 없으며,
`compare_strong_order_fallback()`, `compare_weak_order_fallback()`, `compare_partial_order_fallback()`은 [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 없는 경우이더라도 [삼중 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90)를 가능하게 합니다.

```cpp
class T {
private:
    int m_Val;

public:
    explicit T(int val) : m_Val(val) {}

    bool operator ==(const T& other) const {return m_Val == other.m_Val;}
    bool operator <(const T& other) const {return m_Val < other.m_Val;}

    // 삼중 비교 연산자가 없습니다.
    // std::strong_ordering operator<=>(const T& other) const {return m_Val <=> other.m_Val;}
};

// EXPECT_TRUE(T{1} <=> T{2} == 0); // (X) 컴파일 오류. 삼중 비교 연산자가 없습니다.
// EXPECT_TRUE(std::compare_three_way{}(T{1}, T{2}) < 0); // (X) 컴파일 오류. 삼중 비교 연산자가 없습니다.
// EXPECT_TRUE(std::strong_order(T{1}, T{2}) < 0); // (X) 컴파일 오류. 삼중 비교 연산자가 없습니다.
EXPECT_TRUE(std::compare_strong_order_fallback(T{1}, T{2}) < 0); // 삼중 비교 연산자가 없더라도, 삼중 비교를 할 수 있습니다.
```

# (C++20~) 유틸리티의 constexpr 개선

[유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/)에서 점진적으로 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/) 지원을 개선하고 있습니다. 

특히 C++20 부터는 `swap()`함수도 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)로 변경되었습니다.

따라서 다음과 같이 `swap()`을 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)내에서 사용할 수 있습니다.

```cpp
constexpr std::pair<int, int> ConstSwap(int&& a, int&& b) {
    int resultA{std::move(a)};
    int resultB{std::move(b)};
    
    std::swap(resultA, resultB); // 컴파일 타임에 두 수를 바꿔치기 합니다.
    return std::make_pair(resultA, resultB);
}

constexpr std::pair<int, int> result{ConstSwap(0, 1)};
static_assert(result.first == 1 && result.second == 0);  
```

