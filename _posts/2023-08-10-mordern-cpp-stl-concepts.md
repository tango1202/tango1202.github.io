---
layout: single
title: "#40. [모던 C++ STL] (C++20~) concepts"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [same_as, derived_from, convertible_to, integral, floating_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%ED%95%B5%EC%8B%AC-%EC%96%B8%EC%96%B4-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [equality_comparable, three_way_comparable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B9%84%EA%B5%90-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [movable, copyable, semiregular, regular](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EA%B0%9C%EC%B2%B4-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [invocable, predicate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%ED%95%A8%EC%88%98%EC%9E%90-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [forward_iterator, predicate, bidirectional_iterator, random_access_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)등이 추가되었습니다.

# 핵심 언어 컨셉

|항목|내용|
|--|--|
|`same_as<T, U>` (C++20~)|`T`, `U` 타입이 같습니다.|
|`derived_from<Base, Derived>` (C++20~)|`Base`는 `Derived`의 부모 개체이며, `Derived`는 `Base`로 변환될 수 있습니다.|
|`convertible_to<From, To>` (C++20~)|`From`은 `To`로 변환될 수 있습니다.|
|`common_reference_with` (C++20~)|(작성중)|
|`common_with` (C++20~)|(작성중)|
|`integral<T>` (C++20~)|`T`는 정수 타입(`bool`, `char`, `signed char`, `unsigned char`, `wchar_8`, `char8_t`, `char16_t`, `char32_t`, `short`, `unsigned short`, `int`, `unsigned int`, `long`, `unsigned long`, `long long`, `unsigned long long`)입니다.| 
|`signed_integral<T>` (C++20~)|`integral<T>` 이고 부호가 있습니다.| 
|`unsigned_integral<T>` (C++20~)|`integral<T>` 이고 부호가 없습니다.|
|`floating_point<T>` (C++20~)|`T`는 실수 타입(`float`, `double`, `long double`)입니다.|
|`assignable_from<Left, Right>` (C++20~)|`Left` 타입에 `Right` 타입을 [복사 대입](??)이나 [이동 대입](??)할 수 있습니다.|
|`swappable<T>, swappable_with<T, U>` (C++20~)|`T`를 바꿔치기 할 수 있습니다.<br/>`T`와 `U`를 바꿔치기 할 수 있습니다.|
|`destructible<T>` (C++20~)|`T`를 [예외 발생](??) 없이 소멸할 수 있습니다.|
|`constructible_from<T, ... Args>` (C++20~)|`T`를 [예외 발생](??) 없이 소멸할 수 있고, `Args`로 생성할 수 있습니다.|
|`default_initializable<T>` (C++20~)|`T`를 [예외 발생](??) 없이 소멸할 수 있고, `T{}`로 생성할 수 있습니다.|
|`move_constructible<T>` (C++20~)|`T`를 [예외 발생](??) 없이 소멸할 수 있고, `T`를 [이동 생성](??)할 수 있습니다.|
|`copy_constructible<T>` (C++20~)|`T`를 [예외 발생](??) 없이 소멸할 수 있고, `T`를 [이동 생성](??)할 수 있고, `T`를 [복사 생성](??)할 수 있습니다.|

# 비교 컨셉

|항목|내용|
|--|--|
|`boolean-testable` (C++20~)|(작성중)|
|`equality_comparable<T>, equality_comparable_with<T, U>` (C++20~)|`T` 혹은 `T`와 `U`간에 `==`, `!=` 를 할 수 있습니다.|
|`totally_ordered<T>, totally_ordered_with<T, U>` (C++20~)|`T` 혹은 `T`와 `U`간에 `==`, `!=`, `<`, `>`, `<=`, `>=`를 할 수 있습니다.|
|`three_way_comparable<T>, three_way_comparable_with<T, U>`(C++20~)|`T` 혹은 `T`와 `U`간에 `<=>`를 할 수 있습니다.|

# 개체 컨셉

|항목|내용|
|--|--|
|`movable<T>` (C++20~)|`swappable<T>`이고, `T`를 [예외 발생](??) 없이 소멸할 수 있고, `T`를 [이동 연산](??)할 수 있습니다.|
|`copyable<T>` (C++20~)|`movable<T>`이고, [복사 연산](??)이 가능합니다.|
|`semiregular<T>` (C++20~)|`copyable<T>`이고, `default_initializable<T>`입니다. 즉, [기본 생성자](??), [복사 생성자](??), [이동 생성자](??), [복사 대입 연산자](??), [이동 대입 연산자](??), [예외 발생](??) 없는 [소멸자](??)이고, `swappable<T>`입니다.|
|`regular<T>` (C++20~)|`semiregular<T>`이고, `==`, `!=` 를 할 수 있습니다.|

# 함수자 컨셉

|항목|내용|
|--|--|
|`invocable<F, ... Args>, regular_invocable<F, ... Args>` (C++20~)|`F(Args)`로 호출할 수 있습니다.|
|`predicate<F, ... Args>` (C++20~)|`regular_invocable<F, ... Args>`이고, `bool`을 리턴합니다.|
|`relation` (C++20~)|(작성중)|
|`equivalence_relation` (C++20~)|(작성중)|
|`strict_weak_order` (C++20~)|(작성중)|

# 이터레이터 컨셉 

|항목|내용|
|--|--|
|`indirectly_readable` (C++20~)|(작성중)|
|`indirectly_writable` (C++20~)|(작성중)|
|`weakly_incrementable` (C++20~)|(작성중)|
|`incrementable` (C++20~)|(작성중)|
|`input_or_output_iterator` (C++20~)|(작성중)|
|`sentinel_for` (C++20~)|(작성중)|  
|`sized_sentinel_for` (C++20~)|(작성중)|  
|`input_iterator` (C++20~)|(작성중)|  
|`output_iterator` (C++20~)|(작성중)| 
|`forward_iterator<I>` (C++20~)|`++`, `++`, `*`, `==`, `!=`을 할 수 있습니다.| 
|`bidirectional_iterator<I>` (C++20~)|`forward_iterator<I>`이고, `--`을 할 수 있습니다.| 
|`random_access_iterator<I>` (C++20~)|`bidirectional_iterator<I>`이고, `+=`, `+`, `-=`, `-`, `[]`, `<`, `<=`, `>`, `>=`을 할 수 있습니다.| 
|`contiguous_iterator` (C++20~)|(작성중)|  

# 유틸리티 컨셉 

|항목|내용|
|--|--|
|`indirectly_unary_invocable` (C++20~)<br/>`indirectly_regular_unary_invocable` (C++20~)|(작성중)|
|`indirect_unary_predicate` (C++20~)|(작성중)|
|`indirect_binary_predicate` (C++20~)|(작성중)|
|`indirect_equivalence_relation` (C++20~)|(작성중)| 
|`indirect_strict_weak_order` (C++20~)|(작성중)|
|`indirectly_movable` (C++20~)|(작성중)|
|`indirectly_movable_storable` (C++20~)|(작성중)|
|`indirectly_copyable` (C++20~)|(작성중)|
|`indirectly_copyable_storable` (C++20~)|(작성중)|
|`indirectly_swappable` (C++20~)|(작성중)|  
|`indirectly_comparable` (C++20~)|(작성중)|  
|`permutable` (C++20~)|(작성중)|  
|`mergeable` (C++20~)|(작성중)|  
|`sortable` (C++20~)|(작성중)| 
|`indirect_result_t` (C++20~)|(작성중)|
|`projected` (C++20~)|(작성중)| 

# 범위 컨셉

|항목|내용|
|--|--|
|`ranges::range` (C++20~)|(작성중)|
|`ranges::borrowed_range` (C++20~)|(작성중)|
|`ranges::sized_range` (C++20~)|(작성중)|
|`ranges::view` (C++20~)|(작성중)|
|`ranges::input_range` (C++20~)|(작성중)| 
|`ranges::output_range` (C++20~)|(작성중)|
|`ranges::forward_range` (C++20~)|(작성중)|
|`ranges::bidirectional_range` (C++20~)|(작성중)|
|`ranges::random_access_range` (C++20~)|(작성중)|
|`ranges::contiguous_range` (C++20~)|(작성중)|
|`ranges::common_range` (C++20~)|(작성중)|
|`ranges::viewable_range` (C++20~)|(작성중)|
|`ranges::constant_range` (C++20~)|(작성중)|
