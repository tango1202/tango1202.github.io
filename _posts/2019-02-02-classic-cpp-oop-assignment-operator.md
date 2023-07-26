---
layout: single
title: "#2. [고전 C++ 개체 지향] 대입 연산자와 nothrow Swap"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 1개라면, 암시적 대입 연산자가 정상 동작하도록 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 정의시 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 사용하고, 필요없다면 못쓰게 만들어라.
> * [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 2개 이상이라면, 대입 연산자를 예외에 안정적이도록 `swap`으로 구현하고, 필요없다면 못쓰게 만들어라.

 # 개요

|항목|내용|
|--|--|
|`=`|대입 연산자|

# 암시적 대입 연산자

[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 마찬가지로, 대입 연산자를 정의하지 않으면, 컴파일러는 암시적으로 대입 연산자를 정의합니다. 기본 동작은 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 유사하게 멤버별 대입입니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) : 
        m_X(x), 
        m_Y(y) {} 
    // 암시적 복사 생성자의 기본 동작은 멤버별 복사 생성자 호출입니다.    
    // T(const T& other) :
    //     m_X(other.m_X),
    //     m_Y(other.m_Y) {}

    // 암시적 대입 연산자의 기본 동작은 멤버별 대입입니다.    
    // T& operator =(const T& other) {
    //     m_X = other.m_X;
    //     m_Y = other.m_Y;
    // }

    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};
T t1(10, 20);
T t2(1, 2); 
t2 = t1; // (O) 암시적 대입 연산자 호출

EXPECT_TRUE(t2.GetX() == 10 && t2.GetY() == 20);
```

# swap을 이용한 예외 안정 대입 연산자

예외가 발생하면, [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)에 언급된 것처럼 예외가 발생하기 전의 상태로 되돌아가야 합니다.

암시적 대입 연산자는 각 멤버 변수별로 대입을 하는데요, 그러다 보니 중간에 예외가 발생했을 경우, 이전에 이미 수정한 개체를 복원할 수 없어 예외 처리가 어렵습니다.

```cpp
T& operator =(const T& other) {
    m_X = other.m_X;
    m_Y = other.m_Y; // 여기서 예외가 발생했다면 m_X를 되돌려야 합니다.
}
```

이러한 문제를 해결하기 위해, 

1. 임시 개체를 만든 뒤,
2. `swap`을 이용해 `this`와 임시 개체를 바꿔치기하여,

예외에 안정적인 대입 연산자를 구현할 수 있습니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) : 
        m_X(x), 
        m_Y(y) {} 
    // 암시적 복사 생성자의 기본 동작은 멤버별 복사 생성자 호출입니다.    
    // T(const T& other) :
    //     m_X(other.m_X),
    //     m_Y(other.m_Y) {}
    
    T& operator =(const T& other) {

        // other를 복제한 임시 개체를 만듭니다.
        T temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

        // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
        // this는 이제 other를 복제한 값을 가집니다.
        Swap(temp); 

        return *this;
        
    } // temp는 지역 변수여서 자동으로 소멸됩니다.

    // 멤버 변수들의 값을 바꿔치기 합니다.
    void Swap(T& other) {
        // (△) 비권장. int 형이라 복사 부하가 크지는 않습니다만, 
        // 조금 큰 개체라면 복사 부하가 있고 예외를 발생할 수 있습니다.
        std::swap(this->m_X, other.m_X); 
        std::swap(this->m_Y, other.m_Y);
    }

    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};
T t1(10, 20);
T t2(1, 2); 
t2 = t1; // (O) swap 버전 대입 연산자 호출

EXPECT_TRUE(t2.GetX() == 10 && t2.GetY() == 20);
```

**`swap`의 복사 부하**

임시 개체를 만들고 버리는 것은 미세한 차이는 있겠으나 멤버별 대입 연산과 동등한 부하입니다.
하지만, `swap`의 과정에서 복사 대입이 일어나므로 대용량의 자료 구조라면 심각한 복사 부하가 있을 수 있습니다.(`int`같은 기본 자료형은 복사 부하가 거의 없다고 보셔도 됩니다.)

