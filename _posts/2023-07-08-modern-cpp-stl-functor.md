---
layout: single
title: "#8. [모던 C++ STL] 함수자"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) 기존에 제공되던 [함수자 타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)([unary_function, binary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)등), [바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)([bind1st(), bind2nd()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)등), [어뎁터와 부정자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)([mem_fun(), mem_fun_ref(), ptr_fun(), not1(), not2(), unary_negate(), binary_negate(), pointer_to_unary_function(), pointer_to_binary_function()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 등)가 [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D), [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function), [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind), [mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn)등으로 대체되어 deprecate 되었습니다.
> * (C++11~) [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function)이 추가되어 `()`로 호출 가능한 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 저장할 수 있습니다.
> * (C++11~) [mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn)이 추가되었습니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 있는 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)도 호출하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 좀 더 간편하게 만들어 줍니다.
> * (C++11~) [reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper)가 추가되어 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이나 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 안되는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 래핑할 수 있습니다.
> * (C++11~) [ref(), cref()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#ref-cref)가 추가되어 [reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper) 개체를 좀더 간편하게 생성할 수 있습니다.
> * (C++11~) [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)가 추가되어 `placeholders::_1`(*GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨*)와 같은 자리 표시자와 조합하여 특정 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)만을 사용하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 생성할 수 있습니다.
> * (C++11~) [is_bind_expression](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_bind_expression)가 추가되어 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)로 생성한 함수인지 검사할 수 있습니다.
> * (C++11~) [is_placeholder](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_placeholder)가 추가되어 자리 표시자를 사용했는지 검사할 수 있습니다.
> * (C++14~) [bit_not](https://tango1202.github.io/cpp-stl/modern-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90)이 추가되었습니다.
> * (C++17~) [문자열 검색기](https://tango1202.github.io/cpp-stl/modern-cpp-stl-functor/#c17-searcher)(`default_searcher`, `boyer_moore_searcher`, `boyer_moore_horspool_searcher`)가 추가되어 [search()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#search)의 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)로 사용할 수 있습니다.
> * (C++17~) [not_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-functor/#%EB%B6%80%EC%A0%95%EC%9E%90)이 추가되었습니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)(단항, 이항 제한이 없습니다.)로 전달한 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 부정하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 만듭니다.
> * (C++17~) [invoke()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c17-invoke)가 추가되어 일반 함수와 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 동일한 방식으로 호출할 수 있습니다.
> * (C++20~) [invoke_r()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-functor/#%ED%95%A8%EC%88%98-%EB%9E%98%ED%8D%BC)이 추가되었습니다.
> * (C++20~) [unwrap_reference, unwrap_ref_decay](https://tango1202.github.io/cpp-stl/modern-cpp-stl-functor/#c11-%EC%B0%B8%EC%A1%B0-%EB%9E%98%ED%8D%BC)가 추가되었습니다.
> * (C++20~) [identity](https://tango1202.github.io/cpp-stl/modern-cpp-stl-functor/#c20-identity)가 추가되었습니다.

# 개요

C++11 부터 기존에 제공되던 [함수자 타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)([unary_function, binary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)등), [바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)([bind1st(), bind2nd()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)등), [어뎁터와 부정자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)([mem_fun(), mem_fun_ref(), ptr_fun(), not1(), not2(), unary_negate(), binary_negate(), pointer_to_unary_function(), pointer_to_binary_function()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 등)가 [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D), [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function), [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind), [mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn)등으로 대체되어 deprecate 되었습니다.




# 함수자 타입 특성 클래스

|항목|내용|
|--|--|
|[unary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits) (~C++11)|단항 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)|
|[binary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits) (~C++11)|이항 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)|

# 함수자

|항목|내용|
|--|--|
|`equal_to`|`arg1 == arg2`|
|`not_equal_to`|`arg1 != arg2`|
|`greater`|`arg1 > arg2`|
|[less](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)|`arg1 < arg2`|
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
|[not1()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) (~C++11)|단항 [조건자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 부정합니다.|
|[not2()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) (~C++11)|이항 [조건자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 부정합니다.|
|`not_fn()`(C++17~)|[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)(단항, 이항 제한이 없습니다.)로 전달한 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 부정하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 만듭니다.|

```cpp
// IsSame을 부정하는 함수자를 만듭니다.
auto IsDifferent{std::not_fn(IsSame)};
EXPECT_TRUE(IsDifferent(1, 1, 1) == false); // 인자가 여러개여도 됩니다. 
```

# 바인더

|항목|내용|
|--|--|
|[bind1st(op, x)](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94) (~C++11)<br/>`binder1st()` (~C++11)|알고리즘엔 단항 함수로 전달되며, `op(x, 요소)`로 이항 함수를 호출합니다.|
|[bind2nd(op, y)](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94) (~C++11)<br/>`binder2nd()` (~C++11)|알고리즘엔 단항 함수로 전달되며, `op(요소, y)`로 이항 함수를 호출합니다.|
|[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) (C++11~)|`placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, … `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)만을 사용하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 생성합니다.|
|[is_bind_expression](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_bind_expression) (C++11~)|[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)로 생성한 함수인지 검사합니다.|
|[is_placeholder](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_placeholder) (C++11~)|자리 표시자를 사용했는지 검사합니다.|
|[_1, _2, _3, _4](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) (C++11~)|자리 표시자 입니다.|  
|[bind_front()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c20-bind_front) (C++20~)|[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)처럼 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 생성합니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들을 순서대로 배치하므로, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 순서 변경이 없다면 간편하게 사용할 수 있습니다.|
|`bind_back()` (C++23~)|(작성중)|


# 함수 래퍼

|항목|내용|
|--|--|
|[function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function) (C++11~)|`()`로 호출 가능한 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 저장합니다.|
|[bad_function_call](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bad_function_call) (C++11~)|[function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function)에서 `()`로 호출할 대상이 없을 때 [bad_function_call](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bad_function_call) 예외를 방출합니다.|
|[mem_fun()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) (~C++11)<br/>`mem_fun_t` (~C++11)<br/>`mem_fun1_t` (~C++11)<br/>`const_mem_fun_t` (~C++11)<br/>`const_mem_fun1_t` (~C++11)|알고리즘에서 `f(x)`의 호출을 `x->f()` 처럼 호출되게 합니다.|
|[mem_fun_ref()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) (~C++11)<br/>`mem_fun_ref_t` (~C++11)<br/>`mem_fun1_ref_t` (~C++11)<br/>`const_mem_fun_ref_t` (~C++11)<br/>`const_mem_fun1_ref_t` (~C++11)|알고리즘에서 `f(x)`의 호출을 `x.f()` 처럼 호출되게 합니다.|
|[ptr_fun()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) (~C++11)|[바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90), [부정자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)와 일반 함수가 호환될 수 있도록 일반 함수를 [unary_function 이나 binary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits) 개체로 만듭니다.|
|`pointer_to_unary_function` (~C++11)|(작성중)|
|`pointer_to_binary_function` (~C++11)|(작성중)|
|[mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn) (C++11~)|[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 있는 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)도 호출하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 만들어 줍니다.|
|[invoke(Func, params...)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c17-invoke) (C++17~)|[invoke()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c17-invoke)가 추가되어 일반 함수와 멤[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 동일한 방식으로 호출할 수 있게 합니다. 일반 함수인 경우 `Func(params...)`를 호출하고, [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)인 경우 `params[1].Func(params[2]...)`을 호출합니다.|
|`invoke_r()` (C++20~)|(작성중)|
|`move_only_function` (C++23~)|(작성중)|
|`copyable_function` (C++26~)|(작성중)|
|`function_ref` (C++26~)|(작성중)|

# (C++11~) 참조 래퍼

|항목|내용|
|--|--|
|[reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper) (C++11~)|[복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이나 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 안되는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 래핑합니다.| 
|[ref()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#ref-cref) (C++11~)<br/>[cref()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#ref-cref) (C++11~)|[reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper) 개체를 생성합니다.| 
|`unwrap_reference` (C++20~)<br/>`unwrap_ref_decay` (C++20~)|(작성중)| 

# (C++17~) Searcher

C++17 부터는 문자열 검색기가 추가되어 [search()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#search)의 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)로 사용할 수 있습니다.

|항목|내용|
|--|--|
|`default_searcher` (C++17~)|기본 검색기 입니다.|
|`boyer_moore_searcher` (C++17~)|[Boyer-Moore 문자열 검색 알고리즘](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_string-search_algorithm)을 사용하는 검색기 입니다.| 
|`boyer_moore_horspool_searcher` (C++17~)|[Boyer-Moore-Horspool 문자열 검색 알고리즘](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore%E2%80%93Horspool_algorithm)을 사용하는 검색기 입니다.| 

다음은 `data`문자열에서 `sub`를 찾는 예입니다.

```cpp
std::string data{"Hello world."};
std::string sub{"wo"};

auto itr = std::search(
    data.begin(),
    data.end(),
    std::default_searcher(
        sub.begin(),
        sub.end()
    )
);

// data에서 sub를 찾았다면 오프셋을 구해봅니다.
if (itr != data.end()) {
    EXPECT_TRUE(std::distance(data.begin(), itr) == 6);
}
```

# (C++20~) Identity

|항목|내용|
|--|--|
|`identity` (C++20~)|(작성중)|