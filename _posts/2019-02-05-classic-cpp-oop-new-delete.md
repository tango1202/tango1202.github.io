---
layout: single
title: "#5. [고전 C++ 가이드] 개체 생성과 소멸"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar:
    nav: "docs"
---

> * 개체는 `new` - `delete` 쌍으로 생성/소멸 하라.
> * 배열은 `new[]` - `delete[]` 쌍으로 생성/소멸하라.

# 개요

|항목|내용|
|--|--|
|`new`|개체의 메모리를 할당하고, 생성자를 호출합니다.|
|`delete`|개체의 소멸자를 호출하고, 메모리를 해제합니다.|
|`new[]`|배열의 메모리를 할당하고, 각 요소의 생성자들을 호출합니다.|
|`delete[]`|배열 요소의 소멸자들을 호출하고, 메모리를 해제합니다.|
|`new(std::nothrow)`|메모리 할당 실패시 NULL 을 리턴합니다. 단, 생성자에서 예외를 발생하면 해당 예외를 전파합니다.|
|`operator new(std::size_t)`|개체의 메모리를 할당합니다.|
|`operator delete(void*)`|개체의 메모리를 해제 합니다.|
|`operator new[](std::size_t)`|배열의 메모리를 할당합니다.|
|`operator delete[](void*)`|배열의 메모리를 해제합니다.|
|`new(void*)`|주어진 메모리 위치에 개체를 배치하여 생성자를 호출합니다.(위치 지정 생성)|
|`set_new_handler()`|`new`에서 예외 발생시 호출되는 함수입니다.|

개체를 동적으로 생성/소멸할때 `new`와 `delete`를 사용합니다.

`new`는 다음의 순서로 개체의 메모리 할당과 생성자 호출을 실행합니다.([생성/소멸 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8-%EC%97%B0%EC%82%B0%EC%9E%90) 언급)

1. 전역 `operator new(std::size_t)`를 이용하여 메모리 공간 할당(오류 발생시 `set_new_handler()`에 설정한 `new_handler` 실행)
2. 구조체이거나 클래스이면 `new(void*)`를 실행하여 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 호출
3. 메모리 주소를 해당 타입으로 형변환하여 리턴

`delete`는 다음의 순서로 개체의 소멸자 호출과 메모리 해제를 실행합니다.([소멸자 호출 순서](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90-%ED%98%B8%EC%B6%9C-%EC%88%9C%EC%84%9C) 참고)

1. 개체의 소멸자 호출
2. 개체의 멤버 변수 소멸자 호출
3. 전역 `operator delete(void*)`를 이용하여 메모리 할당 해제

즉 `new`는 메모리 할당, 생성자 호출, 메모리 주소 를 형변환해서 리턴하지만,
`operator new`는 `void*` 형 메모리를 할당하기만 합니다.

또한 `delete`는 소멸자 호출, 메모리 해제를 수행하지만,
`operator delete`는 메모리 해제만 수행합니다.

# 개체 생성/소멸과 배열 생성/소멸

개체 생성/소멸 시에는 `new` - `delete`를 이용합니다.

```cpp
class T {
public:
    T() {
        std::cout<<"New-Delete Test : T::T()"<<std::endl;
    }
    ~T() {
        std::cout<<"New-Delete Test : T::~T()"<<std::endl;
    }
};

T* t = new T; // (O) 메모리 할당. 생성자 호출
delete t; // (O) 소멸자 호출후. 메모리 해제
```

실행 결과는 다음과 같습니다.

```cpp
New-Delete Test : T::T()
New-Delete Test : T::~T()
```

`delete`를 두번 실행하면 예외가 발생합니다. 생성한 개체는 1회만 `delete`해야 합니다.

```cpp
class T {};
T* p = NULL;
delete p; // (O) p가 NULL이어도 안전합니다.
p = new T; 
delete p; // (O) new로 생성한 것은 반드시 delete 해야 합니다.
delete p; // (X) 예외 발생. 두번 죽일 순 없습니다.  
```


