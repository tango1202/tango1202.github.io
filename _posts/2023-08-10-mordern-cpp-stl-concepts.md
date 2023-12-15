---
layout: single
title: "#40. [모던 C++ STL] (C++20~) concepts"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [same_as, derived_from, convertible_to, integral, floating_point, assignable_from, swappable, destructible, constructible_from, default_initializable, move_constructible, copy_constructible](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%ED%95%B5%EC%8B%AC-%EC%96%B8%EC%96%B4-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [equality_comparable, three_way_comparable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B9%84%EA%B5%90-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [movable, copyable, semiregular, regular](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EA%B0%9C%EC%B2%B4-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [invocable, predicate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%ED%95%A8%EC%88%98%EC%9E%90-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [forward_iterator, predicate, bidirectional_iterator, random_access_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [ranges::forward_range, ranges::bidirectional_range, ranges::random_access_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89)등이 추가되었습니다.
> * (C++20~) [유틸리티 컨셉](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0-%EC%BB%A8%EC%85%89)이 추가되었습니다.

# 개요

[컨셉](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)은 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 주어 특정 타입만 사용하게 할 수 있습니다.

```cpp
template<typename T>
requires std::integral<T> || std::floating_point<T> // T 정수 타입이나 실수 타입이어야 하합니다. 
T Add_20(T a, T b) {
    return a + b;
}
```

자세한 사용법은 [컨셉](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 참고하기 바라며, STL에서는 기본으로 사용할 수 있는 다양한 [컨셉](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)들을 제공합니다.

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
|`assignable_from<Left, Right>` (C++20~)|`Left` 타입에 `Right` 타입을 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이나 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)할 수 있습니다.|
|`swappable<T>, swappable_with<T, U>` (C++20~)|`T`를 바꿔치기 할 수 있습니다.<br/>`T`와 `U`를 바꿔치기 할 수 있습니다.|
|`destructible<T>` (C++20~)|`T`를 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 없이 소멸할 수 있습니다.|
|`constructible_from<T, ... Args>` (C++20~)|`T`를 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 없이 소멸할 수 있고, `Args`로 생성할 수 있습니다.|
|`default_initializable<T>` (C++20~)|`T`를 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 없이 소멸할 수 있고, `T{}`로 생성할 수 있습니다.|
|`move_constructible<T>` (C++20~)|`T`를 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 없이 소멸할 수 있고, `T`를 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)할 수 있습니다.|
|`copy_constructible<T>` (C++20~)|`T`를 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 없이 소멸할 수 있고, `T`를 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)할 수 있고, `T`를 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)할 수 있습니다.|

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
|`movable<T>` (C++20~)|`swappable<T>`이고, `T`를 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 없이 소멸할 수 있고, `T`를 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)할 수 있습니다.|
|`copyable<T>` (C++20~)|`movable<T>`이고, 복사 연산이 가능합니다.|
|`semiregular<T>` (C++20~)|`copyable<T>`이고, `default_initializable<T>`입니다. 즉, [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 없는 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)이고, `swappable<T>`입니다.|
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
|[indirectly_readable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)|`*`을 이용하여 간접적으로 읽을 수 있습니다.|
|`indirectly_writable` (C++20~)|(작성중)|
|`weakly_incrementable` (C++20~)|(작성중)|
|`incrementable` (C++20~)|(작성중)|
|[input_or_output_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)| `++`, `*`을 할 수 있습니다.|
|`sentinel_for` (C++20~)|(작성중)|  
|[sized_sentinel_for](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)|[이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/)의 시작(*`begin()`*)과 끝(*`end()`*)의 차로 요소의 갯수를 구할 수 있습니다.|  
|[input_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)|[input_or_output_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)이고, [indirectly_readable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)입니다.|  
|`output_iterator` (C++20~)|(작성중)| 
|[forward_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)|`++`, `++`, `*`, `==`, `!=`을 할 수 있습니다.| 
|[bidirectional_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)|[forward_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)이고, `--`을 할 수 있습니다.| 
|[random_access_iterator] (C++20~)|[bidirectional_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)이고, `+=`, `+`, `-=`, `-`, `[]`, `<`, `<=`, `>`, `>=`을 할 수 있습니다.| 
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

# 범위(Range) 컨셉

|항목|내용|
|--|--|
|[ranges::range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|[범위(Range)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/)입니다. [ranges::begin](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/#%ED%8F%AC%EC%9D%B8%ED%8A%B8-%EA%B0%9C%EC%B2%B4point-object-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)과 [ranges::end](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/#%ED%8F%AC%EC%9D%B8%ED%8A%B8-%EA%B0%9C%EC%B2%B4point-object-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)가 가능해야 합니다.|
|`ranges::borrowed_range` (C++20~)|(작성중)|
|[ranges::sized_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|상수 시간에 크기를 알 수 있는 [범위(Range)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/)입니다.|
|[ranges::view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|[범위(Range)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/) 요소들을 재배치/필터링한 일종의 [범위(Range)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/)입니다.|
|[ranges::input_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|[ranges::range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89)이고 [input_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)를 제공합니다.| 
|`ranges::output_range` (C++20~)|(작성중)|
|[ranges::forward_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|[ranges::input_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89)이고 [forward_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)를 제공합니다.|
|[ranges::bidirectional_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|[ranges::forward_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89)이고, [bidirectional_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89)를 제공합니다.|
|[ranges::random_access_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|랜덤 접근을 지원하는 [범위(Range)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/)입니다.|
|`ranges::contiguous_range` (C++20~)|(작성중)|
|[ranges::common_range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89) (C++20~)|[ranges::range](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-concepts/#%EB%B2%94%EC%9C%84range-%EC%BB%A8%EC%85%89)이고, [ranges::begin](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/#%ED%8F%AC%EC%9D%B8%ED%8A%B8-%EA%B0%9C%EC%B2%B4point-object-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)과 [ranges::end](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-range/#%ED%8F%AC%EC%9D%B8%ED%8A%B8-%EA%B0%9C%EC%B2%B4point-object-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)가 같은 타입이어야 합니다.|
|`ranges::viewable_range` (C++20~)|(작성중)|
|`ranges::constant_range` (C++20~)|(작성중)|
