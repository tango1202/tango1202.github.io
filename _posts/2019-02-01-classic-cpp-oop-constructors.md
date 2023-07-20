---
layout: single
title: "#1. [고전 C++ 개체 지향] 생성자"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 기본 생성자가 필요하다면 명시적으로 구현하고, 필요없다면 못쓰게 만들어라.
> * 값 생성자에서는 필요한 인자를 모두 나열하고 초기화하라. 
> * 인자가 1개인 값 생성자는 `explicit`를 사용하여 암시적 형변환을 차단하라.
> * 암시적 복사 생성자가 정상 동작하도록 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 정의시 개체 `Handler`를 사용하고, 필요없다면 못쓰게 만들어라.
> * 생성자에서 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 마라.
> * 상속 전용 기반 클래스는 `protected` 생성자 보다는 [`protected` Non-Vitual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)로 만들어라.
 
# 개요

|항목|내용|
|--|--|
|`T() {}`|기본 생성자|
|`T(int, int) {}`|값 생성자|
|`T(int) {}`|인자가 1개인 값 생성자<br/>(형변환 생성자)|
|`T(const T& other) {}`|복사 생성자|

생성자는 개체가 생성될 때 제일 먼저 호출되는 특수 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)입니다. 개체가 메모리에 할당된 뒤에 호출되고, 초기값을 설정하는 역할을 합니다.

# 기본 생성자

인수없는 생성자를 기본 생성자라고 합니다. `T t;`과 같이 사용하여 개체를 정의(인스턴스화)합니다.(`T t();` 와 같이 하면 `T`를 리턴하는 함수 `f()`선언입니다. [기본 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B8%B0%EB%B3%B8-%EC%B4%88%EA%B8%B0%ED%99%94) 언급)

```cpp
class T {
public:
    T() {}
};
T t; // (O) 개체 정의(인스턴스화)
T t(); // (X) T를 리턴하는 함수 f() 선언
```

# 암시적 기본 생성자

컴파일러는 다른 생성자(값 생성자던, 복사 생성자던)가 정의되지 않으면, 암시적으로 기본 생성자를 정의합니다.

