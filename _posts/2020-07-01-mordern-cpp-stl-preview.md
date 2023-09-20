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
|[type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_index/)|`type_index`는 `type_info`의 래퍼로서 `type_info`를 컨테이너에서 관리할 수 있게 합니다.|

# 컨셉 라이브러리

# 메타 프로그래밍 라이브러리

|항목|내용|
|--|--|
|[타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/)|`type_traits`는 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 합니다.|



# 메모리 관리 라이브러리

|항목|내용|
|--|--|
[default_delete](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#default_delete)를 스마트 포인터의 `deleter`로 사용할 수 있습니다.|
|[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr)|`shared_ptr`은 소유권 공유용 스마트 포인터입니다.<br/>[enable_shared_from_this](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#enable_shared_from_this)를 이용하여 `shared_ptr`이 관리하는 개체로부터 `shared_ptr`을 만들 수 있습니다.<br/>[owner_less](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#owner_less)를 이용하여 소유권 개체의 주소로 비교할 수 있습니다.|
|[bad_weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#bad_weak_ptr)|`shared_ptr`에서 포인터가 잘못된 경우 `bad_weak_ptr` 예외를 방출합니다.|
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
|[function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/)|`function`은 `()`로 호출 가능한 개체를 저장합니다.|
|[bad_function_call](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#bad_function_call)|`function`에서 `()`을 호출할 대상이 없을 때 `bad_function_call` 예외를 방출합니다.|
|[mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#mem_fn)|`mem_fun()`은 인자가 있는 멤버 함수도 호출하는 있는 함수자를 만들어 줍니다.|
|[참조자 래핑](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#reference_wrapper)|[reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#reference_wrapper) 는 복사나 대입이 안되는 참조자를 래핑합니다.<br/>[ref(), cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#ref-cref) 는 `reference_wrapper` 개체를 생성합니다.|
|[bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#bind)|`bind()`는 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.<br/>[is_bind_expression](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#is_bind_expression)는  `bind()`로 생성한 함수인지 검사합니다.<br/>[is_placeholder](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-function/#is_placeholder)는 자리 표시자를 사용했는지 검사합니다.|
|[hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-hash/)|`hash()`는 각 타입별로 데이터의 해시값(Digest)을 리턴하는 함수자 입니다.|

# 컨테이너 라이브러리

각 컨테이너에 `initializer_list`를 이용한 초기화가 추가되었으며, `emplace()` 계열 함수들(요소 개체를 생성해서 전달하는 것이 아니라, 요소 개체의 생성자 인수들을 전달하면 컨테이너 내에서 요소 개체를 직접 생성함)이 추가되었습니다.

|항목|내용|
|--|--|
|[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-array/)|`array`는 기존 C스타일의 배열처럼 연속된 메모리를 사용하는 컨테이너 입니다. C스타일 배열처럼 요소 추가/삭제가 지원되지 않으며 컴파일 타임에 크기가 결정되어 스택에 할당되므로, 힙에 할당되는 `vector` 보다 성능이 좋습니다.|
|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-forward_list/)|`forward_list`는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.|
|[unordered_map<br/>unordered_multimap<br/>unordered_set<br/>unordered_multiset](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-unordered_map-unordered_set/)|`unordered_map`, `unordered_multimap`, `unordered_set`, `unordered_multiset` 은 정렬되지 않은 컨테이너로서, 해시값(Digest)을 사용하는 해시 컨테이너 입니다.|

# 이터레이터 라이브러리

|항목|내용|
|--|--|
|[move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-iterator/)|`move_iterator` 아답터는 이터레이터가 가리키는 요소를 이동 연산 합니다.|
|[이터레이터 유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-iterator/)|`next()`와 `prev()`가 추가되었습니다.|
|[범위 접근](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-iterator/)|`begin()`과 `end()`가 추가되었습니다.|

# 범위 라이브러리

# 알고리즘 라이브러리

|항목|내용|
|--|--|
|C++11|이동 연산 및 일부 알고리즘들이 보강되었습니다.([알고리즘](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-algorithm/) 참고)|

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

# deprecate/remove

|항목|내용|
|--|--|
|[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)|`auto_ptr`은 `delete[]` 미지원, `lvalue` 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다.|
|[함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)<br/>[바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)<br/>[어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)|[함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)(`unary_function`, `binery_function` 등), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)(`bind1st()`, `bind2nd()` 등), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)(`mem_fun()`, `mem_fun_ref()`, `ptr_fun()`, `not1()`, `not2()` 등)가 람다 표현식, `function`, `bind()`, `mem_fn()`등으로 대체되어 deprecate 되었습니다.|

