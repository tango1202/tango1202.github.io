---
layout: single
title: "#16. [모던 C++ STL] (C++11~) 소유권 이전 스마트 포인터(unique_ptr), (C++14~) make_unique()"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)은 소유권 이전용 스마트 포인터입니다. 기존 [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)을 대체합니다. [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 `delete[]` 미지원, `lvalue` 복사 대입 연산시 이동 동작을 하는 등의 사유로 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.
> * (C++14~) [make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)를 이용하여 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)을 효율적으로 생성할 수 있습니다.

# 개요

기존 [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 복사/대입시 개체의 소유권을 이전하고, 소멸시 개체를 `delete`하는 스마트 포인터입니다.

하지만, 다음 문제로 인해 C++11에서 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.

1. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 `delete[]`가 아닌 `delete`로 삭제합니다.(이러면 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소들이 제대로 소멸되지 않습니다. [개체 생성/소멸과 배열 생성/소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8%EA%B3%BC-%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 참고)
2. `lvalue` 복사 대입 연산시 소유권을 이전하는 이동 동작을 합니다.([이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)과 중복됩니다.)

C++11 부터는 상기 문제를 보완한 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)이 제공됩니다.

[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)과 동일하게 소유권을 이전하는 스마트 포인터이며, 다음이 개선되었습니다.

1. 일반 포인터는 `delete`하고, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 `delete[]`합니다.
2. 복사 생성자와 `operator =(const T&)`는 제공하지 않고, 이동 생성자와 `operator =(const T&&)`만 제공합니다. 즉, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)만 제공합니다.

다음은 사용예 입니다.

1. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)도 관리(`unique_ptr<T[]>`와 같이 `[]`사용)할 수 있으며,
2. `c = d;` 대신 `c = std::move(d);`를 하여 소유권을 이동시킵니다.
   
```cpp
class T {
public:
    ~T() {std::cout << "T : Destructor" << std::endl;}    
};

std::unique_ptr<T> a{new T}; // 단일 개체. delete를 호출하여 1개가 소멸됩니다.
std::unique_ptr<T[]> b{new T[2]}; // 배열 개체. delete[] 를 호출하여 2개가 소멸됩니다.

std::unique_ptr<int> c{new int{0}};
std::unique_ptr<int> d{new int{1}};

// 소유권 이전으로 c는 d의 값을 갖고, d는 nullptr 이 됩니다.
// c = d; // (X) 컴파일 오류.
c = std::move(d); // 소유권 이전시 이동 연산을 사용합니다.
EXPECT_TRUE(*c == 1 && d == nullptr);
```

# unique_ptr 멤버 함수

|항목|내용|
|--|--|
|`constexpr unique_ptr() noexcept;` (C++11~)<br/><br/>`explicit unique_ptr(T* p) noexcept;` (C++11~)<br/>`unique_ptr(T* p, deleter) noexcept;` (C++11~)<br/><br>`constexpr unique_ptr(nullptr_t) noexcept;` (C++11~)<br/><br/>`unique_ptr(auto_ptr&&) noexcept;` (C++11~C++17)|[nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/)이나 `p`를 관리합니다. 이때 사용자 정의 `deleter`를 사용할 수 있습니다.|
|`unique_ptr(const unique_ptr&) = delete;` (C++11~)|복사 생성자는 사용할 수 없습니다.|
|`unique_ptr(unique_ptr&& other) noexcept;` (C++11~)|이동 생성합니다.|
|`~unique_ptr()` (C++11~)|관리하는 개체를 `delete` 또는 `delete[]`합니다.|
|`unique_ptr& operator =(const unique_ptr&) = delete;` (C++11~)|복사 대입 연산자는 사용할 수 없습니다.|
|`unique_ptr& operator =(unique_ptr&& other) noexcept;` (C++11~)|이동 대입합니다.<br/>`other`가 관리하는 개체를 [this](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)로 이동시킵니다.|
|`operator *() const noexcept;` (C++11~)|관리하는 개체의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다.|
|`operator ->() const noexcept;` (C++11~)|관리하는 개체의 포인터를 리턴합니다.|
|`operator [](size_t) const;` (C++11~)|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 관리하는 경우 각 요소 개체의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다.|
|`explicit operator bool() const noexcept;` (C++11~)|[bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)로 형변환시 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) 이면 `false`를 리턴합니다.|
|`get() const noexcept;` (C++11~)|관리하는 개체의 포인터를 리턴합니다.|
|`swap(unique_ptr& other) noexcept;` (C++11~)|관리하는 개체를 `other`와 바꿔치기 합니다.|
|`reset(T* p) noexcept;` (C++11~)|기존에 관리하던 개체를 해제하고 `p`를 관리합니다.|
|`release() noexcept;` (C++11~)|관리하는 개체를 해제합니다.|
|`get_deleter() noexcept;` (C++11~)|관리하는 개체를 소멸시키는 `deleter`를 리턴합니다.|
|`==` (C++11~)<br/>`!=` (~C++20)|관리하는 개체의 주소로 비교합니다.|
|`<, <=, >, >=` (C++11~)<br/>`<=>` (C++20~)|관리하는 개체의 주소로 비교합니다.|
|`<<` (C++20~)|관리하는 개체의 내용을 스트림에 출력합니다.|

# unique_ptr을 활용한 함수 인자, 리턴 타입

