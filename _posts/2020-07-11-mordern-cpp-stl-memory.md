---
layout: single
title: "#11. [모던 C++ STL] (C++11~) 동적 메모리 관리(pointer_traits, addressof(), align() 등)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# pointer_traits

포인터와 유사한 타입들을 다루는 표준화된 방법을 제공합니다.

|항목|내용|
|--|--|
|`pointer` (C++11~)|포인터 타입입니다.|
|`element_type` (C++11~)|포인터가 가리키는 값의 타입입니다.|
|`difference_type` (C++11~)|포인터끼리 뺀 값의 타입입니다.|
|`template<typename U> using rebind` (C++11~)|`U`에 바인딩되는 타입입니다.|
|`pointer pointer_to(element_type& r);` (C++11~)|`element_type`을 `pointer` 타입으로 변환합니다.|

# addressof()

`operator &()`가 오버로딩 되었어도 실제 주소를 리턴합니다.

```cpp
template<typename T>
T* addressof(T& arg) noexcept;
```

# align()

`space` 크기의 버퍼 공간에 `size`크기의 개체를 `alignment` 로 메모리 정렬했을때의 연속된 메모리 포인터인 `ptr`을 구합니다.

```cpp
void* align( 
    std::size_t alignment, // 메모리 정렬, alignof() 값
    std::size_t size, // 졍렬할 요소의 크기
    void*& ptr, // 최소 space 바이트의 연속된 메모리 포인터
    std::size_t& space // 작업할 버퍼의 최대 크기
);
```

# 할당자

기존에는 `allocator`만 있었으나([할당자(Allocator)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-allocator/) 참고), 좀더 많은 내용이 보강되었습니다.

|항목|내용|
|--|--|
|`allocator`|컨테이너 등 STL에서 개체 할당에 사용합니다.([할당자(Allocator)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-allocator/) 참고)|
|`allocator_traits` (C++11~)|(작성중)|
|`allocator_arg` (C++11~)|(작성중)|
|`uses_allocator` (C++11~)|(작성중)|
|`scoped_allocator_adaptor` (C++11~)|(작성중)|
|`polymorphic_allocator` (C++17)|(작성중)|
|`uses_allocator_construction_args` (C++20~)|(작성중)|
|`make_obj_using_allocator` (C++20~)|(작성중)|
|`uninitialized_construct_using_allocator` (C++20~)|(작성중)|
|`allocator_result` (C++23~)|(작성중)|

# 가비지 컬렉터 지원

|항목|내용|
|--|--|
|`declare_reachable()` (C++11~C++23)|(작성중)|
|`undeclare_reachable()` (C++11~C++23)|(작성중)|
|`declare_no_pointers()` (C++11~C++23)|(작성중)|
|`undeclare_no_pointers()` (C++11~C++23)|(작성중)|
|`pointer_safety` (C++11~C++23)|(작성중)|
|`get_pointer_safety()` (C++11~C++23)|(작성중)|

# 메모리 리소스

|항목|내용|
|--|--|
|`memory_resource` (C++17~)|(작성중)| 
|`new_delete_resource()` (C++17~)|(작성중)|
|`null_memory_resource()` (C++17~)|(작성중)|
|`get_default_resource()` (C++17~)|(작성중)|
|`set_default_resource()` (C++17~)|(작성중)| 
|`pool_options` (C++17~)|(작성중)|
|`synchronized_pool_resource` (C++17~)|(작성중)| 
|`unsynchronized_pool_resource` (C++17~)|(작성중)| 
|`monotonic_buffer_resource` (C++17~)|(작성중)| 

# 초기화 되지 않은 스토리지

|항목|내용|
|--|--|
|`raw_storage_iterator` (~C++17)|(작성중)|
|`get_temporary_buffer` (~C++17)|(작성중)|
|`return_temporary_buffer` (~C++17)|(작성중)|

# 메모리 관리

|항목|내용|
|--|--|
|`to_address()` (C++20~)|(작성중)|
|`assum_aligned()` (C++20)|(작성중)|

# 스마트 포인트 어뎁터

|항목|내용|
|--|--|
|`out_ptr_t`(C++23~)<br>`out_ptr()` (C++23)|(작성중)|
|`inout_ptr_t`(C++23~)<br>`inout_ptr()` (C++23)|(작성중)|

# 명시적 수명 관리

|항목|내용|
|--|--|
|`start_lifetime_as` (C++23~)<br/>`start_lifetime_as_array` (C++23~)|(작성중)|