배열 생성/소멸 시에는 `new[]` - `delete[]`를 이용합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete[] arr; // (O) 소멸자 3회 호출. 메모리 해제(sizeof(T) * 3 + 오버헤드)
```

로 하면 실행 결과는 다음과 같이 배열 요소의 갯수 만큼 생성자와 소멸자가 호출됩니다.

```cpp
New-Delete Test : T::T()
New-Delete Test : T::T()
New-Delete Test : T::T()
New-Delete Test : T::~T()
New-Delete Test : T::~T()
New-Delete Test : T::~T()
```

**`delete`와 `delete[]` 의 차이**

`delete`는 개체의 소멸자를 1회 호출하고, 주어진 메모리를 해제하는 역할을 하고, `delete[]`는 배열 요소 갯수만큼 소멸자를 호출하고, 주어진 메모리를 해제하는 역할을 합니다.

그러다 보니 `new[]`로 배열을 생성할때 배열 요소의 갯수가 내부적으로 저장되고, 이를 `delete[]`시에 사용합니다.

1. `int`등 기본 자료형일 경우, 생성자/소멸자를 호출할 필요가 없으므로, 메모리가 `sizeof(int)` * 배열 요소 갯수로 할당됩니다.
2. 클래스인 경우 배열 요소 갯수 만큼 생성자/소멸자가 호출되어야 하므로, 내부적으로 배열 요소 갯수를 저장하는 오버헤드 공간이 추가되어 할당됩니다.(`sizeof(T)` * 배열 요소 갯수 + 오버헤드(4byte 이거나 8byte))

`new[]`나 `delete[]`는 오버헤드 공간의 배열 요소 갯수 정보를 참조하여 각 요소의 생성자와 소멸자를 각각 호출합니다.

따라서, 혹여나 실수로 배열을 `delete`로 소멸시키면, 소멸자가 1회만 호출되고, 프로그램이 다운될 수 있습니다. 따라서, `new[]`로 생성한 배열은 꼭 `delete[]`로 소멸시켜야 합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete arr; // (X) 예외 발생. 소멸자가 1회만 호출되고, 프로그램이 다운될 수 있음
```

# `operator new`와 `operator delete` 재정의

개체의 메모리를 할당하고 해제하는 `operator new`와 `operator delete`는 사용자가 재정의 할 수 있습니다.

주로 다음을 위해 사용합니다.

1. 데이터 오버런(overrun) 및 언더런(underrun) 등 잘못된 힙 사용을 탐지하기 위해 탐지용 바이트를 추가로 할당하는 경우([진단](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-diagnostics/) 참고)
2. 할당 및 해제의 효율을 향상시키기 위해 **동적 메모리 오버헤드**를 줄이고 메모리 관리를 직접 수행하는 경우([메모리 풀](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-memory-pool/) 참고)
3. 동적 할당 메모리의 실제 사용에 관한 통계 정보를 수집하는 경우([개체 수명 로그](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-object-life-log/) 참고)

**동적 메모리 오버헤드**

우리가 동적 메모리를 할당하면, 나중에 `delete` 할때 얼마만큼의 메모리를 해제해야 할지 알아야 하기 때문에, 요청한 크기의 메모리보다 더 많은 오버헤드 공간을 사용합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9f03346e-1457-4b92-90db-52d18e467086)

컴파일러 마다 그 크기가 다를 수 있으며, 보통 4 ~ 8byte 사이의 추가 오버헤드 공간을 사용합니다.

따라서, 크기가 작은 개체를 아주 많이 동적 생성을 해야 한다면, 메모리 공간을 효율적으로 사용하기 위해 `operator new`를 재정하여 사용하는게 좋습니다.


**기본 재정의 방법**

