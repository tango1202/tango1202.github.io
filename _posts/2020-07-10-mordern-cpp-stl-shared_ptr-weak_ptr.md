---
layout: single
title: "#10. [모던 C++ STL] (C++11~) share_ptr, make_shared(), enable_shared_from_this, owner_less, 형변환, weak_ptr, bad_weak_ptr"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `shared_ptr`은 소유권 공유용 스마트 포인터입니다.
> * `weak_ptr`은 `shared_ptr`의 상호 참조 문제를 해결합니다.

# 개요

C++11 부터는 소유권을 이전하는 스마트 포인터인 `auto_ptr`과 `unique_ptr` 외에, 개체의 소유권을 공유하는 스마트 포인터를 추가로 제공합니다.

|항목|내용|
|--|--|
|`shared_ptr` (C++11~)|소유권 공유용 스마트 포인터입니다.|
|`make_shared()` (C++11~)|`shared_ptr`을 효율적으로 생성합니다.|
|`weak_ptr` (C++11~)|`shared_ptr`의 상호 참조 문제 해결용 스마트 포인터입니다.|

# shared_ptr

`unique_ptr`은 소유권을 이전합니다만, `shared_ptr`은 개체의 소유권을 공유하는 스마트 포인터입니다. 소유권이 공유될때마다 내부적으로는 참조 카운트를 증가시키고, 스마트 포인터가 소멸될때마다 참조 카운트를 감소시켜 `0`이 될때 `delete`합니다.

다음 코드를 보면 참조 카운트가 

1. `T{}`로 생성한 개체를 `a`에 저장할 때 1이 되고, 
2. 이를 `b`에 복사 생성할 때 2, 
3. `c`에 복사 생성할때 3이 되고, 
4. 스마트 포인터가 소멸할 때 1씩 감소하는 것을 알 수 있습니다.

또한 `a`, `b`, `c`에서 각각 관리 대상 개체를 수정하더라도 서로 개체를 공유하므로 값이 동일한 것을 알 수 있습니다.

```cpp
class T {
public:
    int m_Val;
public:
    T() {std::cout<<"T : Constructor"<<std::endl;}
    ~T() {std::cout<<"T : Destructor"<<std::endl;}
};
std::shared_ptr<T> a{new T{}};
EXPECT_TRUE(a.use_count() == 1); 

std::shared_ptr<T> b{a};
EXPECT_TRUE(a.use_count() == 2 && b.use_count() == 2); 
{
    std::shared_ptr<T> c{a};
    EXPECT_TRUE(a.use_count() == 3 && b.use_count() == 3 && c.use_count() == 3);  

    // a, b, c가 관리하는 T는 서로 공유합니다.
    a->m_Val = 1; 
    EXPECT_TRUE(a->m_Val == 1 && b->m_Val == 1 && c->m_Val == 1); 

    b->m_Val = 2;
    EXPECT_TRUE(a->m_Val == 2 && b->m_Val == 2 && c->m_Val == 2); 

    c->m_Val = 3;
    EXPECT_TRUE(a->m_Val == 3 && b->m_Val == 3 && c->m_Val == 3);              
} // 유효 범위를 벗어났기 때문에 c가 소멸됨
// c가 소멸되었으므로 2
EXPECT_TRUE(a.use_count() == 2 && b.use_count() == 2);
```

실행 시키면 다음과 같이 생성자와 소멸자는 1회 호출됩니다.

```cpp
T : Constructor
T : Destructor
```
# shared_ptr의 제어 블록(Control Block)

`shared_ptr`은 내부적으로 제어 블록을 별도로 할당하며, 다음 정보를 관리합니다.

1. 관리 대상 개체 포인터
2. `shared_ptr` 참조 카운트
3. `weak_ptr` 참조 카운트(하기 `weak_ptr` 참고)
4. 관리 대상 개체의 `deleter`(있는 경우)
5. 관리 대상 개체의 `allocator`(있는 경우)

```cpp
class T{};

std::shared_ptr<T> a{new T{}};
std::shared_ptr<T> b{a};
```

상기 코드의 상황을 그림으로 도식화 하면 다음과 같습니다. `a`와 `b`는 같은 제어 블록을 공유함으로서, 관리 대상 개체 포인터와 참조 카운트를 공유합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5117fc7c-c283-41d3-811f-f27354c338cc)

# shared_ptr 멤버 함수 

