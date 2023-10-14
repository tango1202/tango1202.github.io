---
layout: single
title: "#3. [모던 C++ STL] 유틸리티"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 일반 유틸리티

|항목|내용|
|--|--|
|`swap()`|바꿔치기 합니다.|
|[exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/) (C++14~)|주어진 값을 바꾸고 이전값을 리턴합니다. 이동 생성자와 이동 대입 연산자 구현에 활용할 수 있습니다.|

# 타입 변환

|항목|내용|
|--|--|
[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move) (C++11~)|좌측값을 우측값으로 형변환 합니다.|
|[move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept) (C++11~)|nothrow 보증이 되는 경우에만 `&&`로 형변환 합니다.|
|[forword()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward) (C++11~)|값 카테고리를 유지하며 인자를 다른 함수에 전달합니다.|
|`forward_like()` (C++23)|(작성중)|
|[declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval) (C++11~)|주어진 타입을 참조 타입으로 변환하여, 참조 타입 표현식으로 변경해 줍니다.|
|`as_const(T& param)` (C++17~)|인자 `param`에 `const`를 붙여 `const T&`로   변환합니다.|
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
|`in_place` (C++17~)<br/>`in_place_type` (C++17~)<br/>`in_place_index` (C++17~)<br/>`in_place_t` (C++17~)<br/>`in_place_index_t` (C++17~)|(작성중)|

# 프로그램 지원

|항목|내용|
|--|--|
|`abort()`|프로그램을 비정상 종료 시킵니다.[terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 참고|
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

가변 인자를 처리하는 예는 [가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 참고하기 바랍니다.

|항목|내용|
|--|--|
|`va_start`|가변 인자 액세스 시작 매크로 함수|
|`va_arg`|가변인자 추출 매크로 함수|
|`va_end`|가변 인자 사용 종료 매크로 함수|
|`va_list`|가변 인자에 대한 `typedef`|
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
