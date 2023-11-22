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
> * [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 널이면 아무 작업 안한다. 괜히 널검사하지 마라.
> * [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 `bad_alloc()`을 발생시킨다. 괜히 널검사하지 마라.
> * `operator new`를 `private`로 만들어 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에만 생성되는 개체를 만들 수 있다.

> **모던 C++**
> * (C++17~) [uninitialized_move(), uninitialized_default_construct(), uninitialized_value_construct(), destroy(), destroy_at()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0-%EC%9E%91%EC%97%85) 함수를 추가하여 [위치 지정 생성자 호출과 소멸자 호출](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)의 새로운 방법을 제공합니다.
> * (C++17~) [polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/)는 런타임 다형성을 지원하는 할당자입니다. [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 사용하여 메모리 풀을 손쉽게 만들 수 있습니다.

# 개요

|항목|내용|
|--|--|
|[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|개체의 메모리를 할당하고, 생성자를 호출합니다.|
|[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출하고, 메모리를 해제합니다.|
|`new[]`|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 할당하고, 각 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소의 생성자들을 호출합니다.|
|[delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|각 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)들을 호출하고, 메모리를 해제합니다.|
|`new(std::nothrow)`|메모리 할당 실패시 NULL 을 리턴합니다. 단, 생성자에서 예외를 발생시키면 해당 예외를 전파합니다.|
|`operator new(std::size_t)`|개체의 메모리를 할당합니다.|
|`operator delete(void*)`|개체의 메모리를 해제 합니다.|
|`operator new[](std::size_t)`|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 할당합니다.|
|`operator delete[](void*)`|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 메모리를 해제합니다.|
|`operator new(void*)`|주어진 메모리 위치에 개체를 배치하여 생성자를 호출합니다.([위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1))|
|`set_new_handler()`|[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)에서 예외 발생시 호출되는 함수입니다.|

개체를 동적으로 생성/소멸할때([힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 할당됩니다.) [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)와 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 사용합니다.

[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 다음의 순서로 개체의 메모리 할당과 생성자 호출을 실행합니다.([생성/소멸 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8-%EC%97%B0%EC%82%B0%EC%9E%90) 언급)

1. 전역 `operator new(std::size_t)`로 메모리 공간 할당(오류 발생시 `set_new_handler()`에 설정한 `new_handler` 실행)
2. 구조체이거나 클래스이면 `operator new(void*)`([위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1))를 실행하여 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 호출
3. 메모리 주소를 해당 타입으로 형변환하여 리턴

[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 다음의 순서로 개체의 소멸자 호출과 메모리 해제를 실행합니다.([개체 소멸 순서](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EA%B0%9C%EC%B2%B4-%EC%86%8C%EB%A9%B8-%EC%88%9C%EC%84%9C) 참고)

1. 개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출
2. 개체의 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 소멸
3. 전역 `operator delete(void*)`로 메모리 할당 해제

즉 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 메모리 할당, 생성자 호출, 메모리 주소를 형변환해서 리턴하지만,
`operator new`는 `void*` 형 메모리를 할당하기만 합니다.

또한 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출, 메모리 해제를 수행하지만,
`operator delete`는 메모리 해제만 수행합니다.

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
New-Delete Test : T::T()
New-Delete Test : T::~T()
```

# 배열 생성/소멸

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 생성/소멸 시에는 [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 이용합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete[] arr; // (O) 소멸자 3회 호출. 메모리 해제(sizeof(T) * 3 + 오버헤드)
```

로 하면 실행 결과는 다음과 같이 [배열 요소의 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98) 만큼 생성자와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출됩니다.

```cpp
New-Delete Test : T::T()
New-Delete Test : T::T()
New-Delete Test : T::T()
New-Delete Test : T::~T()
New-Delete Test : T::~T()
New-Delete Test : T::~T()
```

# delete와 delete[] 의 차이

[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 1회 호출하고, 주어진 메모리를 해제하는 역할을 하고, [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)만큼 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출하고, 주어진 메모리를 해제하는 역할을 합니다.

그러다 보니 `new[]`로 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 생성할때 [배열 요소의 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)가 내부적으로 오버헤드 공간에 저장되고, 이를 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)시에 사용합니다.

1. `int`등 기본 자료형일 경우, 생성자/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출할 필요가 없으므로, 메모리가 `sizeof(int)` * [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)로 할당됩니다.
2. 클래스인 경우 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소 갯수 만큼 생성자/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 호출되어야 하므로, 내부적으로 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)를 저장하는 오버헤드 공간이 추가되어 할당됩니다.(*`sizeof(T)` * [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98) + 오버헤드(4byte 이거나 8byte)*)

혹여나 실수로 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸시키면, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 1회만 호출되고, 프로그램이 다운될 수 있습니다. 따라서, `new[]`로 생성한 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 꼭 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸시켜야 합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete arr; // (X) 예외 발생. 소멸자가 1회만 호출되고, 프로그램이 다운될 수 있음
```

# delete 여러번 호출 금지

[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 여러번 실행하면 예외가 발생합니다. 생성한 개체는 1회만 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)해야 합니다.

```cpp
class T {};
T* p = NULL;
delete p; // (O) p가 NULL이어도 안전합니다.
p = new T; 
delete p; // (O) new로 생성한 것은 반드시 delete 해야 합니다.
delete p; // (X) 예외 발생. 두번 죽일 순 없습니다.  
```

# delete NULL

널을 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하면 아무 동작 안합니다. 따라서 다음과 같이 `if`검사를 할 필요가 없습니다. 그냥 delete 하시면 됩니다.

```cpp
if (p != NULL) { // (△) 비권장. 괜히 널검사합니다.
    delete p;
}

delete p; // (O) 널검사 없이 바로 delete 합니다.
```

# new(nothrow) 와 무의미한 널검사

[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 메모리가 부족하여 할당이 실패하면, `bad_alloc()` 예외를 발생시킵니다.(C++98이후부터)

C++98 이전에는 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)가 널을 리턴해서 다음과 같이 검사 했는데요,

```cpp
T* t = new T;
if (t == NULL) {
    ...
}
```

C++98 이후부터는 `bad_alloc()` 예외를 발생시키므로 하기와 같이 검사해야 합니다.

```cpp
try {
    T* t = new T;
}
catch (std::bad_alloc&) {
    // 할당에 실패했을때의 코드
}
```

이전 스타일로 널검사를 하고 싶다면 `new(std::nothrow)` 을 사용할 수 있습니다. 하지만, 메모리 할당에 실패하면 널을 리턴하지만, 생성자에서 예외를 발생시키면 전파됩니다. 그래서 그냥 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 사용하고 `try-catch()`를 사용하는게 좋습니다.

```cpp
class T {};
T* t = new(std::nothrow) T; // (△) 비권장. 메모리 할당에 실패하면 널을 리턴하지만, 생성자에서 예외를 발생시키면 전파됩니다.

if (t == NULL) {
    // 할당 실패시 처리할 코드
}
```

# operator new와 operator delete 재정의

개체의 메모리를 할당하고 해제하는 `operator new`와 `operator delete`는 사용자가 재정의 할 수 있습니다.

주로 다음을 위해 사용합니다.

1. 데이터 오버런(overrun) 및 언더런(underrun) 등 잘못된  [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 사용을 탐지하기 위해 탐지용 byte를 추가로 할당하는 경우([진단](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-diagnostics/) 참고)
2. 할당 및 해제의 효율을 향상시키기 위해 **동적 메모리 오버헤드**를 줄이고 메모리 관리를 직접 수행하기 위한 메모리 풀을 만드는 경우([polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/) 참고)

    > *(C++17~) [polymorphic_allocator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/)는 런타임 다형성을 지원하는 할당자입니다. [메모리 리소스](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-polymorphic_allocator/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A6%AC%EC%86%8C%EC%8A%A4)를 사용하여 메모리 풀을 손쉽게 만들 수 있습니다.*
3. 동적 할당 메모리의 실제 사용에 관한 통계 정보를 수집하는 경우([개체 수명 로그](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-object-life-log/) 참고)

**동적 메모리 오버헤드**

우리가 동적 메모리를 할당하면, 나중에 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 할때 얼마만큼의 메모리를 해제해야 할지 알아야 하기 때문에, 요청한 크기의 메모리보다 더 많은 오버헤드 공간을 사용합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9f03346e-1457-4b92-90db-52d18e467086)

컴파일러나 시스템마다 그 크기가 다를 수 있으며, 보통 4byte 이거나 8byte의 추가 오버헤드 공간을 사용합니다.

따라서, 크기가 작은 개체를 아주 많이 동적 생성을 해야 한다면, 메모리 공간을 효율적으로 사용하기 위해 `operator new`의 재정의가 필요할 수 있습니다.


**기본 재정의 방법**

전역 `operator new` - 전역 `operator delete`를 이용하거나 `malloc()` - `free()`를 이용할 수 있습니다. 

`operator new`를 재정의하려면 다음을 준수하여야 합니다.

1. 최소 1byte를 할당해야 합니다.
2. 할당 실패시 `new_handler`를 호출해야 합니다. `new_handler`가 없다면 `bad_alloc()`을 발생시켜야 합니다.
3. `new_handler`에서 `bad_alloc()` 또는 이로부터 파생된 예외를 발생시키거나, 프로그램을 종료할 때까지 반복해야 합니다. 

전역 `operator new`를 이용하면 상기 내용을 준수하므로 다음과 같이 전역 버전을 호출하면 됩니다.

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

`malloc()` - `free()`를 이용한다면, 

1. 최소 크기 1byte를 설정해 주어야 하고,
2. `malloc()`에서는 오류 발생시 `NULL`을 리턴하므로, `new_handler` 와 `bad_alloc()` 처리를 해주어야 합니다.

`set_new_handler()` 함수에 대응하는 `get_new_handler()` 함수는 없습니다. 그래서 `handler = std::set_new_handler(NULL);` 과 같이 호출하여, `new_handler`를 구하고, 이를 다시 복원해야 합니다.(개체 지향스럽고, [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/)이 되는 방법은 [NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/)를 참고하세요.)

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

    static void* operator new(std::size_t sz) {
        // 혹시 모르니 검사하여 최소 1byte로 만듬    
        if (sz == 0) { 
            ++sz;
        }

        // 핸들러가 예외를 발생시키거나 프로그램을 종료할 때까지 반복
        while (true) {
            void* ptr = malloc(sz);
            if (ptr != NULL) {
                return ptr;
            }

            std::new_handler handler = std::set_new_handler(NULL); // 대충 NULL을 세팅하고 핸들러를 구합니다.
            std::set_new_handler(handler); // 핸들러 복원
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

`operator new`의 경우 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 추가하여 다양한 사용자 정의를 할 수 있습니다.

```cpp
static void* operator new(std::size_t sz, Param1 param1, Param2 param2 ...);
```

다음과 같이 `int val` 등 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 추가된 버전을 만들 수 있고,

```cpp
static void* operator new(std::size_t sz, int val) { // int val을 인자로 전달받습니다.
    return ::operator new(sz);
}
```

해당 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 인수(예제에서는 그냥 `1`)를 전달하여 `new(1)`과 같이 호출할 수 있습니다.

```cpp
T* t = new(1) T; // T::operator new(std::size_t sz, int val), 기본 생성자 호출
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);
delete t;

t = new(1) T(1, 2); // T::operator new(std::size_t sz, int val), T::T(int, int) 생성자 호출
EXPECT_TRUE(t->GetX() == 1 && t->GetY() == 2);
delete t;
```

**operator delete 오버로딩**

`operator delete`는 두가지 유형이 있습니다. 두개를 모두 정의하더라도, 둘중에 하나만 사용됩니다. 

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

**부모 클래스의 `operator new`, `operator delete`**

자식 클래스에서 별다르게 `operator new`와 `operator delete`를 정의하지 않으면, 부모 클래스 것을 사용합니다.

```cpp
// sizeof(Base) == sizeof(int) * 2 + 가상 함수 테이블 크기
class Base { 
    int m_X;
    int m_Y;
public:
    virtual ~Base() {}

    // sz : sizeof(Base) 또는 sizeof(Derived) 크기
    static void* operator new(std::size_t sz) {
        return ::operator new(sz);
    }
    // sz : sizeof(Base) 또는 sizeof(Derived) 크기
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

이에 모든 자식 클래스에서 `operator new`, `operator delete`를 일일이 구현하지 말고, 부모 클래스(`Base`)에서 자식 클래스(`Derived`)를 고려해서 구현하는게 좋습니다.

고려라고 해봐야 인수 `sz`에 전달된 만큼 메모리를 할당/해제하는 것이니 특별히 할 건 없습니다.

# virtual 소멸자인 경우 `operator delete`

[다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 지원하려면, 꼭 [가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용해야 한다고 언급한바 있습니다. 그렇지 않으면 `Base` [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)만 불려서 `Derived`의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 불리지 않아, 메모릭 릭이 발생할 수 있다고요.

`operator delete`에서도 마찬가지 입니다. 자식 클래스에서 할당된 메모리를 모두 해제하려면 꼭 [가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하셔야 합니다.

다음 코드는 상기 코드에서 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)의 `virtual`만 뺀 코드입니다. 실행시켜 확인해 보면, 

1. `operator new` 시에는 `Derived`의 크기인 16byte가 전달되나, 
2. `operator delete`시에는 `Base`의 크기인 8byte가 전달되어, 
   
메모리의 일부만 해제되어 메모리 릭이 발생합니다. 그러니, [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 하려면 꼭 [가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하세요.

```cpp
// sizeof(Base) == sizeof(int) * 2 = 8byte
class Base { 
    int m_X;
    int m_Y;
public:
    ~Base() {}

    // sizeof(Derived) 크기 : 16byte
    static void* operator new(std::size_t sz) {
        return ::operator new(sz);
    }
    // (X) 오동작. 소멸시에는 Base의 크기 : 8byte. 메모리 릭 발생
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

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 `operator new[]`와 `operator delete[]`를 사용합니다. 일반 자료형의 경우에는 `sz`에 `sizeof(T) * [배열 요수 갯수]`가 전달되지만, 클래스와 같이 생성자/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 있는 개체는 [배열 요소 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)만큼 생성자/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출해야 하므로, 내부적으로 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소 갯수를 관리하는 오버헤드(4byte 이거나 8byte)가 추가 되어 전달됩니다.

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
T* arr = new T[10]; // operator new[](std::size_t sz), sizeof(T) * 10 + 오버헤드
delete[] arr; // operator delete[](void* ptr, std::size_t sz) 호출
```

# operator new(ptr) : Placement New(위치 지정 생성)

`void* operator new(size_t sz, void* ptr)`와 같이 `void*` 를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달받는 `operator new`를 특별히 [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)이라 합니다. [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)은 주어진 `operator new` 등으로 할당한 메모리 위치에 생성자를 실행합니다. 즉, 해당 메모리 위치에 개체를 생성한다고 보셔도 됩니다. 

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

다음과 같이 할당된 `buffer`에 `new(buffer) T;`로 생성자를 호출하여 개체를 생성시킬 수 있습니다. 이때 명시적으로 `t->~T()`와 같이 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출해야 합니다.

```cpp
void* buffer = malloc(sizeof(T)); // T 클래스 크기만큼 메모리를 할당합니다.

T* t = new(buffer) T; // T 의 기본 생성자를 호출합니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);

t->~T(); // Placement New를 사용하면 명시적으로 소멸자를 호출해야 합니다.
free(buffer); // malloc 으로 할당한 메모리를 해제합니다.
```

기존 `buffer`의 메모리 영역을 재활용하여 

1. 다른 개체를 배치시킬 수도 있고,
2.  `buffer`를 직접 수정하여 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 의 값을 변경할 수도 있으며(비권장), 
3.  [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 호출 후에도 값 접근을 할 수 있습니다.(비권장) 

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

`operator new`를 재구현 하면, 전역 [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)을 가리기 때문에, 재정의해야 합니다.

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

    static void* operator new(std::size_t sz) {
        return ::operator new(sz);
    }
    static void operator delete(void* ptr) {
        ::operator delete(ptr);    
    }
    // Placement New 재정의
    static void* operator new(size_t sz, void* ptr) { 
        return ptr;
    }
}; 

void* buffer = T::operator new(sizeof(T)); 
T* t = new(buffer) T; // T 의 기본 생성자를 호출합니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);

// (△) 비권장. buffer를 직접 수정해도 동작하는지 확인합니다.
// 테스트를 위한 것이니 실제 코드에서 이런거 하시면 캡슐화가 무너집니다.
int* p = static_cast<int*>(buffer);
*p = 100;
*(p + 1) = 200;
EXPECT_TRUE(t->GetX() == 100 && t->GetY() == 200);

t->~T(); // Placement New를 사용하면 명시적으로 소멸자를 호출해야 합니다.
T::operator delete(buffer);
```

[위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)을 재정의했다고 해서 기본 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 할때 [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)이 사용되지는 않습니다.

```cpp
T* t = new T; // operator new(size_t sz, void* ptr) 가 호출되지는 않습니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);
delete t;
```

> *(C++17~) [uninitialized_move(), uninitialized_default_construct(), uninitialized_value_construct(), destroy(), destroy_at()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0-%EC%9E%91%EC%97%85) 함수를 추가하여 [위치 지정 생성자 호출과 소멸자 호출](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)의 새로운 방법을 제공합니다.*

# 스택에만 생성되는 개체

[new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 개체를 생성하는데요, 외부에서 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 못하게 하고, [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에만 생성되게 하고 싶다면, `operator new`를 `private`로 만들면 됩니다.([OnlyStackAssignable](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-only-stack-assignable/) 참고)

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

[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 `private`나 `protected` 로 만들면, [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 생성된 개체가 암시적으로 소멸될때,([소멸자 호출 시점](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90-%ED%98%B8%EC%B6%9C-%EC%8B%9C%EC%A0%90) 참고) [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에 접근하지 못해 컴파일 오류가 납니다. 이걸 활용하면 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에만 생성되는 개체를 만들 수 있습니다. 단 명시적으로 소멸할 수 있도록 `Destroy()` 함수를 별도로 제공해야 합니다.

```cpp
class T {
protected: 
    ~T() {} // 외부에서 암시적으로 소멸할 수 없습니다.
public:
    void Destroy() const {delete this;} // 자기 자신을 소멸시킵니다.
};
{
    T t; // (X) 컴파일 오류. 스택으로 만들고 암시적으로 소멸될 때 컴파일 오류가 발생합니다.
} 
{
    T* p = new T;
    delete p; // (X) 컴파일 오류. delete는 외부에서 호출할 수 없습니다.
    p->Destroy(); // (O)
}
```

# set_new_handler() 함수를 이용한 오류 처리

`operator new`는 오류 발생시 

1. `set_new_handler()` 로 전역적으로 설정된 `new_handler`를 호출하고(없다면, `bad_alloc()`을 발생시키고), 
2. `new_handler`에서 오류를 해결할 기회를 줍니다. 
 
이 과정은 예외를 발생시키거나 프로그램이 종료할때까지 무한 반복됩니다.

따라서, `new_handler`는 다음 작업중 하나를 해주어야 합니다.

1. 미리 예약된 메모리 공간을 해제하여 메모리를 추가 확보해 주거나
2. 다른 `new_handler`를 설치하여 처리를 위임하거나
3. `new_handler`를 제거하거나(제거되면 `bad_alloc()` 발생)
4. `bad_alloc()` 또는 이로부터 파생된 예외를 발생시켜 처리를 포기하거나
5. `abort()`을 하여 프로그램을 종료합니다.

다음 테스트 코드는 

1. `T`는 `1000 M * sizeof(int)` 의 큰 공간을 할당하는 클래스입니다. 시스템에 따라 다르겠습니다만, 대략 5 ~ 6개 할당되면 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)가 실패하게 됩니다.
2. `MyExceptionHandler()` 는 다른 처리 없이 `bad_alloc`을 상속한 `MyException`을 발생시킵니다.
3. 테스트 코드에서 `set_new_handler()`함수를 이용하여 `MyExceptionHandler()`로 교체합니다. 사용이 끝나면 원래 `new_handler`로 복원합니다.
4. `T`를 계속 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하고, 예외가 발생하면, 기존에 생성했던 `T`를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)합니다.

실행시켜보면 `MyExceptionHandler()` 가 호출되고, `MyException` 예외를 발생시킨 것을 알 수 있습니다.

```cpp
class MyException : public std::bad_alloc {};

// 엄청 큰 데이터를 관리하는 클래스 입니다.
class T {
    int m_Big[1024 * 1024 * 1000]; // 1000 M * sizeof(int)
public:
    // bad_alloc으로 포기합니다.
    static void MyExceptionHandler() {
        throw MyException();
    }
    static void* operator new(std::size_t sz) { 

        // 내부적으로는 메모리를 할당하고, 실패하면 new_handler를 실행하는 과정을 무한히 반복합니다.
        // new_handler가 NULL 이거나 std::bad_alloc이나 std::abort()를 할때 까지요.
        return ::operator new(sz);
    } 
};

std::new_handler oldHandler = std::set_new_handler(&T::MyExceptionHandler); // 핸들러를 설치합니다.

T* arr[100] = {}; // 모두 NULL(0)로 초기화
for (int i = 0; i < 100; ++i) { // 대략 예외를 발생시킬때까지 반복합니다.
    try {
        arr[i] = new T;
    }
    catch (MyException& e) {
        // i 이전까지 생성된 T 개체를 모두 삭제합니다.
        for (int j = 0; j < i; ++j) {
            delete arr[j];
        }
        break; // T 할당을 그만두기 위해 for문을 탈출합니다.
    }
}

std::set_new_handler(oldHandler); // 이전 핸들러로 복원합니다.
```

미리 예약된 메모리 공간을 활용하는 `new_handler` 구현 방법은 [NewHandler](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-new_handler/)를 참고하기 바랍니다.
