---
layout: single
title: "#2. [고전 C++ 개체 지향] 대입 연산자"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 복사 생성자와 마찬가지로, 대입 연산자가 필요하다면, 암시적 대입 연산자가 정상 동작하도록 멤버 개체 핸들러를 활용하고, 필요없다면 못쓰게 만들어라.
> * 대입 연산자는 예외에 안정적이도록 `swap`을 이용하여 구현하라.

 # 개요

|항목|내용|
|--|--|
|`=`|대입 연산자|

# 암시적 대입 연산자

대입 연산자도 복사 생성자와 마찬가지로 사용자가 특별히 정의하지 않으면 암시적으로 정의됩니다. 기본 동작은 복사 생성자와 마찬가지로 멤버별 대입입니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) : 
        m_X(x), 
        m_Y(y) {} 
    // 암시적 복사 생성자의 기본 동작은 멤버별 복사입니다.    
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

**포인터 멤버 변수의 소유권 분쟁과 개체 핸들러**

복사 생성자에서와 마찬가지로, 멤버 변수에 포인터가 있다면 소유권 분쟁을 합니다. 동일한 힙 개체를 2번 `delete` 하게 되니까요.([복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)

이를 해결하기 위해, 암시적 대입 연산자를 사용하지 않고, 다음처럼 대입 연산자를 명시적으로 구현하여, 힙 개체의 복제본을 만들 수 있습니다.

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

    T& operator =(const T& other) {

         // (△) 비권장. 기존에 관리하는 포인터는 삭제합니다. 사실 미리 삭제하는 건 예외 안정에 좋지 않습니다. `swap`을 이용한 대입 연산자 구현 참고
        delete m_Val; 

        if (other.m_Val != NULL) { 
            m_Val = new int(*other.m_Val);
        }
        else {
            m_Val = NULL;
        }

        return *this;
    }

    // 힙 개체를 메모리에서 제거 합니다.
    ~T() {delete m_Val;} 
};
// (O) 힙 개체를 복제하여 소유권 분쟁이 없습니다.
{
    T t1(new int(10));
    T t2(t1); // 새로운 int형 개체를 만들고 10을 복제합니다.
    T t3(new int(20)); 
    t3 = t2; // (O) t3의 힙 개체는 delete 하고, t2의 힙개체를 복제합니다.
} 
```

복사 생성자의 경우와 마찬가지로, 대입 연산자를 케이스에 따라 이렇게 일일이 명시적으로 개발하기 보다는, 암시적 대입 연산자를 그대로 사용할 수 있도록 핸들러를 만드는게 좋습니다.

암시적 대입 연산자가 내부적으로 대입을 할때 핸들러의 대입 연산자가 호출되고, 핸들러가 포인터를 복제합니다.

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

    // (O) NULL 포인터가 아니라면 복제합니다.     
    Handler& operator =(const Handler& other) {
        // (△) 비권장. 기존에 관리하는 포인터는 삭제합니다. 사실 미리 삭제하는 건 예외 안정에 좋지 않습니다. `swap`을 이용한 대입 연산자 구현 참고
        delete m_Ptr;  

        if (other.m_Ptr != NULL) {
            m_Ptr = new int(*other.m_Ptr);
        }
        else {
            m_Ptr = NULL;
        }
        return *this;
    }

    // 힙 개체를 메모리에서 제거 합니다.
    ~Handler() {delete m_Ptr;}
};

class T {
    // (O) Handler를 이용하여 복사 생성과 대입시 포인터의 복제본을 만들고, 소멸시 Handler에서 delete 합니다.
    // 암시적 복사 생성자와 암시적 대입 연산자에서 정상 동작하므로, 명시적으로 복사 생성자와 대입 연산자를 구현할 필요가 없습니다.
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
// (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(new int(20)); 
    t2 = t1;// t2의 힙 개체를 delete 하고, t1의 힙 개체를 복제합니다.
} 
```

**대입 연산자 사용 제한**