암시적 기본 생성자에서는 [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 수행하기 때문에 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)의 메모리 영역이 제로(`0`)로 초기화 된다고는 합니다.([cppreference.com](https://en.cppreference.com/w/cpp/language/zero_initialization) 참고) 

1. [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 실제 테스트 해보니 GCC 디버그 모드에서는 `0`이 아닙니다. 신뢰할 수 없으니 명시적으로 초기화 하세요.(값 초기화를 이용하는게 좋습니다.) 
2. 참조자 형식이나 상수형 개체는 생성시 초기값에 전달되야 하기 때문에, 암시적 기본 생성자로 초기화 할 수 없습니다.(값 초기화를 이용해야 합니다.)

```cpp
class T1 {
    int m_Val; // (△) 암시적으로 기본 생성자가 자동 제로 초기화된다고 하는데, GCC 디버그 모드에서는 0이 아닙니다. 명시적으로 초기화 하세요.
};

class T2 {
private:
    const int& m_Val; // 멤버 변수에 참조자가 있어, 암시적으로 생성한 기본 생성자에서 초기화 할 수 없음
};

class T3 {
    const T1 m_Val; // 멤버 변수에 상수형 개체가 있어, 암시적으로 생성한 기본 생성자에서 초기화 할 수 없음
};

class T4 {
public:
    T4() {} // 사용자 정의 기본 생성자가 있음
};

class T5 {
public:
    T5(int, int) {} // 값 생성자가 있어 암시적 기본 생성자가 정의되지 않음
};

class T6 {
public:
    T6(const T6& other) {*this = other;} // 복사 생성자가 있어 암시적 기본 생성자가 정의되지 않음
};

T1 t1; // (O) 컴파일러가 암시적으로 정의한 기본 생성자
T2 t2; // (X) 컴파일 오류. 기본 생성자에서 멤버 변수 초기화 안됨
T3 t3; // (X) 컴파일 오류. 기본 생성자에서 멤버 변수 초기화 안됨 
T4 t4; // (O) 사용자가 정의한 기본 생성자 사용
T5 t5; // (X) 컴파일 오류. 기본 생성자 정의 안됨
T6 t6; // (X) 컴파일 오류. 기본 생성자 정의 안됨
```

# 명시적 기본 생성자

기본 생성자가 필요하다면, 암시적인 기본 생성자를 활용하기 보다는 명시적으로 정의해서 사용하는 편이 유지보수 측면에서 좋습니다.([명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 모든 요소를 나열하고 초기화 하는게 **코딩 계약**상 좋습니다.)

```cpp
class T {
    int m_Val;
}
T t; // (△) 비권장. 암시적 기본 생성자 사용
```

보다는, [기본값 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90)를 이용하여,

```cpp
class T {
    int m_Val;
public:
    explicit T(int val = 0) : // 값 생성자 의 기본값을 이용해 디폴트 생성자를 없앴습니다.
        m_Val(val) {}
};
T t1; // (O) 기본값으로 값 생성자 호출
T t2(0); // (O) 기본값과 동일한 값으로 값 생성자 호출
T t3(10); // (O) 임의 값으로 값 생성자 호출
```
가 낫습니다.

# 값 생성자

값 생성자는 값들을 바탕으로 개체를 생성시킵니다.

값 생성자 구현은 

1. [명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 모든 요소를 나열하고 초기화하는게 **코딩 계약**상 좋고,([초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 참고)
2. 불필요한 대입의 오버헤드를 줄이기 위해 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)를 이용하여 모든 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)들을 초기화하는게 좋습니다.

```cpp
class T {
private:
    int m_X;
    int m_Y;
public:
    T(int x, int y) : // 필요한 모든 인자를 나열
        m_X(x), // 초기화 리스트를 이용하여 모든 멤버 변수 초기화
        m_Y(y) {}
};
T t(10, 20); // (O) 개체 정의(인스턴스화)
```

**형변환 생성자**

특별히 값 생성자에 인자가 1개만 있으면, 암시적인 형변환을 하므로 형변환 생성자라고도 합니다. 암시적 형변환이 없도록 꼭 `explicit`를 사용해야 합니다.([명시적 변환 생성 지정자(`explicit`)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit) 참고)

# 암시적 복사 생성자

복사 생성자는 사용자가 특별히 정의하지 않으면 암시적으로 정의됩니다. 기본 동작은 멤버별 복사 생성자 호출입니다.

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
    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};
T t1(10, 20);
T t2 = t1; // (O) 타입이 같다면 복사 생성자 호출
T t3(t1); // (O) 명시적으로 복사 생성자 호출

EXPECT_TRUE(t2.GetX() == 10 && t2.GetY() == 20);
EXPECT_TRUE(t3.GetX() == 10 && t3.GetY() == 20);
```

# 포인터 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)의 소유권 분쟁과 개체 `Handler`

`new` 로 생성한 것은 `delete`로 소멸([힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 참고) 시켜야 합니다. 그렇지 않으면 메모리 릭이 발생합니다. 

다음 코드는 생성자에서 `new`로 생성한 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 개체를 전달받고, 소멸자에서 `delete`합니다. 

그런데, 암시적 복사 생성자를 이용하여 포인터 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 복사하면, 소멸자에서 `delete`시 소유권 분쟁이 생깁니다. 동일한 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 개체를 `t1`, `t2`가 참조하게 되어, `t1` 소멸시에도 `delete`하고, `t2` 소멸시에도 `delete`하게 되어, 결국 2번 `delete`하다가 예외가 발생하게 되죠.

```cpp
class T {
    int* m_Val;
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}

    // 암시적 복사 생성자의 기본 동작은 멤버별 복사 생성자 호출입니다.    
    // T(const T& other) : 
    //     m_Val(other.m_Val) {} // !!동일한 힙 개체를 참조합니다.

    // 힙 개체를 메모리에서 제거 합니다.
    ~T() {delete m_Val;} 
};
// (X) 예외 발생. t1이 delete 한 것을 t2도 delete 합니다.
{
    T t1(new int(10));
    T t2(t1); // 복사 생성의 결과 t1과 t2가 동일한 힙 개체를 참조합니다.
} 
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/16376f0c-e8b0-4eb5-b5a0-f28fc5a9cc43)

따라서, 암시적 복사 생성자를 사용하지 말고, 다음처럼 복사 생성자를 명시적으로 구현하여, [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 개체의 복제본을 만들어야 합니다.

```cpp
class T {
    int* m_Val;
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}

    // (O) NULL 포인터가 아니라면 복제합니다.
    T(const T& other) {
        if (other.m_Val != NULL) { 
            m_Val = new int(*other.m_Val);
        }
        else {
            m_Val = NULL;
        }
    }

    // 힙 개체를 메모리에서 제거 합니다.
    ~T() {delete m_Val;} 
};
// (O) 힙 개체를 복제하여 소유권 분쟁이 없습니다.
{
    T t1(new int(10));
    T t2(t1); // 새로운 int형 개체를 만들고 10을 복제합니다.
} 
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/204850fc-ed5b-4eb0-a7b6-e939d722c918)

더 좋은 방법은, 복사 생성자를 케이스에 따라 이렇게 일일이 명시적으로 개발하지 않고, 암시적 복사 생성자를 그대로 사용할 수 있도록 개체 `Handler`([스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/))를 만들어 사용하는 것입니다.

`Handler`는 다음 단계를 통해 포인터 복제를 대행하도록 구현합니다.

1. `Handler`를 클래스 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)로 정의해 둡니다.
2. 암시적 복사 생성자가 내부적으로 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)들의 복사 생성자를 호출합니다.
3. `Handler`의 복사 생성자가 호출됩니다.
4. `Handler`의 복사 생성자에서 포인터 복제를 합니다.
5. `Handler`의 소멸자에서 포인터를 `delete`합니다.

```cpp
// 복사 생성이나 대입시 m_Ptr을 복제하고, 소멸시 delete 합니다.
class Handler {
private:
    int* m_Ptr; // new로 생성된 개체입니다.
public: 
    Handler(int* ptr) :
        m_Ptr(ptr) {}

    // (O) NULL 포인터가 아니라면 복제합니다.    
    Handler(const Handler& other) {
        if (other.m_Ptr != NULL) { 
            m_Ptr = new int(*other.m_Ptr); 
        }
        else {
            m_Ptr = NULL;
        }
    }

    // 힙 개체를 메모리에서 제거 합니다.
    ~Handler() {delete m_Ptr;}
};

class T {
    // (O) Handler를 이용하여 복사 생성과 대입시 포인터의 복제본을 만들고, 소멸시 Handler에서 delete 합니다.
    // 암시적 복사 생성자에서 정상 동작하므로, 명시적으로 복사 생성자를 구현할 필요가 없습니다.
    Handler m_Val;
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}
};
// (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(t1); // 새로운 int형 개체를 만들고 10을 복제합니다.
} 
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ef2ea0ca-4eea-4f8e-bd9f-e945407cd455)

