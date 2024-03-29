---
layout: single
title: "#41. [모던 C++ STL] coroutine(C++20)"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [coroutine_handle](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_handle) 이 추가되어 [코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)을 재개하는 [코루틴 핸들](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%A8%EC%88%98-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EA%B0%9C%EC%B2%B4-%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%B8%EB%93%A4-promise-%EA%B0%9C%EC%B2%B4)을 제공합니다.
> * (C++20~) [suspend_always](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EB%8C%80%EA%B8%B0-%EA%B0%80%EB%8A%A5-%EA%B0%9C%EC%B2%B4suspend_always-%EC%99%80-suspend_never), [suspend_never](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EB%8C%80%EA%B8%B0-%EA%B0%80%EB%8A%A5-%EA%B0%9C%EC%B2%B4suspend_always-%EC%99%80-suspend_never)가 추가되어 [코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)에서 사용하는 [대기 가능 개체](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EB%8C%80%EA%B8%B0-%EA%B0%80%EB%8A%A5-%EA%B0%9C%EC%B2%B4suspend_always-%EC%99%80-suspend_never)를 제공합니다.
> * (C++20~) [coroutine_traits](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_traits)가 추가되어 [코루틴 개체](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%A8%EC%88%98-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EA%B0%9C%EC%B2%B4-%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%B8%EB%93%A4-promise-%EA%B0%9C%EC%B2%B4)의 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)인 `promise_type`을 외부에서 연결해 줄 수 있습니다.

# 개요

C++20 부터 [코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)이 추가되었고, STL 에서는 [코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/) 구현을 위해 다음의 유틸리티를 제공합니다.

|항목|내용|
|--|--|
|[coroutine_traits](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_traits) (C++20~)|[코루틴 개체](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%A8%EC%88%98-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EA%B0%9C%EC%B2%B4-%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%B8%EB%93%A4-promise-%EA%B0%9C%EC%B2%B4)의 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)인 `promise_type`을 외부에서 정의합니다.|
|[coroutine_handle](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_handle) (C++20~)|[코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)을 재개하는 개체입니다.|
|`noop_coroutine_promise` (C++20~)|(작성중)|
|`noop_coroutine_handle` (C++20~)|`std::coroutine_handle<std::noop_coroutine_promise>`입니다.|
|`noop_coroutine()` (C++20~)|(작성중)|
|[suspend_always](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EB%8C%80%EA%B8%B0-%EA%B0%80%EB%8A%A5-%EA%B0%9C%EC%B2%B4suspend_always-%EC%99%80-suspend_never) (C++20~)|`await_ready()`에서 항상 `false`를 리턴하며, 대기합니다.|
|[suspend_never](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EB%8C%80%EA%B8%B0-%EA%B0%80%EB%8A%A5-%EA%B0%9C%EC%B2%B4suspend_always-%EC%99%80-suspend_never) (C++20~)|`await_ready()`에서 항상 `true`를 리턴하며, 대기하지 않습니다.|

# coroutine_traits

[co_await](https://tango1202.github.io/cpp/modern-cpp-coroutine/#co_await)에서 [코루틴 개체](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%A8%EC%88%98-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EA%B0%9C%EC%B2%B4-%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%B8%EB%93%A4-promise-%EA%B0%9C%EC%B2%B4)를 만드려면 명칭이 `promise_type`인 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)이 있어야 한다고 언급했는데요,

```cpp
// 코루틴 개체
class MyCoroutine {  
public:
    struct MyPromise;
    using promise_type = MyPromise; // 명칭이 promise_type인 종속 타입이 있어야 합니다.
private:
    std::coroutine_handle<promise_type> m_Handler; // 코루틴 핸들
public:
    MyCoroutine(std::coroutine_handle<promise_type> handler) : m_Handler(handler) {}
    ~MyCoroutine() {
        if (m_Handler) {
            m_Handler.destroy(); 
        }
    }
    MyCoroutine(const MyCoroutine&) = delete;
    MyCoroutine(MyCoroutine&&) = delete;
    MyCoroutine& operator =(const MyCoroutine&) = delete;
    MyCoroutine& operator =(MyCoroutine&&) = delete;

    void Resume() const { .
        if (!m_Handler.done()) { 
            m_Handler.resume(); 
        }
    } 

    // Promise 개체
    struct MyPromise { 
        MyPromise() = default;
        ~MyPromise() = default;
        MyPromise(const MyPromise&) = delete;
        MyPromise(MyPromise&&) = delete;
        MyPromise& operator =(const MyPromise&) = delete;
        MyPromise& operator =(MyPromise&&) = delete;

        MyCoroutine get_return_object() { 
            return MyCoroutine{std::coroutine_handle<MyPromise>::from_promise(*this)};
        }
        auto initial_suspend() {return std::suspend_always{};} 
        auto final_suspend() noexcept {return std::suspend_always{};} 
        void unhandled_exception() {throw;} 
    };
};

// 코루틴 함수
MyCoroutine CoroutineFunc() {  
    std::cout << "CoroutineFunc #1" << std::endl;
    co_await std::suspend_always{}; 
    std::cout << "CoroutineFunc #2" << std::endl;
}

MyCoroutine obj = CoroutineFunc();
obj.Resume(); // 시작하자 마자 대기한 함수 실행
obj.Resume(); // co_await std::suspend_always{}; 이후 재개 
```
[coroutine_traits](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_traits)는 [코루틴 개체](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%A8%EC%88%98-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EA%B0%9C%EC%B2%B4-%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%B8%EB%93%A4-promise-%EA%B0%9C%EC%B2%B4)의 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)인 `promise_type`을 외부에서 연결해 줄때 사용합니다.

