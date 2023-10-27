---
layout: single
title: "#18. [모던 C++ STL] (C++11~) 동적 메모리 관리"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# C스타일 메모리 관리

|항목|내용|
|--|--|
|[malloc()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|메모리를 할당합니다.|
|`calloc()`|(작성중)|
|`realloc()`|(작성중)|
|[free()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|메모리를 해제합니다.|
|`aligned_alloc()` (C++17~)|정렬된 메모리를 할당합니다.|

# 저수준 메모리 관리

|항목|내용|
|--|--|
|[operator new, operator new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|개체나 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 할당합니다.|
|[operator delete, operator delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|개체나 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 해제합니다.|
|`get_new_handler()` (C++11~)|(작성중)|
|[set_new_handler()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)|`new`에서 예외 발생시 호출되는 함수 입니다.|
|[bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|메모리 할당에 실패했을때 발생합니다.|
|`bad_array_new_length`|(작성중)|
|`align_val_t` (C++17~)|(작성중)|
|[new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)|`new`에서 예외 발생시 호출되는 함수 입니다.|
|[nothrow](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#newnothrow-%EC%99%80-%EB%AC%B4%EC%9D%98%EB%AF%B8%ED%95%9C-%EB%84%90%EA%B2%80%EC%82%AC)|예외 발생 대신 널을 리턴하는 [new(nothrow)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#newnothrow-%EC%99%80-%EB%AC%B4%EC%9D%98%EB%AF%B8%ED%95%9C-%EB%84%90%EA%B2%80%EC%82%AC)에서 사용합니다.|
|`destroying_delete` (C++20~)|(작성중)|
|`launder()` (C++17~)|[위치 지정 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)으로 생성된 개체의 합법적인 포인터를 얻습니다.|

```cpp
class A {
public: 
    const int m_X;  // const 멤버 입니다. 
    int m_Y;
    A(int x, int y) : m_X{x}, m_Y{y} {}
};

A* ptr = new A{1, 2};
EXPECT_TRUE(ptr->m_X == 1 && ptr->m_Y == 2);

// 위치 지정 생성으로 ptr 위치에 다시 생성합니다.
A* newPtr = new(ptr) A{3, 4};
EXPECT_TRUE(newPtr->m_X == 3 && newPtr->m_Y == 4);

// 이전 포인터인 ptr로 다루려면, launder를 사용합니다.
EXPECT_TRUE((ptr->m_X == 1 || ptr->m_X == 3)); // (△) 비권장. 컴파일러 최적화에 의해 const 변수는 이전값 1을 가질 수도 있습니다.
EXPECT_TRUE(ptr->m_Y == 4); // (△) 비권장. 불법입니다만, 잘 동작합니다.
EXPECT_TRUE(std::launder(ptr)->m_X); // launder를 이용하여 합법적으로 사용할 수 있습니다.

delete std::launder(ptr);
```

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
|[allocator](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-allocator/)|컨테이너 등 STL에서 개체 할당에 사용합니다.|
|`allocator_traits` (C++11~)|(작성중)|
|`allocator_arg` (C++11~)|(작성중)|
|`uses_allocator` (C++11~)|(작성중)|
|`scoped_allocator_adaptor` (C++11~)|(작성중)|
|[polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/) (C++17~)|런타임 다형성을 지원하는 할당자입니다. [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 사용하여 메모리 풀을 손쉽게 만들 수 있습니다.|
|`uses_allocator_construction_args` (C++20~)|(작성중)|
|`make_obj_using_allocator` (C++20~)|(작성중)|
|`uninitialized_construct_using_allocator` (C++20~)|(작성중)|
|`allocator_result` (C++23~)|(작성중)|

# 메모리 유틸리티 작업

C++17 부터는 [uninitialized_move(), uninitialized_default_construct(), uninitialized_value_construct(), destroy(), destroy_at()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0-%EC%9E%91%EC%97%85) 함수를 추가하여 [위치 지정 생성자 호출과 소멸자 호출](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)의 새로운 방법을 제공합니다.

|항목|내용|
|--|--|
|`uninitialized_copy()`<br/>`uninitialized_copy_n()` (C++11~)|메모리를 복사합니다.|
|`uninitialized_fill()`<br/>`uninitialized_fill_n()`|메모리를 특정 값으로 채웁니다.|
|`uninitialized_move()` (C++17~)<br/>`uninitialized_move_n()` (C++17~)|메모리의 값을 이동시킵니다.|
|`uninitialized_default_construct()` (C++17~)<br/>`uninitialized_default_construct_n()` (C++17~)|주어진 메모리 영역 개체들을 기본 생성자(`new T`)로 초기화 합니다. [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 하지 않습니다.| 
|`uninitialized_value_construct()` (C++17~)<br/>`uninitialized_value_construct_n()` (C++17~)|주어진 메모리 영역 개체들을 값 생성자(`new T()`)로 초기화 합니다. [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 합니다.| 
|`destroy()` (C++17~)<br/>`destroy_n()` (C++17~)|주어진 메모리 영역 개체들의 소멸자를 호출합니다.| 
|`destroy_at()` (C++17~)|주어진 메모리 영역 개체의 소멸자를 호출합니다.| 
|`construct_at()` (C++20~)|주어진 메모리 영역 개체의 생성자를 호출합니다.| 

다음 예는 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 `data` 버퍼을 생성하고 3개의 `T` 개체를 생성하고 소멸시키는 예입니다.
```cpp
class T {
private:
    int m_X;
    int m_Y;
public:
    T() : 
        m_X{10}, 
        m_Y{20} {
        std::cout << "T : Constructor" << std::endl;
    }    
    ~T() {
        std::cout << "T : Destructor" << std::endl;
    }
    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};

unsigned char data[sizeof(T) * 3]; // T 개체 3개를 저장할 수 있는 스택 영역을 확보합니다.

auto begin{reinterpret_cast<T*>(data)};
auto end{begin + 3}; 

// 요소 3개의 생성자 호출
std::uninitialized_default_construct(begin, end);
EXPECT_TRUE((begin + 0)->GetX() == 10 && (begin + 0)->GetY() == 20);
EXPECT_TRUE((begin + 1)->GetX() == 10 && (begin + 1)->GetY() == 20);
EXPECT_TRUE((begin + 2)->GetX() == 10 && (begin + 2)->GetY() == 20);

// 요소 3개의 소멸자 호출
std::destroy(begin, end);
```

실행 결과를 보면 생성자 3회, 소멸자 3회 호출된 것을 알 수 있습니다.

```cpp
T : Constructor
T : Constructor
T : Constructor
T : Destructor
T : Destructor
T : Destructor
```
# 가비지 컬렉터 지원

|항목|내용|
|--|--|
|`declare_reachable()` (C++11~C++23)|(작성중)|
|`undeclare_reachable()` (C++11~C++23)|(작성중)|
|`declare_no_pointers()` (C++11~C++23)|(작성중)|
|`undeclare_no_pointers()` (C++11~C++23)|(작성중)|
|`pointer_safety` (C++11~C++23)|(작성중)|
|`get_pointer_safety()` (C++11~C++23)|(작성중)|

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
|`assume_aligned()` (C++20)|(작성중)|

# 스마트 포인트 어뎁터

|항목|내용|
|--|--|
|`out_ptr_t`(C++23~)<br>`out_ptr()` (C++23~)|(작성중)|
|`inout_ptr_t`(C++23~)<br>`inout_ptr()` (C++23~)|(작성중)|

# 명시적 수명 관리

|항목|내용|
|--|--|
|`start_lifetime_as` (C++23~)<br/>`start_lifetime_as_array` (C++23~)|(작성중)|