복사 생성자를 위한 `Handler`의 자세한 구현은 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 참고하세요.

# 생성자에서 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 호출 금지

부모 클래스의 생성자에서 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하면, 아직 자식 클래스들이 완전히 생성되지 않은 상태이기에 부모 클래스의 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 호출됩니다. 의도치 않은 동작이므로, 생성자에서는 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 마세요.

```cpp
class Base {
protected:    
    int m_Val;
public:
    Base() : 
        m_Val(0) {
        // (X) 오동작. 가상 함수를 생성자에서 호출합니다.
        //  Derived::SetVal() 이 호출되길 기대하지만, 
        // Base::SetVal()이 호출됩니다.    
        SetVal(); 
    }
    virtual void SetVal() {
        m_Val = 1; // Base 에서는 1
    }
    int GetVal() const {return m_Val;}
};

class Derived : public Base {
public:
    Derived() :
        Base() {} // Base의 기본 생성자를 호출하면서 가상 함수 SetVal()이 호출됩니다.
    virtual void SetVal() {
        m_Val = 2; // Derived 에서는 2
    }
};

Derived d;

// (X) 오동작. Base 생성자에서 가상함수인 SeVal() 을 호출하면, 
// Derived::SetVal() 이 호출되길 기대하나,
// 아직 Derived가 완전히 생성되지 않은 상태이기에,
// Base::SetVal() 이 호출됨
EXPECT_TRUE(d.GetVal() == 1); 
```