1. #1 : `promise_type`을 선언하지 않았습니다.
2. #2 :  `MyCoroutine::MyPromise`를 `promise_type`으로 만듭니다. 이때 `std` [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)에 정의해야 합니다.

```cpp
// 코루틴 개체
class MyCoroutine {  
public:
    struct MyPromise;     
    // using promise_type = MyPromise; // #1. promise_type을 선언하지 않았습니다.
private:
    std::coroutine_handle<MyPromise> m_Handler; // 코루틴 핸들
public:
    MyCoroutine(std::coroutine_handle<MyPromise> handler) : m_Handler(handler) {}
    ~MyCoroutine() {     
        if (m_Handler) {
            m_Handler.destroy();  
        }
    }
    MyCoroutine(const MyCoroutine&) = delete;
    MyCoroutine(MyCoroutine&&) = delete;
    MyCoroutine& operator =(const MyCoroutine&) = delete;
    MyCoroutine& operator =(MyCoroutine&&) = delete;

    void Resume() const { 
    if (!m_Handler.done()) { 
            m_Handler.resume(); 
        }
    } 
    
    // Promise 개체
    struct MyPromise { 
        MyPromise() = default;
        ~MyPromise() = default;
        MyPromise(const MyPromise&) = delete;
        MyPromise(MyPromise&&) = delete;
        MyPromise& operator =(const MyPromise&) = delete;
        MyPromise& operator =(MyPromise&&) = delete;

        MyCoroutine get_return_object() { 
            return MyCoroutine{std::coroutine_handle<MyPromise>::from_promise(*this)};
        }
        auto initial_suspend() {return std::suspend_always{};} 
        auto final_suspend() noexcept {return std::suspend_always{};} 
        void unhandled_exception() {throw;} 
    };
};  

namespace std {
    template<typename... ARGS>
    // #2. MyCoroutine::MyPromise를 promise_type으로 만듭니다.
    struct coroutine_traits<MyCoroutine, ARGS...> { 
        using promise_type = MyPromise;
    };
}

// 코루틴 함수
MyCoroutine CoroutineFunc() {  
    std::cout << "coroutine_traits #1" << std::endl;
    co_await std::suspend_always{}; 
    std::cout << "coroutine_traits #2" << std::endl;
} 

MyCoroutine obj = CoroutineFunc();
obj.Resume(); // 시작하자 마자 대기한 함수 실행
obj.Resume(); // co_await std::suspend_always{}; 이후 재개 
```

# coroutine_handle

[coroutine_handle](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_handle)은 [코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)을 재개하는 [코루틴 핸들](https://tango1202.github.io/cpp/modern-cpp-coroutine/#%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%A8%EC%88%98-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EA%B0%9C%EC%B2%B4-%EC%BD%94%EB%A3%A8%ED%8B%B4-%ED%95%B8%EB%93%A4-promise-%EA%B0%9C%EC%B2%B4)을 제공합니다. 자세한 사용 방법은 [코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)을 참고하시기 바랍니다.

|항목|내용|
|--|--|
|`done()` (C++20~)|[코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)이 종료되었거나 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면 `true`를 리턴합니다.|
|`operator bool` (C++20~)|널검사를 합니다.|
|`operator()` (C++20~)|`resume()`을 실행합니다.|
|`resume()` (C++20~)|[코루틴](https://tango1202.github.io/cpp/modern-cpp-coroutine/)을 재개합니다.|
|`destroy()` (C++20~)|[coroutine_handle](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_handle)을 소멸시킵니다.|
|`promise()` (C++20~)|[promise 개체](https://tango1202.github.io/cpp/modern-cpp-coroutine/#promise-%EA%B0%9C%EC%B2%B4)에 접근합니다.|
|`from_promise()` (C++20~)|[promise 개체](https://tango1202.github.io/cpp/modern-cpp-coroutine/#promise-%EA%B0%9C%EC%B2%B4)로부터 [coroutine_handle](https://tango1202.github.io/cpp-stl/modern-cpp-stl-coroutine/#coroutine_handle)을 생성합니다.|
|`address()` (C++20~)|(작성중)|
|`from_address()` (C++20~)|(작성중)|


