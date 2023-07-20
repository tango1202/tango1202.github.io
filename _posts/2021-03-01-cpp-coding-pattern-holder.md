---
layout: single
title: "#12. [C++ 코딩 패턴] RAII와 Holder 와 Restorer"
categories: "cpp-coding-pattern"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

RAII(Resource Acquisition Is Initialization)는 **자원 획득은 초기화이다** 라는 뜻입니다.

조금 풀어 쓰면,

1. 자원의 안전한 사용을 위해 개체가 쓰는 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 벗어나면 자원을 해제하라.

라는 뜻입니다.

# Holder의 필요성

`new`로 생성된 포인터의 경우를 생각해 봅시다. 다음처럼 포인터 생성후 사용하다가 `delete`시켜주면 됩니다. 하지만 사람이 하는 일이다 보니 잊어 버리기 쉽습니다.

```cpp
class T {};

T* ptr = new T;

// ptr을 사용하는 코드들

delete ptr; // (△) 비권장. 잊어버릴 수 있습니다.
```

잊어버리고 `delete`를 안했을때 메모리 릭이 발생하며, 처음에는 눈에 띄지 않다가 사용하다가 어느 순간 메모리가 부족하여 시스템이 멈추게 됩니다.

코드 검토, 짝 프로그래밍등 다양한 방법으로 이런 실수를 보강할 수도 있습니다만, 예외 발생하는 코드와 혼합되면서 복잡성이 증가합니다. 다음같은 경우에는 예외 발생시 `ptr`이 delete 되지 않습니다.

```cpp
class T {};

T* ptr = new T;
Func1(); // 예외 발생한다면 ptr이 delete 되지 않습니다.
Func2(); // 예외 발생한다면 ptr이 delete 되지 않습니다.

delete ptr;
```

따라서 다음과 같이 작성해야 합니다만, 코드가 너무 복잡합니다.

```cpp
class T {};

T* ptr = new T;

try {
    Func1(); 
}
catch (...) {
    delete ptr; // (△) 비권장. 잘 delete 했습니다만, 코드가 눈을 어지럽힙니다.
}
try {
    Func2(); 
}
catch (...) {
    delete ptr; // (△) 비권장. 잘 delete 했습니다만, 코드가 눈을 어지럽힙니다.
}

delete ptr;
```

고맙게도 유효 범위를 벗어나거나 예외 발생시에는 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)에 따라 스택의 개체들이 하나씩 소멸됩니다. 이를 이용하여 포인터를 관리하는 스택 개체를 만들면 스택에서 소멸되면서 포인터를 `delete`할 수 있습니다. 이렇게 포인터를 관리하는 개체를 `Holder` 라고 합니다.

# 활용 코딩 패턴

1. 복사 생성과 대입연산을 막기 위해 [`Uncopyable`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-uncopyable/)을 사용하고, 
2. 지역 변수(자동 변수)로만 생성되도록 [`OnlyStackAssignable`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-only-stack-assignable/)을 사용합니다.

# Holder의 구현

1. `new`한 개체를 생성자에서 전달받고, 소멸자에서 `delete`합니다.
2. 보통 `Holder`는 기본 생성자, 복사 생성자, 대입 연산자가 필요 없습니다.
3. 보통 `Holder`는 `new` 로 생성되지 않고 스택에 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)(자동 변수)로만 생성됩니다.

```cpp
// 유효 범위가 지나면, T 타입의 포인터를 소멸시키는 개체
template<typename T>
class Holder : 
    private Uncopyable,
    private OnlyStackAssignable {
    T* m_Ptr;
public:
    explicit Holder(T* ptr) : 
        m_Ptr(ptr) {}
    ~Holder() {
        delete m_Ptr;
    }
    bool IsValid() const {return m_Ptr != NULL ? true : false;}
};
```

다음과 같이 테스트합니다.

```cpp
void Func1() {throw std::bad_alloc();} // 일부러 예외를 발생시킴
void Func2() {}   

TEST(TestCppPattern, Holder) {
    class T {
    public:
        ~T() {
            std::cout<<"delete T"<<std::endl; // 화면에 표시되는 지 확인합니다.
        }        
    };
    // Func1에서 예외를 발생해도 정상 동작하는지 확인
    {
        try {
            T* ptr = new T;
            Holder<T> holder(ptr);
            Func1(); // 예외 발생
            Func2();
        } 
        catch (...) {
            // ptr의 소멸자가 호출되고 catch에 진입함
        }
    } // 유효 범위를 벗어나면, 예외가 발생하면 holder의 소멸자가 호출되어 ptr이 소멸됩니다.

    // 복사 생성, 대입 연산, new 생성이 컴파일 안되는지 확인
    {
        Holder<T> holder1(new T);
        Holder<T> holder2(new T);
        Holder<T> holder3(holder1); // (X) 컴파일 오류. 복사 생성자를 막았습니다.
        holder2 = holder1; // (X) 컴파일 오류. 대입 연산자를 막았습니다.
        Holder<T> *p = new Holder<T>(new T); // (X) 컴파일 오류. Holder를 new로 생성하지 못하도록 막았습니다.  
    }
}
```

# Restorer - Holder의 응용

`Holder`를 응용하여 어떤 상태값을 임시적으로 설정했다가 복원하는 기능도 손쉽게 만들 수 있습니다. 이렇게 속성 복원 기능이 있는 것을 `Restorer`라고 합니다.

1. 생성시 기존 값을 읽어와 저장한 뒤, 설정값을 변경합니다.
2. 소멸시 기존 값으로 설정값을 변경합니다.


다음과 같이 구현합니다.

```cpp
// 유효 범위가 지나면, 설정한 속성을 복원시키는 개체
template< typename GetterT, typename SetterT, typename ValueT>
class Restorer : 
    private Uncopyable,
    private OnlyStackAssignable {
    SetterT m_Setter;
    ValueT m_OldValue;
public:
    Restorer(GetterT getter, SetterT setter, ValueT value) :
        m_Setter(setter), 
        m_OldValue(getter()) { // 이전값 기억
            m_Setter(value); // value로 설정
    }
    ~Restorer() {
        m_Setter(m_OldValue); // 이전값 복원
    }
};   
```

다음과 같이 테스트 합니다.

```cpp
TEST(TestCppPattern, Restorer) {
    // 테스트용 속성 관리자
    class Manager {
        int m_Val;
    public:
        int GetVal() const {return m_Val;}
        void SetVal(int val) {m_Val = val;} 
    };

    // 속성값을 리턴하는 함수자
    class ManagerGetter { 
        const Manager& m_Manager; 
    public:
        explicit ManagerGetter(const Manager& manager) : 
            m_Manager(manager) {} 
        int operator ()() const {return m_Manager.GetVal();}
    };

    // 속성값을 세팅하는 함수자
    class ManagerSetter { 
        Manager& m_Manager; 
    public:
        explicit ManagerSetter(Manager& manager) : 
            m_Manager(manager) {} 
        void operator ()(int val) {m_Manager.SetVal(val);}
    }; 

    Manager manager;
    manager.SetVal(10); 

    {
        ManagerGetter getter(manager);
        ManagerSetter setter(manager);
        Restorer<ManagerGetter, ManagerSetter, int> managerRestorer(
            getter,
            setter,
            20
        );
        EXPECT_TRUE(manager.GetVal() == 20); // 20 으로 설정함
    } // Restore가 소멸되면서 원래값 복원  
    EXPECT_TRUE(manager.GetVal() == 10); // 원래값인 10으로 복원함
}
```