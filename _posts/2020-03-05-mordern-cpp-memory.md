---
layout: single
title: "#35. [모던 C++] (C++11~) std::pointer_traits, std::addressof(), std::align()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# std::pointer_traits

포인터와 유사한 유형의 특정 속성에 접근하는 표준화된 방법을 제공합니다.

|항목|내용|
|--|--|
|`pointer`|포인터 타입|
|`element_type`|포인터가 가리키는 값의 타입|
|`difference_type`|포인터끼리 뺀 값의 타입|
|`template<typename U> using rebind`|U에 바인딩되는 타입|
|`pointer pointer_to(element_type& r);`|`element_type`을 `pointer` 타입으로 변환합니다.|

# std::addressof()

`operator &()`가 오버로딩 되었어도 실제 주소를 리턴합니다.

```cpp
template<typename T>
T* addressof(T& arg) noexcept;
```

# std::align()

`space` 크기의 버퍼 공간에 `size`크기의 개체를 `alignment` 로 메모리 정렬했을때의 연속된 메모리 포인터인 `ptr`을 구합니다.

```cpp
void* align( 
    std::size_t alignment, // 메모리 정렬, alignof() 값
    std::size_t size, // 졍렬할 요소의 크기
    void*& ptr, // 최소 space 바이트의 연속된 메모리 포인터
    std::size_t& space // 작업할 버퍼의 최대 크기
);
```