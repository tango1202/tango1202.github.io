---
layout: single
title: "#1. [모던 C++ STL] 개요"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

C++11은,

1. 스마트 포인터,
2. initializer_list
3. tuple, 
4. 컨테이너,
5. function,
6. 동시성 지원
7. 날짜 / 시간
8. 정규 표현식 
   
이 보강 되었고, 템플릿 메타 프로그래밍과 우측값 참조와 해시 처리를 위한 부분들이 보강 되었습니다.



**표준라이브러리**

스마트포인터
스레딩 기능
튜플
해시테이블
정규식
난수식
레퍼 참조
객체를 위한 다형성 레퍼
메타프로그래밍 타입 특성


# 메타 프로그래밍
타입 특성(type_traits)

# 타입 지원
std::type_index

# 동적 메모리 관리

unique_ptr
default_delete

shared_ptr
make_shared()
enable_shared_from_this
std::owner_less
std::const_pointer_cast()
std::static_pointer_cast()
std::dynamic_pointer_cast()
std::reinterpret_pointer_cast()
weak_ptr
bad_weak_ptr

pointer_traits
addressof
align


# 유틸리티

move
move_if_noexcept
forward
declval

std::initializer_list

std::tuple
    piecewise_construct
    tuple_element
    std::tuple_size 
    std::tuple_element

hash
  

  
# 컨테이너
std::array
std::forward_list
std::undordered_map
std::unordered_set

# 이터레이터

next
prev
std::move_iterator

# 알고리즘
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

# 함수자

std::function
mem_fn
reference_wrapper
bad_function_call
is_bind_expression  
is_placeholder
bind
ref
cref



# 문자열

# 동시성

<thread>
<mutex>
<condition_variable>
<atomic>
<future>

# 난수
<random>

# 컴파일 타임 산술
<ratio>

# 에러 핸들링
std::exception_ptr
std::error_code
std::error_condition
<system_error>

# 날짜 / 시간
<chrono>

# 정규 표현식
<regex>

# 로케일
<codecvt>

# C 라이브러리 지원
<cfenv>
<cinttype>
<cstdint>
<cuchar>

