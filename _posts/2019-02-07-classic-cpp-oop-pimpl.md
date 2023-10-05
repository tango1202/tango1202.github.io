---
layout: single
title: "#7. [고전 C++ 개체 지향] PImpl 이디엄"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * PImpl 이디엄은 멤버 변수 접근 부하, 메모리 공간 부하, 과도한 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 사용의 부하가 있으니, 상황에 맞게 도입하라.

 
# 개요 

PImpl(pointer to implementation, 구현에 대한 포인터)은 구현의 상세 정보를 은닉하는 프로그래밍 기법으로서, 코드간 종속성이나, 컴파일 종속성을 최소화 해줍니다.

1. 개체 내부의 [중첩 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A4%91%EC%B2%A9-%ED%81%B4%EB%9E%98%EC%8A%A4)에 멤버 변수들을 정의합니다.
2. 개체 선언부에서 중첩 클래스를 포인터 멤버 변수로 정의합니다.
3. 개체 정의부에서 중첩 클래스의 실제 선언 및 정의를 합니다.

그러면, 선언부에서는 중첩 클래스의 포인터형 변수의 크기만 알면 되기 때문에 [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)만 하면 됩니다.

```cpp
// 선언에서, 아마도 헤더 파일
class T {
    class Impl; // 전방 선언
    Impl* m_Impl; // (O) 포인터 멤버 변수로 선언. 전방 선언만 해주면, 클래스의 포인터이므로 8byte크기라고 생각하고, 컴파일을 계속합니다.
};

// 정의에서, 아마도 cpp 파일
class T::Impl { // 실제 선언 및 정의를 합니다.
public:   
    IntPtr m_Val1; 
    IntPtr m_Val2; 
}; 
```

헤더 파일에는 `Impl`의 전방 선언만 있으므로, 이 헤더파일을 `#include` 해봤자, 내부 구현이 어떻게 되는지 알 수 없고, 알 필요도 없습니다. 즉 , `Impl`의 내부 구현에서 사용한 `IntPtr`이 은닉되어 컴파일 종속성이 최소화됩니다. 이제 `Impl`의 내부 구현을 이리저리 바꾸더라도, 최소한만 컴파일 되기 때문에 대규모 프로젝트에서의 빌드 시간을 단축할 수 있습니다.

다음 예제는  [멤버 변수가 2개 이상인 경우 스마트 포인터와 대입 연산자와의 호환성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EA%B0%80-2%EA%B0%9C-%EC%9D%B4%EC%83%81%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0%EC%99%80-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%EC%9D%98-%ED%98%B8%ED%99%98%EC%84%B1) 의 클래스 `T`를 PImpl 방식으로 리팩토링 한 예입니다.

1. `m_Val1`, `m_Val2`를 `T::Impl`로 이전하였습니다.
2. `T`에서 `class Impl;` 전방 선언을 하고, `Impl* m_Impl;`을 포인터 멤버 변수로 선언하였습니다.
3. `m_Impl` 복사 생성을 위해 `T`의 복사 생성자를 추가했습니다.
4. `m_Impl` 소멸을 위해 `T`의 소멸자를 추가했습니다.
5. `m_Impl` 대입 연산을 위해 `swap`을 이용한 대입 연산자를 추가했습니다. `swap`은 포인터끼리의 바꿔치기이므로 복사 부하가 거의 없습니다.
6. `T::Impl` 정의에서, 복사 생성자는 두고, 대입 연산자는 사용하지 않기에 `private`로 막았습니다.

(`IntPtr`은 [대입 연산자까지 지원하는 스마트 포인터](??)의 내용을 참고하기 바랍니다.)

```cpp
// ----
// 선언에서
// ----
class T {
    class Impl;
    Impl* m_Impl; // T::Impl 타입의 클래스 포인터를 멤버 변수로 사용합니다. 선언 시점에 구체 구현은 은닉되어 있습니다. 
public:
    // val1, val2 : new 로 생성된 것을 전달하세요.
    T(int* val1, int* val2);
    // (△) 비권장 . m_Impl이 포인터 멤버 변수여서, 복사 생성자에서 복제, 소멸자에서 delete, swap을 이용한 대입 연산자를 구현해야 합니다.
    T(const T& other);
    ~T();
    T& operator =(const T& other);
    void Swap(T& other);

    int GetVal1() const;
    int GetVal2() const;
};

// ----
// 정의에서
// ----

// 복사 생성시 m_Ptr을 복제하고, 소멸시 delete 합니다.
// 대입 연산은 임시 개체 생성 후 swap 합니다.
class IntPtr {
    // 대입 연산자까지 지원하는 스마트 포인터 참고
};

class T::Impl {
public: // T 에서 멤버 변수를 자유롭게 쓰도록 public 입니다.
    // 스마트 포인터를 사용합니다. 암시적 복사 생성자에서 복제본을 만들고, 소멸자에서 잘 소멸합니다.
    IntPtr m_Val1;
    IntPtr m_Val2;
    Impl(int* val1, int* val2) : 
        m_Val1(val1),
        m_Val2(val2) {}
private:        
    // 대입 연산자는 사용하지 않으므로 private로 못쓰게 만듭니다.
    Impl& operator =(const Impl& other) {return *this;}  
};

T::T(int* val1, int* val2) :
    m_Impl(new T::Impl(val1, val2)) {}
T::T(const T& other) :
    m_Impl(new T::Impl(*other.m_Impl)) {} // T::Impl의 복사 생성자를 호출합니다.
T::~T() {delete m_Impl;} // T::Impl을 소멸시킵니다.

// Swap으로 대입 연산자를 구현합니다.
T& T::operator =(const T& other) {
    T temp(other); 
    Swap(temp); 
    return *this;
} 
void T::Swap(T& other) {
    // PImpl 이디엄을 사용하면 포인터 끼리의 swap이므로 복사 부하가 거의 없습니다.
    std::swap(this->m_Impl, other.m_Impl);
}

// T::Impl의 멤버 변수를 이용합니다.
int T::GetVal1() const {return *(m_Impl->m_Val1);}
int T::GetVal2() const {return *(m_Impl->m_Val2);}
```

