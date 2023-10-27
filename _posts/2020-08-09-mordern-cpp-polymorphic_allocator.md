---
layout: single
title: "#39. [모던 C++ STL] (C++17~) polymorphic_allocator 와 메모리 리소스"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/)는 런타임 다형성을 지원하는 할당자입니다. [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 사용하여 메모리 풀을 손쉽게 만들 수 있습니다.

# 개요

[polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/)는 런타임 다형성을 지원하는 할당자로서 [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 이용합니다.

`<memory_resource>` 헤더 파일을 포함해야 하며, `std::pmr` [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 사용합니다.

다음은 스택에 100byte 메모리 풀을 사용하고, [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)요소 생성시 해당 메모리 풀에 생성하는 예입니다. 실질적인 메모리 할당과 해제를 한번에 하기 때문에 속도 효율적입니다.

```cpp
unsigned char data[100]; // 100 byte 공간을 할당합니다.
std::pmr::monotonic_buffer_resource pool{data, sizeof(data)}; // 모든 요소가 소멸되면 메모리를 한번에 해제합니다.
std::pmr::polymorphic_allocator<int> pa{&pool};

std::vector<int, std::pmr::polymorphic_allocator<int>> v(pa); 

v.push_back(1); 
v.push_back(2);
EXPECT_TRUE(v[0] == 1 && v[1] == 2); 
```

# 메모리 리소스

|항목|내용|
|--|--|
|`memory_resource` (C++17~)|메모리 리소스의 추상 클래스입니다.| 
|`synchronized_pool_resource` (C++17~)|쓰레드에 안전한 메모리 풀입니다.| 
|`unsynchronized_pool_resource` (C++17~)|쓰레드에 안전하지 않은 메모리 풀입니다.| 
|`monotonic_buffer_resource` (C++17~)|자원이 소멸된 경우에만 메모리를 해제하는 특수 목적 메모리 풀입니다.| 

|항목|내용|
|--|--|
|`new_delete_resource()` (C++17~)|전역 `new`, `delete`를 사용하는 메모리 리소스를 리턴합니다.|
|`null_memory_resource()` (C++17~)|메모리 할당을 안하는 메모리 리소스를 리턴합니다.|
|`get_default_resource()` (C++17~)|기본 메모리 리소스를 구합니다.|
|`set_default_resource()` (C++17~)|기본 메모리 리소스를 설정합니다.| 
|`pool_options` (C++17~)|풀 생성시 사용하는 옵션입니다.|