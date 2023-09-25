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

    템플릿 메타 프로그래밍, 스마트 포인터, 우측값 참조, 중괄호 초기화, tuple, 함수자, 해시, 유니코드, 날짜/시간, 정규 표현식, 쓰레드 처리를 위한 부분들이 보강되었습니다.

# 언어 지원 라이브러리

|항목|내용|
|--|--|
|[기본 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EA%B8%B0%EB%B3%B8-%ED%83%80%EC%9E%85)|`size_t`, `ptrdiff_f`, `NULL` 등 기본 타입을 제공합니다.|
|[타입 최대/최소값](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%ED%83%80%EC%9E%85-%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C%EA%B0%92)|`numeric_limits`를 이용하여 타입의 최대, 최소값을 알 수 있습니다.|
|[런타임 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EB%9F%B0%ED%83%80%EC%9E%84-%ED%83%80%EC%9E%85)|`type_info`를 이용하여 개체의 타입 정보를 알 수 있습니다.|
|[고정 너비 정수 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%A0%95%EC%88%98-%ED%83%80%EC%9E%85) (C++11~)|고정 너비 정수 타입이 추가되었습니다.|
|[고정 너비 실수 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%8B%A4%EC%88%98-%ED%83%80%EC%9E%85) (C++11~)|고정 너비 실수 타입이 추가되었습니다.|
|[type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_index/) (C++11~)|`type_index`는 `type_info`의 래퍼로서 `type_info`를 컨테이너에서 관리할 수 있게 합니다.|
|[initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) (C++11~)|`initializer_list` 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다.|
|[프로그램 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90)|`abort()`, `exit()`등 프로그램 종료 관련 함수들과 시그널 처리 함수를 제공합니다.|
|[소스 코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EC%86%8C%EC%8A%A4-%EC%BD%94%EB%93%9C) (C++20~)|(작성중)|
|[3자 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#3%EC%9E%90-%EB%B9%84%EA%B5%90) (C++20~)|(작성중)|
|[코루틴](??) (C++20~)|(작성중)|
|`version` (C++20~)|라이브러리 테스트를 위한 매크로를 지원합니다. 자세한 내용은 [cppreference](https://en.cppreference.com/w/cpp/feature_test#Library_features)를 참고하세요.|

# 컨셉 라이브러리

# 메타 프로그래밍 라이브러리

|항목|내용|
|--|--|
|[타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/) (C++11~)|`type_traits`는 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 합니다.|
|[ratio](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-ratio/) (C++11~)|`ratio` 개체는 분자와 분모를 따로 저장하여 유리 분수를 표현하며, 유틸리티들을 이용하여 컴파일 타임 유리수(정수와 분수) 연산을 지원합니다.|

# 메모리 관리 라이브러리

|항목|내용|
|--|--|
|[할당자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%ED%95%A0%EB%8B%B9%EC%9E%90)|[allocator](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-allocator/) 등 STL에서 개체 할당에 사용합니다.|
|[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/) (~C++11)|`auto_ptr`은 `delete[]` 미지원, `lvalue` 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다.<br/><br/>**(C++11~)**<br/>[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/) 을 사용합니다.|
|[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/) (C++11~)|`unique_ptr`은 소유권 이전용 스마트 포인터입니다. 기존 `auto_ptr`을 대체합니다. `auto_ptr`은 `delete[]` 미지원, `lvalue` 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다.<br/>[default_delete](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#default_delete)를 스마트 포인터의 `deleter`로 사용할 수 있습니다.|
|[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr) (C++11~)|`shared_ptr`은 소유권 공유용 스마트 포인터입니다.<br/>[enable_shared_from_this](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#enable_shared_from_this)를 이용하여 `shared_ptr`이 관리하는 개체로부터 `shared_ptr`을 만들 수 있습니다.<br/>[owner_less](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#owner_less)를 이용하여 소유권 개체의 주소로 비교할 수 있습니다.<br/>`shared_ptr`에서 포인터가 잘못된 경우 [bad_weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#bad_weak_ptr) 예외를 방출합니다.|
|[weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr) (C++11~)|`weak_ptr`은 `shared_ptr`의 상호 참조 문제를 해결합니다.|
|[make_shared()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#make_shared) (C++11~)|`make_shared`를 이용하여 `shared_ptr`을 효율적으로 생성할 수 있습니다.|
|[shared_ptr 형변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr-%ED%98%95%EB%B3%80%ED%99%98) (C++11~)|`const_pointer_cast()`, `static_pointer_cast()`, `dynamic_pointer_cast()`, `reinterpret_pointer_cast()` 로 `shared_ptr`의 관리 개체를 형변환 할 수 있습니다.|
|[동적 메모리 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/) (C++11~)|`pointer_traits`는 포인터와 유사한 타입들을 다루는 표준화된 방법을 제공합니다.<br/>`addressof()`는 `operator &()`가 오버로딩 되었어도 실제 주소를 리턴합니다.<br/>`align()`은 메모리 정렬된 포인터를 구합니다.|
|[가비지 컬렉터 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%ED%84%B0-%EC%A7%80%EC%9B%90) (C++11~C++23)|(작성중)|
|[메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4) (C++17~)|(작성중)|
|[초기화 되지 않은 스토리지](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%90%98%EC%A7%80-%EC%95%8A%EC%9D%80-%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80) (C++17~)|(작성중)|
|[메모리 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC) (C++20~)|(작성중)|
|[스마트 포인터 어뎁터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%8A%B8-%EC%96%B4%EB%8E%81%ED%84%B0) (C++23~)|(작성중)|
|[명시적 수명 관리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EC%88%98%EB%AA%85-%EA%B4%80%EB%A6%AC) (C++23~)|(작성중)|

# 유틸리티 라이브러리

|항목|내용|
|--|--|
|[일반 유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EC%9D%BC%EB%B0%98-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)|`swap()` 등을 제공합니다.|
|[가변 인자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)|`va_list` 등으로 가변 인자를 처리합니다.|
|[타입 변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%83%80%EC%9E%85-%EB%B3%80%ED%99%98) (C++11~)|[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)는 좌측값을 우측값으로 형변환 합니다.<br/>[move_if_noexcept()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move_if_noexcept)는 nothrow 보증이 되는 경우에만 `&&`로 형변환 합니다.<br/>[forword()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward)는 값 카테고리를 유지하며 인자를 다른 함수에 전달합니다.<br/>[declval()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#declval)은 주어진 타입을 참조 타입으로 변환하여, 참조 타입 표현식으로 변경해 줍니다.|
|[bitset](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-bitset/)|`bitset`은 비트 단위의 데이터를 관리하는 개체입니다.|
|[pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)|`first`와 `second`로 2개의 요소를 관리합니다.<br/>`make_pair()`를 이용하여 `pair`를 쉽게 생성할 수 있습니다.|
|[tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/) (C++11~)|`tuple`은 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.<br>`get()` 으로 요소에 접근합니다.<br/>[make_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#make_tuple), [tie()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tie), [forward_as_tuple()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#forward_as_tuple) 을 이용하여 `tuple`을 쉽게 생성할 수 있습니다.<br/>[tuple_cat()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_cat) 으로 두개의 `tuple`을 합칩니다.<br/>[tuple_size](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_size)로 컴파일 타임에 `tuple`요소 갯수를 구합니다.<br/>[tuple_element](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#tuple_element)을 이용하여 `tuple` 각 요소에 대한 타입을 구합니다.<br/>[piecewise_construct](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/#piecewise_construct)를 이용하여 `pair`의 개체 생성시 `tuple`의 요소들로 개체 생성자를 호출합니다.|
|[optional](??) (C++17~)|(작성중)|
|[expected](??) (C++23~)|(작성중)|
|[varient](??) (C++17~)|(작성중)|
|[any](??) (C++17~)|(작성중)|
|[함수자 타입 특성 클래스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4) (~C++11)|함수자의 타입 특성으로 사용됩니다. [unary_function, binery_function](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)는 deprecate 되었습니다.|
|[함수자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90)|`equal_to()`, `less()` 등의 비교 연산과 `logical_and()`, `logical_or()` 등의 논리 연산과 `plus()`, `minus()`등의 산술 연산과 `bit_and()`, `bit_or()`등의 비트 연산 함수자들을 제공합니다.|
|[부정자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%EB%B6%80%EC%A0%95%EC%9E%90)|반환값을 부정합니다.<br/>[not1(), not2()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90) 는 deprecate 되었습니다.|
|[함수 래퍼](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98-%EB%9E%98%ED%8D%BC)|멤버 함수를 호출할 수 있도록 함수자로 만듭니다.<br/>[mem_fun(), mem_fun_ref(), ptr_fun()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)은 deprecate 되었습니다.<br/><br/>**(C++11~)**<br/>[function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)은 `()`로 호출 가능한 함수자를 저장합니다. `function`에서 `()`을 호출할 대상이 없을 때 [bad_function_call](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bad_function_call) 예외를 방출합니다.<br/>[mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn)은 인자가 있는 멤버 함수도 호출하는 함수자를 만들어 줍니다.|
|[참조 래퍼](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%ED%95%A8%EC%88%98-%EB%9E%98%ED%8D%BC) (C++11~)|[reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper) 는 복사나 대입이 안되는 참조자를 래핑합니다.<br/>[ref(), cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref) 는 `reference_wrapper` 개체를 생성합니다.|
|[바인더](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)|함수 인자값을 고정하여 호출 형태를 변경합니다.<br/>[`bind1st()`, `bind2nd()`](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94) 는 deprecate 되었습니다.<br/><br/>**(C++11~)**<br/>[bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)는 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.<br/>[is_bind_expression](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#is_bind_expression)는  `bind()`로 생성한 함수인지 검사합니다.<br/>[is_placeholder](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#is_placeholder)는 자리 표시자를 사용했는지 검사합니다.|
|[Searcher](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#searcher)|(작성중)|
|[Invoke](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#invoke)|(작성중)|
|[Identity](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#identity)|(작성중)|
|[hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-hash/) (C++11~)|`hash()`는 각 타입별로 데이터의 해시값(Digest)을 리턴하는 함수자 입니다.|
|[문자열 변환](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%B3%80%ED%99%98) (C++17~)|(작성중)|
|[모호성 해소](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EB%AA%A8%ED%98%B8%EC%84%B1-%ED%95%B4%EC%86%8C) (C++17~)|(작성중)|
|[정수 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%EC%A0%95%EC%88%98-%EB%B9%84%EA%B5%90) (C++20~)|`operator <()`등은 deprecate되었습니다.<br/><br/>**(C++20~)**<br/>`cmp_euqal()`등으로 정수를 비교합니다.|
|[포맷팅](??) (C++20~)|(작성중)|

# 컨테이너 라이브러리

각 컨테이너에 `initializer_list`를 이용한 초기화가 추가되었으며, `emplace()` 계열 함수들(요소 개체를 생성해서 전달하는 것이 아니라, 요소 개체의 생성자 인수들을 전달하면 컨테이너 내에서 요소 개체를 직접 생성함)이 추가되었습니다.

|항목|내용|
|--|--|
|[시퀀스 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)|[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/), `list`, `queue`, `stack`, `deque`, `priority_queue` 를 제공합니다.|
|[연관 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)|`map`, `multimap`, `set`, `multiset`을 제공합니다.|
|[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/) (C++11~)|`array`는 기존 C스타일의 배열처럼 연속된 메모리를 사용하는 컨테이너 입니다. C스타일 배열처럼 요소 추가/삭제가 지원되지 않으며 컴파일 타임에 크기가 결정되어 스택에 할당되므로, 힙에 할당되는 `vector` 보다 성능이 좋습니다.|
|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/) (C++11~)|`forward_list`는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.|
|[unordered_map<br/>unordered_multimap<br/>unordered_set<br/>unordered_multiset](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/) (C++11~)|`unordered_map`, `unordered_multimap`, `unordered_set`, `unordered_multiset` 은 정렬되지 않은 컨테이너로서, 해시값(Digest)을 사용하는 해시 컨테이너 입니다.|
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
|[범위 접근](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/) (C++11~)|`begin()`과 `end()`가 추가되었습니다.|
|[이터레이터 사용자 정의](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98) (C++20~)|(작성중)|
|[이터레이터 컨셉](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%BB%A8%EC%85%89) (C++20~)|(작성중)|
|[이터레이터 연관 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%97%B0%EA%B4%80-%ED%83%80%EC%9E%85) (C++20~)|(작성중)|
|[알고리즘 컨셉 및 연관 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EC%BB%A8%EC%85%89-%EB%B0%8F-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0) (C++20~)|(작성중)|

# 범위 라이브러리

# 알고리즘 라이브러리

|항목|내용|
|--|--|
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
|[std::u16string](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B0%9C%EC%B2%B4) (C++11~)|`std::basic_string<char16_t>` UTF-16 인코딩을 지원합니다.|
|[std::u32string](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B0%9C%EC%B2%B4) (C++11~)|`std::basic_string<char32_t>` UTF-32 인코딩을 지원합니다.|
|[std::u8string](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#c20-char8_t-stdu8string) (C++20~)|`std::basic_string<char8_t>` UTF-8 인코딩을 지원합니다.|

# 동시성 라이브러리

|항목|내용|
|--|--|
|[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread) (C++11~)|`thread`는 주어진 함수자를 쓰레드로 실행시킵니다. `yield()`, `sleep_for()`, `sleep_until()`등으로 실행 순서나 속도를 제어할 수 있습니다.|
|[mutex<br/>timed_mutex<br/>recursive_mutex<br/>recursive_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex) (C++11~)<br/>`shared_timed_mutex` (C++14~)<br/>`shared_mutex` (C++17~)|`mutex`, `timed_mutex`, `recusive_mutex`, `recusive_timed_mutex` 등은 쓰레드간 경쟁 상태를 해결하기 위한 동기화 개체 입니다.|
|[lock_guard<br/>unique_lock<br/>lock()<br>try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock) (C++11~)<br/>`shared_lock` (C++14~)<br/>`scope_lock` (C++17~)|`lock_guard`, `unique_lock` 등은 `mutex`의 잠금 상태를 관리합니다.|
|[call_once()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#call_once-once_flag) (C++11~)|`call_once()`는 주어진 함수자를 여러 쓰레드에서 실행해도 한번만 호출되게 합니다.|
|[condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable) (C++11~)|`condition_variable`은 쓰레드간 동기화를 위해 쓰레드를 `wait()`시킨뒤, 특정 조건이 되었을때 활성화 시켜 주는 개체입니다.|

thread
mutex
condition_variable
atomic
future

# 수치 라이브러리

random

https://en.cppreference.com/w/cpp/numeric/math

rand
srand
RAND_MAX

**complex**

https://en.cppreference.com/w/cpp/numeric/complex

**valarray**

https://en.cppreference.com/w/cpp/numeric/valarray


# 에러 핸들링 라이브러리

assert 
https://en.cppreference.com/w/cpp/error#System_error

std::exception_ptr
std::error_code
std::error_condition
system_error

# 날짜 / 시간 라이브러리

|항목|내용|
|--|--|
|[C스타일 시간 유틸리티](??)|`time()`, `clock()`등 C스타일 시간 함수를 제공합니다.|
|[chrono](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/) (C++11~)|[system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock), [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point), [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) 개체를 이용하여 좀더 다양한 정확도로 시간을 추적할 수 있습니다.|
|[날짜 / 시간 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#%EB%82%A0%EC%A7%9C--%EC%8B%9C%EA%B0%84-%EB%A6%AC%ED%84%B0%EB%9F%B4) (C++14~)|(작성중)|
|[시/분/초 서식](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#%EC%8B%9C%EB%B6%84%EC%B4%88-%EC%84%9C%EC%8B%9D) (C++20~)|(작성중)|
|[Calender](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#calendar) (C++20~)|(작성중)|
|[Timezone](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-zone) (C++20~)|(작성중)|

# 정규 표현식 라이브러리
regex

# 현지화 라이브러리

codecvt

# 입출력 라이브러리

# 파일 시스템 라이브러리

# C 라이브러리 지원

cfenv
cinttype
cstdint
cuchar

# deprecate/remove

|항목|내용|
|--|--|
|[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)|`auto_ptr`은 `delete[]` 미지원, `lvalue` 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다.|
|[함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)<br/>[바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)<br/>[어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)|[함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)(`unary_function`, `binery_function` 등), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)(`bind1st()`, `bind2nd()` 등), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)(`mem_fun()`, `mem_fun_ref()`, `ptr_fun()`, `not1()`, `not2()` 등)가 람다 표현식, `function`, `bind()`, `mem_fn()`등으로 대체되어 deprecate 되었습니다.|



