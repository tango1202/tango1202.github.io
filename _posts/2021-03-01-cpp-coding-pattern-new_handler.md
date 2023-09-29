---
layout: single
title: "#12. [C++ 코딩 패턴] NewHandler - `new`의 예외 처리"
categories: "cpp-coding-pattern"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

`new`시 오류 발생시 처리하는 `new_handler`를 만들 수 있습니다.

`new_handler` 는 다음 처리를 해주어야 합니다.([`set_new_handler()`](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#set_new_handler) 참고)

1. 미리 예약된 공간을 해제하여 메모리를 추가 확보해 주거나
2. 다른 `new_handler`를 설치하여 처리를 위임하거나
3. `new_handler`를 제거하거나(제거되면 `bad_alloc()` 발생)
4. `bad_alloc()` 또는 이로부터 파생된 예외를 발생시켜 처리를 포기하거나
5. `abort()`을 하여 프로그램을 종료합니다.

# NewHandler의 필요성

`operator new`를 활용할때 코드를 단순하게 유지할 수 있습니다.

# 활용 코딩 패턴

1. 복사 생성과 대입연산을 막기 위해 [`Uncopyable`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-uncopyable/)을 사용하고, 
2. 지역 변수(자동 변수)로만 생성되도록 [`OnlyStackAssignable`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-only-stack-assignable/)을 사용합니다.
3. 테스트를 위해 생성된 포인터를 삭제하기 위해 [`Holder`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-only-stack-assignable/)를 사용합니다.
4. `set_new_handler()`로 설정한 값을 복원하기 위해 [`Restorer`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/#restorer---holder%EC%9D%98-%EC%9D%91%EC%9A%A9)를 응용합니다.

5. `NewHandler`를 전역적으로 처리하기 위해 [`Singleton`](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-singleton/)으로 구현합니다.

# NewHandler의 사양

1. `Restorer`를 제공하여, `set_new_handler()` 설정 및 복원을 합니다.
2. `Reserved` 를 통해 미리 예약된 공간을 만들고, `Reset()`할 수 있습니다. `ResetReserved(0)`하여 예약된 공간을 초기화할 수 있습니다.
3. `GetHandler()`에서 `m_Mode`에 따라 다른 핸들러를 리턴합니다.
4. `UsingReservedHandler()` 는 예약된 메모리 공간을 해제하고 `new`를 재시도 할 수 있게 해줍니다.
5. `AnotherHandler()` 에서 다른 처리 방법을 사용자 정의하여 구현 할 수 있습니다.
6. `RemoveHandler()` 는 기존 핸들러를 제거합니다.
7. `BadAllocHandler()`은 별다른 처리없이 `bad_alloc()`을 발생시킵니다.(`RemoveHandler()` 와 동일합니다.)
8. `AbortHandler()`는 프로그램을 종료합니다.

다음과 같이 구현합니다.

```cpp
// new시 오류가 발생하면 재시도시 오류가 발생하지 않도록 처리하는 개체
class NewHandler {

public:
    // 생성시 NewHandler를 설정하고, 소멸시 이전 new_handler로 복원합니다.
    class Restorer : 
        private Uncopyable,
        private OnlyStackAssignable {
        std::new_handler m_OldHandler;
    public:
        explicit Restorer() : 
            m_OldHandler(std::set_new_handler(GetInstance().GetHandler())) {
        }
        ~Restorer() {
            std::set_new_handler(m_OldHandler);
        }
    };
private:
    // 미리 예약된 메모리 공간입니다. 
    // new 실패시 UsingReservedHandler 에서 해제하여 메모리 공간을 확보해 줍니다.
    class Reserved : private Uncopyable {
        char* m_Ptr;
    public:
        explicit Reserved(size_t size = 0) : 
            m_Ptr(NULL) { 
            Reset(size);          
        }
        ~Reserved() {
            Release(); // 메모리를 해제합니다.
        } 
        void Reset(size_t size) {
            Release();
            if (0 < size) {
                m_Ptr = new char[size];
            }
        }
    public:    
        // 예약된 공간을 해제하고, NULL로 초기화 합니다. 
        void Release() {
            delete[] m_Ptr; 
            if (m_Ptr != NULL) {
            }
            m_Ptr = NULL; 
        }
        // 예약된 공간이 있는지 없는지 리턴합니다.
        bool IsValid() const {return m_Ptr != NULL ? true : false;} 
    }; 
public:    
    // 모드설정에 따라 new_handler가 변경됩니다.
    enum Mode {UsingReserved, Another, Remove, BadAlloc, Abort}; 
private:      
    Mode m_Mode; 
    Reserved m_Reserved;
public:

private:
    explicit NewHandler(Mode mode, size_t reservedSize) : 
        m_Mode(mode),
        m_Reserved(reservedSize) {}
public:
    // 전역적으로만 생성됩니다. BadAlloc을 사용하며 예약 공간은 0입니다.
    static const NewHandler& GetInstance() {
        static NewHandler s_NewHandler(BadAlloc, 0);
        return s_NewHandler;
    }
    static NewHandler& GetInstanceRef() {
        return const_cast<NewHandler&>(GetInstance());
    }
public:
    // mode : UsingReserved, Another, Remove, BadAlloc, Abort
    Mode GetMode() const {return m_Mode;}
    void SetMode(Mode mode) {m_Mode = mode;} 
    void ResetReserved(size_t size) {
        m_Reserved.Reset(size); // 메모리 예약 공간을 재할당합니다. 
    }
private:    
    // m_Mode 에 맞게 Handler 리턴합니다.
    std::new_handler GetHandler() const {
        std::new_handler result = &BadAllocHandler;
        switch(GetMode()) {
        case UsingReserved: result = &UsingReservedHandler; break;
        case Another: result = &AnotherHandler; break;
        case Remove: result = &RemoveHandler; break;
        case BadAlloc: result = &BadAllocHandler; break;
        case Abort: result = &AbortHandler; break;
        }
        return result;
    }
private:            
    // new 할당에 실패하면, 예약된 공간을 해제하여 메모리를 늘려줍니다.
    static void UsingReservedHandler() {
        // 예약된 공간이 있다면, 해제하고 재시도 하고,
        if (GetInstance().m_Reserved.IsValid()) {
            GetInstanceRef().m_Reserved.Release();
        }
        // 예약된 공간이 없다면 다른 new_handler를 설치합니다.
        else {
            std::set_new_handler(&AnotherHandler);
        }
    }
    // 다른 new_handler를 설치합니다.
    static void AnotherHandler() {
        // [Todo] 다른 처리 방법이 있다면 시도합니다.
        std::set_new_handler(&BadAllocHandler);
    }
    // new 처리자의 설치 제거합니다. 아마도 std::bad_alloc이 발생됩니다.
    static void RemoveHandler() {
        std::set_new_handler(NULL);
    }
    // bad_alloc으로 포기합니다.
    static void BadAllocHandler() {
        throw std::bad_alloc();
    }
    // std::abort()로 프로그램 종료합니다.
    static void AbortHandler() {
        std::abort();
    }
};
```

다음과 같이 테스트합니다.

```cpp
// 엄청 큰 데이터를 관리하는 클래스 입니다.
class T {
    int m_Big[1024 * 1024 * 1000]; // 1000 M * sizeof(int)
public:
    static void* operator new(std::size_t sz) { 

        // 예외가 발생하거나 유효 범위가 벗어나면 NewHandler::Restorer 소멸자에서 이전 handler로 복원해 줍니다.
        NewHandler::Restorer restorer;

        // 내부적으로는 메모리를 할당하고, 실패하면 handler를 실행하는 과정을 무한히 반복합니다.
        // handler가 std::bad_alloc이나 std::abort()를 할때 까지요.
        std::cout << "----## ::operator new : Start" << std::endl;  
        void* ptr = ::operator new(sz); 
        std::cout << "----## ::operator new : End" << std::endl; 
        return ptr;
    } 
};
class Tester {
public:    
    // new가 실패할때까지 반복해서 재귀 할당 합니다.
    // Holder에 생성된 개체를 담습니다.
    static void Recursive() {
        std::cout << "## Recursive" << std::endl;
        Holder<T> holder(new T);
        Recursive();
    }
};

// 최초 사용시 메모리 영역을 예약합니다.
NewHandler::GetInstanceRef().ResetReserved(sizeof(T) * 2);
{
    try {
        // new 실패시 UsingReserved -> Another -> BadAlloc 순으로 Handler를 변경합니다.
        NewHandler::GetInstanceRef().SetMode(NewHandler::UsingReserved); 
        Tester::Recursive();
    }
    catch (std::bad_alloc& e) {
        std::cout << "## [UsingReserved] catch (std::bad_alloc& e)" << std::endl;    
    }
}
{
    // Handler 를 제거합니다. std::bad_alloc을 발생시킵니다.
    try {
        NewHandler::GetInstanceRef().SetMode(NewHandler::Remove); 
        Tester::Recursive();
    }
    catch (std::bad_alloc& e) {
        std::cout << "## [Remove] catch (std::bad_alloc& e)" << std::endl;    
    }
}
{
    // 프로그램을 종료합니다.
    NewHandler::GetInstanceRef().SetMode(NewHandler::Abort);  
    Tester::Recursive();
}
```