`malloc` - `free`를 이용하거나 전역 `operator new` - 전역 `operator delete`를 이용할 수 있습니다.

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
        return malloc(sz);
        // return ::operator new(sz); // 전역 operator new
    }
    static void operator delete(void* ptr) {
        free(ptr); // ptr == NULL 일 경우 아무 작업 안함
        // ::operator delete(ptr); // 전역 operator delete. ptr == NULL 일 경우 아무 작업 안함
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

`operator new`의 경우 다양한 사용자 정의를 할 수 있습니다.

```cpp
static void* operator new(std::size_t sz, Param1 param1, Param2 param2 ...);
```

다음과 같이 `int val` 등 인자가 추가된 버전을 만들 수 있고,

```cpp
static void* operator new(std::size_t sz, int val) { // int val을 인자로 전달받습니다.
    return malloc(sz);
}
```

해당 인자만 전달하여 `new(1)`과 같이 호출할 수 있습니다.

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

다음의 경우

1. `operator delete(void* ptr)` 가 사용되며, 
2. `operator delete(void* ptr, std::size_t sz)`를 사용하려면, `operator delete(void* ptr, std::size_t sz)` 만 정의

해야 합니다.

```cpp
class T {
public:
    static void* operator new(std::size_t sz) {
        return malloc(sz);
    }    
    static void operator delete(void* ptr) {
        std::cout<<"T::delete(void* ptr)"<<std::endl;
        free(ptr); 
    }
    // sz : 해제할 바이트 수                     
    static void operator delete(void* ptr, std::size_t sz) {
        std::cout<<"delete(void* ptr, std::size_t sz)"<<std::endl;
        free(ptr); 
    }            
}; 

T* t = new T; 
delete t; // T::delete(void* ptr) 호출
```

**부모 클래스의 `operator new`, `operator delete`**

자식 클래스에서 별다르게 `operator new`와 `operator delete`를 정의하지 않으면, 부모 클래스에 정의된 것이 사용됩니다.

```cpp
// sizeof(Base) == sizeof(int) * 2 + 가상 함수 테이블 크기
class Base { 
    int m_X;
    int m_Y;
public:
    virtual ~Base() {}

    // sz : sizeof(Base) 또는 sizeof(Derived) 크기
    static void* operator new(std::size_t sz) {
        return malloc(sz);
    }
    // sz : sizeof(Base) 또는 sizeof(Derived) 크기
    static void operator delete(void* ptr, std::size_t sz) {
        free(ptr); 
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

이에 일일이 모든 자식 클래스에서 `operator new`, `operator delete`를 구현하지 말고, 부모 클래스(`Base`)에서 자식 클래스(`Derived`)를 고려해서 생성하는게 좋습니다.

고려라고 해봐야 전달된 `sz`만큼 메모리를 할당하고, `sz`만큼 해제하는 것이니 특별히 할 건 없습니다.

**`public` Virtual 소멸자인 부모 클래스의 `operator delete`**

[`public` Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 에서 다형 소멸을 지원하려면, 꼭 `virtual` 소멸자를 지원해야 한다고 언급한바 있습니다.

`operator delete`에서도 마찬가지 입니다. 자식 클래스에서 할당된 메모리를 모두 해제하려면 꼭 `public` Virtual 소멸자를 사용하셔야 합니다.

다음 코드는 상기 코드에서 소멸자의 `virtual`만 뺀 코드입니다. 실행시켜 확인해 보면, 

1. `operator new` 시에는 `Derived`의 크기인 16byte가 전달되나, 
2. `operator delete`시에는 `Base`의 크기인 8byte가 전달되어, 
   
메모리의 일부만 해제되어 메모리 릭이 발생합니다. 그러니, 다형 소멸을 하려면 꼭 [`public` Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90)를 사용하세요.

```cpp
// sizeof(Base) == sizeof(int) * 2 = 8byte
class Base { 
    int m_X;
    int m_Y;
public:
    ~Base() {}

    // (X) 오동작. sizeof(Derived) 크기 : 16byte
    static void* operator new(std::size_t sz) {
        return malloc(sz);
    }
    // (X) 오동작. 소멸시에는 Base의 크기 : 8byte. 메모리 릭 발생
    static void operator delete(void* ptr, std::size_t sz) {
        free(ptr); 
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

**`operator new`는 최소 1byte 할당**

0byte인 자료형은 없으므로 최소 1byte는 할당해야 합니다. 사실 클래스 선언시 빈 클래스라면 알아서 크기를 1로 설정은 해줍니다만, 혹시 모르니 다음과 같이 `++sz;` 추가해 두는 것이 좋습니다. 

```cpp
class T {
public:
    static void* operator new(std::size_t sz) { // 1byte 전달됨
        if (sz == 0) { // 혹시 모르니 검사
            ++sz;
        }
        return malloc(sz);
    }
    static void operator delete(void* ptr) {
        free(ptr);  
    }            
};

EXPECT_TRUE(sizeof(T) == 1); // 빈 클래스도 1byte 할당됨
T* t = new T;
delete t;
```

# `operator new[]`와 `operator delete[]` 재정의

배열은 `operator new[]`와 `operator delete[]`를 사용합니다. 일반 자료형의 경우에는 `sz`에 `sizeof(T) * 배열 요수 갯수`가 전달되지만, 클래스와 같이 생성자와 소멸자가 있는 개체는 배열 요소 개수만큼 생성자와 소멸자를 호출해야 하므로, 내부적으로 배열 요소 갯수를 관리하는 오버헤드가 추가 되어 전달됩니다.

```cpp
class T {
public:
    // sz : sizeof(T) * 배열 요수 갯수 + 오버헤드
    void* operator new[](std::size_t sz) { 
        return malloc(sz);
    }
    // sz : sizeof(T) * 배열 요수 갯수 + 오버헤드
    void operator delete[](void* ptr, std::size_t sz) { 
        free(ptr);
    } 
};
T* arr = new T[10]; // operator new[](std::size_t sz), sizeof(T) * 10 + 오버헤드
delete[] arr; // operator delete[](void* ptr, std::size_t sz) 호출
```

# `new(ptr)` : 위치 지정 생성(Placement New)

`void* operator new(size_t sz, void* ptr)`와 같이 `void*` 를 인자로 전달받는 `operator new`를 특별히 위치 지정 생성이라 합니다. 위치 지정 생성은 주어진 `operator new` 등으로 할당한 메모리 위치에 생성자를 실행합니다. 즉, 해당 메모리 위치에 개체를 생성한다고 보셔도 됩니다. 

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

다음과 같이 할당된 `buffer`에 `new(buffer) T;`로 생성자를 호출하여 개체를 생성시킬 수 있습니다. 이때 명시적으로 `t->~T()`와 같이 소멸자를 호출해야 합니다.

```cpp
void* buffer = malloc(sizeof(T)); // T 클래스 크기만큼 메모리를 할당합니다.

T* t = new(buffer) T; // T 의 기본 생성자를 호출합니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);

t->~T(); // 위치 지정 생성을 사용하면 명시적으로 소멸자를 호출해야 합니다.
free(buffer); // malloc 으로 할당한 메모리를 해제합니다.
```

기존 `buffer`의 메모리 영역을 재활용하여 다른 개체를 배치시킬 수도 있고, `buffer`를 직접 수정하여 멤버 변수의 값을 변경할 수도 있으며(비권장), 소멸자 호출 후에도 값 접근을 할 수 있습니다.(비권장) 

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

`operator new`를 재구현 했다면 전역 `void* operator new(size_t sz, void* ptr)` 를 가려서 재정의해야 합니다.

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
        if (sz == 0) {
            ++sz;
        }
        return malloc(sz);
    }
    static void operator delete(void* ptr) {
        free(ptr);  
    }

    // 위치 지정 생성 재정의
    static void* operator new(size_t sz, void* ptr) { 
        return ptr;
    }
}; 
```

다음처럼 `operator new`, `operator delete`, 위치 지정 생성을 테스트할 수 있습니다. 

```cpp
void* buffer = T::operator new(sizeof(T)); // static 함수 호출하듯이 사용합니다.
T* t = new(buffer) T; // T 의 기본 생성자를 호출합니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);

// (△) 비권장. buffer를 직접 수정해도 동작하는지 확인합니다.
// 테스트를 위한 것이니 실제 코드에서 이런거 하시면 캡슐화가 무너집니다.
int* p = static_cast<int*>(buffer);
*p = 100;
*(p + 1) = 200;
EXPECT_TRUE(t->GetX() == 100 && t->GetY() == 200);

t->~T(); // 위치 지정 생성을 사용하면 명시적으로 소멸자를 호출해야 합니다.
T::operator delete(buffer); // static 함수 호출하듯이 사용합니다.
```

기본 `new` - `delete` 사용시에는 위치 지정 생성이 사용되지 않습니다.

```cpp
T* t = new T; // operator new(size_t sz, void* ptr) 가 호출되지는 않습니다.
EXPECT_TRUE(t->GetX() == 10 && t->GetY() == 20);
delete t;
```

# `new(std::nothrow)` 와 무의미한 널검사

예외불가(nothrow) new 는 영향력이 제한되어 있다. 메모리 할당 자체에만 적용되기 때문이다. 이후에 호출되는 생성자에서는 얼마든지 예외를 던질 수 있다.

new 는 bad_alloc 예외를 던짐
T* p = new T;
if (!p) {} // 예외를 던진다는데 왜 널검사를 하니? 널검사 하고 싶으면 다음처럼... T* p = new(std::nothrow) T; // 실패하면 0 리턴


C++ 표준 라이브러리의 함수는 new C++98 이후 C++ 표준에 지정된 동작을 지원합니다. 할당 요청에 operator new 대한 메모리가 부족한 경우 는 예외를 std::bad_alloc throw합니다.

이전 C++ 코드는 실패한 할당에 대해 null 포인터를 반환했습니다. throw하지 않는 버전 new이 필요한 코드가 있는 경우 프로그램을 와 nothrownew.obj연결합니다. 이 파일은 nothrownew.obj 할당이 실패할 경우 를 반환 nullptr 하는 버전으로 전역 operator new 을 대체합니다. operator new 더 이상 을 throw하지 않습니다 std::bad_alloc. 및 기타 링커 옵션 파일에 대한 nothrownew.obj 자세한 내용은 링크 옵션을 참조하세요.

동일한 애플리케이션에서 null 포인터를 확인하는 코드와 전역 operator new 예외를 확인하는 코드를 혼합할 수 없습니다. 그러나 다르게 동작하는 클래스 로컬 operator new 을 만들 수 있습니다. 이 가능성은 컴파일러가 기본적으로 방어적으로 작동하고 호출에 new null 포인터 반환에 대한 검사를 포함해야 한다는 것을 의미합니다. 이러한 컴파일러 검사를 최적화하는 방법에 대한 자세한 내용은 를 참조하세요 /Zc:throwingnew.

#  할당실패 테스트

```cpp
#include <iostream>
#include <new>
using namespace std;
#define BIG_NUMBER 10000000000LL
int main() {
   int *pI = new(nothrow) int[BIG_NUMBER];
   if ( pI == nullptr ) {
      cout << "Insufficient memory" << endl;
      return -1;
   }
}
```

# set_new_handler

**`new_handler`와의 호환성 유지**




1. 관례적으로, operator new 함수는 메모리 할당을 반복해서 시도하는 무한 루프를 가져야 하고, 메모리 할당 요구를 만족시킬 수 없을 때 new 처리자를 호출해야 하며, 0바이트에 대한 대책도 있어야 한다. 클래스 전용 버전은 자신이 할당하기로 예정된 크기보다 더 큰(틀린) 메모리 블록에 대한 요구도 처리해야 한다.
2. operator delete 함수는 널 포인터가 들어왔을 때 아무 일도 하지 않아야 한다. 클래스 전용 버전의 경우에는 예정 크기보다 더 큰 블록을 처리해야 한다.

```cpp
// 커스텀 operator new 함수는 다른 매개변수를 추가로 가질 수 있다
static void* operator new(std::size_t sz) throw(std::bad_alloc)
{
	using namespace std;

	// 0 바이트 요청이 들어오면 1 바이트 요구로 간주하고 처리
	if (sz == 0)
	{
		sz = 1;
	}

    // 메모리 할당 실패 시 루프를 통해 다시 시도
	while (true)
	{
		size 바이트를 할당해 본다;
		if (할당에 성공)
		{
			return (할당된 메모리에 대한 포인터);
		}

		// 할당에 실패했을 경우, 현재의 new 처리자 함수가
		// 어느 것으로 설정되어 있는지 찾아낸다
		new_handler globalHandler = set_new_handler(0);
		set_new_handler(globalHandler);

		if (globalHandler) (*globalHandler)();
		else throw std::bad_alloc();
	}
}
```
항목 49에서, new 처리자 함수는 가용 메모리를 늘려 주던가, 다른 new 처리자를 설치하든가, new 처리자의 설치를 제거하든가, bad_alloc 혹은 bad_alloc 에서 파생된 타입의 예외를 던지든가, 아예 함수 복귀를 포기하고 중단을 시켜야 한다고 이야기했다. 이렇듯 new 처리자 함수가 4가지 중 하나를 택해야 하는 이유는, 위의 operator new 함수의 구현처럼 내부 루프를 끝낼 수 있도록 구현되어야 하기 때문이다.




set_new_handler 함수를 쓰면 메모리 할당 요청이 만족되지 못했을 때 호출되는 함수를 지정할 수 있다.



```cpp
namespace std
{
  typedef void (*new_handler)();

  // throw() 는 예외지정으로, 예외를 던지지 않는다
  new_handler set_new_handler(new_handler p) throw();
}

void outOfMem()
{
  std::cerr << "Unable to satisfy request for memory\n";
  std::abort();
}

int main()
{
  std::set_new_handler(outOfMem);

  // Unable to satisfy request for memory 출력!
  int *pBigDataArray = new int[100000000000000l];
}
```

operator new 와의 고려사항

1. 사용할 수 있는 메모리를 더 많이 확보한다
2. 다른 new 처리자를 설치한다 : 현재의 new 처리자 안에서 set_new_handler 를 설치하고 호출한다
3. new 처리자의 설치를 제거한다 : set_new_handler 에 널 포인터를 넘긴다
4. 예외를 던진다
5. 복귀하지 않는다 : 대개 abort 혹은 exit 을 호출한다

  1) 사용할 수 있는 메모리를 더 많이 확보한다.

    - 프로그램이 시작할때, 메모리 블록을 크게 할당해 놓았다가 new처리자 첫 호출(메모리할당 첫실패시) 될 때 그 메모리를 쓸수있도록한다.

  2) 다른 new 처리자를 설치한다.

    - 호출된 현재의 new처리자가 감당하지 못할때, 다른 new처리자를 설치한다.(현재의 new 처리자에서 set_new_handler(다른 new_handler)를 호출)

  3) new 처리자의 설치를 제거한다.

    - set_new_handler에 널 포인터를 넘긴다. new처리자 미설치시, operator new는 처음얘기한것처럼 사용자에러처리함수 없을시, 예외를 던진다.

  4) 예외를 던진다.

    - bad_alloc 혹은 bad _alloc에서 파생된 타입의 예외를 던진다. operator_new는 이 쪽종류의 에러 대응 능력이 없기에, 이 예외는 메모리 할당을 호출한쪽으로 예외 전이(propagate)된다.

  5) 복귀하지 않는다.

    - abort, exit 호출하여 종료시킨다.

```cpp
class NewHandlerHolder
{
public:
  explicit NewHandlerHolder(std::new_handler nh)
  : handler(nh) {}

  ~NewHandlerHolder()
  { std::set_new_handler(handler); }

private:
  // 핸들러를 기억해 둔다
  std::new_handler handler;

  // 복사를 막기 위한 부분 (항목 14 참고)
  NewHandlerHolder(const NewHandlerHolder&);
  NewHandlerHolder& operator=(const NewHandlerHolder&);
};
class Widget
{
public:
  static std::new_handler set_new_handler(std::new_handler p) throw(){
      std::new_handler oldHandler = currentHandler;
      currentHandler = p;
      return oldHandler;
  }
  static void* operator new(std::size_t sz) throw(std::bad_alloc) {
    // Widget 의 new 처리자 설치
    // NewHandlerHolder 는 currentHandler 로 핸들러가
    // 바뀌기 전의 oldHandler 를 쥐고 있음!
    NewHandlerHolder h(std::set_new_handler(currentHandler));

    // 할당 실패 시 예외를 던짐
    return ::operator new(sz);

    // 이전의 전역 new 처리자가 자동으로 복원됨
  }
private:
  static std::new_handler currentHandler;
};

// 널로 초기화
std::new_handler Widget::currentHandler = 0;

int main()
{
  // Widget 객체에 대한 메모리 할당이 실패했을 때 호출할 함수
  void outOfMem();

  // Widget 의 new 처리자 함수로 outOfMem 설치
  Widget::set_new_handler(outOfMem);

  // 메모리 할당 실패 시 outOfMem 호출
  Widget *pw1 = new Widget;

  // 메모리 할당 실패 시 전역 new 처리자 함수 호출(있으면)
  std::string *ps = new std::string;

  // Widget 클래스의 new 처리자 함수를 null 로 설정
  Widget::set_new_handler(0);

  // 메모리 할당이 실패하면 이제 바로 예외를 던짐
  Widget *pw2 = new Widget;
}
```
