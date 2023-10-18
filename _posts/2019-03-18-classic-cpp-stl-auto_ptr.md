---
layout: single
title: "#18. [고전 C++ STL] auto_ptr, 함수 인자, 리턴 타입 설계"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었으니 더이상 사용하지 마라.
> * 복사 부하, 상수성, 스마트 포인터를 이용하여 좀더 단단한 코딩 계약을 만들어라.

> **모던 C++**
> * (C++11~) [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)은 소유권 이전용 스마트 포인터입니다. 기존 [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)을 대체합니다. [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 배열의 `delete[]` 미지원, `lvalue` 복사 대입 연산시 이동 동작을 하는 등의 사유로 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.
> * (C++11~) [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr)은 소유권 공유용 스마트 포인터입니다.
> * (C++11~) [weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr)은 [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#shared_ptr)의 상호 참조 문제를 해결합니다.

# 개요

[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)는 포인터 관리를 좀더 편안하게 할 수 있도록 표준에서 제공하는 스마트 포인터 입니다.

하지만 몇가지 문제점이 있습니다.

1. 배열을 `delete[]`가 아닌 `delete`로 삭제합니다.(이러면 배열 요소들이 제대로 소멸되지 않습니다. [개체 생성/소멸과 배열 생성/소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8%EA%B3%BC-%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 참고)
2. 복사 대입 연산자에서 소유권을 이전합니다.(의도한 코드입니다만, 이게 암시적으로 동작하면서 런타임 오류가 많이 발생합니다.)

> *(C++11~) [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되어 더이상 사용하시면 안되고, [unique_ptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-unique_ptr/)을 사용해야 합니다.*



포인터를 사용할 때 머리가 복잡해 지는 것은 

1. 잊지 않고 잘 삭제했느냐
2. 여러번 삭제되지는 않았느냐

입니다.

다음 코드에서 `U`는 `T`의 포인터를 저장하고, `Func()`은 `U` 를 생성하여 리턴합니다. 그런데 `Func()`에서 `T`를 `new`하고 `delete`를 다 해버립니다.

```cpp
class T {};
class U {
    T* m_T; // T의 포인터를 저장합니다.
public:
    explicit U(T* t) : m_T(t) {}
};

U Func() {
    T* t = new T();
    U u(t);
    delete t; // (△) 비권장. t를 new로 생성했으니 delete 합니다. 그럼 u에 저장된 t는 어쩔까요?
    return u; // (△) 비권장. 삭제된 t를 담고 있는 u를 사용하면 안되죠.
} 
```

그렇다고 `delete`를 안한다면, `U`를 사용할때마다 잊지 않고 `delete`를 호출해야 합니다. 실수하기 쉽죠.

```cpp
class T {};
class U {
public:
    T* m_T; // T의 포인터를 저장합니다.
public:
    explicit U(T* t) : m_T(t) {}
};

U u1(new T);
U u2(new T);

delete u1.m_T; // (△) 비권장. U의 사용법을 잘 숙지하고, 엄청 꼼꼼해서 잊지 않고 호출해야 합니다.
delete u2.m_T;
```

그래서 [포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81) 에서 언급한 대로 소멸자에서 소멸시켜야 합니다. 그러나 이 방법도 `U`를 복사 생성자로 생성하거나 복사 대입 연산자를 사용하면 소유권 분쟁이 일어나 낭패입니다.([포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81) 참고)

```cpp
class T {};
class U {
public:
    T* m_T; // T의 포인터를 저장합니다.
public:
    explicit U(T* t) : m_T(t) {}
    ~U() {delete m_T;} // 소멸자에서 소멸시킵니다.
};

U u1(new T); // (O) delete m_T;는 U 소멸자에서 호출합니다.
U u2(new T);

u1 = u2; //(X) 예외 발생. 기존 u1 것은 delete해야 하고, u1, u2는 동일한 포인터를 소유합니다. 누가 삭제해야 하나요?
```

[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 이러한 소유권 분쟁을 해결하기 위해 소유권을 이전하는 스마트 포인터 입니다.

1. 복사 생성시, 복사 대입시 소유권을 이전합니다.
2. 소멸시 관리하는 개체를 `delete`합니다.

따라서 다음과 같이 `T* m_T;` 대신 `auto_ptr<T> m_T;`로 관리하면 소유권을 이전하면서 사용할 수 있습니다.

```cpp
class T {};
class U {
private:
    // T의 포인터를 auto_ptr로 관리합니다.
    // 복사 생성자, 복사 대입 연산자에서 소유권을 이전합니다.
    // auto_ptr 소멸시 T를 알아서 소멸시킵니다. 
    std::auto_ptr<T> m_T;
public:
    explicit U(T* t) : m_T(t) {}
};

U u1(new T); // (O)명시적으로 T를 delete하지 않아도 auto_ptr 자동 소멸시 소멸됩니다.
U u2(new T);

u1 = u2; //(O) 소유권을 u1으로 이전합니다.
```

# auto_ptr 멤버 함수

|항목|내용|
|--|--|
|`auto_ptr& operator =(auto_ptr& other);`|기존 관리하던 개체를 해제하고 `other`의 개체를 관리합니다. 이때 `other`는 더이상 개체를 관리하지 않습니다.|
|`*`|관리하는 개체의 참조자를 리턴합니다.|
|`->`|관리하는 개체의 포인터를 리턴합니다.|
|`get();`|관리하는 개체의 포인터를 리턴합니다.|
|`reset(T* p);`|기존에 관리하던 개체를 해제하고 `p`를 관리합니다.|
|`release();`|관리하는 개체를 해제합니다.|

# auto_ptr 구현

[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)은 다음처럼 구현됩니다.

1. 생성시 `ptr`을 멤버 변수로 저장합니다.
2. 소멸시 `m_Ptr`을 `delete`합니다.
3. 복사 생성과 복사 대입 연산시 기존 포인터는 삭제하고, 신규 포인터를 저장 관리합니다. 이때 `other`가 관리하는 포인터는 `NULL`로 초기화하여 더이상 관리하지 않습니다.
4. `->`, `*`연산자를 오버로딩하여 관리하는 포인터에 접근할 수 있게 합니다.

```cpp
template<typename T>
class my_auto_ptr { 
    T* m_Ptr; 
public:
    my_auto_ptr(T* ptr) : 
        m_Ptr(ptr) {}
    ~my_auto_ptr() {
        delete m_Ptr;
    }

    // 소유권 이전
    my_auto_ptr(my_auto_ptr& other) : 
        m_Ptr(other.m_Ptr) { 
        other.m_Ptr = NULL; // 소유권이 this로 왔으므로 other는 NULL 
    }
    my_auto_ptr& operator =(my_auto_ptr& other) { 
        delete m_Ptr; // 이전 관리하던 포인터는 삭제
        m_Ptr = other.m_Ptr; 
        other.m_Ptr = NULL; // 소유권이 this로 왔으므로 other는 NULL 

        return *this;
    } 
    T* operator ->() {
        return m_Ptr;
    } 
    T& operator *() {
        return *m_Ptr;
    } 
    T* get() {
        return m_Ptr;
    } 
    T* release() { 
        T* result = m_Ptr; 
        m_Ptr = NULL; // 더이상 소유권 없음
        return result; 
    }
};

my_auto_ptr<int> a(new int(10));
my_auto_ptr<int> b(new int(20));

a = b;

EXPECT_TRUE(*a == 20); // 이전 b 값을 저장
EXPECT_TRUE(b.get() == NULL); // 더이상 포인터를 관리하지 않음
```

# auto_ptr을 활용한 함수 인자, 리턴 타입

[함수, 함수 포인터, 리턴, 인자(매개변수, Parameter)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/) 에서 


1. 값을 전달받을 것인지, 참조자를 전달받을 것인지
2. 상수를 전달받을 것인지, 비 상수를 전달받을 것인지

신중하게 결정해야 복사 부하를 줄이고, 타입에 기반한 코딩 계약 을 수립할 수 있다고 언급하였고,

[Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)와 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98) 에서 멤버 함수의 바람직한 인자와 리턴값 설계를 예시하였습니다.

[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)을 활용하면, 

1. `new`로 생성한 포인터를 전달할지, 
2. 리턴으로 전달받은 개체를 `delete`해야 할지

좀더 단단한 코딩 계약이 가능합니다.

다음은 전체적인 예시입니다.

```cpp
T GetData const; // (O, △) 부분적으로 비권장. 임시 객체를 리턴합니다. 하지만 혹시 멤버 변수를 리턴하는지 확인해 봐야 합니다.
const T& GetData() const; // (O) 멤버 변수를 리턴합니다. 수정하면 안됩니다.
T& GetData(); // (O) 멤버 변수를 리턴합니다.
T& GetData() const; // (△) 비권장. const 함수인데, 리턴받은 곳에서 멤버 수정이 가능합니다.
const T* GetData() const; // (△) 비권장. 리턴값을 delete 해야 하는건지 아닌건지 좀 모호합니다.
auto_ptr<T> GetData() const; // (O) 리턴값은 delete 해야합니다.

void f(T v); // (O, △) 부분적으로 비권장. 객체 전달입니다만, 복사 부하가 있을지 확인해 봐야 합니다.
void f(const T* p); // (△) 비권장. f에서 널검사는 하는지 좀 봐야 합니다.
void f(T* p); // (△) 비권장. f에서 널검사는 하는지 좀 봐야 합니다.
void f(const T& r); // (O) 널검사도 필요없고 참 좋습니다.
void f(T& r); // (O) 널검사도 필요없고 참 좋습니다.
void f(const auto_ptr<T>& p); // (△) 비권장. 쓸데없이 auto_ptr을 전달하지 않고, f(const T& r)이나, f(T& r)을 사용하는게 낫습니다.
void f(auto_ptr<T>& p); // (△) 비권장. p를 수정하겠다는 건지, auto_ptr을 수정하겠다는 건지 불분명 합니다. f(const T& r)이나, f(T& r)을 사용하는게 낫습니다.
void f(auto_ptr<T> p); // (O) new로 생성한 개체를 전달해야 합니다.
```
