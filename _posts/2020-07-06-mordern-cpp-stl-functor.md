---
layout: single
title: "#6. [모던 C++ STL] 함수자"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 함수자 타입 특성 클래스

|항목|내용|
|--|--|
|`unary_function` (~C++11)|단항 함수자([함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits) 참고)|
|`binary_function`(~C++11)|이항 함수자([함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits) 참고)|

# 함수자

|항목|내용|
|--|--|
|`equal_to`|`arg1 == arg2`|
|`not_equal_to`|`arg1 != arg2`|
|`greater`|`arg1 > arg2`|
|`less`|`arg1 < arg2`([바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94) 참고)|
|`greater_equal`|`arg1 >= arg2`|
|`less_equal`|`arg1 <= arg2`|
|`logical_and`|`arg1 == arg2`|
|`logical_or`|`arg1 || arg2`|
|`logical_not`|`!arg`|
|`plus`|`arg1 + arg2`|
|`minus`|`arg1 - arg2`|
|`multiplies`|`arg1 * arg2`|
|`divides`|`arg1 / arg2`|
|`modulus`|`arg1 % arg2`|
|`negate`|`-arg`|
|`bit_and`|`arg1 & arg2`|
|`bit_or`|`arg1 | arg2`|
|`bit_xor`|`arg1 ^ arg2`|
|`bit_not` (C++14~)|`~arg`|

# 부정자

|항목|내용|
|--|--|
|`unary_negate` (~C++11)|(작성중)|
|`binary_negate` (~C++11)|(작성중)|
|`not1()` (~C++11)|단항 조건자의 반환값을 부정합니다.([아답터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 참고)|
|`not2()` (~C++11)|이항 조건자의 반환값을 부정합니다.([아답터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 참고)|
|`not_fn()`(C++17~)|(작성중)|

# 바인더

|항목|내용|
|--|--|
|`bind1st(op, x)` (~C++11)<br/>`binder1st()` (~C++11)|알고리즘엔 단항 함수로 전달되며, `op(x, 요소)`로 이항 함수를 호출합니다.([바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94) 참고)|
|`bind2nd(op, y)` (~C++11)<br/>`binder2nd()` (~C++11)|알고리즘엔 단항 함수로 전달되며, `op(요소, y)`로 이항 함수를 호출합니다.([바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94) 참고)|
|[bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind) (C++11~)|`placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, … `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.|
|[is_bind_expression](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#is_bind_expression) (C++11~)|`bind()`로 생성한 함수인지 검사합니다.|
|[is_placeholder](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#is_placeholder) (C++11~)|자리 표시자를 사용했는지 검사합니다.|
|`_1, _2, _3, _4` (C++11~)|자리 표시자 입니다.([bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind) 참고)|  
|`bind_front()` (C++20~)|(작성중)|
|`bind_back()` (C++23~)|(작성중)|


# 함수 래퍼

|항목|내용|
|--|--|
|[function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function) (C++11~)|`()`로 호출 가능한 함수자를 저장합니다.|
|[bad_function_call](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bad_function_call) (C++11~)|`function`에서 `()`로 호출할 대상이 없을 때 `bad_function_call` 예외를 방출합니다.|
|`mem_fun()` (~C++11)<br/>`mem_fun_t` (~C++11)<br/>`mem_fun1_t` (~C++11)<br/>`const_mem_fun_t` (~C++11)<br/>`const_mem_fun1_t` (~C++11)|알고리즘에서 `f(x)`의 호출을 `x->f()` 처럼 호출되게 합니다.([아답터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 참고)|
|`mem_fun_ref()` (~C++11)<br/>`mem_fun_ref_t` (~C++11)<br/>`mem_fun1_ref_t` (~C++11)<br/>`const_mem_fun_ref_t` (~C++11)<br/>`const_mem_fun1_ref_t` (~C++11)|알고리즘에서 `f(x)`의 호출을 `x.f()` 처럼 호출되게 합니다.([아답터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 참고)|
|`ptr_fun()` (~C++11)|바인더, 아답터, 부정자와 일반 함수가 호환될 수 있도록 일반 함수를 `unary_function`이나 `binary_function` 개체로 만듭니다.([아답터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 참고)|
|`pointer_to_unary_function` (~C++11)|(작성중)|
|`pointer_to_binary_function` (~C++11)|(작성중)|
|[mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn) (C++11~)|인자가 있는 멤버 함수도 호출하는 함수자를 만들어 줍니다.|
|`move_only_function` (C++23~)|(작성중)|
|`copyable_function` (C++26~)|(작성중)|
|`function_ref` (C++26~)|(작성중)|

# 참조 레퍼

|항목|내용|
|--|--|
|[reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper) (C++11~)|복사나 대입이 안되는 참조자를 래핑합니다.| 
|[ref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref) (C++11~)<br/>[cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref) (C++11~)|`reference_wrapper` 개체를 생성합니다.| 
|`unwrap_reference` (C++20~)<br/>`unwrap_ref_decay` (C++20~)|(작성중)| 

# Searcher

|항목|내용|
|--|--|
|`default_searcher` (C++17~)|(작성중)|
|`boyer_moore_searche` (C++17~)|(작성중)| 
|`boyer_moore_horspool_searcher` (C++17~)|(작성중)| 

# Invoke

|항목|내용|
|--|--|
|`invoke()` (C++17~)|(작성중)|
|`invoke_r()` (C++20~)|(작성중)|

# Identity

|항목|내용|
|--|--|
|`identity` (C++20~)|(작성중)|