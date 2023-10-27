---
layout: single
title: "#13. [고전 C++ STL] 할당자(Allocator)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * (C++17~) [polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/)는 런타임 다형성을 지원하는 할당자입니다. [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 사용하여 메모리 풀을 손쉽게 만들 수 있습니다.

# 개요

컨테이너의 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 보면 `Allocator`를 사용하는 버전이 있습니다.

```cpp
template<typename T, typename Allocator = allocator<T>>
class vector {
    ...
};
```

할당자(`Allocator`)는 컨테이너의 메모리 할당을 직접 제어하여 성능을 향상시키는 도구입니다.([개체 생성과 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/) 참고)


# 구현 방법

할당자는 다음 주요 함수들을 구현해야 합니다.

|항목|내용|
|--|--|
|`allocate()`|`n`개 만큼 저장할 수 있는 메모리 영역을 할당합니다.|
|`deallocate()`|`allocate()`에서 할당한 메모리를 해제합니다.|
|`construct()`|지정한 메모리 위치에 요소 개체의 생성자를 호출합니다. ([위치 지정 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1) 참고)|
|`destroy()`|지정한 메모리 위치에 요소 개체의 소멸자를 호출합니다.([위치 지정 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1) 참고)|
|`rebind`|요소 타입에 대한 할당자를 이용하여 다른 타입용 컨테이너를 할당할 수 있게 합니다.|

다음은 간단한 `Allocator` 구현 예입니다.

```cpp
template<typename T>
class MyAllocator {
public:
    typedef size_t    size_type;
    typedef ptrdiff_t difference_type;
    typedef T*        pointer;
    typedef const T*  const_pointer;
    typedef T&        reference;
    typedef const T&  const_reference;
    typedef T         value_type;

    MyAllocator() {}
    MyAllocator(const MyAllocator&) {}

    // T를 n개 만큼 저장할 수 있는 메모리 영역을 할당합니다.
    pointer allocate(size_type n, const void * = 0) {
        T* t = (T*) malloc(n * sizeof(T));
        std::cout << "MyAllocator::allocate()" << std::endl;
        return t;
    }

    // allocate에서 할당한 메모리를 해제합니다.
    void deallocate(void* p, size_type) {
        free(p);
        std::cout << "MyAllocator::deallocate()" << std::endl;
    }

    pointer address(reference x) const {return &x;}
    const_pointer address(const_reference x) const {return &x;}
    MyAllocator<T>& operator =(const MyAllocator&) {return *this;}

    // p 메모리 위치에 val을 복사 생성합니다. 
    void construct(pointer p, const T& val) { 
        new (p) T(val);
    }
    // p 메모리 위치의 T 개체의 소멸자를 호출합니다.
    void destroy(pointer p) {
        p->~T();
    }

    // 지원되는 최대 할당 크기를 리턴합니다.
    size_type max_size() const {
        return std::numeric_limits<size_t>::max();
    }

    // T 타입에 대한 할당자(another)를 이용하여
    // 다른 타입인 U 타입용 컨테이너를 할당할 수 있게 합니다.
    // T::rebind<U>::other(another).allocate(10, static_cast<U*>(0));
    template<class U>
    struct rebind {typedef MyAllocator<U> other;};

    template<class U>
    MyAllocator(const MyAllocator<U>&) {}

    template<class U>
    MyAllocator& operator =(const MyAllocator<U>&) {return *this;}
};

class A {
public:
    A() {
        std::cout << "A()" << std::endl;
    }
    A(const A& other) {
        std::cout << "A(const A& other)" << std::endl;        
    }
    ~A() {
        std::cout << "~A()" << std::endl;     
    }
};

std::vector<A, MyAllocator<A>> v;

v.push_back(A()); 
```

실행 결과는 다음과 같습니다.

```cpp
A() // push_back() 의 인수인 A() 생성
MyAllocator::allocate() // 할당자로 컨테이너 메모리 영역 생성
A(const A& other) // 할당자의 construct()를 호출하여 할당한 메모리 영역에 A 복사 생성
~A() // push_back()시 임시로 생성한 개체 소멸
~A() // 할당자의 destroy()를 호출하여 할당한 메모리 영역의 개체 소멸
MyAllocator::deallocate() // 컨테이너 메모리 영역 해제
```