# 기본 생성자, 복사 생성자 사용 제한

만약 기본 생성자나 복사 생성자가 필요없다면, 생성자를 사용할 수 없게 만드는게 좋습니다. 어짜피 사용하지 않을거라 내버려 뒀는데, 누군가가 유지보수 하면서 무심결에 사용하게 된다면, 오동작을 할 수 있거든요. 의도하지 않았다면 동작하지 않게 해야 합니다.

* 기본 생성자 : 다른 생성자(값 생성자던, 복사 생성자던)가 정의되면, 암시적 기본 생성자가 정의되지 않으므로 사용이 제한됩니다. 그렇지 않은 경우 명시적으로 기본 생성자를 구현하고, `private` 또는 `protected`를 이용하여 사용을 제한합니다.
* 복사 생성자 : `private`로 정의하면 다른 곳에서는 사용하지 못하고,([`Uncopyable`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-uncopyable/) 참고) `protected`로 정의하면 상속받은 자식 개체에서만 사용할 수 있습니다.


```cpp
class T {
public:
    T(int, int) {} // (O) 값 생성자를 정의하면 암시적 기본 생성자를 사용할 수 없습니다.
private:
    T(const T& other) {} // (O) private여서 외부에서 복사 생성자 사용할 수 없습니다.
};

T t1; // (X) 컴파일 오류. 
T t2(0, 0); // (O)
T t3(t1); // (X) 컴파일 오류. 복사 생성자를 사용할 수 없게 private로 하여 단단히 코딩 계약을 했습니다.
```

# 상속 전용 기반 클래스 - `protected` 생성자

상속해서만 사용할 수 있는 클래스는 `protected`로 생성자를 만들 수 있습니다. 그러면 개체 정의(인스턴스화)에서는 사용할 수 없고, 상속해서만 사용할 수 있습니다.(생성자는 여러개 만들 수 있어서 모두가 `protected` 인지 신경 쓰기 번거로울 수 있기 때문에, [`protected` Non-Vitual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하는게 더 좋습니다.)

```cpp
class Base {
protected: // 개체 정의(인스턴스화)에서는 사용할 수 없고, 상속해서만 사용할 수 있습니다.
    Base() {} 
public:
    virtual void f() {}
};
class Derived : Base {
    virtual void f() {}
};

Base b; // (X) 컴파일 오류
Derived d;
```

# 생성자 접근 차단 - `private` 생성자

외부에서 생성자 접근을 못하게 생성자를 `private`로 만들고, 생성을 위한 `Create()`계열 함수를 별도로 만들 수 있습니다. 다양한 생성 방식을 개체에서 통제하고 싶을때 사용합니다.
```cpp
class T {
private:
    T(int a, int b, int c) {} // 외부에서는 접근 불가
public:
    static T CreateFromA(int a) {return T(a, 0, 0);}
    static T CreateFromB(int b) {return T(0, b, 0);}
    static T CreateFromC(int c) {return T(0, 0, c);}
};

T t(10, 0, 0); // (X) 컴파일 오류
T* p = new T(10, 0, 0); // (X) 컴파일 오류
delete p;

T t(T::CreateFromA(10)); // (O) T를 복사 생성    
```