[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)의 경우와 동일하게([auto_ptr을 활용한 함수 인자, 리턴 타입](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/#auto_ptr%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%9E%90-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 참고) [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)을 사용하면 좀더 단단한 코딩 계약이 가능합니다.

```cpp
T GetData const; // (O, △) 부분적으로 비권장. 임시 객체를 리턴합니다. 하지만 혹시 멤버 변수를 리턴하는지 확인해 봐야 합니다.
const T& GetData() const; // (O) 멤버 변수를 리턴합니다. 수정하면 안됩니다.
T& GetData(); // (O) 멤버 변수를 리턴합니다.
T& GetData() const; // (△) 비권장. const 함수인데, 리턴받은 곳에서 멤버 수정이 가능합니다.
const T* GetData() const; // (△) 비권장. 리턴값을 delete 해야 하는건지 아닌건지 좀 모호합니다.
unique_ptr<T> GetData() const; // (O) 리턴값은 delete 해야합니다.

void f(T v); // (O, △) 부분적으로 비권장. 객체 전달입니다만, 복사 부하가 있을지 확인해 봐야 합니다.
void f(const T* p); // (△) 비권장. f에서 널검사는 하는지 좀 봐야 합니다.
void f(T* p); // (△) 비권장. f에서 널검사는 하는지 좀 봐야 합니다.
void f(const T& r); // (O) 널검사도 필요없고 참 좋습니다.
void f(T& r); // (O) 널검사도 필요없고 참 좋습니다.
void f(const unique_ptr<T>& p); // (△) 비권장. 쓸데없이 unique_ptr을 전달하지 않고, f(const T& r)이나, f(T& r)을 사용하는게 낫습니다.
void f(unique_ptr<T>& p); // (△) 비권장. p를 수정하겠다는 건지, unique_ptr을 수정하겠다는 건지 불분명 합니다. f(const T& r)이나, f(T& r)을 사용하는게 낫습니다.
void f(unique_ptr<T> p); // (O) new로 생성한 개체를 전달해야 합니다.
```

# unique_ptr을 컨테이너 요소로 사용하기

[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)은 복사 생성자가 없기 때문에 그냥 `push_back()`에 전달하면 컴파일 오류가 발생합니다.

```cpp
std::vector<std::unique_ptr<int>> v;
std::unique_ptr<int> a{new int{10}};
std::unique_ptr<int> b{new int{20}};

v.push_back(a); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.
v.push_back(b); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.
```

다음처럼 

1. [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 이용하여 이동 시키거나,
2. `emplace_back()`을 이용하여 내부에서 개체를 생성합니다.(`emplace()`계열의 함수는 컨테이너 요소 개체 생성을 위한 인수를 전달받아, 컨테이너 내에서 요소 개체를 생성한 뒤 삽입합니다.)

```cpp
std::vector<std::unique_ptr<int>> v;
std::unique_ptr<int> a{new int{10}};
std::unique_ptr<int> b{new int{20}};

// v.push_back(a); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.
// v.push_back(b); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.

// move를 이용하여 이동시킵니다.
v.push_back(std::move(a)); // (O) 
v.push_back(std::move(b)); // (O) 

// 요소 개체(std::unique_ptr<int>)를 생성할 인자(int*)만 전달하고, 내부에서 개체(std::unique_ptr<int>)를 생성합니다.
v.emplace_back(new int{30});

EXPECT_TRUE(*v[0] == 10 && *v[1] == 20 && *v[2] == 30);
```

# default_delete 

[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)은 관리하는 개체를 소멸시키는 `deleter`를 사용자 정의 할 수 있습니다. 사용자 정의하는 방법은 [shared_ptr Deleter](https://tango1202.github.io/mordern-cpp/mordern-cpp-shared_ptr-weak_ptr/#shared_ptr-deleter) 를 참고하세요.

[default_delete](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#default_delete)는 기본적으로 일반 포인터는 `delete` 로 소멸하고, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 `delete[]`로 소멸시킵니다.

```cpp
std::unique_ptr<int> a{new int{10}, std::default_delete<int>{}};
```

# (C++14~) make_unique()

[make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)는 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)을 생성합니다. 이때 `T`개체의 생성자 인자를 전달받아 내부적으로 `T`개체를 생성합니다.

```cpp
template<typename T, typename... Args>
unique_ptr<T> make_unique(Args&&... args); // args 를 인자로 받는 T 개체를 관리하는 unique_ptr을 생성합니다.

template< class T >
unique_ptr<T> make_unique(std::size_t size); // size 개의 배열 개체를 관리하는 unique_ptr을 생성합니다.
```

1. `[]` 실수 방지

    [make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)를 사용하면 `[]` 실수를 방지할 수 있습니다.

    ```cpp
    std::unique_ptr<T> a{new T}; 
    std::unique_ptr<T> b{new T[2]}; // (△) 비권장. unique_ptr<T[]>인데, 실수로 []을 빼먹었지만 컴파일 됩니다.
    //std::unique_ptr<T> c{std::make_unique<T>(2)};  // (X) 컴파일 오류
    std::unique_ptr<T[]> d{std::make_unique<T[]>(2)};
    ```

2. 예외 안전성 향상

    또한, 예외에 좀더 안전합니다. 다음 코드는 예외에 안전한 듯 하지만, 

    ```cpp
    class T {};
    class U {};
    void Func(std::unique_ptr<T> t, std::unique_ptr<U> u) {}

    Func(std::unique_ptr<T>{new T}, std::unique_ptr<U>{new U}); // (△) 비권장. new T, new U 호출 순서에 따라 예외가 발생합니다.
    ```

    1. `new T`
    2. `new U`
    3. `unique_ptr<T>`
    4. `unique_ptr<U>`

    의 순서로 실행될 경우 `new U`에서 예외가 발생할 경우 `new T`는 소멸되지 않습니다. 따라서 [make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)를 사용하는게 좋습니다.
    ```cpp
    class T {};
    class U {};
    void Func(std::unique_ptr<T> t, std::unique_ptr<U> u) {}

    Func(std::make_unique<T>(), std::make_unique<U>()); // (O) 
    ```
    
# (C++20~) make_unique_for_override (작성중)