복사 생성자의 경우와 마찬가지로 만약 대입 연산자가 필요없다면, 암시적 대입 연산자도 사용할 수 없도록 `private` 로 만드는게 좋습니다. 어짜피 사용하지 않을거라 내버려 뒀는데, 누군가가 유지보수 하면서 무심결에 대입 연산자를 사용하게 된다면, 오동작을 할 수 있거든요. 의도하지 않았다면 동작하지 않게 해야 합니다.

# `swap`을 이용한 예외 안정 대입 연산자 구현

포인터형 멤버 변수를 사용하는 경우, 기존에 참조되던 포인터를 `delete`하고, 새로운 힙 개체를 할당하는데요, 할당시 예외가 발생하면, 이미 삭제한 힙 개체를 복원할 수 없어 불안정해집니다.

```cpp
Handler& operator =(const Handler& other) {
    // (△) 비권장. 기존 힙 개체를 삭제합니다.
    delete m_Ptr;  

    // (△) 비권장. new 하다가 예외가 발생하면, 삭제한 힙 개체를 돌이킬 방법이 없습니다.
    if (other.m_Ptr != NULL) {
        m_Ptr = new int(*other.m_Ptr);
    }
    else {
        m_Ptr = NULL;
    }
    return *this;
}
```

예외가 발생하면, [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)에 언급된 것처럼 예외가 발생하기 전의 상태로 되돌아가야 합니다. 

다음처럼 임시 개체를 만들고, `swap`을 이용해 `this`와 임시개체를 바꿔치기하면, 예외에 안정적인 대입 연산자를 구현할 수 있습니다.

```cpp
Handler& operator =(const Handler& other) {

    // other의 힙 개체를 복제한 임시 개체를 만듭니다.
    Handler temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

    // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
    // this는 이제 other의 힙 개체를 복제한 값을 가집니다.
    Swap(temp); // (O) 포인터 끼리의 값 변경이므로 예외가 발생하지 않습니다.

    return *this;
    // temp는 지역 변수여서 자동으로 소멸됩니다.
    // 소멸되면서 this가 이전에 가졌던 힙 개체를 소멸합니다.
}

// 멤버 변수들의 값을 바꿔치기 합니다.
void Swap(Handler& other) {
     std::swap(this->m_Ptr, other.m_Ptr); // (O) 포인터 끼리의 값 변경이므로 예외가 발생하지 않습니다.   
}
```

임시 개체를 만들고 버립니다만, 대입을 위해 `new`한 것을 `swap`으로 가져 오고, `delete`할 것은 임시 개체에 주기 때문에 불필요한 자원 낭비는 없습니다.

핸들러의 전체 코드는 다음과 같습니다.

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

    // (O) 예외에 안정적이도록 swap을 이용하여 대입합니다.   
    Handler& operator =(const Handler& other) {

        // other의 힙 개체를 복제한 임시 개체를 만듭니다.
        Handler temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다.

        // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
        // this는 이제 other의 힙 개체를 복제한 값을 가집니다.
        Swap(temp); // (O) 포인터 끼리의 값 변경이므로 예외가 발생하지 않습니다.

        return *this;
        // temp는 지역 변수여서 자동으로 소멸됩니다.
        // 소멸되면서 this가 이전에 가졌던 힙 개체를 소멸합니다.
    }

    // 힙 개체를 메모리에서 제거 합니다.
    ~Handler() {delete m_Ptr;}

    // 멤버 변수들의 값을 바꿔치기 합니다.
    void Swap(const Handler& other) {
        std::swap(this->m_Ptr, other.m_Ptr); // (O) 포인터 끼리의 값 변경이므로 예외가 발생하지 않습니다.   
    }
};

class T {
    // (O) Handler를 이용하여 복사 생성과 대입시 포인터의 복제본을 만들고, 소멸시 Handler에서 delete 합니다.
    // 암시적 복사 생성자와 암시적 대입 연산자에서 정상 동작하므로, 명시적으로 복사 생성자와 대입 연산자를 구현할 필요가 없습니다.
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
// (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(new int(20)); 
    t2 = t1;// t2의 힙 개체를 delete 하고, t1의 힙 개체를 복제합니다.
} 
```
