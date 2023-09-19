---
layout: single
title: "#4. [모던 C++ STL] (C++11~) unique_ptr, default_delete"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `unique_ptr`은 소유권 이전용 스마트 포인터입니다. 기존 `auto_ptr`을 대체합니다. `auto_ptr`은 `delete[]` 미지원, `lvalue` 대입 연산시 소유권을 이전하는 이동 동작을 하는 등의 사유로 deprecate 되었습니다.

# 개요

기존 `auto_ptr`은 복사/대입시 개체의 소유권을 이전하고, 소멸시 개체를 `delete`하는 스마트 포인터입니다.([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/) 참고)

하지만, 다음 문제로 인해 C++11에서 deprecate 되었습니다.

1. 배열을 `delete[]`가 아닌 `delete`로 삭제합니다.(이러면 배열 요소들이 제대로 소멸되지 않습니다. [개체 생성/소멸과 배열 생성/소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8%EA%B3%BC-%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 참고)
2. `lvalue` 대입 연산시 소유권을 이전하는 이동 동작을 합니다.(이동 연산과 중복됩니다.)

C++11 부터는 상기 문제를 보완한 `unique_ptr`이 제공됩니다.

`auto_ptr`과 동일하게 소유권을 이전하는 스마트 포인터이며, 다음이 개선되었습니다.

1. 일반 포인터는 `delete`하고, 배열은 `delete[]`합니다.
2. 복사 생성자와 `operator =(const T&)`는 제공하지 않고, 이동 생성자와 `operator =(const T&&)`만 제공합니다. 즉 이동 연산만 제공합니다.

다음은 사용예 입니다.

1. `unique_ptr<T[]>` 와 같이 배열도 관리할 수 있으며,
2. `c = d;` 대신 `c = std::move(d);`를 하여 소유권을 이동시킵니다.
   
```cpp
class T {
public:
    ~T() {std::cout<<"T : Destructor"<<std::endl;}    
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

# std::unique_ptr 멤버 함수

|항목|내용|
|--|--|
|`constexpr unique_ptr() noexcept;`<br/><br/>`explicit unique_ptr(T* p) noexcept;`<br/>`unique_ptr(T* p, deleter) noexcept;`<br/><br>`constexpr unique_ptr(nullptr_t) noexcept;`<br/><br/>`unique_ptr(auto_ptr&&) noexcept;` (C++11~C++17)|`nullptr`이나 `p`를 관리합니다. 이때 사용자 정의 `deleter`를 사용할 수 있습니다.|
|`unique_ptr(const unique_ptr&) = delete;`|복사 생성자는 사용할 수 없습니다.|
|`unique_ptr(unique_ptr&& other) noexcept;`|이동 생성합니다.|
|`~unique_ptr()`|관리하는 개체를 `delete` 또는 `delete[]`합니다.|
|`unique_ptr& operator =(const unique_ptr&) = delete;`|대입 연산자는 사용할 수 없습니다.|
|`unique_ptr& operator =(unique_ptr&& other) noexcept;`|`other`가 관리하는 개체를 `this`로 이동시킵니다.|
|`operator *() const noexcept;`|관리하는 개체의 참조자를 리턴합니다.|
|`operator ->() const noexcept;`|관리하는 개체의 포인터를 리턴합니다.|
|`operator [](size_t) const;`|배열을 관리하는 경우 각 요소 개체의 참조자를 리턴합니다.|
|`explicit operator bool() const noexcept;`|`bool`로 형변환시 `nullptr` 이면 `false`를 리턴합니다.|
|`get() const noexcept;`|관리하는 개체의 포인터를 리턴합니다.|
|`swap(unique_ptr& other) noexcept;`|관리하는 개체를 `other`와 바꿔치기 합니다.|
|`reset(T* p) noexcept;`|기존에 관리하던 개체를 해제하고 `p`를 관리합니다.|
|`release() noexcept;`|관리하는 개체를 해제합니다.|
|`get_deleter() noexcept;`|관리하는 개체를 소멸시키는 `deleter`를 리턴합니다.|
|`==`<br/>`!=`<br/>`<`<br/>`<=`<br/>`>`<br/>`>=`|관리하는 개체의 주소로 비교합니다.|
|`<<` (C++20~)|관리하는 개체의 내용을 스트림에 출력합니다.|

# unique_ptr을 활용한 함수 인자, 리턴 타입

`auto_ptr` 의 경우와 동일하게([auto_ptr을 활용한 함수 인자, 리턴 타입](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/#auto_ptr%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%9E%90-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 참고) `unique_ptr`을 사용하면 좀더 단단한 코딩 계약이 가능합니다.

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

`unique_ptr`은 복사 생성자가 없기 때문에 그냥 `push_back()`에 전달하면 컴파일 오류가 발생합니다.

```cpp
std::vector<std::unique_ptr<int>> v;
std::unique_ptr<int> a{new int{10}};
std::unique_ptr<int> b{new int{20}};

v.push_back(a); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.
v.push_back(b); // (X) 컴파일 오류. unique_ptr은 복사 생성자가 없습니다.
```

다음처럼 

1. `move()`를 이용하여 이동 시키거나,
2. `emplace_back()`을 이용하여 내부에서 개체를 생성합니다.([](??) 참고)

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

`unique_ptr`은 관리하는 개체를 소멸시키는 `deleter`를 사용자 정의 할 수 있습니다. 사용자 정의하는 방법은 [shared_ptr Deleter](https://tango1202.github.io/mordern-cpp/mordern-cpp-share_ptr-weak_ptr/#shared_ptr-deleter) 를 참고하세요.

`default_deleter`는 기본적으로 일반 포인터는 `delete` 로 소멸하고, 배열은 `delete[]`로 소멸시킵니다.

```cpp
std::unique_ptr<int> a{new int{10}, std::default_delete<int>{}};
```

# (C++14~) std::make_unique (작성중)