# 스마트 포인터를 이용한 PImpl 이디엄 구현

또한, 멤버 변수가 `m_Impl` 1개 이므로, 스마트 포인터로 만들면, 복사 생성자, 소멸자, 대입 연산자를 별도로 작성하지 않아도 됩니다.([대입 연산자까지 지원하는 스마트 포인터](??) 참고)

다만, 

```cpp
class T {
    class Impl;
    Impl* m_Impl;
...
};    
```

을 스마트 포인터로 변경하면,

```cpp
class T {
    class Impl;
    ImplPtr m_Impl; // (X) 컴파일 오류. ImplPtr의 제대로된 선언이 필요합니다.
...
};  
```
와 같이 해야 하는데, `ImplPtr`의 실제 크기등을 알아아 하므로, 제대로된 선언이 필요합니다.

그래서, 다음처럼 `ImplPtr`을 선언하면, 이번에는 `T::Impl`의 전방 선언이 필요합니다.

```cpp
class ImplPtr {
    T::Impl* m_Ptr; // (X) 컴파일 오류. T::Impl 의 전방 선언이 필요합니다.
};
class T {
    class Impl;
    ImplPtr m_Impl; 
};
```

그래서, 다음처럼 `T::Impl`의 전방 선언을 해보시면, 중첩 클래스여서 전방 선언이 안됩니다.(중첩 클래스는 전방 선언이 안됩니다. [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8) 참고)

```cpp
class T::Impl; // (X) 컴파일 오류. 중첩 클래스는 전방 선언을 할 수 없습니다.
class ImplPtr {
    T::Impl* m_Ptr; 
};
class T {
    class Impl;
    ImplPtr m_Impl; 
};
```

그래서, 스마트 포인터로 PImpl을 구현하려면, 중첩 클래스가 아닌 일반 클래스로 정의해야 됩니다.

이제 다음처럼 복사 생성자, 소멸자, 대입 연산자 정의 없이 간소하게 클래스를 작성할 수 있습니다.

```cpp
class T {
    TImplPtr m_Impl; 
public:
    T(int* val1, int* val2);

    int GetVal1() const;
    int GetVal2() const;
};
```

다음은 전체 코드입니다.

1. 중첩 클래스인 `T::Impl`을 밖으로 빼서 `TImpl`로 선언하였습니다.
2. 스마트 포인터인 `TImplPtr`을 정의했습니다.
3. 스마트 포인터를 사용하므로 `T`의 복사 생성자, 소멸자, 대입 연산자, `Swap`을 제거했습니다.

