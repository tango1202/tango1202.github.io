---
layout: single
title: "#3. [모던 C++ STL] 유틸리티"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)는 좌측값을 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 형변환 합니다.
> * (C++11~) [move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept)는 [nothrow 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 되는 경우에만 `&&`로 형변환 합니다.
> * (C++11~) [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward)는 함수가 전달받은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수로 [완벽하게 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)합니다.
> * (C++11~) [declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval)은 주어진 타입을 참조 타입으로 변환하여, 참조 타입 표현식으로 변경해 줍니다.
> * (C++14~) [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)는 주어진 값을 바꾸고 이전값을 리턴합니다. [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.
> * (C++17~) [as_const()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%83%80%EC%9E%85-%EB%B3%80%ED%99%98)는 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 붙여 줍니다.
> * (C++17~) [optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/)이 추가되어 값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 성공 여부 처리를 좀더 단순하게 할 수 있습니다.
> * (C++17~) [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/)가 추가되어 타입의 변동 가능성이 있는 데이터를 비교적 안전하게 사용할 수 있습니다.
> * (C++17~) [variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/)가 추가되어 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다.

# 일반 유틸리티

|항목|내용|
|--|--|
|`swap()`|바꿔치기 합니다.|
|[exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/) (C++14~)|주어진 값을 바꾸고 이전값을 리턴합니다. [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.|

# 간단한 데이터 개체
|항목|내용|
|--|--|
|[pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)|`first`와 `second`로 2개의 요소를 관리합니다. `make_pair()`를 이용하여 [pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)를 쉽게 생성할 수 있습니다.|
|[tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/) (C++11~)|다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.|
|[optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/) (C++17~)|값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 성공 여부 처리를 좀더 단순하게 할 수 있습니다.|
|[any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/)|타입의 변동 가능성이 있는 데이터를 비교적 안전하게 사용할 수 있습니다.|
|[variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/) (C++17~)|타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있습니다.|


# 타입 변환

|항목|내용|
|--|--|
[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move) (C++11~)|좌측값을 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 형변환 합니다.|
|[move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept) (C++11~)|[nothrow 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 되는 경우에만 `&&`로 형변환 합니다.|
|[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) (C++11~)|함수가 전달받은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수로 [완벽하게 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)합니다.|
|`forward_like()` (C++23)|(작성중)|
|[declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval) (C++11~)|주어진 타입을 참조 타입으로 변환하여, 참조 타입 표현식으로 변경해 줍니다.|
|`as_const(T& param)` (C++17~)|[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) `param`에 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 붙여 `const T&`로 변환합니다.|
|`to_underlying()` (C++23~)|(작성중)|

# 정수 비교

|항목|내용|
|--|--|
|`cmp_equal()` (C++20~)<br/>`cmp_not_equal()` (C++20~)<br/>`cmp_less()` (C++20~)<br/>`cmp_greater()` (C++20~)<br/>`cmp_less_equal()` (C++20~)<br/>`cmp_greater_equal()` (C++20~)|(작성중)|
|`in_range()` (C++20~)|(작성중)|

# 대소 비교

|항목|내용|
|--|--|
|`!=` (~C++20)|(작성중)|
|`>, <=, >=` (~C++20)|(작성중)|
  
# 모호성 해소

|항목|내용|
|--|--|
|`in_place` (C++17~)<br/>`in_place_type` (C++17~)<br/>`in_place_index` (C++17~)<br/>`in_place_t` (C++17~)<br/>`in_place_index_t` (C++17~)|내부 개체를 생성해서 전달하는 것이 아니라, 내부 개체의 생성자 인수들을 전달하면 내부 개체를 직접 생성하라는 의미로 [optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/), [any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/), [variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/) 생성자에 전달되는 더미 개체 입니다.|

# 프로그램 지원

|항목|내용|
|--|--|
|`abort()`|프로그램을 비정상 종료 시킵니다. [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 참고|
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

# 소스 코드

|항목|내용|
|--|--|
|`source_location` (C++20~)|(작성중)|

# 3자 비교

|항목|내용|
|--|--|
|`three_way_comparable` (C++20~)<br/>`three_way_comparable_with`(C++20~)|(작성중)|
|`partial_ordering` (C++20~)|(작성중)| 
|`weak_ordering` (C++20~)|(작성중)| 
|`strong_ordering` (C++20~)|(작성중)|  
|`is_eq, is_neq` (C++20~)|(작성중)
|`is_lt, is_lteq, is_gt, is_gteq` (C++20~)|(작성중)|  
|`compare_three_way` (C++20~)|(작성중)|
|`compare_three_way_result` (C++20~)|(작성중)|
|`common_comparison_category` (C++20~)|(작성중)|
|`strong_order` (C++20~)|(작성중)|
|`weak_order` (C++20~)|(작성중)|
|`partial_order` (C++20~)|(작성중)|
|`compare_strong_order_fallback` (C++20~)|(작성중)|
|`compare_weak_order_fallback` (C++20~)|(작성중)|
|`compare_partial_order_fallback` (C++20~)|(작성중)|

# 가변 인자

[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 처리하는 예는 [가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 참고하기 바랍니다.

|항목|내용|
|--|--|
|[va_start()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 액세스 시작 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|[va_arg()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 추출 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|[va_end()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90) 사용 종료 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)|
|[va_list](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)에 대한 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)|
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
|`timespec` (C++17~)|초(`tv_sec`)와 나노초(`tv_nsec`	)로 구분합니다.|