|항목|내용|
|--|--|
`constexpr shared_ptr() noexcept;` (C++11~)<br/><br/>`explicit shared_ptr(T* p);` (C++11~)<br/>`shared_ptr(T* p, deleter);` (C++11~)<br/>`shared_ptr(T* p, deleter, allocator);` (C++11~)<br/><br/>`constexpr shared_ptr(nullptr_t) noexcept;` (C++11~)<br/>`shared_ptr(nullptr_t p, deleter);` (C++11~)<br/>`shared_ptr(nullptr_t p, deleter, allocator);` (C++11~)<br/><br/>`explicit shared_ptr(const weak_ptr&);` (C++11~)<br/>`shared_ptr(unique_ptr&&);` (C++11~)<br/><br/>별칭 생성자<br/>`shared_ptr(const shared_ptr& other, element_type* p) noexcept;` (C++11~)<br/><br/>`shared_ptr( auto_ptr&&);` (C++11~C++17)|`nullptr`이나 `p`를 공유하며, 참조 카운트를 증가시킵니다. 이때 사용자 정의 `deleter`와 `allocator` 를 사용할 수 있습니다. `weak_ptr`과 `unique_ptr`로 생성할 수도 있습니다.|
|`shared_ptr(const shared_ptr& other) noexcept;` (C++11~)|개체와 소유권을 공유하고 참조 카운트를 증가시킵니다.|
|`shared_ptr(const shared_ptr&& other) noexcept;` (C++11~)|이동 생성합니다.|
|`~shared_ptr();` (C++11~)|관리하던 개체의 참조 카운트를 감소시키고, `0`이 되면 `delete`또는 `delete[]`(C++17~)합니다.|
|`shared_ptr& operator =(const shared_ptr& other) noexcept;` (C++11~)|`other` 개체와 소유권을 공유하고 참조 카운트를 증가시킵니다.|
|`shared_ptr& operator =(shared_ptr&& r) noexcept;` (C++11~)|`other`가 관리하는 개체를 `this`로 이동시킵니다.|
|`operator *() const noexcept;` (C++11~)|관리하는 개체의 참조자를 리턴합니다.|
|`operator ->() const noexcept;` (C++11~)|관리하는 개체의 포인터를 리턴합니다.|
|`operator [](ptrdiff_t) const;` (C++17~)|배열을 관리하는 경우 각 요소 개체의 참조자를 리턴합니다.|
|`explicit operator bool() const noexcept;` (C++11~)|`bool`로 형변환시 `nullptr` 이면 `false`를 리턴합니다.|
|`get() const noexcept;` (C++11~)|관리하는 개체의 포인터를 리턴합니다.|
|`swap(shared_ptr& other) noexcept;` (C++11~)|관리하는 개체를 `other`와 바꿔치기 합니다.|
|`reset(T* p);`|기존에 관리하던 개체를 해제하고 `p`를 관리합니다.|
|`use_count() const noexcept;` (C++11~)|참조 카운트를 리턴합니다.|
|`unique() const noexcept;`(C++11~C++17)|다른 `shared_ptr`로 관리하는지 검사합니다. `use_count() == 1`과 같습니다.|
|`lock() const noexcept;` (C++11~)|관리하는 개체 접근을 위해 임시 `shared_ptr`을 생성합니다.|
|`owner_before() const noexcept;` (C++11~)|소유권 개체로 `<` 비교를 합니다.|
|`get_deleter()` (C++11~)|관리하는 개체를 소멸시키는 `deleter`를 리턴합니다.|
|`==` (C++11~)<br/>`!=` (C++11~C++20)<br/>`<` (C++11~C++20)<br/>`<=` (C++11~C++20)<br/>`>` (C++11~C++20)<br/>`>=` (C++11~C++20)<br/>`<=>` (C++20~)|관리하는 개체의 주소로 비교합니다.|
|`<<` (C++11~)|관리하는 개체의 내용을 스트림에 출력합니다.|

# make_shared()

`make_shared()`은 가변 템플릿으로서 `T`개체의 생성자 인자를 전달받아 `T`개체를 생성하고, `T`개체를 관리하는 `shared_ptr`을 생성하여 리턴합니다.

```cpp
template<typename T, typename... Args>
shared_ptr<T> make_shared(Args&&... args);
```

`make_shared()`를 이용하면 좀더 메모리 효율적으로 `shared_ptr`을 사용할 수 있습니다.

다음 코드는 `T`개체를 메모리에 할당하고, 내부적으로 제어 블록을 메모리에 할당하여 메모리를 2회 할당하지만,

```cpp
class T {
public:
    explicit T(int) {}    
};
std::shared_ptr<T> a{new T{10}}; // (△) 비권장. T를 할당하고, 내부적으로 제어 블록을 할당
```

다음은 `T`와 제어 블록의 크기를 합하여 메모리를 1회 할당합니다.