```cpp
class T {
public:
    T() {}
    T(const T& other) {
        std::cout<<"T::T(const T& other)"<<std::endl;   
    }
    T& operator =(const T& other) {
        std::cout<<"T::operator =()"<<std::endl;
        return *this; 
    } 
};
T t1;
T t2;

t1 = t2; // 대입 1회
std::swap(t1, t2); // 복사 생성 1회 대입 2회
```

상기 코드를 테스트 해보면,

1. [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)별 대입 방식을 사용하면, 대입 연산이 1회 일어나지만, 
2. `swap`을 이용하면, 복사 생성 1회와 대입 연산 2회가 발생하는 걸 알 수 있습니다. 

보통 `swap`은 다음과 같이 임시 개체를 만들고, 각각 값을 대입하기 때문에 대입 연산에서 복사 부하가 있을 수 밖에 없습니다. 또한 대입 과정에서 또다른 예외가 더 발생할 수도 있기 때문에 좋지 않습니다. 

```cpp
swap(T& left, T& right) {
    T temp(right); // 복사 생성 1회, 멤버별 대입 연산과 거의 동등한 부하
    right = left;  // 대입 연산 1회 - swap에 따른 추가 복사 부하
    left = temp; // 대입 연산 1회 - swap에 따른 추가 복사 부하
}
```

# nothrow swap - 포인터 멤버 변수를 이용한 swap 최적화

개체가 `int`형과 같은 기본 자료형 멤버 변수를 1~2개 사용하고 있다면, 대입 연산 부하도 적고, 예외 발생 확률도 낮습니다. 그냥 예외 발생이 없는 `nothrow swap`으로 취급해도 무방합니다. 

그러나, 아주 많은 기본 자료형을 사용하거나 동적으로 할당하는 자료를 가지고 있다면, 복사 부하도 크고, 예외 발생 확률도 높습니다.

이럴 경우 `swap`으로 대입 연산자를 구현하려면 비교적 복사 부하가 적고, 예외 발생 확률도 낮은 [포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)로 작성해야 합니다.(포인터 복사는 8byte끼리의 복사이므로 복사 부하가 적고, 예외 발생 확률도 낮습니다.)

다음 예제에서

1. `Big`은 임의의 큰 데이터를 처리하는 클래스로 가정합니다.
2. `Big`의 복사 생성자와 대입 연산자에 메시지를 출력해서 복사 부하를 확인합니다.
3. `T`는 `Big`을 [포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)로 관리합니다.
4. [포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81) 이 없도록 `T`의 복사 생성자에서 `Big`을 복제하고 소멸자에서 `delete` 합니다.
5. 대입 연산자는 임시 개체를 만들고 `swap`으로 구현합니다.
6. `swap`은 [포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)들끼리 교체하여 복사 부하를 줄입니다.

```cpp
class Big {
    int m_Val; // 실제로는 복사 부하가 큰 데이터라고 생각해 주세요.
public:
    explicit Big(int val) : 
        m_Val(val) {}
    Big(const Big& other) : 
        m_Val(other.m_Val) {
        std::cout<<"Big::Big(const Big& other)"<<std::endl;  
    }
    Big& operator =(const Big& other) {
        m_Val = other.m_Val;
        std::cout<<"Big::operator =(const Big& other)"<<std::endl;  
        return *this;
    }    
    int GetVal() const {return m_Val;}
    void SetVal(int val) {m_Val = val;}
};
class T {
    Big* m_X; // 복사 부하가 큰 데이터는 포인터로 관리합니다.
    Big* m_Y;
public:
    T(Big* x, Big* y) : 
        m_X(x), 
        m_Y(y) {} 
    // NULL 포인터가 아니라면 복제합니다.
    T(const T& other) :
        m_X(other.m_X != NULL ? new Big(*other.m_X) : NULL),
        m_Y(other.m_Y != NULL ? new Big(*other.m_Y) : NULL) {
    }
    // 힙 개체를 메모리에서 제거 합니다.
    ~T() {
        delete m_X;
        delete m_Y;
    }
    
    T& operator =(const T& other) {

        // other를 복제한 임시 개체를 만듭니다.
        T temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

        // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
        // this는 이제 other를 복제한 값을 가집니다.
        Swap(temp); 

        return *this;
        
    } // temp는 지역 변수여서 자동으로 소멸됩니다.

    // 멤버 변수들의 값을 바꿔치기 합니다.
    void Swap(T& other) {
        // (O) 포인터 변수끼리의 복사/대입이라 복사 부하가 크지 않습니다.
        // 예외가 발생할 확률도 낮습니다.
        std::swap(this->m_X, other.m_X); 
        std::swap(this->m_Y, other.m_Y);
    }

    const Big* GetX() const {return m_X;}
    const Big* GetY() const {return m_Y;}
};
T t1(new Big(10), new Big(20));
T t2(new Big(1), new Big(2)); 
t2 = t1; // (O) swap 버전 대입 연산자 호출

EXPECT_TRUE(t2.GetX()->GetVal() == 10 && t2.GetY()->GetVal() == 20);
```
하기는 실행 결과 입니다. 대입 연산시 임시 개체(`temp`)를 생성하느라 복사 생성자(`T(const T& other)`) 에서 `Big` 개체 2개를 복사 생성한 것(멤버별 대입에서와 거의 동등한 부하입니다.) 외에는 다른 복사 부하가 없습니다.

