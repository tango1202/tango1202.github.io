---
layout: single
title: "#16. [모던 C++ STL] (C++11~) 소유권 이전 스마트 포인터(unique_ptr), (C++14~) make_unique()"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#18] 소유권 독점 자원의 관리에는 std::unique_ptr를 사용하라.
> * [MEC++#22] PImpl 관용구를 사용할때에는 특수 멤버 함수들을 구현파일에서 정의하라.(재현되지는 않음. 사유는 암묵적으로 inline인 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)인 ~T()는, pImpl의 raw 포인터를 삭제하기 전에 static_assert()를 검사하는데, 이 시점에 impl은 전방 선언이어서 불완전하여 delete가 해석되지 않음. 따라서 Impl 구현 코드가 있는 cpp에 T()::~T() = default; 넣어 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 정의 시점을 변경함.)

> * (C++11~) [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)은 소유권 이전용 스마트 포인터입니다. 기존 [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)을 대체합니다. [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)의 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 미지원, [좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)의 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산시 이동 동작을 하는 등의 사유로 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.
> * (C++14~) [make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)를 이용하여 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)을 효율적으로 생성할 수 있습니다.

# 개요

기존 [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 복사/대입시 개체의 소유권을 이전하고, 소멸시 개체를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하는 스마트 포인터입니다.

하지만, 다음 문제로 인해 C++11에서 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.

1. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)가 아닌 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 삭제합니다.(이러면 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소들이 제대로 소멸되지 않습니다. [delete와 delete[] 의 차이](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#delete%EC%99%80-delete-%EC%9D%98-%EC%B0%A8%EC%9D%B4) 참고)
2. `lvalue` [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산시 소유권을 이전하는 이동 동작을 합니다.([이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)과 중복됩니다.)

C++11 부터는 상기 문제를 보완한 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)이 제공됩니다.

[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)과 동일하게 소유권을 이전하는 스마트 포인터이며, 다음이 개선되었습니다.

1. 일반 포인터는 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하고, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)합니다.
2. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 `operator =(const T&)`는 제공하지 않고, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)와 `operator =(const T&&)`만 제공합니다. 즉, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)만 제공합니다.

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
|`constexpr unique_ptr() noexcept;` (C++11~)<br/><br/>`explicit unique_ptr(T* p) noexcept;` (C++11~)<br/>`unique_ptr(T* p, deleter) noexcept;` (C++11~)<br/><br>`constexpr unique_ptr(nullptr_t) noexcept;` (C++11~)<br/><br/>`unique_ptr(auto_ptr&&) noexcept;` (C++11~C++17)|[nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr)이나 `p`를 관리합니다. 이때 사용자 정의 `deleter`를 사용할 수 있습니다.|
|`unique_ptr(const unique_ptr&) = delete;` (C++11~)|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)는 사용할 수 없습니다.|
|`unique_ptr(unique_ptr&& other) noexcept;` (C++11~)|[이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.|
|`~unique_ptr()` (C++11~)|관리하는 개체를 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 또는 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)합니다.|
|`unique_ptr& operator =(const unique_ptr&) = delete;` (C++11~)|[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 사용할 수 없습니다.|
|`unique_ptr& operator =(unique_ptr&& other) noexcept;` (C++11~)|[이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.<br/>`other`가 관리하는 개체를 [this](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)로 이동시킵니다.|
|`operator *() const noexcept;` (C++11~)|관리하는 개체의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다.|
|`operator ->() const noexcept;` (C++11~)|관리하는 개체의 포인터를 리턴합니다.|
|`operator [](size_t) const;` (C++11~)|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 관리하는 경우 각 요소 개체의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다.|
|`explicit operator bool() const noexcept;` (C++11~)|[bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)로 형변환시 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr) 이면 `false`를 리턴합니다.|
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

[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)은 복사 생성자가 없기 때문에 그냥 [push_back()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C)에 전달하면 컴파일 오류가 발생합니다.

```cpp
std::vector<std::unique_ptr<int>> v;
std::unique_ptr<int> a{new int{10}};
std::unique_ptr<int> b{new int{20}};

v.push_back(a); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.
v.push_back(b); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.
```

다음처럼 

1. [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 이용하여 이동 시키거나,
2. `emplace_back()`을 이용하여 내부에서 개체를 생성합니다.(*`emplace()`계열의 함수는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 요소 개체 생성을 위한 인수를 전달받아, [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 내에서 요소 개체를 생성한 뒤 삽입합니다. [emplace() 계열 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)를 참고하세요.*)

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

[unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)은 관리하는 개체를 소멸시키는 `deleter`를 사용자 정의 할 수 있습니다. 사용자 정의하는 방법은 [shared_ptr Deleter](https://tango1202.github.io/mordern-cpp/mordern-cpp-shared_ptr-weak_ptr/#shared_ptr-deleter)를 참고하세요.

[default_delete](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#default_delete)는 기본적으로 일반 포인터는 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 로 소멸하고, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸시킵니다.

```cpp
std::unique_ptr<int> a{new int{10}, std::default_delete<int>{}};
```

# unique_ptr을 이용한 PImpl 구현

[PImpl 이디엄](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-pimpl/)에서 구현에 대한 상세 정보를 은닉하여, 컴파일 종속성을 최소화한 방법을 소개해 드렸는데요, [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)을 이용하면 좀더 손쉽게 구현할 수 있습니다.

다음은 `T`개체 내부에 [중첩 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A4%91%EC%B2%A9-%ED%81%B4%EB%9E%98%EC%8A%A4)인 `Impl`을 구현한 예입니다.

선언부에서는,

1. `Impl`을 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)만 합니다.
2. [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)로 `m_Impl` 개체를 만듭니다. 스마트 포인터이므로, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)할 필요가 없습니다.
3. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 선언합니다.
4. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 정의되어 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)가 암시적으로 정의되지 않으므로 [default](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#default%EC%99%80-delete)를 사용하여 명시적으로 정의합니다.(*[암시적 이동 생성자와 암시적 이동 대입 연산자의 default 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98) 참고*)

```cpp
// ----
// 선언에서
// ----
class T {
    class Impl; // 포인터만 사용되므로 전방 선언으로 충분합니다.
    std::unique_ptr<Impl> m_Impl; // 스마트 포인터여서 소멸자에서 delete 합니다.

public:
    T(int x, int y);
    T(const T& other); // 복사 생성자입니다.
    T(T&& other) noexcept = default; // 복사 생성자를 정의했기 때문에 이동 생성자가 암시적으로 정의되지 않습니다. 따라서, 명시적으로 정의합니다.

    bool IsValid() const;
    int GetX() const;
    int GetY() const;

    void SetX(int x);
    void SetY(int y);
};
```

정의부에서는,

1. `Impl`을 구현합니다.
2. `T`를 구현합니다. 이때 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)는 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)이 관리하는 개체의 복제본을 사용합니다. 

```cpp
// ----
// 정의에서
// ----
class T::Impl {
public:
    int m_X;
    int m_Y;
    Impl(int x, int y) : m_X(x), m_Y(y) {}
}; 

T::T(int x, int y) : m_Impl{std::unique_ptr<T::Impl>{new Impl{x, y}}} {}
T::T(const T& other) : m_Impl{other.m_Impl ? new Impl(*other.m_Impl) : nullptr} {}

bool T::IsValid() const {return m_Impl ? true : false;}
int T::GetX() const {return m_Impl->m_X;}
int T::GetY() const {return m_Impl->m_Y;}

void T::SetX(int x) {m_Impl->m_X = x;}
void T::SetY(int y) {m_Impl->m_Y = y;}

T a{10, 20};
EXPECT_TRUE(a.IsValid() && a.GetX() == 10 && a.GetY() == 20);

T b{a}; // 복사 생성합니다.
EXPECT_TRUE(b.IsValid() && b.GetX() == 10 && b.GetY() == 20);
a.SetX(1);
a.SetY(2);
EXPECT_TRUE(a.IsValid() && a.GetX() == 1 && a.GetY() == 2); 
EXPECT_TRUE(b.IsValid() && b.GetX() == 10 && b.GetY() == 20);

T c{std::move(b)};
EXPECT_TRUE(b.IsValid() == false); // 이동되어 Impl은 무효화되었습니다.
EXPECT_TRUE(c.IsValid() && c.GetX() == 10 && c.GetY() == 20);
```

# (C++14~) make_unique()

[make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)는 [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)을 생성합니다. 이때 `T`개체의 생성자 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달받아 내부적으로 `T`개체를 생성합니다.

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

2. [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/) 향상

    또한, 예외에 좀더 안전합니다. 다음 코드는 [예외를 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/)하는 듯 하지만, 

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

    의 순서로 실행될 경우 `new U`에서 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)할 경우 `new T`는 소멸되지 않습니다. 따라서 [make_unique()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/#c14-make_unique)를 사용하는게 좋습니다.
    ```cpp
    class T {};
    class U {};
    void Func(std::unique_ptr<T> t, std::unique_ptr<U> u) {}

    Func(std::make_unique<T>(), std::make_unique<U>()); // (O) 
    ```
    
# (C++20~) make_unique_for_override (작성중)