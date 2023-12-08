---
layout: single
title: "#5. [고전 C++ 가이드] 개체 생성(new)과 소멸(delete)"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar:
    nav: "docs"
---

> * 개체는 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 쌍으로 생성/소멸 하라.
> * [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 쌍으로 생성/소멸하라. [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)한 것을 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 만 하면, 메모리 릭이 발생한다. 꼭 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하라.
> * [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [널 값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)이면 아무 작업 안한다. 괜히 널검사하지 마라.
> * [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킨다. 괜히 널검사하지 마라.
> * [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 `private`로 만들어 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에만 생성되는 개체를 만들 수 있다.

> **모던 C++**
> * (C++17~) [uninitialized_move(), uninitialized_default_construct(), uninitialized_value_construct(), destroy(), destroy_at()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0-%EC%9E%91%EC%97%85) 함수를 추가하여 [위치 지정 생성자 호출과 소멸자 호출](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)의 새로운 방법을 제공합니다.
> * (C++17~) [polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/)는 런타임 [다형성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-polymorphism/)을 지원하는 [할당자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%ED%95%A0%EB%8B%B9%EC%9E%90)입니다. [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 사용하여 [메모리 풀](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%ED%92%80)을 손쉽게 만들 수 있습니다.

# 개요

개체의 생성과 소멸은 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)와 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)을 사용하고, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 생성과 소멸은 [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)와 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 사용합니다. 그리고, 메모리 할당시에 실패하면 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)을 리턴하고요. 이정도만 알면 별 무리없이 C++을 사용할 수 있습니다.

하지만, 메모리를 좀더 알뜰하게 쓰거나, 메모리 할당 실패시 무언가 더 시도를 해서 어떻게든 동작하게 만드려면, [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)와 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 좀더 세부적으로 통제해야 하고, 그러기 위해선, 좀 난해하긴 합니다만, [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98), [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98), [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1), [set_new_handler()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)를 이해해야 합니다.

|항목|내용|
|--|--|
|[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|개체의 메모리를 할당하고, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 호출합니다.|
|[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출하고, 메모리를 해제합니다.|
|[new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 할당하고, 각 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소의 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)들을 호출합니다.|
|[delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|각 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)들을 호출하고, 메모리를 해제합니다.|
|[new(std::nothrow)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#newnothrow-%EC%99%80-%EB%AC%B4%EC%9D%98%EB%AF%B8%ED%95%9C-%EB%84%90%EA%B2%80%EC%82%AC)|메모리 할당 실패시 [널 값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)을 리턴합니다. 단, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)에서 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면 해당 [예외를 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)합니다.|
[operator new(std::size_t)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|개체의 메모리를 할당합니다.|
|[operator delete(void*)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|개체의 메모리를 해제 합니다.|
|[`operator new[](std::size_t)`](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98-1)|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 할당합니다.|
|[`operator delete[](void*)`](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98-1)|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 해제합니다.|
|[operator new(size_t, void*)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)|주어진 메모리 위치에 개체를 배치하여 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 호출합니다.(*[위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)*)|
|[set_new_handler()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)|[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)에서 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시 호출되는 함수입니다.|

개체를 동적으로 생성/소멸할때(*[힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 할당됩니다.*) [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)와 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 사용합니다.

**new의 세부 동작**

[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 다음의 순서로 개체의 메모리 할당과 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 호출을 실행합니다.(*[생성/소멸 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8-%EC%97%B0%EC%82%B0%EC%9E%90) 언급*)

1. 전역 [operator new(std::size_t)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)로 메모리 공간 할당(*오류 발생시 [set_new_handler()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)에 설정한 [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC) 실행*)
2. 구조체이거나 클래스면 [operator new(size_t, void*)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)(*[위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)*)를 실행하여 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 호출
3. 메모리 주소를 해당 타입으로 형변환하여 리턴

즉, [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 메모리 할당, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 호출, 메모리 주소를 형변환해서 리턴하지만,
[operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)는 `void*` 형 메모리를 할당하기만 합니다.

**delete의 세부동작**

[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 다음의 순서로 개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출과 메모리 해제를 실행합니다.(*[개체 소멸 순서](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EA%B0%9C%EC%B2%B4-%EC%86%8C%EB%A9%B8-%EC%88%9C%EC%84%9C) 참고*)

1. 개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출
2. 개체의 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 소멸
3. 전역 [operator delete(void*)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)로 메모리 할당 해제

즉, [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출, 메모리 해제를 수행하지만,
[operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)는 메모리 해제만 수행합니다.

# 개체 생성/소멸

개체 생성/소멸 시에는 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 이용합니다. 생성된 개체는 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 할당됩니다.

```cpp
class T {
public:
    T() {
        std::cout << "New-Delete Test : T::T()" << std::endl;
    }
    ~T() {
        std::cout << "New-Delete Test : T::~T()" << std::endl;
    }
};

T* t = new T; // (O) 메모리 할당. 생성자 호출
delete t; // (O) 소멸자 호출. 메모리 해제
```

실행 결과는 다음과 같습니다.

```cpp
New-Delete Test : T::T() // 생성자 호출
New-Delete Test : T::~T() // 소멸자 호출
```

# 배열 생성/소멸

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 생성/소멸 시에는 [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 이용합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete[] arr; // (O) 소멸자 3회 호출. 메모리 해제(sizeof(T) * 3 + 오버헤드)
```

로 하면 실행 결과는 다음과 같이 [배열 요소의 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98) 만큼 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출됩니다.

```cpp
New-Delete Test : T::T() // 생성자 3회 호출
New-Delete Test : T::T()
New-Delete Test : T::T()
New-Delete Test : T::~T() // 소멸자 3회 호출
New-Delete Test : T::~T()
New-Delete Test : T::~T()
```

# delete와 delete[] 의 차이

[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 개체 1개의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 1회 호출한뒤 주어진 메모리를 해제하는 역할을 하고, [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)만큼 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)들을 호출한뒤 주어진 메모리를 해제하는 역할을 합니다.

그러다 보니 [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 생성할때 [배열 요소의 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)가 내부적으로 오버헤드 공간에 저장되고, 이를 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)시에 사용합니다.(*[operator new[]와 operator delete[] 재정의](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98-1) 참고*)

1. `int`등 기본 자료형일 경우, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출할 필요가 없으므로, 메모리가 `sizeof(int)` * [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)로 할당됩니다.
2. 클래스인 경우 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98) 만큼 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출되어야 하므로, 내부적으로 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)를 저장하는 오버헤드 공간이 추가되어 할당됩니다.(*`sizeof(T)` * [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98) + 오버헤드(4byte 이거나 8byte)*)

혹여나 실수로 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸시키면, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 1회만 호출되고, 프로그램이 다운될 수 있습니다. 따라서, [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 생성한 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 꼭 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸시켜야 합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete arr; // (X) 예외 발생. 소멸자가 1회만 호출되고, 프로그램이 다운될 수 있음
```

# delete 여러번 호출 금지

[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 여러번 실행하면 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다. 생성한 개체는 1회만 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)해야 합니다.

```cpp
class T {};
T* p = NULL;
delete p; // (O) p가 NULL이어도 안전합니다.
p = new T; 
delete p; // (O) new로 생성한 것은 반드시 delete 해야 합니다.
delete p; // (X) 예외 발생. 두번 죽일 순 없습니다.  
```

# delete NULL

[널 값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)을 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하면 아무 동작 안합니다. 따라서 다음과 같이 `if`검사를 할 필요가 없습니다. 그냥 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 하시면 됩니다.

```cpp
if (p != NULL) { // (△) 비권장. 괜히 널검사합니다.
    delete p;
}

delete p; // (O) 널검사 없이 바로 delete 합니다.
```

# new(nothrow) 와 무의미한 널검사

[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 메모리가 부족하여 할당에 실패하면, [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킵니다.(*C++98이후부터요.*)

C++98 이전에는 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)가 메모리 할당에 실패하면 [널 값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)을 리턴하기 때문에 다음과 같이 검사 했는데요,

```cpp
T* t = new T;
if (t == NULL) {
    ...
}
```

C++98 이후부터는 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키므로 하기와 같이 검사해야 합니다.

```cpp
try {
    T* t = new T;
}
catch (std::bad_alloc&) {
    // 할당에 실패했을때의 코드
}
```

이전 스타일로 널검사를 하고 싶다면 `new(std::nothrow)`을 사용할 수 있습니다. 

```cpp
class T {};
T* t = new(std::nothrow) T; // (△) 비권장. 메모리 할당에 실패하면 널을 리턴하지만, 생성자에서 예외를 발생시키면 전파됩니다.

if (t == NULL) {
    // 할당 실패시 처리할 코드
}
```

그런데, 메모리 할당에 실패하면 [널 값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)을 리턴하지만, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)에서 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키면 [예외가 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)됩니다. 

```cpp
class T {
public:
    T() {
        throw "My Error"; // 생성자에서 예외를 발생시킵니다.
    }
};

try {
    T* t = new(std::nothrow) T; // (△) 비권장. 메모리 할당에 실패하면 널을 리턴하지만, 생성자에서 예외를 발생시키면 전파됩니다.
    if (t == NULL) {
        // 할당 실패시 처리할 코드
    }
}
catch (const char* e) {
    // 생성자에서 예외를 발생할 경우 처리할 코드
    std::cout << e << std::endl; // My Error 출력
}
```

따라서 어짜피 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 사용할 수 밖에 없기 때문에 그냥 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 사용하고 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 사용하는게 좋습니다.

```cpp
class T {
public:
    T() {
        throw "My Error"; // 생성자에서 예외를 발생시킵니다.
    }
};

try {
    T* t = new T; 
}
catch (std::bad_alloc& e) {
    // 할당 실패시 처리할 코드
}
catch (const char* e) {
    // 생성자에서 예외를 발생할 경우 처리할 코드
    std::cout << e << std::endl; // My Error 출력
}
```

# operator new와 operator delete 재정의

개체의 메모리를 할당하고 해제하는 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)와 [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)는 사용자가 재정의 할 수 있습니다.

주로 다음을 위해 사용합니다.

1. 데이터 오버런(overrun) 및 언더런(underrun) 등 잘못된  [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 사용을 탐지하기 위해 탐지용 byte를 추가로 할당하는 경우(*[진단](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-diagnostics/) 참고*)
2. 할당 및 해제의 효율을 향상시키기 위해 **동적 메모리 오버헤드**를 줄이고 메모리 관리를 직접 수행하기 위한 [메모리 풀](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%ED%92%80)을 만드는 경우(*[polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/) 참고*)

    > *(C++17~) [polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/)는 런타임 [다형성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-polymorphism/)을 지원하는 [할당자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%ED%95%A0%EB%8B%B9%EC%9E%90)입니다. [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 사용하여 [메모리 풀](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%ED%92%80)을 손쉽게 만들 수 있습니다.*
3. 동적 할당 메모리의 실제 사용에 관한 통계 정보를 수집하는 경우(*[개체 수명 로그](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-object-life-log/) 참고*)

**동적 메모리 오버헤드**

우리가 동적 메모리를 할당하면, 나중에 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 할때 얼마만큼의 메모리를 해제해야 할지 알아야 하기 때문에, 요청한 크기의 메모리보다 더 많은 오버헤드 공간을 사용합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9f03346e-1457-4b92-90db-52d18e467086)

컴파일러나 시스템마다 그 크기가 다를 수 있으며, 보통 4byte 이거나 8byte의 추가 오버헤드 공간을 사용합니다.

따라서, 크기가 작은 개체를 아주 많이 동적 생성을 해야 한다면, 메모리 공간을 효율적으로 사용하기 위해 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)의 재정의가 필요할 수 있습니다.


**기본 재정의 방법**

전역 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) - 전역 [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 이용하거나 [malloc()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC) - [free()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC)를 이용할 수 있습니다. 

[operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 재정의하려면 다음 3가지를 준수하여야 합니다.

1. 최소 1byte를 할당해야 합니다.
2. 메모리 할당 실패시 [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)를 호출해야 합니다. [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)가 없다면 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜야 합니다.(*이덕에 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)가 메모리 할당에 실패하면 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킵니다.*)
3. [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)에서 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) 또는 이로부터 파생된 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키거나, 프로그램을 종료할 때까지 반복해야 합니다. 

전역 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 이용하면 상기 내용을 준수하므로 다음과 같이 전역 버전을 호출하면 됩니다.

```cpp
class T {
public:
    static void* operator new(std::size_t sz) {
        return ::operator new(sz); // 전역 operator new
    }
    static void operator delete(void* ptr) {
        ::operator delete(ptr); // 전역 operator delete
    }
};
```

[malloc()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC) - [free()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC)를 이용한다면, 

1. 최소 1byte를 할당해 주어야 하고,
2. [malloc()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC)에서는 오류 발생시 `NULL`을 리턴하므로, [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC) 와 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) 처리를 해주어야 합니다.


다음 코드에서,

1. #1 : `static void* operator new(std::size_t sz)`에 전달되는 크기는 오버헤드 공간을 제외한 크기이며, [malloc()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC)은 오버헤드 공간을 제외한 크기를 전달받아 오버헤드 공간을 포함한 영역을 할당합니다. 이후 [free()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC)에서 오버헤드 공간의 정보를 이용하여 메모리를 해제합니다.

2. #2 : [set_new_handler()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC) 함수에 대응하는 `get_new_handler()` 함수는 없습니다. 그래서 `handler = std::set_new_handler(NULL);` 과 같이 호출하여, [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)를 구하고, 이를 다시 복원해야 합니다.(*개체 지향스럽고, [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/)이 되는 방법은 [NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/)를 참고하세요.*)

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T() :
        m_X(10),
        m_Y(20) {}
    T(int x, int y) :
        m_X(x),
        m_Y(y) {}
    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}

    // sz는 오버헤드 공간을 제외한 크기가 전달됩니다. 
    static void* operator new(std::size_t sz) { // #1
        // 혹시 모르니 검사하여 최소 1byte로 만듬    
        if (sz == 0) { 
            ++sz;
        }

        // 핸들러가 예외를 발생시키거나 프로그램을 종료할 때까지 반복
        while (true) {
            void* ptr = malloc(sz); // #1. 오버헤드 공간을 포함하여 할당합니다.
            if (ptr != NULL) {
                return ptr; // 성공적으로 할당했다면 리턴
            }

            std::new_handler handler = std::set_new_handler(NULL); // #2. 대충 NULL을 세팅하고 핸들러를 구합니다.
            std::set_new_handler(handler); // #2. 핸들러 복원
            // 핸들러가 있다면 실행
            if (handler != NULL) {
                handler(); // 핸들러가 발생시킨 예외 전파
            }
            // 핸들러가 없다면 std::bad_alloc
            else {
                throw std::bad_alloc();
            }
        }
        return NULL;
    }
    static void operator delete(void* ptr) {
        free(ptr); // ptr == NULL 일 경우 아무 작업 안함
    }
};
T* t = new T; // T::operator new(std::size_t sz), 기본 생성자 호출
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);
delete t; // T::operator delete(void* ptr)

t = new T(1, 2); // T::operator new(std::size_t sz), T::T(int, int) 생성자 호출
EXPECT_TRUE(t->GetX() == 1 && t->GetY() == 2);
delete t; // T::operator delete(void* ptr)
```

**operator new 오버로딩**

[operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)의 경우 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 추가하여 다양한 사용자 정의 버전을 만들 수 있습니다.

```cpp
static void* operator new(std::size_t sz, Param1 param1, Param2 param2 ...);
```

다음과 같이 `int val` 등 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 추가된 버전을 만들 수 있고,

```cpp
static void* operator new(std::size_t sz, int val) { // int val을 인자로 전달받습니다.
    return ::operator new(sz);
}
```

해당 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 인수(*예제에서는 그냥 `1`*)를 전달하여 `new(1)`과 같이 호출할 수 있습니다.

```cpp
T* t = new(1) T; // T::operator new(std::size_t sz, int val), 기본 생성자 호출
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);
delete t;

t = new(1) T(1, 2); // T::operator new(std::size_t sz, int val), T::T(int, int) 생성자 호출
EXPECT_TRUE(t->GetX() == 1 && t->GetY() == 2);
delete t;
```

[operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)의 오버로딩은 주로 디버그 모드에서 파일명과 라인 번호를 확인하기 위해 사용합니다.

다음 코드에서,

1. #1 : `g_NewInfoMap`에 파일명과 라인 번호를 저장합니다.
2. #2 : `T` 개체를 생성할때 `g_NewInfoMap`에 정보를 추가합니다.
3. #3 : `T` 개체를 소멸할때 `g_NewInfoMap`에서 정보를 제거합니다.
4. #4 : `MY_DEBUG` 모드에서는 `new`를 호출했을때 `new(__FILE__, __LINE__)`가 호출되도록 합니다.
5. #5 : 프로그램 종료시 `g_NewInfoMap`에 무언가가 남아 있다면 메모리 릭입니다.

```cpp
#define MY_DEBUG

#ifdef MY_DEBUG
// MY_DEBUG 가 정의된 경우만 사용하는 new 관련 정보입니다.
class NewInfo { 
public:
    std::string m_Filename; // 파일명
    int m_Line; // 라인 번호
    NewInfo(const char* filename, int line) : m_Filename(filename), m_Line(line) {}
};
std::map<void*, NewInfo> g_NewInfoMap; // #1

#endif 

class T {
public:
#ifdef MY_DEBUG // MY_DEBUG 모드에서는 new 할때 g_NewInfoMap에 파일명과 라인 번호를 저장하고 delete시 지웁니다.        
    // 눈을 어지럽힐 수 있으니, 예외처리 부분은 생략했습니다.
    static void* operator new(size_t sz, const char* filename, int line) {
        void* ptr = ::operator new(sz);

        g_NewInfoMap.insert( // #2
            std::make_pair(
                ptr, // 할당한 영역을 키로하여
                NewInfo(filename, line) // new 시의 파일명, 라인 번호 저장
            )
        );
        return ptr;
    }
    static void operator delete(void* ptr) {
        
        std::map<void*, NewInfo>::iterator result = g_NewInfoMap.find(ptr); // #3
        if (result != g_NewInfoMap.end()) {
            g_NewInfoMap.erase(result); // 찾았다면 삭제
        }
        ::operator delete(ptr);
    }
#endif
};

#ifdef MY_DEBUG
#define new new(__FILE__, __LINE__) // #4. MY_DEBUG 모드에서는 new를 new(__FILE__, __LINE__)로 대체합니다.
#endif

T* ptr1 = new T; // MY_DEBUG 모드에서는 new(__FILE__, __LINE__) 입니다.
delete ptr1;

T* ptr2 = new T; // MY_DEBUG 모드에서는 new(__FILE__, __LINE__) 입니다.
// delete ptr2; // 테스트를 위해 의도적으로 delete를 생략합니다.

#ifdef MY_DEBUG
EXPECT_TRUE(g_NewInfoMap.size() == 1); // #5. 무언가가 남아 있다면 메모리 릭입니다.
#endif

#undef MY_DEBUG
```

**operator delete 오버로딩**

[operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)는 두가지 유형이 있습니다. 두개를 모두 정의하더라도, 둘중에 하나만 사용됩니다. 

다음 코드의 경우

1. `operator delete(void* ptr)` 가 사용되며, 
2. `operator delete(void* ptr, std::size_t sz)`를 사용하려면, `operator delete(void* ptr, std::size_t sz)` 만 정의

해야 합니다.

```cpp
class T {
public:
    static void* operator new(std::size_t sz) {
        return ::operator new(sz);
    }    
    static void operator delete(void* ptr) {
        std::cout << "T::delete(void* ptr)" << std::endl;
        ::operator delete(ptr); 
    }
    // sz : 해제할 byte 수                     
    static void operator delete(void* ptr, std::size_t sz) {
        std::cout << "delete(void* ptr, std::size_t sz)" << std::endl;
        ::operator delete(ptr); 
    }            
}; 

T* t = new T; 
delete t; // T::delete(void* ptr) 호출
```

**부모 클래스의 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98), [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)**

자식 클래스에서 특별히 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)와 [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 정의하지 않으면, 부모 클래스 것을 사용합니다.

```cpp
// sizeof(Base) == sizeof(int) * 2 + 가상 함수 테이블 크기
class Base { 
    int m_X;
    int m_Y;
public:
    virtual ~Base() {} // virtual 입니다.

    // sz : sizeof(Base) 또는 sizeof(Derived) 크기
    static void* operator new(std::size_t sz) {
        return ::operator new(sz);
    }
    // sz : 소멸자가 virtual이어서 sizeof(Base) 또는 sizeof(Derived) 크기
    static void operator delete(void* ptr, std::size_t sz) {
        ::operator delete(ptr); 
    } 
};

// sizeof(Derived) == sizeof(Base) + sizeof(int) * 2
class Derived : public Base {
    int m_A;
    int m_B;
public:
    virtual ~Derived() {}         
};

Base* base = new Derived; // Base::operator new(std::size_t sz) 호출
delete base; // Base::delete(void* ptr, std::size_t sz) 호출
```

이에 모든 자식 클래스에서 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98), [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 일일이 구현하지 말고, 부모 클래스(`Base`)에서 자식 클래스(`Derived`)를 고려해서 구현하는게 좋습니다.(*고려라고 해봐야 인수 `sz`에 전달된 만큼 메모리를 할당/해제하는 것이니 특별히 할 건 없죠.*)

# virtual 소멸자인 경우 [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)

[다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 지원하려면, 꼭 [가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용해야 한다고 언급한바 있습니다. 그렇지 않으면 `Base` [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)만 불려서 `Derived`의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 불리지 않아, 메모릭 릭이 발생할 수 있다고요.

[operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)에서도 마찬가지 입니다. 자식 클래스에서 할당된 메모리를 모두 해제하려면 꼭 [가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하셔야 합니다.

다음 코드는 상기 코드에서 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)의 `virtual`만 뺀 코드입니다. 실행시켜 확인해 보면, 

1. [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) 시에는 `Derived`의 크기인 16byte가 전달되나, 
2. [operator delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)시에는 `Base`의 크기인 8byte가 전달되어, 
   
메모리의 일부만 해제되어 메모리 릭이 발생합니다. 그러니, [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 하려면 꼭 [가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하세요.

```cpp
// sizeof(Base) == sizeof(int) * 2 = 8byte
class Base { 
    int m_X;
    int m_Y;
public:
    ~Base() {} // (X) 오동작. virtual이 아닙니다.

    // sizeof(Derived) 크기 : 16byte
    static void* operator new(std::size_t sz) {
        return ::operator new(sz);
    }
    // (X) 오동작. 소멸자가 virtual이 아니어서 Base의 크기인 8byte만 전달됩니다. 메모리 릭 발생
    static void operator delete(void* ptr, std::size_t sz) {
        ::operator delete(ptr); 
    } 
};

// sizeof(Derived) == sizeof(Base) + sizeof(int) * 2 = 16byte
class Derived : public Base {
    int m_A;
    int m_B;
public:
    ~Derived() {}         
};

Base* base = new Derived; // Base::operator new(std::size_t sz) 호출
delete base; // (X) 오동작. Base의 소멸자가 호출되고 Base의 크기가 전달됨. Base::delete(void* ptr, std::size_t sz) 호출    
```

# operator new[]와 operator delete[] 재정의

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 [operator new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98-1)와 [operator delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98-1)를 사용합니다. 일반 자료형의 경우에는 `sz`에 `sizeof(T) * [배열 요수 갯수]`가 전달되지만, 클래스와 같이 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 있는 개체는 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)만큼 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출해야 하므로, 내부적으로 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)를 관리하는 오버헤드(*4byte 이거나 8byte*)가 추가 되어 전달됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0ce2adfd-e3be-4271-8f25-e351d1914181)

```cpp
class T {
public:
    // sz : sizeof(T) * 배열 요수 갯수 + 오버헤드
    void* operator new[](std::size_t sz) { 
        return ::operator new[](sz);
    }
    // sz : sizeof(T) * 배열 요수 갯수 + 오버헤드
    void operator delete[](void* ptr, std::size_t sz) { 
        ::operator delete[](ptr);
    } 
};
T* arr = new T[3]; // operator new[](std::size_t sz), sizeof(T) * 3 + 오버헤드
delete[] arr; // operator delete[](void* ptr, std::size_t sz) 호출
```

# operator new(ptr) : Placement New(위치 지정 생성)

[void* operator new(size_t sz, void* ptr)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)와 같이 `void*` 를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달받는 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 특별히 [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)이라 합니다. [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)은 주어진 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) 등으로 할당한 메모리 위치에 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 실행합니다. 즉, 해당 메모리 위치에 개체를 생성한다고 보셔도 됩니다. 

다음과 같은 클래스 `T`가 있는 경우,

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T() : 
        m_X(10), 
        m_Y(20) {}
    T(int x, int y) :
        m_X(x), 
        m_Y(y) {}
    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};
```

할당된 `buffer`에 `new(buffer) T;`로 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 호출하여 개체를 생성시킬 수 있습니다. 이때 명시적으로 `t->~T()`와 같이 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출해야 합니다.

```cpp
void* buffer = malloc(sizeof(T)); // T 클래스 크기만큼 메모리를 할당합니다.

T* t = new(buffer) T; // T 의 기본 생성자를 호출합니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);

t->~T(); // Placement New를 사용하면 명시적으로 소멸자를 호출해야 합니다.
free(buffer); // malloc 으로 할당한 메모리를 해제합니다.
```

[위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)을 이용하면, 기존 `buffer` 영역을 재활용하여 

1. 다른 개체를 배치시킬 수도 있고,
2.  `buffer`를 직접 수정하여 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 의 값을 변경할 수도 있으며(*비권장*), 
3.  [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출 후에도 값 접근을 할 수 있습니다.(*비권장*) 

```cpp
void* buffer = malloc(sizeof(T));
T* t = new(buffer) T; // T 의 기본 생성자를 호출합니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);
t->~T();

// (O) 기존 t 위치에 other를 사용합니다.
T* other = new(buffer) T(1, 2); // T의 값 생성자를 호출합니다. 
EXPECT_TRUE(t->GetX() == 1 && t->GetY() == 2);

// (△) 비권장. buffer를 직접 수정해도 동작하는지 확인합니다.
// 테스트를 위한 것이니 실제 코드에서 이런거 하시면 캡슐화가 무너집니다.
int* p = static_cast<int*>(buffer);
*p = 100;
*(p + 1) = 200;
EXPECT_TRUE(other->GetX() == 100 && other->GetY() == 200);

other->~T();

// (△) 비권장. 소멸자를 호출하기는 했습니다만, 아직 buffer는 유효하여 other 접근이 되기는 합니다.
EXPECT_TRUE(other->GetX() == 100 && other->GetY() == 200); 
free(buffer);    
```

만약 [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 재구현 하면, 전역 [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)을 가리기 때문에 컴파일 오류가 발생합니다.

```cpp
class T {
    public:
    static void* operator new(std::size_t sz) { // operator new를 정의하면 전역 위치 지정 생성을 가립니다.
        return ::operator new(sz);
    }
    void* buffer = T::operator new(sizeof(T));
    T* t = new(buffer) T; // (X) 컴파일 오류. 전역 위치 지정 생성이 가려졌습니다.
}; 
```

따라서 다음과 같이 [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)을 함께 정의해야 합니다.

```cpp
class T {
public:
    static void* operator new(std::size_t sz) { // operator new를 정의하면 전역 위치 지정 생성을 가립니다.
        return ::operator new(sz);
    }

    // Placement New 재정의
    static void* operator new(size_t sz, void* ptr) { // (O) 위치 지정 생성을 정의했습니다.
        return ptr;
    }
}; 

void* buffer = T::operator new(sizeof(T));
T* t = new(buffer) T; // (O) 위치 지정 생성을 호출합니다.      
```

> *(C++17~) [uninitialized_move(), uninitialized_default_construct(), uninitialized_value_construct(), destroy(), destroy_at()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0-%EC%9E%91%EC%97%85) 함수를 추가하여 [위치 지정 생성자 호출과 소멸자 호출](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)의 새로운 방법을 제공합니다.*

# 스택에만 생성되는 개체

[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 개체를 생성하는데요, 외부에서 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 못하게 하고, [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에만 생성되게 하고 싶다면, [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)를 `private`로 만들면 됩니다.(*[OnlyStackAssignable](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-only-stack-assignable/) 참고*)

```cpp
class T {
private:
    static void* operator new(std::size_t sz) {
        return NULL;
    }    
};

T t; // (O)
T* p = new T; // (X) 컴파일 오류
delete p;
```
# 힙에만 생성되는 개체

[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 `private`나 `protected` 로 만들면, [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 생성된 개체가 암시적으로 소멸될때,(*[소멸자 호출 시점](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90-%ED%98%B8%EC%B6%9C-%EC%8B%9C%EC%A0%90) 참고*) [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에 접근하지 못해 컴파일 오류가 납니다. 이걸 활용하면 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에만 생성되는 개체를 만들 수 있습니다. 단 명시적으로 소멸할 수 있도록 `Destroy()` 함수를 별도로 제공해야 합니다.

```cpp
class T {
protected: 
    ~T() {} // 외부에서 암시적으로 소멸할 수 없습니다.
public:
    void Destroy() const {delete this;} // 자기 자신을 소멸시킵니다.
};
{
    T t; // (X) 컴파일 오류. 스택에 만들고 암시적으로 소멸될 때 컴파일 오류가 발생합니다.
} 
{
    T* p = new T;
    delete p; // (X) 컴파일 오류. delete는 외부에서 호출할 수 없습니다.
    p->Destroy(); // (O)
}
```

# set_new_handler() 함수를 이용한 오류 처리

[operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)는 오류 발생시 

1. [set_new_handler()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)로 전역적으로 설정된 [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)를 호출하고(*없다면, [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키고*), 
2. [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)에서 오류를 해결할 기회를 줍니다. 
 
이 과정은 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키거나 프로그램이 종료할때까지 무한 반복됩니다.

따라서, [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)는 다음 작업중 하나를 해주어야 합니다.

1. 미리 예약된 메모리 공간을 해제하여 메모리를 추가 확보해 주거나(*[NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/) 참고*)

2. 다른 [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)를 설치하여 처리를 위임하거나(*[NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/) 참고*)
3. [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)를 제거하거나(*[NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/) 참고*)
4. [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)이나 이로부터 파생된 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜 처리를 포기하거나
5. [abort()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90)를 호출하여 프로그램을 종료시킵니다.(*[NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/) 참고*)

다음 테스트 코드는 오류 발생시 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)이나 이로부터 파생된 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜 처리를 포기하는 예입니다.

1. #1 : `T`는 `1000 M * sizeof(int)` 의 큰 공간을 할당하는 클래스입니다. 시스템에 따라 다르겠습니다만, 대략 5 ~ 6개 할당되면 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)가 실패하게 됩니다.
2. #2 : `MyExceptionHandler()` 는 다른 처리 없이 [bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)을 상속한 `MyException` [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킵니다.
3. #3 : 테스트 코드에서 [set_new_handler()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)함수를 이용하여 `MyExceptionHandler()`로 교체합니다. 사용이 끝나면 원래 [new_handler](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler-%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)로 복원합니다.
4. #4 : `T`를 계속 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하고, [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, 기존에 생성했던 `T`를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)합니다.

실행시켜보면 `MyExceptionHandler()` 가 호출되고, `MyException` [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킨 것을 알 수 있습니다.

```cpp
class MyException : public std::bad_alloc {}; // #2

// 엄청 큰 데이터를 관리하는 클래스 입니다.
class T { // #1
    int m_Big[1024 * 1024 * 1000]; // 1000 M * sizeof(int)
public:
    // bad_alloc으로 포기합니다.
    static void MyExceptionHandler() { // #2
        throw MyException();
    }
    static void* operator new(std::size_t sz) { 

        // 내부적으로는 메모리를 할당하고, 실패하면 new_handler를 실행하는 과정을 무한히 반복합니다.
        // new_handler가 NULL 이거나 std::bad_alloc이나 std::abort()를 할때 까지요.
        return ::operator new(sz);
    } 
};

std::new_handler oldHandler = std::set_new_handler(&T::MyExceptionHandler); // #3. 핸들러를 설치합니다.

T* arr[100] = {}; // 모두 NULL(0)로 초기화
for (int i = 0; i < 100; ++i) { // 대략 예외를 발생시킬때까지 반복합니다. 5 ~ 6개정도 할당하면 발생합니다.
    try {
        arr[i] = new T; // #4
    }
    catch (MyException& e) {
        // i 이전까지 생성된 T 개체를 모두 삭제합니다.
        for (int j = 0; j < i; ++j) {
            delete arr[j]; // #4
        }
        break; // T 할당을 그만두기 위해 for문을 탈출합니다.
    }
}

std::set_new_handler(oldHandler); // #3. 이전 핸들러로 복원합니다.
```