```cpp
Big::Big(const Big& other)
Big::Big(const Big& other)
```

 임시 개체(`temp`)에서 `new`된 `Big`개체들은 `this`에 포인터 복사되고, `this`가 관리하던 `Big`개체들은 임시 개체에 전달된 후 버려집니다. 따라서 `swap`으로 인한 복사는 포인터 복사(8byte 복사) 뿐이므로, 복사 부하는 거의 없다고 보셔도 무방합니다.

즉, [포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)로 정의한 개체의 대입 연산자를 `swap`으로 구현하면,

1. 예외 안정적이고,
2. 복사 부하는 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)별 대입과 거의 동등합니다.

# 대입 연산자까지 지원하는 스마트 포인터

대입 연산자 지원을 위해 [복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)에 `swap`을 이용한 대입 연산자 지원 기능을 추가하면, 

1. 복사 생성시 스마트 포인터에서 복제를 해주고,
2. 소멸시 스마트 포인터에서 `delete` 해주고,
3. 대입 연산시 스마트 포인터에서 `swap`해 주므로,

암시적 복사 생성자, [암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90), 암시적 대입 연산자와 호환되어 별도로 구현할 필요가 없어집니다. 따라서 다음처럼 복사 생성자, 소멸자, 대입 연산자 정의 없이 간소하게 클래스를 작성할 수 있습니다.

```cpp
class T {
    IntPtr m_Val;
public:
    explicit T(int* val) :
        m_Val(val) {}
    int GetVal() const {return *m_Val;}
};
```

다음은 전체 코드입니다.

```cpp
// 복사 생성시 m_Ptr을 복제하고, 소멸시 delete 합니다.
// 대입 연산은 임시 개체 생성 후 swap 합니다.
class IntPtr {
private:
    int* m_Ptr; // new로 생성된 개체입니다.
public: 
    explicit IntPtr(int* ptr) :
        m_Ptr(ptr) {}

    // (O) NULL 포인터가 아니라면 복제합니다.    
    IntPtr(const IntPtr& other) :
        m_Ptr(other.IsValid() ? new int(*other.m_Ptr) : NULL) {}

    // 힙 개체를 메모리에서 제거 합니다.
    ~IntPtr() {delete m_Ptr;}

    IntPtr& operator =(const IntPtr& other) {

        // other의 힙 개체를 복제한 임시 개체를 만듭니다.
        IntPtr temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

        // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
        // this는 이제 other의 힙 개체를 복제한 값을 가집니다.
        Swap(temp); // (O) 포인터 끼리의 값 변경이므로 복사 부하가 없고, 예외가 발생하지 않습니다.

        return *this;
        // temp는 지역 변수여서 자동으로 소멸됩니다.
        // 소멸되면서 this가 이전에 가졌던 힙 개체를 소멸합니다.
    }
    // 멤버 변수들의 값을 바꿔치기 합니다.
    void Swap(IntPtr& other) {
        std::swap(this->m_Ptr, other.m_Ptr); // (O) 포인터 끼리의 값 변경이므로 복사 부하도 없고, 예외가 발생하지 않습니다.   
    }

    // 포인터 연산자 호출시 m_Ptr에 접근할 수 있게 합니다.
    const int* operator ->() const {return m_Ptr;}
    int* operator ->() {return m_Ptr;}

    const int& operator *() const {return *m_Ptr;}
    int& operator *() {return *m_Ptr;}

    // 유효한지 검사합니다.
    bool IsValid() const {return m_Ptr != NULL ? true : false;}    
};

class T {
    // (O) IntPtr로 복사 생성과 대입시 포인터의 복제본을 만들고, 소멸시 IntPtr에서 delete 합니다.
    // (O) 암시적 복사 생성자에서 정상 동작하므로, 명시적으로 복사 생성자를 구현할 필요가 없습니다.
    // (O) 포인터 멤버 변수가 1개 있고, 내부적으로 대입 연산시 swap하므로 대입 연산자를 구현할 필요가 없습니다.
    IntPtr m_Val;
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}
    int GetVal() const {return *m_Val;}
};
// (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(t1); // 새로운 int형 개체를 만들고 10을 복제합니다.

    EXPECT_TRUE(t2.GetVal() == 10);
} 
// (O) 대입 연산 시에도 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(new int(20));
    t2 = t1; // (O) swap 버전 대입 연산자 호출
    EXPECT_TRUE(t2.GetVal() == 10);
}
```

