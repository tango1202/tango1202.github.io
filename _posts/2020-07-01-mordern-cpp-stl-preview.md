---
layout: single
title: "#1. [모던 C++ STL] 개요"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

C++11 STL은,

템플릿 메타 프로그래밍과 우측값 참조와 중괄호 초기화, 해시 처리를 위한 부분들이 보강되었으며,

1. 스마트 포인터
2. initializer_list
3. tuple 
4. function
5. 추가 컨테이너
6. 추가 알고리즘
7. 유니코드 문자열
8. 동시성 지원
9. random, ratio
10. 진단 개선
11. chrono
12. 정규 표현식 
13. codecvt
   
이 추가 되었습니다.

# 언어 지원 라이브러리

|항목|내용|
|--|--|
|[type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_index/)|`type_index`는 `type_info`의 래퍼로서 컨테이너에서 관리할 수 있게 합니다.|

# 컨셉 라이브러리

# 메타 프로그래밍 라이브러리

|항목|내용|
|--|--|
|[타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/)|`type_traits`는 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 합니다.|



# 메모리 관리 라이브러리

|항목|내용|
|--|--|
|[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)|`unique_ptr`은 소유권 이전용 스마트 포인터입니다. 기존 `auto_ptr`을 대체합니다.<br/>[default_delete](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#default_delete)를 스마트 포인터의 `deleter`로 사용할 수 있습니다.|
|[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr)|`shared_ptr`은 소유권 공유용 스마트 포인터입니다.<br/>[enable_shared_from_this](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#enable_shared_from_this)를 이용하여 `shared_ptr`이 관리하는 개체로부터 `shared_ptr`을 만들 수 있습니다.<br/>[owner_less](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#owner_less)를 이용하여 소유권 개체의 주소로 비교할 수 있습니다.<br/>[bad_weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#bad_weak_ptr) 예외를 방출합니다.|
|[maked_shared()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#make_shared)|`make_shared`를 이용하여 `shared_ptr`을 효율적으로 생성할 수 있습니다.|
|[shared_ptr 형변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr-%ED%98%95%EB%B3%80%ED%99%98)|`const_pointer_cast()`, `static_pointer_cast()`, `dynamic_pointer_cast()`, `reinterpret_pointer_cast()` 로 `shared_ptr`의 관리 개체를 형변환 할 수 있습니다.|
|[weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr)|`weak_ptr`은 `shared_ptr`의 상호 참조 문제를 해결합니다.|
|[pointer_traits](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-memory/#pointer_traits)|`pointer_traits`는 포인터와 유사한 타입들을 다루는 표준화된 방법을 제공합니다.|
|[addressof()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-memory/#addressof)|`addressof()`는 operator &()`가 오버로딩 되었어도 실제 주소를 리턴합니다.|
|[align()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-memory/#align)|`align()`은 메모리 정렬된 포인터를 구합니다.|

# 유틸리티 라이브러리

|항목|내용|
|--|--|
|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/) 지원|[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)는 좌측값을 우측값으로 형변환합니다.<br/>[move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept)는 nothrow 보증이 되는 경우에만 `&&`로 형변환 합니다.<br/>[forword()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward)는 값 카테고리를 유지하며 인자를 다른 함수에 전달합니다.|
|[declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval)|`declval()`은 주어진 타입을 참조 타입으로 변환하여, 참조 타입 표현식으로 변경해 줍니다.|
|[initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)|`initializer_list` 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다.|
|[tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)|`tuple`은 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.<br>[get()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#get) 으로 요소에 접근합니다.<br/>[make_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#make_tuple), [tie()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#tie), [forward_as_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#forward_as_tuple) 을 이용하여 `tuple`을 쉽게 생성할 수 있습니다.<br/>[tuple_cat()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#tuple_cat) 으로 두개의 `tuple`을 합칩니다.<br/>[tuple_size](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#tuple_size)로 컴파일 타임에 `tuple`요소 갯수를 구합니다.<br/>[tuple_element](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#tuple_element)을 이용하여 `tuple` 각 요소에 대한 타입을 구합니다.<br/>[piecewise_construct](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-tuple/#piecewise_construct)를 이용하여 `pair`의 개체 생성시 `tuple`의 요소들로 개체 생성자를 호출합니다.|




hash

std::function
mem_fn
reference_wrapper
bad_function_call
is_bind_expression  
is_placeholder
bind
ref
cref
  

# 컨테이너 라이브러리
std::array
std::forward_list
std::undordered_map
std::unordered_set

# 이터레이터 라이브러리

next
prev
std::move_iterator

# 범위 라이브러리

# 알고리즘 라이브러리

std::all_of, std::any_of, std::none_of,
std::find_if_not,
std::copy_if, std::copy_n,
std::move, std::move_backward,
std::random_shuffle, std::shuffle,
std::is_partitioned, std::partition_copy, std::partition_point,
std::is_sorted, std::is_sorted_until,
std::is_heap, std::is_heap_until,
std::minmax, std::minmax_element,
std::is_permutation,
std::iota,
std::uninitialized_copy_n


# 문자열 라이브러리

유니코드 문자열

# 동시성 라이브러리


<thread>
<mutex>
<condition_variable>
<atomic>
<future>

# 수치 라이브러리

<random>
<ratio>

# 진단 라이브러리

std::exception_ptr
std::error_code
std::error_condition
<system_error>

# 날짜 / 시간 라이브러리
<chrono>

# 정규 표현식 라이브러리
<regex>

# 현지화 라이브러리

<codecvt>

# 입출력 라이브러리

# 파일 시스템 라이브러리

# C 라이브러리 지원

<cfenv>
<cinttype>
<cstdint>
<cuchar>