```cpp
std::shared_ptr<T> b = std::make_shared<T>(10); // T + 제어 블록 크기만큼 할당
```

또한 코드가 간결해지는 효과도 있습니다. `shared_ptr<T>{new T{10}}` 보다는 `make_shared<T>(10)`가 간결합니다.

```cpp
class T {
public:
    explicit T(int) {}    
};
std::vector<std::shared_ptr<T>> v;

v.push_back(std::shared_ptr<T>{new T{10}});
v.push_back(std::make_shared<T>(10)); // 구문이 좀더 간결합니다.
v.push_back(std::shared_ptr<T>{}); // nullptr

EXPECT_TRUE(v.size() == 3 && v[2] == nullptr);
```

# enable_shared_from_this

항상 `shared_ptr`로만 생성할 수 있도록 [생성자 접근 차단](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A0%91%EA%B7%BC-%EC%B0%A8%EB%8B%A8---private-%EC%83%9D%EC%84%B1%EC%9E%90)을 하고 `Create()`함수를 제공한다고 합시다.

```cpp
class T {
private:
    T() {} // private로 만들어서 생성자 접근 차단
public:
    static std::shared_ptr<T> Create() {
        return std::shared_ptr<T>{new T{}};      
    }  
};

std::shared_ptr<T> a{new T{}}; // (X) 컴파일 오류. 생성자가 private
std::shared_ptr<T> a{T::Create()};
```

이제, `T{}` 개체를 공유하는 다른 `shared_ptr`을 만드려면, 다음처럼 하면 됩니다.

```cpp
// a의 제어 블록을 공유합니다.
std::shared_ptr<T> b{a}; 

EXPECT_TRUE(a.use_count() == 2);
```

그런데, 상기 방법은 개체를  `shared_ptr` 형태로 알고 있어야 가능합니다. 그래서 여러개의 함수에서 사용할때 `shared_ptr`을 인자로 전달하며 사용해야 합니다. 

하지만 이렇게 항상 `shared_ptr`을 인수로 전달하는 건 부담이라, 개체 포인터나 개체 참조자로 전달했다가 `shared_ptr`을 만들어야 하는 경우가 오면 낭패입니다. 

만약 관리하는 개체로부터 `shared_ptr`을 만들기 위해 다음과 같이 작성한다면, `b`가 `a`의 제어 블록을 공유하지 않고 새로운 제어 블록을 만들어 관리하기 때문에 추후 소유권 분쟁이 발생합니다.

```cpp
// (X) 오동작. 동일한 개체를 각각의 제어 블록으로 관리합니다. 소유권을 공유하지 않았기 때문에 소멸시 각각 delete 하여 소유권 분쟁이 발생합니다.
// a가 관리하는 포인터를 이용하여 새로운 제어 블록을 만듭니다.
std::shared_ptr<T> b{a.get()}; 

EXPECT_TRUE(a.use_count() == 1 && b.use_count() == 1);
```

다행스럽게도 이런 경우를 위하여 `enable_shared_from_this`를 제공하고 있습니다.

`enable_shared_from_this`는 `shared_ptr`이 관리하는 개체로부터 `shared_ptr`을 생성할 수 있습니다. 다만, `shared_ptr`이 미리 만들어져 있어야 합니다.

1. `enable_shared_from_this` 를 `public`상속합니다.(`protected`나 `private`상속은 동작하지 않습니다.)
2. `shared_from_this()`를 호출합니다.

```cpp
class T : public std::enable_shared_from_this<T> {
private:
    T() {} // private로 만들어서 생성자 접근 차단
public:
    // this 포인터로부터 shared_ptr을 생성합니다.
    std::shared_ptr<T> GetPtr() {
        return shared_from_this();
    }
    static std::shared_ptr<T> Create() {
        return std::shared_ptr<T>{new T{}};      
    }  
};

std::shared_ptr<T> a{T::Create()}; // shared_ptr이 만들어 졌습니다. 이제 shared_from_this()를 사용할 수 있습니다.
T* t{a.get()};
std::shared_ptr<T> b{t->GetPtr()}; // 관리하는 개체로부터 shared_ptr을 생성합니다.

EXPECT_TRUE(a.use_count() == 2);   
```

# shared_ptr을 이용한 복사 생성자, 대입 연산자