# 멤버 변수가 2개 이상인 경우 스마트 포인터와 대입 연산자와의 호환성

대입 연산자를 지원하는 스마트 포인터를 사용하더라도, 만약 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 2개 이상이라면, 암시적 대입 연산자와 기본적인 호환은 되나, 예외 안정은 지원하지 않습니다.

```cpp
class T {
    IntPtr m_Val1;
    IntPtr m_Val2;
public:
    // 암시적 대입 연산자의 기본 동작은 멤버별 대입입니다.    
    T& operator =(const T& other) {
        m_X = other.m_X;
        m_Y = other.m_Y; // 여기서 예외가 발생했다면 m_X를 되돌려야 합니다.
    }    
};
```

따라서, 스마트 포인터를 사용했더라도, [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 2개 이상이라면, `swap`으로 대입 연산자를 구현해야 합니다.

```cpp
class T {
    // (O) IntPtr로 복사 생성과 대입시 포인터의 복제본을 만들고, 소멸시 IntPtr에서 delete 합니다.
    // (O) 암시적 복사 생성자에서 정상 동작하므로, 명시적으로 복사 생성자를 구현할 필요가 없습니다.
    // (O) 포인터 멤버 변수가 2개 있어, 예외에 안정적이지 않으므로 swap으로 대입 연산자를 구현합니다.
    IntPtr m_Val1;
    IntPtr m_Val2;
public:
    // val1, val2 : new 로 생성된 것을 전달하세요.
    T(int* val1, int* val2) :
        m_Val1(val1),
        m_Val2(val2) {}
    T& operator =(const T& other) {
        T temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.
        Swap(temp); // (O) 포인터 끼리의 값 변경이므로 복사 부하가 없고, 예외가 발생하지 않습니다.
        return *this;
    } 
    void Swap(T& other) {
        m_Val1.Swap(other.m_Val1); // 포인터 끼리의 값 변경이므로 복사 부하도 없고, 예외가 발생하지 않습니다. 
        m_Val2.Swap(other.m_Val2);
    }

    int GetVal1() const {return *m_Val1;}
    int GetVal2() const {return *m_Val2;}
};
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
```

혹은 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 무조건 1개로 유지하는 방법도 있습니다.([PImpl 이디엄](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-pimpl/) 참고)

# 대입 연산자 사용 제한

[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)의 경우와 마찬가지로, 만약 대입 연산자가 필요없다면, 암시적 대입 연산자도 사용할 수 없도록 `private` 로 만드는게 좋습니다.([`Uncopyable`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-uncopyable/) 참고) 어짜피 사용하지 않을거라 내버려 뒀는데, 누군가가 유지보수 하면서 무심결에 대입 연산자를 사용하게 된다면, 오동작을 할 수 있거든요. 의도하지 않았다면 동작하지 않게 해야 합니다.