```cpp
// --------
// 선언에서
// --------

// ----
// T클래스의 Impl 전방 선언
// ----
class TImpl; // 중첩 클래스는 전방 선언이 안되어 별도 클래스로 선언하고, 전방 선언합니다.

// ----    
// TImplPtr 선언
// ----
class TImplPtr {
private:
    TImpl* m_Ptr; 
public: 
    explicit TImplPtr(TImpl* ptr);
    TImplPtr(const TImplPtr& other);
    ~TImplPtr();

    TImplPtr& operator =(const TImplPtr& other);
    void Swap(TImplPtr& other);

    const TImpl* operator ->() const;
    TImpl* operator ->();

    const TImpl& operator *() const;
    TImpl& operator *();

    bool IsValid() const;  
};

// ----    
// (O) T 선언 : 복사 생성자, 소멸자, swap을 이용한 대입 연산자, Swap 불필요
// ----
class T {
    // 중첩 클래스는 전방 선언이 안되어 별도 클래스로 선언하고, 전방 선언합니다.
    // (O) 스마트 포인터를 사용하여, 복사 생성자, 소멸자를 구현할 필요가 없고, 
    // (O) 멤버 변수도 1개여서 Swap으로 대입 연산자를 구현할 필요가 없습니다.
    TImplPtr m_Impl; 
public:
    // val1, val2 : new 로 생성된 것을 전달하세요.
    T(int* val1, int* val2);

    int GetVal1() const;
    int GetVal2() const;
};

// --------
// 정의에서
// --------

// 복사 생성시 m_Ptr을 복제하고, 소멸시 delete 합니다.
// 대입 연산은 임시 개체 생성 후 swap 합니다.
class IntPtr {
private:
    int* m_Ptr; 
public: 
    explicit IntPtr(int* ptr) :
        m_Ptr(ptr) {}
    IntPtr(const IntPtr& other) :
        m_Ptr(other.IsValid() ? new int(*other.m_Ptr) : NULL) {} 
    ~IntPtr() {delete m_Ptr;}

    IntPtr& operator =(const IntPtr& other) {
        IntPtr temp(other); 
            Swap(temp); 
        return *this;
    }
    void Swap(IntPtr& other) {
        std::swap(this->m_Ptr, other.m_Ptr);  
    }

    const int* operator ->() const {return m_Ptr;}
    int* operator ->() {return m_Ptr;}

    const int& operator *() const {return *m_Ptr;}
    int& operator *() {return *m_Ptr;}

    bool IsValid() const {return m_Ptr != NULL ? true : false;}    
};

// ----
// TImpl 정의
// ----
class TImpl {
public: // T 에서 멤버 변수를 자유롭게 쓰도록 public 입니다.
    // 스마트 포인터를 사용합니다. 암시적 복사 생성자에서 복제본을 만들고, 소멸자에서 잘 소멸합니다.
    IntPtr m_Val1;
    IntPtr m_Val2;
    TImpl(int* val1, int* val2) : 
        m_Val1(val1),
        m_Val2(val2) {}
private:        
    // 대입 연산자는 사용하지 않으므로 private로 못쓰게 만듭니다.
    TImpl& operator =(const TImpl& other) {return *this;}  
};

// ----
// TImplPtr 정의
// ----
TImplPtr::TImplPtr(TImpl* ptr) :
    m_Ptr(ptr) {}
TImplPtr::TImplPtr(const TImplPtr& other) :
    m_Ptr(other.IsValid() ? new TImpl(*other.m_Ptr) : NULL) {} // TImpl의 복사 생성자를 호출합니다.
TImplPtr::~TImplPtr() {delete m_Ptr;} // TImpl을 소멸시킵니다.

TImplPtr& TImplPtr::operator =(const TImplPtr& other) {
    TImplPtr temp(other); 
    Swap(temp); 
    return *this;
}
void TImplPtr::Swap(TImplPtr& other) {
    std::swap(this->m_Ptr, other.m_Ptr);  
}

const TImpl* TImplPtr::operator ->() const {return m_Ptr;}
TImpl* TImplPtr::operator ->() {return m_Ptr;}

const TImpl& TImplPtr::operator *() const {return *m_Ptr;}
TImpl& TImplPtr::operator *() {return *m_Ptr;}

bool TImplPtr::IsValid() const {return m_Ptr != NULL ? true : false;}    

// ----
// T 정의
// ----
T::T(int* val1, int* val2) :
    m_Impl(new TImpl(val1, val2)) {}

// TImpl의 멤버 변수를 이용합니다.
int T::GetVal1() const {return *(m_Impl->m_Val1);}
int T::GetVal2() const {return *(m_Impl->m_Val2);}    

TEST(TestClassicCpp, PImpl) {
    {
        // (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
        {
            T t1(new int(10), new int(20));
            T t2(t1); // 새로운 int형 개체를 만들고 10, 20을 복제합니다.

            EXPECT_TRUE(t2.GetVal1() == 10 && t2.GetVal2() == 20);
        } 
        // (O) 대입 연산 시에도 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
        {
            T t1(new int(10), new int(20));
            T t2(new int(1), new int (2));
            t2 = t1; // (O) swap 버전 대입 연산자 호출
            EXPECT_TRUE(t2.GetVal1() == 10 && t2.GetVal2() == 20);
        }
    }
}
```

# PImpl 이디엄 오버헤드

PImpl 이디엄은 

1. 구현 코드를 은닉하여 코드간 종속성이나, 컴파일 종속성을 최소화 하고,
2. `swap`을 이용한 대입 연산자 구현 편의성을 제공합니다만,

다음 오버헤드가 있으니, 상황에 맞게 도입하셔야 합니다.

1. 멤버 변수 접근 오버헤드 : `m_Impl`을 통해 간접적으로 접근합니다.
2. 메모리 공간 오버헤드 : `m_Impl` 포인터의 추가 오버헤드가 필요합니다.
3. [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 공간 오버헤드 : `m_Impl`과 멤버 변수 들이 모두 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 공간에만 배치됩니다.