[대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 에서 개체의 복사 생성이나 대입 연산시 소유권 분쟁이 없도록 포인터를 복제해서 관리하는 `IntPtr`을 소개해 드렸습니다.

`shared_ptr`을 이용하면 개체의 복사 생성이나 대입 연산시 포인터를 공유하는 방식으로 소유권 분생을 해결할 수 있습니다.

다음 코드는,

1. `shared_ptr`을 멤버 변수로 사용합니다.
2. `new`로 생성된 `Data`를 인자로 전달받기 위해 생성자에서 `unique_ptr`을 인자로 사용합니다.
3. 복사 생성자에서 `other`개체의 각 멤버의 복사 생성자를 호출합니다.
4. `shared_ptr` 에서 참조 카운트가 0이 되면 소멸시킬 것이므로 소멸자에서는 별다른 작업을 하지 않습니다.
5. 대입 연산자에서는 각 멤버를 대입합니다. 
   
    이때 `shared_ptr`의 대입 연산자는 `noexcept`로 선언되어 있어서 예외를 발생하지 않습니다. 포인터 복사와 참조 카운트 변경만 있으니까 예외 발생할게 없는거죠.

    ```cpp
    shared_ptr& operator=( const shared_ptr& r ) noexcept; 
    ```
 
    예외 발생을 하지 않으므로, 굳이 `Swap()` 을 사용할 필요가 없어서 코드가 간결합니다.([멤버 변수가 2개 이상인 경우 스마트 포인터와 대입 연산자와의 호환성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EA%B0%80-2%EA%B0%9C-%EC%9D%B4%EC%83%81%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0%EC%99%80-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%EC%9D%98-%ED%98%B8%ED%99%98%EC%84%B1)에서 개체 대입시 예외가 발생할 수 있으므로, `Swap()`을 사용하는게 예외에 안전하다고 한 바 있습니다.)

코드를 테스트 해보면, `T`개체를 복사, 대입하는 경우 소유권 분쟁은 없으며, `Data`를 서로 공유한다는 것을 알 수 있습니다.

```cpp
class Data {
    int m_Val;
public:
    explicit Data(int val) : m_Val(val) {}
    int GetVal() const {return m_Val;}
    void SetVal(int val) {m_Val = val;}
};
class T {
public:    
    std::shared_ptr<Data> m_Data1;
    std::shared_ptr<Data> m_Data2;
public:
    // new로 생성된 Data를 받기 위해 unique_ptr을 인자로 사용합니다.
    T(std::unique_ptr<Data> data1, std::unique_ptr<Data> data2) :
        m_Data1(std::move(data1)),
        m_Data2(std::move(data2)) {}

    // 컴파일러의 암시적 버전과 동일합니다.
    T(const T& other) : 
        m_Data1(other.m_Data1),
        m_Data2(other.m_Data2) {}

    // 컴파일러의 암시적 버전과 동일합니다.
    ~T() {}

    // 컴파일러의 암시적 버전과 동일합니다.
    T& operator =(const T& other) {
        // shared_ptr& operator=( const shared_ptr& r ) noexcept; 입니다. 
        // 포인터 복사와 참조 카운트 변경만 있으므로 예외 발생은 없습니다.
        m_Data1 = other.m_Data1; 
        m_Data2 = other.m_Data2; 
        
        return *this;
    }
};

T a{std::unique_ptr<Data>{new Data{1}}, std::unique_ptr<Data>{new Data{1}}};
T b{std::unique_ptr<Data>{new Data{2}}, std::unique_ptr<Data>{new Data{2}}};
T c{std::unique_ptr<Data>{new Data{3}}, std::unique_ptr<Data>{new Data{3}}};

EXPECT_TRUE(a.m_Data1.use_count() == 1 && b.m_Data1.use_count() == 1 && c.m_Data1.use_count() == 1);

//복사 생성합니다. a와 d는 동일 Data 입니다.
T d{a};
EXPECT_TRUE(a.m_Data1.use_count() == 2 && b.m_Data1.use_count() == 1 && c.m_Data1.use_count() == 1 && d.m_Data1.use_count() == 2);

// a의 Data 값을 수정하면 d의 값도 수정됩니다.
a.m_Data1->SetVal(10);
EXPECT_TRUE(a.m_Data1->GetVal() == 10 && b.m_Data1->GetVal() == 2 && c.m_Data1->GetVal() == 3 && d.m_Data1->GetVal() == 10);

// 대입 합니다. a, b, d는 동일 Data 입니다.
b = d;
EXPECT_TRUE(a.m_Data1.use_count() == 3 && b.m_Data1.use_count() == 3 && c.m_Data1.use_count() == 1 && d.m_Data1.use_count() == 3);

// a의 Data 값을 수정하면 b, d의 값도 수정됩니다.
a.m_Data1->SetVal(20);
EXPECT_TRUE(a.m_Data1->GetVal() == 20 && b.m_Data1->GetVal() == 20 && c.m_Data1->GetVal() == 3 && d.m_Data1->GetVal() == 20);        
```

상기 코드를 보면, 복사 생성자, 소멸자, 대입 연산자가 모두 암시적 버전과 동일한 것을 알 수 있습니다.

즉, 다음처럼 훨씬 더 간결하게 작성할 수 있습니다.

```cpp
class T {
public:    
    std::shared_ptr<Data> m_Data1;
    std::shared_ptr<Data> m_Data2;
public:
    // new로 생성된 Data를 받기 위해 unique_ptr을 인자로 사용합니다.
    T(std::unique_ptr<Data> data1, std::unique_ptr<Data> data2) :
        m_Data1(std::move(data1)),
        m_Data2(std::move(data2)) {}

    // 컴파일러의 암시적 버전과 동일합니다.
    T(const T& other) = default;
    ~T() = default;
    T& operator =(const T& other) = default;
};
```

# shared_ptr Deleter 

`shared_ptr`은 관리하는 개체를 소멸시키는 `deleter`를 사용자 정의 할 수 있습니다. 

다음 코드에서는 람다 표현식으로 `deleter`를 전달했는데요, 함수, 함수자 모두 가능합니다.

```cpp
std::shared_ptr<int> a{
    new int{10}, 
    [](int* obj) { // 람다 표현식외에 함수, 함수자 모두 가능합니다.
        delete obj;
        std::cout<<"deleter test"<<std::endl;
    }
};  

std::shared_ptr<int>b{a};
*b = 20;
EXPECT_TRUE(*a == 20 && *b == 20);
```

# shared_ptr Allocator

`shared_ptr`은 관리하는 개체를 메모리에 할당하는 `allocator`를 사용자 정의 할 수 있습니다.

`allocator`의 구현 방법은 [할당자(Allocator)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-allocator/)를 참고하기 바랍니다.

```cpp
std::shared_ptr<int> a{
    new int {10},
    std::default_delete<int>{}, // 기본 deleter
    std::allocator<int>{} // 기본 allocater
};
std::shared_ptr<int>b{a};
*b = 20;
EXPECT_TRUE(*a == 20 && *b == 20);
```

# shared_ptr 별칭 생성자

`shared_ptr`은 별칭 생성자(Aliasing Constructor)라는 좀 특별한 생성자를 가지고 있습니다. 이 생성자는 소유권 대상인 개체와 접근 대상 개체가 다른 경우에 사용합니다.

보통,

```cpp
std::shared_ptr<T> data{new T{}};
```

와 같이 생성하면, `T` 개체의 소유권에 대해 참조 카운팅을 하고, `*`, `->`등으로 `T`개체에 접근하는데요, 별칭 생성자로 생성하면, 서로 다른 개체에 대해 참조 카운팅과 접근을 할 수 있습니다.

선언은 다음과 같은데요, `other`개체에 참조 카운팅을 하고, `p` 개체를 접근합니다. 따라서, `p`의 수명 주기가 전적으로 `other`에 종속적일 때만 안전하게 사용 가능합니다. 예를 들어 `p`가 `other`의 멤버 변수 일때 처럼요.

```cpp
shared_ptr(const shared_ptr& other, element_type* p) noexcept;
```

다음의 경우에 별칭 생성자를 사용할 수 있습니다.

1. `MainData` 개체가 `SubData`를 멤버 변수로 포함하고 있고, 
2. `MainData` 개체가 `shared_ptr`로 관리될 때,
3. 임의의 함수 `f()` 가 `shared_ptr<SubData>`를 인자로 사용하는 경우

`MainData` 가 애초에 `shared_ptr<SubData>`를 멤버 변수로 가지면, 그냥 `shared_ptr<SubData>`를 전달해 버리면 되는 일입니다만, 상황에 따라 그러지 못할 수도 있으니깐요.

다음 코드는 별칭 생성자의 사용 예입니다.

1. `MainData`에서 `m_SubData`를 가지고 있으며,
2. 별칭 생성자로 `shared_ptr<SubData> subData` 개체를 만듭니다.
3. `mainData1`, `mainData2`, `subData` 가 참조 카운트를 공유하며,
4. `subData`의 개체 접근을 통해 `mainData1`의 `m_SubData`를 수정합니다.

```cpp
class SubData {
    int m_Val{0};
public:
    SubData() {std::cout<<"SubData : Constructor"<<std::endl;}
    ~SubData() {std::cout<<"SubData : Destructor"<<std::endl;}

    int GetVal() const {return m_Val;}
    void SetVal(int val) {m_Val = val;}
};
class MainData {
    SubData m_SubData;
public:
    MainData() {std::cout<<"MainData : Constructor"<<std::endl;}
    ~MainData() {std::cout<<"MainData : Destructor"<<std::endl;}            
    SubData& GetSubData() {return m_SubData;}        
};

std::shared_ptr<MainData> mainData1{new MainData{}};
std::shared_ptr<MainData> mainData2{mainData1};

EXPECT_TRUE(mainData1.use_count() == 2 && mainData2.use_count() == 2);

// 별칭 생성자를 만듭니다. 생성과 소멸은 MainData로 하지만, 참조하는 포인터는 SubData입니다.
std::shared_ptr<SubData> subData{mainData1, &(mainData1->GetSubData())};

// mainData1, mainData2, subData에서 사용하므로 3입니다.
EXPECT_TRUE(mainData1.use_count() == 3 && mainData2.use_count() == 3 && subData.use_count() == 3);

// subData를 수정하면 MainData에서도 같이 수정됩니다.
subData->SetVal(10);
EXPECT_TRUE(mainData1->GetSubData().GetVal() == 10 && mainData2->GetSubData().GetVal() == 10 && subData->GetVal() == 10);
```

실행 결과를 보면 생성한 만큼 잘 소멸하는 것을 알 수 있습니다.

```cpp
SubData : Constructor
MainData : Constructor
MainData : Destructor
SubData : Destructor
```

# owner_before() 비교

`shared_ptr`의 대소 비교 연산자는 관리하는 개체의 주소를 비교합니다. 보통은 소유권을 가진 개체와 관리하는 개체가 같으므로 상관 없습니다만, 별칭 생성자를 이용하면 이둘이 서로 다르므로, 소유권을 가진 개체로 비교 하고 싶을 때는 `owner_before()`를 사용합니다.

다음 코드에서 별칭 생성자로 `c`를 만들고, `<`와 `owner_before()`의 차이를 나타내었습니다.

```cpp
std::shared_ptr<int> a{new int{10}};
std::shared_ptr<int> b{a};

// a, b는 동일한 개체이므로 < 비교시 !(a < b) && !(b < a). 즉,동등함 
EXPECT_TRUE(!(a < b) && !(b < a));

// 비록 값은 10으로 같으나 주소가 다른 영역에 있으므로 동등하지 않음
b = std::shared_ptr<int>{new int{10}};
EXPECT_TRUE(!(!(a < b) && !(b < a)));

// 별칭 생성자로 생성
// 소유권 개체 : a, 접근 대상 개체 : data
int data{10};
std::shared_ptr<int> c{a, &data};

// a, c 가 접근 대상 개체가 주소가 다른 영역에 있으므로 동등하지 않음
EXPECT_TRUE(!(!(a < c) && !(c < a)));

// a, c 의 소유권 개체는 동일하므로 동등함
EXPECT_TRUE(!(a.owner_before(c)) && !(c.owner_before(a)));
```
# owner_less 

`owner_before()`를 이용하여 소유권 개체의 주소로 비교하는 함수자입니다.

# shared_ptr 형변환 

`shared_ptr` 형변환 할때

```cpp 
dynamic_cast<std::shared_ptr<Base>>(derived);
```

를 사용하면, 내부 관리 개체를 형변환 하는 것이 아니라 `shared_ptr`자체를 형변환 하는 것이므로 동작하지 않습니다.

이를 위해 다음과 같은 형변환 함수들을 제공합니다.

|항목|내용|
|--|--|
|`const_pointer_cast()`|상수성만 변환|
|`static_pointer_cast()`|타입 유사성을 지키며 변환|
|`dynamic_pointer_cast()`|타입 유사성을 지키며 변환.<br/>[Runtime Type Info(RTTI)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#runtime-type-infortti%EC%99%80-%ED%98%95%EB%B3%80%ED%99%98)가 있는 개체([가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있는 개체)만 가능.|
|`reinterpret_pointer_cast()`|상속관계를 무시하고 변환.<br/>정수를 포인터로 변환.|

```cpp
class Base {
public:
    virtual ~Base() {}
    virtual int Func() {return 1;} // #1
};

class Derived : public Base {
public: 
    virtual int Func() override {return 2;} // #2
};

std::shared_ptr<Derived> derived{new Derived{}};

// dynamic_cast<std::shared_ptr<Base>>(derived); // (X) 컴파일 오류

// 관리하는 개체를 Derived에서 Base로 형변환합니다.
std::shared_ptr<Base> base{std::dynamic_pointer_cast<Base>(derived)};

// 가상 함수가 잘 호출됩니다.
EXPECT_TRUE(derived->Func() == 2 && base->Func() == 2);

// drived, base 총 2개
EXPECT_TRUE(derived.use_count() == 2 && base.use_count() == 2);
```

# 상호 참조

트리의 노드를 구현해 봅시다. 보통 `Node`는 부모로 이동할 수 있는 포인터와 자식들을 관리하는 컨테이너로 구성됩니다.

다음 코드에서는,

1. `shared_ptr`로 부모 포인터를 관리합니다.
2. `vector<shared_ptr<Node>>` 로 자식을 관리합니다.
3. 생성자에서 부모 포인터를 전달받고, `Add()`로 자식을 추가합니다.

`root`를 생성하고, 자식 2개를 `Add`했으니 `Node`는 총 3개 생성됩니다. 그리고, `root`의 참조 카운트는 `root` 에서 1개, 자식 2개에서 `m_Parent`로 저장하고 있으니, 총 3개가 됩니다.

```cpp
class Node {
    std::shared_ptr<Node> m_Parent; // 트리의 부모 노드
    std::vector<std::shared_ptr<Node>> m_Children; // 트리의 자식 노드

public:
    explicit Node(std::shared_ptr<Node> parent) : m_Parent(parent) {
        std::cout<<"Node : Constructor"<<std::endl;               
    } 
    ~Node() {
        std::cout<<"Node : Destructor"<<std::endl;    
    }
    void Add(std::shared_ptr<Node> child) {
        m_Children.push_back(child);
    }
};

// root는 부모가 없어서 nullptr로 노드를 생성합니다.
std::shared_ptr<Node> root{new Node{std::shared_ptr<Node>{}}};

// root를 부모 삼아 2개의 자식 노드를 생성합니다.
root->Add(std::shared_ptr<Node>{new Node{root}});
root->Add(std::shared_ptr<Node>{new Node{root}});

// root 1개 + child 2개 = 3개. root 개체가 소멸되면 참조 카운트만 2로 만드로 Node를 소멸시키지 않습니다.
EXPECT_TRUE(root.use_count() == 3);
```

실행 결과를 보면 다음과 같이 `Node`의 생성자만 불리고 소멸자가 안불립니다. 

```
Node : Constructor
Node : Constructor
Node : Constructor
```

이는 `root`가 소멸할때 참조 카운트를 `3`에서 `2`로 바꿀뿐 관리하는 `Node` 소멸은 안하기 때문입니다. `root`에서 관리하는 `Node`의 소멸자가 안불리니 `m_Children`에 있는 자식 `Node`들도 소멸되지 않아 아무런 소멸자도 불리지 않게 됩니다. 자식 `Node`가 `m_Parent`로 소유권을 갖고 있기 때문에 벌어진 일입니다.

이러한 경우를 상호 참조라 하며, 상호 참조시에 소유권을 공유하면, 서로 소유권을 주장하다가 결국 상기 예제처럼 아무것도 소멸을 못시키게 됩니다. 

그래서 `m_Parent`를 `shared_ptr`이 아닌 소유권이 없는 일반 포인터로 만들 수도 있지만, `weak_ptr`로 해결하는게 예외에 좀더 안전합니다.

# weak_ptr

`weak_ptr`은 `shared_ptr`로부터 생성되며, `shared_ptr`과 개체는 공유하지만, 소유권은 공유하지 않는 포인터 입니다. 즉, 참조 카운트를 증가/감소 하지 않고, 제어 블록에서 별도의 `weak_ptr` 카운트를 증가/감소 시키기만 할 뿐, 개체를 `delete` 하지 않습니다.(개체는 `shared_ptr`에서 `delete`합니다.)

상호 참조에서 `m_Parent`를 `weak_ptr`로 수정해 보면,

```cpp
class Node {
    std::weak_ptr<Node> m_Parent; // 트리의 부모 노드는 소유권이 없습니다.
    std::vector<std::shared_ptr<Node>> m_Children; // 트리의 자식 노드

public:
    explicit Node(std::weak_ptr<Node> parent) : m_Parent(parent) {
        std::cout<<"Node : Constructor"<<std::endl;               
    } 
    ~Node() {
        std::cout<<"Node : Destructor"<<std::endl;    
    }
    void Add(std::shared_ptr<Node> child) {
        m_Children.push_back(child);
    }
};

// root는 부모가 없어서 nullptr로 노드를 생성합니다.
std::shared_ptr<Node> root{new Node{std::weak_ptr<Node>{}}};

// root를 부모 삼아 2개의 자식 노드를 생성합니다.
root->Add(std::shared_ptr<Node>{new Node{root}});
root->Add(std::shared_ptr<Node>{new Node{root}});

// root 1개. weak_ptr 참조 카운트는 2개
EXPECT_TRUE(root.use_count() == 1);
```

`root` 의 참조 카운트는 1개여서 `root`소멸시 `Node`를 소멸시키며, `vector`가 소멸되고, `vector`에 추가됐던 자식 `Node`들이 소멸되어, 생성된 `Node`개체들이 모두 정상적으로 소멸됩니다.

```cpp
Node : Constructor
Node : Constructor
Node : Constructor
Node : Destructor
Node : Destructor
Node : Destructor
```

# weak_ptr 멤버 함수

|항목|내용|
|--|--|
|`constexpr weak_ptr() noexcept;` (C++11~)<br/><br/>`weak_ptr(const shared_ptr& other) noexcept;` (C++11~)|`shared_ptr`로 부터 생성합니다.|
|`weak_ptr(const weak_ptr& other) noexcept;` (C++11~)|복사 생성합니다.|
|`weak_ptr(const weak_ptr&& other) noexcept;` (C++11~)|이동 생성합니다.|
|`~weak_ptr();` (C++11~)|기존에 관리하던 개체를 해제합니다. `shared_ptr`의 `weak_ptr` 참조 카운트만 감소시킬 뿐 개체에 대한 `delete`를 수행하지는 않습니다.|
|`weak_ptr& operator =(const weak_ptr& other) noexcept;` (C++11~)<br/><br/>`weak_ptr& operator =(const shared_ptr& other) noexcept;` (C++11~)|`weak_ptr`이나 `shared_ptr`로 부터 대입받습니다.|
|`weak_ptr& operator =(weak_ptr&& other) noexcept;` (C++11~)|`other`가 관리하는 개체를 `this`로 이동시킵니다.|
|`swap(unique_ptr& other) noexcept;` (C++11~)|관리하는 개체를 `other`와 바꿔치기 합니다.|
|`reset() noexcept;` (C++11~)|기존에 관리하던 개체를 해제합니다. `shared_ptr`의 `weak_ptr` 참조 카운트만 감소시킬 뿐 개체에 대한 `delete`를 수행하지는 않습니다.|
|`use_count() const noexcept;` (C++11~)|`shared_ptr`의 참조 카운트를 리턴합니다.|
|`expired() const noexcept;` (C++11~)|관리하는 개체가 소멸됐거나 소멸중인지 검사합니다. `use_count() == 0`과 같습니다.|
|`lock() const noexcept;` (C++11~)|관리하는 개체 접근을 위해 임시 `shared_ptr`을 생성합니다.|
|`owner_before() const noexcept;` (C++11~)|소유권 개체로 `<` 비교를 합니다.|

# weak_ptr 사용

`weak_ptr`는 `shared_ptr`이나 `weak_ptr`로 부터 생성되며, 관리하는 개체에 접근하려면, `lock()`함수를 이용하여 `shared_ptr`을 임시로 생성한 뒤, 이를 사용해야 합니다.

```cpp
std::shared_ptr<int> sp1{new int{10}};
std::shared_ptr<int> sp2{sp1};
std::weak_ptr<int> wp1{sp1}; // shared_ptr로부터 생성됩니다.

EXPECT_TRUE(sp1.use_count() == 2 && sp1.use_count() == wp1.use_count()); // weak_ptr::use_count()는 shared_ptr의 참조 카운트 입니다.

std::weak_ptr<int> wp2{wp1}; // weak_ptr로부터 생성됩니다.

// *wp1 = 20; // weak_ptr은 관리하는 개체에 직접 접근할 수 없습니다.
std::shared_ptr<int> temp1{wp1.lock()}; // shared_ptr을 만들어 접근해야 합니다.
std::shared_ptr<int> temp2{wp2.lock()};

*temp1 = 20;
EXPECT_TRUE(*sp1 == 20 && *sp2 == 20 && *temp1 == 20 && *temp2 == 20);

EXPECT_TRUE(sp1.use_count() == 4); // sp1, sp2, temp1, temp2 총 4개 입니다.
```

# bad_weak_ptr

`share_ptr`에서 잘못된 `weak_ptr`을 사용할때 발생하는 예외입니다. 

다음 코드는 `shared_ptr`이 소멸된 후에 `weak_ptr`을 사용하다가 `bad_weak_ptr` 예외가 발생한 예입니다.

```cpp
std::weak_ptr<int> wp;
{
    std::shared_ptr<int> sp{new int{10}};
    wp = sp;
} // sp가 소멸되었습니다.
try {
    // 소멸된 sp를 사용하는 wp로 shared_ptr을 만듭니다.
    // bad_weak_ptr 예외가 발생합니다.
    std::shared_ptr<int> error{wp};
}
catch (std::bad_weak_ptr&) {
    std::cout<<"bad_weak_ptr"<<std::endl;
}
```