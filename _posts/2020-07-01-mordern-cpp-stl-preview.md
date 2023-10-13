---
layout: single
title: "#1. [모던 C++ STL] 개요"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

모던 C++ STL은,

1. C++11

    템플릿 메타 프로그래밍([type_traits](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/), [ratio](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-ratio/)), 스마트 포인터([unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr), [weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr)), 우측값 참조와 이동 연산([move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move), [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward)), 참조 타입 표현식([declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval)), 중괄호 초기화([initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)), 유틸리티([tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)), 래퍼([function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function), [mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn), [reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper), [ref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref), [cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref)), 바인더([bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)), 해시([hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-hash/)), 컨테이너([array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/), [forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/), [unordered_map](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/), [unordered_multimap](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/), [unordered_set](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/), [unordered_multiset](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/)), 문자열([u16string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [u32string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/)), [난수 생성기](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%EB%82%9C%EC%88%98-%EC%83%9D%EC%84%B1%EA%B8%B0), [chrono](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), 정규 표현식([regex_match()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_match), [regex_search()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_search), [regex_replace()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_replace)), 동시성([thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread), [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex), [lock_guard](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock_guard---mutex%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0), [unique_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock), [call_once()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#call_once-once_flag), [condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable), [atomic](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-atomic/#atomic), [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise), [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future), [packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task), [async()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#async)) 처리를 위한 부분들이 보강되었습니다.

2. C++14

    컴파일 타임 정수 타입 시퀀스([integer_sequence](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-integer_sequence/)), 스마트 포인터 유틸리티([make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)), 
    [tuple의 타입 기반 get()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#c14-%ED%83%80%EC%9E%85-%EA%B8%B0%EB%B0%98-get), [is_final()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1), [연관 컨테이너의 이종 탐색](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-heterogeneous-lookup/), 이터레이터 범위 접근([rbegin(), rend(), cbegin(), cend(), crbegin(), crend()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)), [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/), [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/), 쓰레드 소유권 공유([shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock), [shared_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)), [quoted()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%9E%85%EC%B6%9C%EB%A0%A5-%EC%A0%9C%EC%96%B4) 가 추가되었습니다.


STL

# 제거

std::iterator,
std::raw_storage_iterator,
std::get_temporary_buffer,
return_temporary_buffer
std::is_literal_type,
std::result_of,
all of <codecvt>
the obsolete iostreams aliases ~C++17

# Utility types

std::tuple:
std::apply
std::make_from_tuple
deduction guides
std::any
std::optional <optional>
std::variant

Searcher
default_searcher
boyer_moore_searche
boyer_moore_horspool_searcher

std::as_const
`to_underlying()`


string_view
std::not_fn
invoke
<filesystem> 
std::byte
std::clamp
sample(https://github.com/AnthonyCalandra/modern-cpp-features#stdsample)
std::reduce, transform_reduce
std::gcd
std::lcm
for_each_n

<charconv> from_char_result
https://github.com/AnthonyCalandra/modern-cpp-features#string-conversion-tofrom-numbers
std::to_chars
to_chars_result
std::from_chars
from_chars_result
chars_format

쓰레드 관련
hardware_destructive_interference_size
shared_mutex
scope_lock scoped_lock
is_allways_lock_free

크로노
floor, ceil, round, abs(time_spec
rounding functions for std::chrono::duration and std::chrono::time_point)
C스타일 시간 유틸리티 std::timespec_get

unorderd_map
erase_f, insert_or_assign
extract
merge
try_implace

# 기타

<execution> sequenced_policy parallel_policy parallel_unsequenced_policy unsequenced_policy seq par unseq is_execution_policy

prefix-sum-algorithms https://github.com/AnthonyCalandra/modern-cpp-features#prefix-sum-algorithms

bool_constant

# Memory management

<memory_resource>
align_val_t
std::launder
uninitialized memory algorithms
std::destroy_at
std::destroy
std::destroy_n
construct_at
std::uninitialized_move
std::uninitialized_value_construct
weak_from_this
할당자
std::pmr::memory_resource and std::polymorphic_allocator
std::aligned_alloc
transparent std::owner_less
array support for std::shared_ptr
allocation functions with explicit alignment

# Compile-time programming

std::conjunction/std::disjunction/std::negation
type trait variable templates (xxx_+v)
std::is_swappable
is_invocable
is_aggregate
std::has_unique_object_representations

# Algorithms

parallel algorithms and execution policies
std::inclusive_scan
std::exclusive_scan
transform_exclusive_scan
transform_inclusive_scan

# Iterators and containers

map/set extract and map/set merge(slicing for maps and sets https://github.com/AnthonyCalandra/modern-cpp-features#splicing-for-maps-and-sets)
map/unordered_map try_emplace and insert_or_assign
contiguous iterators (LegacyContiguousIterator)
범위 접근. non-member std::size/std::empty/std::data
# Numerics
특수 수학 함수 mathematical special functions 
3D std::hypot
# Others
cache line interface

std::uncaught_exceptions


모호성 해소
in_place
in_place_type
in_place_index
in_place_t
in_place_index_t

# 자가 진단
bad_any_cast
bad_optional_access
bad_variant_access
uncaught_exception

# 언어 지원 라이브러리

|항목|내용|
|--|--|
|[기본 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EA%B8%B0%EB%B3%B8-%ED%83%80%EC%9E%85)|`size_t`, `ptrdiff_f`, `NULL` 등 기본 타입을 제공합니다.<br/><br/>**(C++11~)**<br/>[고정 너비 정수 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%A0%95%EC%88%98-%ED%83%80%EC%9E%85)이 추가되었습니다.<br/>[고정 너비 실수 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%8B%A4%EC%88%98-%ED%83%80%EC%9E%85)이 추가되었습니다.|
|[타입 최대/최소값](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%ED%83%80%EC%9E%85-%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C%EA%B0%92)|`numeric_limits`를 이용하여 타입의 최대, 최소값을 알 수 있습니다.|
|[런타임 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EB%9F%B0%ED%83%80%EC%9E%84-%ED%83%80%EC%9E%85)|`type_info`를 이용하여 개체의 타입 정보를 알 수 있습니다.<br/><br/>**(C++11~)**<br/>[type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_index/) `type_info`의 래퍼로서 `type_info`를 컨테이너에서 관리할 수 있게 합니다.|
|[initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) (C++11~)|**(C++11~)**<br/>[initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다.|
|[프로그램 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90)|`abort()`, `exit()`등 프로그램 종료 관련 함수들과 시그널 처리 함수를 제공합니다.|
|[표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/) (C++14~)|**(C++14~)**<br/>[표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.|
|[소스 코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EC%86%8C%EC%8A%A4-%EC%BD%94%EB%93%9C) (C++20~)|(작성중)|
|[3자 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#3%EC%9E%90-%EB%B9%84%EA%B5%90) (C++20~)|(작성중)|
|[코루틴](??) (C++20~)|(작성중)|
|`version` (C++20~)|**(C++20~)**<br/>라이브러리 테스트를 위한 매크로를 지원합니다. 자세한 내용은 [cppreference](https://en.cppreference.com/w/cpp/feature_test#Library_features)를 참고하세요.|

# 컨셉 라이브러리

(C++20~) (작성중)

# 메타 프로그래밍 라이브러리

|항목|내용|
|--|--|
|[타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/) (C++11~)|**(C++11~)**<br/>[type_traits](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/)는 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 합니다.<br/><br/>**(C++14~)**<br/>[is_final]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1))이 추가되어 `final`클래스 타입인지 검사합니다.
|[ratio](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-ratio/) (C++11~)|**(C++11~)**<br/>[ratio](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-ratio/) 개체는 분자와 분모를 따로 저장하여 유리 분수를 표현하며, 유틸리티들을 이용하여 컴파일 타임 유리수(정수와 분수) 연산을 지원합니다.|
|[integer_sequence](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-integer_sequence/) (C++14~)|**(C++141~)**<br/>[integer_sequence](https://tango1202.github.io/mordern-cpp-stl/) 는 컴파일 타임에 정수 타입의 시퀀스를 만듭니다.|

# 메모리 관리 라이브러리

|항목|내용|
|--|--|
|[C스타일 메모리 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC)|`malloc()`, `free()`등 C스타일 메모리 할당/해제 함수를 제공합니다.|
|[저수준 메모리 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EC%A0%80%EC%88%98%EC%A4%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC)|`operator new`, `operator delete`, `set_new_handler()`등 C++ 스타일의 메모리 할당/해제시 제어하는 방법을 제공합니다.|
|[할당자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%ED%95%A0%EB%8B%B9%EC%9E%90)|[allocator](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-allocator/) 등 STL에서 개체 할당에 사용합니다.|
|[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/) (~C++11)|**(C++11~)**<br/>[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 `delete[]` 미지원, `lvalue` 복사 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다. 이제 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/) 을 사용합니다.|
|[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/) (C++11~)|**(C++11~)**<br/>[`unique_ptr`]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/))은 소유권 이전용 스마트 포인터입니다. 기존 `auto_ptr`을 대체합니다. `auto_ptr`은 `delete[]` 미지원, `lvalue` 복사 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다.<br/>[default_delete](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#default_delete)를 스마트 포인터의 `deleter`로 사용할 수 있습니다.<br/><br/>**(C++14~)**<br/>[make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique) 를 이용하여 `unique_ptr`을 효율적으로 생성할 수 있습니다.|
|[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr) (C++11~)|**(C++11~)**<br/>[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr)은 소유권 공유용 스마트 포인터입니다.<br/>[enable_shared_from_this](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#enable_shared_from_this)를 이용하여 `shared_ptr`이 관리하는 개체로부터 `shared_ptr`을 만들 수 있습니다.<br/>[owner_less](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#owner_less)를 이용하여 소유권 개체의 주소로 비교할 수 있습니다.<br/>`shared_ptr`에서 포인터가 잘못된 경우 [bad_weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#bad_weak_ptr) 예외를 방출합니다.<br/>[make_shared()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#make_shared) 를 이용하여 `shared_ptr`을 효율적으로 생성할 수 있습니다.<br/>[shared_ptr 형변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr-%ED%98%95%EB%B3%80%ED%99%98)(`const_pointer_cast()`, `static_pointer_cast()`, `dynamic_pointer_cast()`, `reinterpret_pointer_cast()`)을 이용하여 `shared_ptr`의 관리 개체를 형변환 할 수 있습니다.|
|[weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr) (C++11~)|**(C++11~)**[weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr)은 `shared_ptr`의 상호 참조 문제를 해결합니다.|
|[동적 메모리 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/) (C++11~)|**(C++11~)**<br/>[pointer_traits](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#pointer_traits)는 포인터와 유사한 타입들을 다루는 표준화된 방법을 제공합니다.<br/>[addressof()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#addressof)는 `operator &()`가 오버로딩 되었어도 실제 주소를 리턴합니다.<br/>[align()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#align)은 메모리 정렬된 포인터를 구합니다.|
|[가비지 컬렉터 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%ED%84%B0-%EC%A7%80%EC%9B%90) (C++11~C++23)|(작성중)|
|[메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4) (C++17~)|(작성중)|
|[초기화 되지 않은 스토리지](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%90%98%EC%A7%80-%EC%95%8A%EC%9D%80-%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80) (C++17~)|(작성중)|
|[메모리 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC) (C++20~)|(작성중)|
|[스마트 포인터 어뎁터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%8A%B8-%EC%96%B4%EB%8E%81%ED%84%B0) (C++23~)|(작성중)|
|[명시적 수명 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EC%88%98%EB%AA%85-%EA%B4%80%EB%A6%AC) (C++23~)|(작성중)|

# 유틸리티 라이브러리

|항목|내용|
|--|--|
|[일반 유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EC%9D%BC%EB%B0%98-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)|`swap()` 등을 제공합니다.<br/><br/>**(C++14~)**<br/>[exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)는 주어진 값을 바꾸고 이전값을 리턴합니다. 이동 생성자와 이동 대입 연산자 구현에 활용할 수 있습니다.|
|[가변 인자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|`va_list` 등으로 가변 인자를 처리합니다.|
|[타입 변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%83%80%EC%9E%85-%EB%B3%80%ED%99%98) (C++11~)|**(C++11~)**<br/>[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)는 좌측값을 우측값으로 형변환 합니다.<br/>[move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept)는 nothrow 보증이 되는 경우에만 `&&`로 형변환 합니다.<br/>[forword()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward)는 값 카테고리를 유지하며 인자를 다른 함수에 전달합니다.<br/>[declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval)은 주어진 타입을 참조 타입으로 변환하여, 참조 타입 표현식으로 변경해 줍니다.<br/><br/>**(C++17~)**<br/>`as_const()`는 인자에 `const`를 붙여 줍니다.|
|[bitset](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-bitset/)|[bitset](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-bitset/)은 비트 단위의 데이터를 관리하는 개체입니다.|
|간단한 데이터 개체|[pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)는 `first`와 `second`로 2개의 요소를 관리합니다. `make_pair()`를 이용하여 `pair`를 쉽게 생성할 수 있습니다.<br/><br/>**(C++11~)**<br/>[tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)은 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.<br/>`get()` 으로 요소에 접근합니다.<br/>[make_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#make_tuple), [tie()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tie), [forward_as_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#forward_as_tuple) 을 이용하여 `tuple`을 쉽게 생성할 수 있습니다.<br/>[tuple_cat()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_cat) 으로 두개의 `tuple`을 합칩니다.<br/>[tuple_size](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_size)로 컴파일 타임에 `tuple`요소 갯수를 구합니다.<br/>[tuple_element](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_element)을 이용하여 `tuple` 각 요소에 대한 타입을 구합니다.<br/>[piecewise_construct](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#piecewise_construct)를 이용하여 `pair`의 개체 생성시 `tuple`의 요소들로 개체 생성자를 호출합니다.<br/><br/>**(C++14~)**<br/>[타입 기반 `get()`](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#c14-%ED%83%80%EC%9E%85-%EA%B8%B0%EB%B0%98-get)을 이용하여 타입들이 서로 다르다면 타입으로 데이터에 접근할 수 있습니다.<br/><br/>**(C++17~)**<br/>[apply()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#c17-apply)는 `tuple` 형식을 전달받아 함수자를 호출합니다.<br/>[make_from_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#c17-make_from_tuple)은 `tuple` 형식을 전달받아 개체를 생성합니다.<br/>[optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/)이 추가되어 값이 있을 수도 있고, 없을 수도 있는 데이터를 처리할 수 있어, 미확정 상태, 값을 처리하기 부적절한 상태, 함수 리턴값 성공 여부 처리를 좀더 단순하게 할 수 있습니다.<br/>[any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/)가 추가되어 타입의 변동 가능성이 있는 데이터를 비교적 안전하게 사용할 수 있습니다.<br/>[variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/)를 제공하여 타입이 다른 여러 데이터들을 동일한 메모리 공간에서 쉽게 관리할 수 있게 합니다.<br/>[expected](??) (C++23~)(작성중)|
|[함수자 타입 특성 클래스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4)|함수자의 타입 특성으로 사용됩니다.<br/><br/>**(C++11~)**<br/>[unary_function, binery_function](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)는 deprecate 되었습니다.|
|[함수자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90)|`equal_to()`, `less()` 등의 비교 연산과 `logical_and()`, `logical_or()` 등의 논리 연산과 `plus()`, `minus()`등의 산술 연산과 `bit_and()`, `bit_or()`등의 비트 연산 함수자들을 제공합니다.<br/><br/>**(C++17~)**<br/>[search()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)의 함수자로 사용되는 [문자열 검색기](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#searcher)(`default_searcher`, `boyer_moore_searcher`, `boyer_moore_horspool_searcher`)가 추가되었습니다.|
|[부정자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%EB%B6%80%EC%A0%95%EC%9E%90)|반환값을 부정합니다.<br/><br/>**(C++11~)**<br/>[not1(), not2()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 는 deprecate 되었습니다.|
|[함수 래퍼](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98-%EB%9E%98%ED%8D%BC)|멤버 함수를 호출할 수 있도록 함수자로 만듭니다.<br/><br/>**(C++11~)**<br/>[mem_fun(), mem_fun_ref(), ptr_fun()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)은 deprecate 되었습니다.<br/>[function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)은 `()`로 호출 가능한 함수자를 저장합니다. `function`에서 `()`로 호출할 대상이 없을 때 [bad_function_call](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bad_function_call) 예외를 방출합니다.<br/>[mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn)은 인자가 있는 멤버 함수도 호출하는 함수자를 만들어 줍니다.|
|[참조 래퍼](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98-%EB%9E%98%ED%8D%BC) (C++11~)|**(C++11~)**<br/>[reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper) 는 복사 생성이나 복사 대입이 안되는 참조자를 래핑합니다.<br/>[ref(), cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref) 는 `reference_wrapper` 개체를 생성합니다.|
|[바인더](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)|함수 인자값을 고정하여 호출 형태를 변경합니다.<br/><br/>**(C++11~)**<br/>[`bind1st()`, `bind2nd()`](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94) 는 deprecate 되었습니다.<br/>[bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)는 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.<br/>[is_bind_expression](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#is_bind_expression)는  `bind()`로 생성한 함수인지 검사합니다.<br/>[is_placeholder](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#is_placeholder)는 자리 표시자를 사용했는지 검사합니다.|
|[hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-hash/) (C++11~)|**(C++11~)**<br/>[hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-hash/)는 각 타입별로 데이터의 해시값(Digest)을 리턴하는 함수자 입니다.|
|[Searcher](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#searcher) (C++17~)|(작성중)|
|[Invoke](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#invoke) (C++17~)|(작성중)|
|[Identity](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#identity) (C++20~)|(작성중)|
|[문자열 변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%B3%80%ED%99%98) (C++17~)|(작성중)|
|[모호성 해소](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EB%AA%A8%ED%98%B8%EC%84%B1-%ED%95%B4%EC%86%8C) (C++17~)|(작성중)|
|[정수 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EC%A0%95%EC%88%98-%EB%B9%84%EA%B5%90)|**(C++20~)**<br/>[`operator >()`](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EB%8C%80%EC%86%8C-%EB%B9%84%EA%B5%90)등은 deprecate되었습니다. `cmp_euqal()`등으로 정수를 비교합니다.|
|[포맷팅](??) (C++20~)|(작성중)|

# 컨테이너 라이브러리

각 컨테이너에 `initializer_list`를 이용한 초기화가 추가되었으며, `emplace()` 계열 함수들(요소 개체를 생성해서 전달하는 것이 아니라, 요소 개체의 생성자 인수들을 전달하면 컨테이너 내에서 요소 개체를 직접 생성함)이 추가되었습니다.

|항목|내용|
|--|--|
|[시퀀스 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)|[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/), `list`, `queue`, `stack`, `deque`, `priority_queue` 를 제공합니다.<br/><br/>**(C++11~)**<br/>[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)는 기존 C스타일의 배열처럼 연속된 메모리를 사용하는 컨테이너 입니다. C스타일 배열처럼 요소 추가/삭제가 지원되지 않으며 컴파일 타임에 크기가 결정되어 스택에 할당되므로, 힙에 할당되는 `vector` 보다 성능이 좋습니다.<br/>[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.|
|[연관 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)|`map`, `multimap`, `set`, `multiset`을 제공합니다.<br/><br/>**(C++11~)**<br/>[`unordered_map`, `unordered_multimap`, `unordered_set`, `unordered_multiset`](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/)은 정렬되지 않은 컨테이너로서, 해시값(Digest)을 사용하는 해시 컨테이너 입니다.<br/><br/>**(C++14~)**<br/>[연관 컨테이너의 이종 탐색](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-heterogeneous-lookup/)을 지원하여 **Key**와 다른 타입이더라도 탐색이 가능합니다.|
|`flat_set`<br/>`flat_map`<br/>`flat_multiset`<br/>`flat_multimap` (C++23~)|(작성중)|
|`span` (C++23~)|(작성중)|
|`mdspan` (C++23~)|(작성중)|

# 이터레이터 라이브러리

|항목|내용|
|--|--|
|[이터레이터 기본 요소](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EA%B8%B0%EB%B3%B8-%EC%9A%94%EC%86%8C)|`iterator`와 `iterator_traits`를 제공합니다.|
|[이터레이터 아답터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%95%84%EB%8B%B5%ED%84%B0)|`reverse_iterator`, `back_insert_iterator`등 이터레이터를 래핑한 아답터를 제공합니다.<br/><br/>**(C++11~)**<br/>[move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/) 아답터는 이터레이터가 가리키는 요소를 이동 연산 합니다.| 
|[스트림 이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%8A%A4%ED%8A%B8%EB%A6%BC-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)|`istream_iterator`, `ostream_iterator` 등 스트림 처리를 위한 이터레이터를 제공합니다.|
|[이터레이터 유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)|`advance()`, `distance()` 로 이터레이터를 이동시킵니다.<br/><br/>**(C++11~)**<br/>`next()`와 `prev()`가 추가되었습니다.|
|[범위 접근](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC) (C++11~)|**(C++11~)**<br/>[begin(), end()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)가 추가되었습니다.<br/><br/>**(C++14~)**<br/>[rbegin(), rend(), cbegin(), cend(), crbegin(), crend()]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC))가 추가되었습니다.|
|[이터레이터 사용자 정의](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98) (C++20~)|(작성중)|
|[이터레이터 컨셉](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)|(작성중)|
|[이터레이터 연관 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%97%B0%EA%B4%80-%ED%83%80%EC%9E%85) (C++20~)|(작성중)|
|[알고리즘 컨셉 및 연관 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EC%BB%A8%EC%85%89-%EB%B0%8F-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0) (C++20~)|(작성중)|

# 범위 라이브러리

(C++20~) (작성중)

# 알고리즘 라이브러리

|항목|내용|
|--|--|
|[C스타일 알고리즘 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%ED%95%A8%EC%88%98)|`qsort()`, `bsearch()`를 제공합니다.|
|[수정되지 않는 시퀀스 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)|`for_each()`, `find()`, `search()`등 시퀀스를 수정되지 않는 작업입니다.|
|[수정되는 시퀀스 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)|`copy()`, `fill()`, `transform()`, `generate()`, `remove()`, `replace()` 등 시퀀스가 수정되는 작업입니다.|
|[분할 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EB%B6%84%ED%95%A0-%EC%9E%91%EC%97%85)|`partition()` 등으로 시퀀스의 요소들을 분할하여 재배치 합니다.|
|[정렬 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%A0%95%EB%A0%AC-%EC%9E%91%EC%97%85)|`sort()`, `nth_element()` 등으로 시퀀스를 정렬합니다.|
|[이진 검색 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%9D%B4%EC%A7%84-%EA%B2%80%EC%83%89-%EC%9E%91%EC%97%85)|`lower_bound()`, `upper_bound()`, `binary_search()` 등으로 검색합니다.|
|[정렬된 범위에서의 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%A0%95%EB%A0%AC%EB%90%9C-%EB%B2%94%EC%9C%84%EC%97%90%EC%84%9C%EC%9D%98-%EC%9E%91%EC%97%85)|`merge()`, `set_union()`, `set_difference()`, `set_intersection()` 등으로 정렬된 시퀀스를 결합합니다.|
|[최대/최소 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C-%EC%9E%91%EC%97%85)|`min()`, `max()` 등으로 최대/최소값을 구합니다.|
|[비교 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EB%B9%84%EA%B5%90-%EC%9E%91%EC%97%85)|`equal()`, `lexicographical_compare()` 등으로 비교합니다.|
|[순열 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%9C%EC%97%B4-%EC%9E%91%EC%97%85)|`next_permutation()`, `prev_permutation()` 등으로 순열(요소들을 중복없이 순서를 변경하여 나열)을 배치합니다.|
|[힙 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%ED%9E%99-%EC%9E%91%EC%97%85)|(작성중)|
|[수학 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)|(작성중)|
|[메모리 유틸리티 작업](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0-%EC%9E%91%EC%97%85)|(작성중)|
|[실행 정책](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%8B%A4%ED%96%89-%EC%A0%95%EC%B1%85) (C++17~)|(작성중)|

# 문자열 라이브러리

|항목|내용|
|--|--|
|[C스타일 문자열 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)|`strcpy()`, `srlen()`등 [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 지원하는 C스타일 문자열 함수를 제공합니다.|
|문자열 개체|[string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/)은 바이트 문자열을 지원합니다.<br/>[wstring](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/)은 와이드 문자열을 지원합니다.<br/><br/>**(C++11~)**<br/>[u16string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/)은 UTF-16을 지원합니다.<br/>[u32string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/)은 UTF-32를 지원합니다.<br/><br/>**(C++20~)**<br/>[u8string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/)은 UTF-8을 지원합니다.|
|[strerror()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#strerror)| [strerror()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#strerror)는 `errorno`([오류 번호](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%A4%EB%A5%98-%EB%B2%88%ED%98%B8) 참고)를 문자열로 출력해 줍니다.|
|basic_string_view (C++17~)|(작성중)|

# 동시성 라이브러리

|항목|내용|
|--|--|
|[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread) (C++11~)|**(C++11~)**<br/>[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)는 주어진 함수자를 쓰레드로 실행시킵니다. `yield()`, `sleep_for()`, `sleep_until()`등으로 실행 순서나 속도를 제어할 수 있습니다.<br/>[call_once()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#call_once-once_flag)를 이용하면 주어진 함수자를 여러 쓰레드에서 실행해도 한번만 호출합니다.|
|동기화 개체 (C++11~)|**(C++11~)**<br/>[mutex, timed_mutex, recusive_mutex, recusive_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex) 등은 쓰레드간 경쟁 상태를 해결하기 위한 동기화 개체 입니다.<br/>[lock_guard, unique_lock, lock(), try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock) 등은 `mutex`의 잠금 상태를 관리합니다.<br/><br/>**(C++14~)**<br/>[shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)을 이용하여 `mutex`의 소유권을 쓰레드끼리 공유할 수 있습니다.<br/>[shared_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)을 이용하여  `mutex`의 소유권을 쓰레드끼리 공유할 수 있습니다.<br/><br/>**(C++17~)**<br/>`shared_mutex`(작성중)<br/>`scope_lock`(작성중)|
|[atomic](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-atomic/#atomic) (C++11~)|**(C++11~)**<br/>[atomic](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-atomic/#atomic)은 메모리에서 값을 읽고, 수정하고, 저장하는 작업을 단일 명령 단위로 구성합니다.<br/>[memory_order](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-atomic/#memory_order)는 `atomic`에서 명령을 실행할 때 순차적 일관성 처리 방식을 지정하는 열거형 상수 입니다.|
|동기/비동기 처리 (C++11~)|**(C++11~)**<br/>[condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable)은 쓰레드간 동기화를 위해 쓰레드를 `wait()`시킨뒤, 특정 조건이 되었을때 활성화 시켜 주는 개체입니다.<br/>[promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)는 비동기 처리를 위한 데이터를 저장합니다.<br/>[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)는 비동기 함수가 완료될때까지 대기하고, 데이터를 추출합니다.<br/>[shared_future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#shared_future)는 여러곳에서 공유할 수 있는 `future` 입니다.<br/>[packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task)는 `promise`를 캡슐화하여 비동기 함수 설정만 하면 되는 유틸리티 개체 입니다.<br/>[async()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#async)는 `packaged_task`를 쉽게 사용할 수 있도록 만든 유틸리티 함수입니다.|

# 숫자 라이브러리

|항목|내용|
|--|--|
|[complex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex)|복소수를 처리하는 개체입니다.|
|[valarray](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#valarray)|값의 배열을 다루는 개체입니다.|
|[공통 수학 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%EA%B3%B5%ED%86%B5-%EC%88%98%ED%95%99-%ED%95%A8%EC%88%98)|`abs()`, `exp()`, `sin()` 등 일반 수학 함수, 지수 함수, 거듭 제곱 함수, 삼각 함수, 에러 및 감마 함수, 부동 소수점 함수, 분류 및 비교 함수를 제공합니다.|
|[부동 소수점 환경](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%ED%99%98%EA%B2%BD) (C++11~)|(작성중)|
|[특수 수학 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%ED%8A%B9%EC%88%98-%EC%88%98%ED%95%99-%ED%95%A8%EC%88%98) (C++17~)|(작성중)|
|[수학 상수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%EC%88%98%ED%95%99-%EC%83%81%EC%88%98) (C++20~)|(작성중)|
|[비트 조작](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%EB%B9%84%ED%8A%B8-%EC%A1%B0%EC%9E%91) (C++20~)|(작성중)|
|난수 생성기|[C스타일 난수 생성기](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%82%9C%EC%88%98-%EC%83%9D%EC%84%B1%EA%B8%B0)는 `rand()`, `srand()`를 제공합니다.<br/><br/>**(C++11~)**<br/>[난수 생성기](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%EB%82%9C%EC%88%98-%EC%83%9D%EC%84%B1%EA%B8%B0)를 제공하여 다양한 형태의 난수를 만듭니다.|

# 날짜 / 시간 라이브러리

|항목|내용|
|--|--|
|[C스타일 시간 유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%8B%9C%EA%B0%84-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)|`time()`, `clock()`등 C스타일 시간 함수를 제공합니다.|
|[chrono](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/) (C++11~)|**(C++11~)**<br/>[system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock), [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point), [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) 개체를 이용하여 좀더 다양한 정확도로 시간을 추적할 수 있습니다.<br/><br/>**(C++14~)**<br/>`operator ""h`와 같은 [날짜 / 시간 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#%EB%82%A0%EC%A7%9C--%EC%8B%9C%EA%B0%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 제공됩니다.<br/><br/>**(C++20~)**<br/>[시/분/초 서식](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#%EC%8B%9C%EB%B6%84%EC%B4%88-%EC%84%9C%EC%8B%9D)(작성중)<br/>[Calender](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#calendar)(작성중)<br/>[Timezone](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-zone)(작성중)<br/>`parse()`(작성중)|

# 정규 표현식 라이브러리

|항목|내용|
|--|--|
|[regex_match()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_match) (C++11~)|**(C++11~)**<br/>[regex_match()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_match)는 문자열의 전체가 정규 표현식과 일치하는지 검사합니다.|
|[regex_search()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_search) (C++11~)|**(C++11~)**<br/>[regex_search()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_search)는 문자열의 일부가 정규 표현식과 일치하는지 검사합니다.<br/>[regex_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_iterator)는 문자열 전체에 대해 `regex_search()` 한 결과의 이터레이터입니다.|
|[regex_replace()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_replace) (C++11~)|**(C++11~)**<br/>[regex_replace()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_replace)은 문자열에서 정규 표현식과 일치하는 부분을 수정합니다.|

# 입출력 라이브러리

|항목|내용|
|--|--|
|[C스타일 입출력](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5)|`fopen()`, `fclose()`등 C스타일 입출력을 제공합니다.|
|C++스타일 입출력|[파일 입출력](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%ED%8C%8C%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5), [문자열 입출력](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%9E%85%EC%B6%9C%EB%A0%A5), [배열 입출력](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EB%B0%B0%EC%97%B4-%EC%9E%85%EC%B6%9C%EB%A0%A5) 을 제공하고, [스트림 기반 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%8A%A4%ED%8A%B8%EB%A6%BC-%EA%B8%B0%EB%B0%98-%ED%83%80%EC%9E%85) 으로 재정의되어, `iosteam`, `filebuf`등을 제공합니다.|
|[사전 정의된 표준 스트림 개체](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%82%AC%EC%A0%84-%EC%A0%95%EC%9D%98%EB%90%9C-%ED%91%9C%EC%A4%80-%EC%8A%A4%ED%8A%B8%EB%A6%BC-%EA%B0%9C%EC%B2%B4)|`cin`으로 표준 입력을 받고, `cout`으로 표준 출력을 합니다.|
|[입출력 제어](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%9E%85%EC%B6%9C%EB%A0%A5-%EC%A0%9C%EC%96%B4)|`endl()`등 표준 입출력 제어를 제공합니다.<br/><br/>**(C++14~)**<br/>[quoted()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%9E%85%EC%B6%9C%EB%A0%A5-%EC%A0%9C%EC%96%B4)는 `stringstream` 으로 입출력시에 공백, 이스케이프 등을 유지시켜 줍니다.|
|[예외](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%98%88%EC%99%B8) (C++11~)|(작성중)|
|[동기화 출력](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EB%8F%99%EA%B8%B0%ED%99%94-%EC%B6%9C%EB%A0%A5) (C++20~)|(작성중)|
|[프린트 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%ED%94%84%EB%A6%B0%ED%8A%B8-%ED%95%A8%EC%88%98) (C++23~)|(작성중)|

# 파일 시스템 라이브러리

(C++17~) (작성중)

# 현지화 라이브러리

|항목|내용|
|--|--|
|[C스타일 Locale](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#c%EC%8A%A4%ED%83%80%EC%9D%BC-locale)|(작성중)|
|[Locale](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#locale)|(작성중)|
|[Facet 카테고리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#facet-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)|(작성중)|
|[Facet](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#facet)|(작성중)|
|[문자 분류(Locale)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#%EB%AC%B8%EC%9E%90-%EB%B6%84%EB%A5%98locale)|(작성중)|
|[문자 조작(Locale)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#%EB%AC%B8%EC%9E%90-%EC%A1%B0%EC%9E%91locale)|(작성중)|
|[문자열 현지화 변환)()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%98%84%EC%A7%80%ED%99%94-%EB%B3%80%ED%99%98) (C++11~C++17)|(작성중)|

# 진단 라이브러리

|항목|내용|
|--|--|
|[진단](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%A7%84%EB%8B%A8)|[assert()]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%A7%84%EB%8B%A8))로 설계한 기능이 올바르게 사용되는지 디버그 모드에서 확인합니다.|
|[exception](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#exception)|`throw`를 이용한 예외 보고시 일관된 인터페이스를 제공합니다.|
|[표준 예외](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%ED%91%9C%EC%A4%80-%EC%98%88%EC%99%B8)|`out_of_range`, `bad_alloc`등의 예외를 제공합니다.|
|[예외 처리 실패](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%88%EC%99%B8-%EC%B2%98%EB%A6%AC-%EC%8B%A4%ED%8C%A8)|예외를 `catch()`하지 않았을때 `terminate()`를 호출하여 `terminate_handler`를 호출합니다.|
|[동적 예외 사양 실패](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91-%EC%8B%A4%ED%8C%A8)|동적 예외 사양에서 명시한 예외 이외의 예외가 발생할때 `unexpected()`를 호출하여 `unexpected_handler`를 호출합니다.<br/><br/>**(C++11~)**<br/>deprecate 되었습니다.|
|[오류 번호](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%A4%EB%A5%98-%EB%B2%88%ED%98%B8)|`<cerrono>` 에 매크로 상수로 정의되어 있습니다.<br/>자세한 내용은 [cppreference.com](https://en.cppreference.com/w/cpp/error/errno_macros)을 참고하기 바랍니다.|
|[예외 캡쳐 및 저장](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%88%EC%99%B8-%EC%BA%A1%EC%B3%90-%EB%B0%8F-%EC%A0%80%EC%9E%A5) (C++11~)|(작성중)|
|[시스템 오류](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98)(C++11~)|(작성중)|
|[Stacktrace](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#stacktrace) (C++23~)|(작성중)|

# deprecate/remove

|항목|내용|
|--|--|
|(~C++11)|[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 `delete[]` 미지원, `lvalue` 복사 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다.<br/>[함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)(`unary_function`, `binery_function` 등), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)(`bind1st()`, `bind2nd()` 등), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)(`mem_fun()`, `mem_fun_ref()`, `ptr_fun()`, `not1()`, `not2(), unary_negate(), binary_negate(), pointer_to_unary_function(), pointer_to_binary_function()` 등)가 람다 표현식, `function`, `bind()`, `mem_fn()`등으로 대체되어 deprecate 되었습니다.<br/>[동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 이 deprecate 되면서 [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)도 함께 deprecate 되었습니다.<br/>[gets()]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5))가 deprecate 되었습니다.|
|(~C++14)|[random_shuffle()]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85))이 deprecate 되었습니다. `shuffle()`을 사용하세요.|



