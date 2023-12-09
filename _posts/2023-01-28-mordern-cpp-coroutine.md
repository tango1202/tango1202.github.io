---
layout: single
title: "#28. [모던 C++] (C++20~) 코루틴(coroutine)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


# 개요

일반적인 함수는 리턴할때까지 실행되고 종료되는데요, [코루틴](??)은 함수를 일시 정지(*Suspend*)하고 재개(*Resume*)할 수 있는 함수입니다. 일시 정지하고 재개를 위해 필요한 정보는 [힙](??)에 저장합니다.

그림

[코루틴](??)의 일시 정지를 위해서는 다음이 사용됩니다.

|항목|내용
|--|--|
|[co_await](??)|일시 정지하고 제어권을 호출자에게 넘깁니다.|
|[co_yield](??)|일시 정지하고 제어권을 호출에게 넘깁니다. 이때 [리턴값](??)을 전달합니다.|
|[co_return](??)|최종적으로 종료하고 제어권을 호출자에게 넘깁니다. 이때 [리턴값](??)을 전달합니다.|


# 코루틴 함수, 코루틴 개체, 코루틴 핸들, promise 개체

함수의 경우에는 [리턴값]으로 값을 바로 호출자가 전달받을 수 있는데요, 

그림

[코루틴](??)은 일시 정지와 재개를 해야 하고, 일시 정지한 상태에서 값을 전달받아야 하기 때문에 여러 개체들이 협력하여 동작합니다.

그림 실행과 일시 정지

그림 데이터 를 호출자로 전달하는 흐름

|항목|내용|
|--|--|
|[코루틴 함수](??)|함수 본문입니다. 함수내에서 [co_await](??), [co_yield](??), [co_return](??)로 일시 정지합니다.(*함수 본문에 [co_await](??), [co_yield](??), [co_return](??)을 작성하면 [코루틴 함수](??)가 됩니다.*)|
|[코루틴 개체](??)|[코루틴 함수](??) 호출자와 데이터 통신을 하며, [코루틴 핸들](??)을 관리합니다.|
|[코루틴 핸들](??)|[promise 개체](??)를 관리합니다. 주로 [코루틴 함수](??)를 재개하는데 사용합니다.|
|[promise 개체](??)|[코루틴 함수](??)의 일시 정지 방법과 [코루틴 함수](??)에서 [co_yield](??), [co_return](??)시 데이터 통신을 합니다.|

# co_await

다음은 일시 정지 후 재개하는 기본적인 코루틴입니다.

[promise 개체](??), [코루틴 핸들](??)로 구성된 [코루틴 개체](??)와 이를 사용하는 [코루틴 함수](??)로 구성되어 있습니다.

1. #1 : 함수 내부에 [co_await](??)를 사용한 [코루틴 함수](??)입니다. [co_await](??)를 할때마다 함수를 일시 정지하고 제어권을 호출자에게 넘깁니다.
2. #2 : [코루틴 개체](??)입니다. #3의 [코루틴 핸들](??)을 관리하며, 일시 정지한 [코루틴](??)을 `m_Handler.resume()`으로 외부에서 재개할 수 있게 합니다.
3. #3 : [코루틴 핸들](??)입니다. [코루틴 개체](??)의 [소멸자](??)에서 `destroy()`됩니다.
4. #4 : [promise 개체](??) 입니다. `get_return_object()`로 [코루틴 개체](??)를 생성합니다.
5. #5 : [코루틴 함수](??)를 시작할때 일시 정지를 어떻게 할 것인지 지정합니다. 본 예에서는 [suspend_always](??)를 사용하여 시작할때 일시 정지하도록 설정했습니다.
5. #6 : [코루틴 함수](??)를 종료할때 일시 정지를 어떻게 할 것인지 지정합니다. 본 예에서는 [suspend_always](??)를 사용하여 종료할때 일시 정지하도록 설정했습니다.

```cpp
// 코루틴 개체
class MyCoroutine {  // #2
public:
    struct MyPromise;
    using promise_type = MyPromise;
private:
    std::coroutine_handle<promise_type> m_Handler; // #3. 코루틴 핸들
public:
    MyCoroutine(std::coroutine_handle<promise_type> handler) : m_Handler(handler) {}
    ~MyCoroutine() {
        if (m_Handler) {
            m_Handler.destroy(); // #3. 코루틴 개체에서 소멸해야 합니다.  
        }
    }
    MyCoroutine(const MyCoroutine&) = delete;
    MyCoroutine(MyCoroutine&&) = delete;
    MyCoroutine& operator =(const MyCoroutine&) = delete;
    MyCoroutine& operator =(MyCoroutine&&) = delete;

    void Resume() const { // #2. 일시 정지된 코루틴을 재개합니다.
        if (!m_Handler.done()) { // 코루틴이 종료되지 않았다면
            m_Handler.resume(); // 재개합니다.
        }
    } 

    // Promise 개체
    struct MyPromise { // #4
        MyPromise() = default;
        ~MyPromise() = default;
        MyPromise(const MyPromise&) = delete;
        MyPromise(MyPromise&&) = delete;
        MyPromise& operator =(const MyPromise&) = delete;
        MyPromise& operator =(MyPromise&&) = delete;

        MyCoroutine get_return_object() { // #4. 코루틴 개체를 리턴합니다.
            return MyCoroutine{std::coroutine_handle<MyPromise>::from_promise(*this)};
        }
        auto initial_suspend() {return std::suspend_always{};} // #5. 코루틴 함수 실행 초기의 일시 정지 상태입니다.
        auto final_suspend() noexcept {return std::suspend_always{};} //#6. 코루틴 함수 종료시 일시 정지 상태입니다.
        void unhandled_exception() {} // 예외 발생시 호출됩니다.
    };
};

// 코루틴 함수
MyCoroutine CoroutineFunc() {  // MyCoroutine 개체를 사용하는 코루틴 함수 
    std::cout << "CoroutineFunc #1" << std::endl;
    co_await std::suspend_always{}; // #1
    std::cout << "CoroutineFunc #2" << std::endl;
    co_await std::suspend_always{}; // #1
    std::cout << "CoroutineFunc #3" << std::endl;
}
```

상기 예를 다음과 같이 실행하면,

```cpp
std::cout << "Call #1" << std::endl;
MyCoroutine object = CoroutineFunc();
std::cout << "Call #2" << std::endl;
```

다음처럼 `CoroutineFunc()`의 어떠한 내용도 실행되지 않습니다. 이는 #5에서 `initial_suspend()`에서 `std::suspend_always{}`를 하여 실행 초기에 일시 정지를 해뒀기 때문입니다.

```cpp
Call #1
Call #2
```

따라서 다음과 같이 `Resume()`을 해줘야 합니다.

```cpp
MyCoroutine obj = CoroutineFunc();

std::cout << "Call #1" << std::endl;
obj.Resume(); // CoroutineFunc #1

std::cout << "Call #2" << std::endl;
obj.Resume(); // CoroutineFunc #2

std::cout << "Call #3" << std::endl;
obj.Resume(); // CoroutineFunc #3
```

그러면, 호출자의 내용과 `CoroutineFunc()`이 번갈아 실행되는 것을 확인할 수 있습니다.

```cpp
Call #1 // 호출자 실행 
CoroutineFunc #1 // 코루틴 함수 재개
Call #2 // 호출자 실행
CoroutineFunc #2 // 코루틴 함수 재개
Call #3 // 호출자 실행
CoroutineFunc #3 // 코루틴 함수 재개
```

# promise 개체

[코루틴 함수](??)의 일시 정지 방법과 [코루틴 함수](??)에서 [co_yield](??), [co_return](??)시 데이터 통신을 합니다. 사용자가 정의해야 하는 필수 [멤버 함수](??)는 다음과 같습니다. (*[대기 가능 개체](??)(*AwaitableType*)는 일시 정지 방법을 결정합니다. [대기 가능 개체](??)(AwaitableType)를 참고하세요.*)

|항목|내용|
|--|--|
|[기본 생성자](??)|[기본 생성자](??)는 필수입니다.|
|`CoroutineObjectType get_return_object()`|[코루틴 개체](??)를 생성하여 리턴합니다.|
|`AwaitableType initial_suspend()`|[코루틴 함수](??) 실행 초기의 일시 정지 상태를 리턴합니다.|
|`AwaitableType final_suspend() noexcept`|[코루틴 함수](??) 종료시 일시 정지 상태를 리턴합니다.|
|`void unhandled_exception()`|[코루틴 함수](??)에서 예외 발생시 호출됩니다.|

또한, [co_return](??), [co_yield](??)사용 여부에 따라 다음 함수를 선택적으로 구현합니다.

|항목|내용|
|--|--|
|`void return_void()`|[코루틴 함수](??)에서 [co_return;](??) 시 호출됩니다.(*[co_return](??) 참고*)|
|`void return_value(ValueType val)`|[코루틴 함수](??)에서 [co_return val;](??) 시 호출됩니다.(*[co_return](??) 참고*)|
|`AwaitableType yield_value(ValueType val)`|[코루틴 함수](??)에서 [co_yield val;](??) 시 호출됩니다.(*[co_yield](??) 참고*)|


# 대기 가능 개체(suspend_always 와 suspend_never)

[대기 가능 개체](??)는 다음 [멤버 함수](??)를 구현한 개체입니다.

|항목|내용|
|--|--|
|`await_ready()`|결과가 준비되었는지 여부입니다. `false`를 리턴하면, `await_suspend()`를 호출합니다.|
|`await_suspend()`|[코루틴](??)의 실행 재개나 소멸을 결정합니다.|
|`await_resume()`|[co_wait](??)의 결과를 제공합니다.|

STL에서는 다음 2가지의 미리 정의된 대기 가능 개체를 제공합니다.

|항목|내용|
|--|--|
|[suspend_always](??)|`await_ready()`에서 항상 `false`를 리턴하며, 대기합니다.|
|[suspend_never](??)|`await_ready()`에서 항상 `true`를 리턴하며, 대기하지 않습니다.|

[co_await](??)에서 `initial_suspend()`시 [suspend_always](??)를 이용했습니다.

```cpp
auto initial_suspend() {return std::suspend_always{};} // #5. 코루틴 함수 실행 초기의 일시 정지 상태입니다.
```

이에 따라 [코루틴 함수](??)를 호출하자 마자 일시 정지가 되어 다음처럼 `Resume()`을 호출했었습니다. 
   
```cpp
MyCoroutine obj = CoroutineFunc();

std::cout << "Call #1" << std::endl;
obj.Resume(); // 코루틴 함수를 호출하자마자 일시 정지하므로 Resume()을 호출합니다.

std::cout << "Call #2" << std::endl;
obj.Resume(); 

std::cout << "Call #3" << std::endl;
obj.Resume(); 
```

실행 결과는 다음과 같습니다.

```cpp
Call #1 // 호출자 실행 
CoroutineFunc #1 // 코루틴 함수 재개
Call #2 // 호출자 실행
CoroutineFunc #2 // 코루틴 함수 재개
Call #3 // 호출자 실행
CoroutineFunc #3 // 코루틴 함수 재개
```

다음은  `initial_suspend()`시 [suspend_never](??)를 사용한 예입니다. [코루틴 함수](??)를 시작하자 마자 일시 정지 하지 않고 [co_await](??)를 만날때까지 실행되므로, 호출되는 순서가 이전과는 달라집니다.

```cpp
// 코루틴 개체
class MyCoroutine { 
    // ...중략 ...
public:
    // ...중략 ...

    // Promise 개체
    struct MyPromise { 
        // ... 중략 ...
        auto initial_suspend() {return std::suspend_never{};} // 코루틴 함수 실행 초기의 일시 정지 상태입니다.
        auto final_suspend() noexcept {return std::suspend_always{};} 
        void unhandled_exception() {} 
    };
};
// 코루틴 함수
MyCoroutine CoroutineFunc() { 
    std::cout << "CoroutineFunc #1" << std::endl; // #1. 최초 실행시 일시 정지 하지 않고 실행됩니다.
    co_await std::suspend_always{}; // #2. 일시 정지 합니다.
    std::cout << "CoroutineFunc #2" << std::endl;
    co_await std::suspend_always{}; // 일시 정지 합니다.
    std::cout << "CoroutineFunc #3" << std::endl;
}
```

다음과 같이 `CoroutineFunc()`이 실행하면, #1이 실행된 다음 #2에서 일시 정지 합니다.

```cpp
MyCoroutine obj = CoroutineFunc(); // CoroutineFunc #1

std::cout << "Call #1" << std::endl;
obj.Resume(); // CoroutineFunc #2

std::cout << "Call #2" << std::endl;
obj.Resume(); // CoroutineFunc #3

std::cout << "Call #3" << std::endl;
```

실행 결과는 다음과 같습니다.

```cpp
CoroutineFunc #1
Call #1
CoroutineFunc #2
Call #2
CoroutineFunc #3
Call #3
```

# promise 개체의 예외 처리

[코루틴 함수](??)에서 [예외가 발생](??)하면, 특별히 [try-catch()](??)하지 않아도 [promise 개체](??)의 `unhandled_exception()` 함수가 호출됩니다. 이때 [코루틴](??)이 종료되었으므로, `Resume()`시 `m_Handler.resume()`이 호출되지 않도록 `m_Handler.done()`으로 조건 검사를 해야 합니다.

```cpp
// 코루틴 개체
class MyCoroutine { 
public:
    // ...중략 ...
public:
    // ...중략 ...
    void Resume() const { 
        if (!m_Handler.done()) { // 예외가 발생되면 코루틴은 종료됩니다.  
            m_Handler.resume(); // 따라서 m_Handler.resume(); 이 호출되지 않도록 합니다.
        }
    }     
    // ...중략 ...

    // Promise 개체
    struct MyPromise { 
        // ...중략 ...
        auto initial_suspend() {return std::suspend_always{};} 
        auto final_suspend() noexcept {return std::suspend_always{};} 
        void unhandled_exception() {std::cout << "Coroutine : unhandled_exception" << std::endl;} // 예외 발생시 호출됩니다. 
    };
};

// 코루틴 함수
MyCoroutine CoroutineFunc() { 
    std::cout << "CoroutineFunc #1" << std::endl; 
    throw 1; // 억지로 예외를 발생시킵니다.
    co_await std::suspend_always{}; // 일시 정지 합니다.
    std::cout << "CoroutineFunc #2" << std::endl;
    co_await std::suspend_always{}; // 일시 정지 합니다.
    std::cout << "CoroutineFunc #3" << std::endl;
}

MyCoroutine obj = CoroutineFunc(); 

std::cout << "Call #1" << std::endl;
obj.Resume(); // CoroutineFunc #1 출력 후 예외를 발생합니다.

std::cout << "Call #2" << std::endl;
obj.Resume(); // CoroutineFunc #2 -> 실행되지 않습니다.

std::cout << "Call #3" << std::endl;
obj.Resume(); // CoroutineFunc #3 -> 실행되지 않습니다.
```

실행 결과는 다음과 같습니다. [예외 발생](??)시 `unhandled_exception()`에 작성된 내용이 실행되고, [코루틴](??)은 재개되지 않습니다.

```cpp
CoroutineFunc #1
Coroutine : unhandled_exception
Call #2
Call #3
```

# 코루틴 함수의 promise 개체 제어 흐름

[co_await](??), [co_yield](??), [co_return](??)가 포함된 [코루틴 함수](??)는 다음처럼 함수 본문을 실행하기 전에 [promise 개체](??)를 처리해 줍니다.

1. #1 : [promise 개체](??)를 생성합니다.
2. #2 : [코루틴 개체](??)를 생성합니다.
3. #3 : [promise 개체](??)의 `initial_suspend()`와 `final_suspend()` 에 따라 최초 실행시와 종료시 일시 정지를 합니다. 
4. #4 : 함수 본문을 일시 정지/재개 하면서 실행하고, [예외 발생](??)시 `unhandled_exception()`을 실행합니다.
5. #5 : [co_return]을 한 경우 `return_void()`나 `return_value()`를 실행합니다.(*[co_return](??) 참고*)

```cpp
MyPromise p; // #1. promise 개체를 생성합니다.
MyCoroutine obj = p.get_return_object(); // #2. 코루틴 개체를 생성합니다.

co_await p.initial_suspend(); // #3. 최초 실행시 일시 정지를 합니다.

try {
    // #4. 함수 본문을 처리합니다.
}
catch (...) {
    p.unhandled_exception(); // #4
}
p.return_void(); // #5. 혹은 p.return_value(); co_return을 한 경우만 실행됩니다. 
co_await p.final_suspend(); // #3. 종료시 일시 정지를 합니다.
```

# co_return

[co_return](??)은 [코루틴 함수](??)를 종료하고 리턴합니다. 일반적인 함수라면 함수의 [리턴값](??)을 그대로 전달받을 수 있으나, [코루틴 함수](??)는 다음과 같이 [코루틴 함수](??)->[promise 개체](??)->[코루틴 개체](??)를 통해 호출자에 전달됩니다.

1. #1 : [코루틴 함수](??)에서 값을 리턴합니다.
2. #2 : [promise 개체](??)의 `return_value()`에서 #3의 [멤버 변수](??)에 저장합니다.(*값 전달 없이 `co_return;`을 하면 `return_void()`가 호출됩니다.*)
3. #4 : [코루틴 개체](??)에서 [코루틴 핸들](??)을 통해 [promise 개체](??)의 `m_Val`에 접근합니다.
4. #5 : 호출자에서 [코루틴 개체](??)를 통해 [코루틴 함수](??)의 [리턴값](??)에 접근합니다.

```cpp
// 코루틴 개체
class MyCoroutine { 
public:
    struct MyPromise;
    using promise_type = MyPromise;
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

    int GetVal() const {return m_Handler.promise().m_Val;} // #4. promise 개체의 멤버 변수 값을 리턴합니다.
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

        int m_Val{0}; // #3. return_value()에서 설정된 값을 저장합니다.
        MyCoroutine get_return_object() { 
            return MyCoroutine{std::coroutine_handle<MyPromise>::from_promise(*this)};
        }
        auto initial_suspend() {return std::suspend_always{};} 
        auto final_suspend() noexcept {return std::suspend_always{};} 
        void return_value(int val) {m_Val = val;} // #2. co_return val; 시 호출됩니다.
        // void return_void() {} // co_return; 시 호출
        void unhandled_exception() {} 
    };
};

// 코루틴 함수
MyCoroutine CoroutineFunc() {  
    std::cout << "CoroutineFunc #1" << std::endl;
    co_await std::suspend_always{};
    std::cout << "CoroutineFunc #2" << std::endl;
    co_await std::suspend_always{};
    std::cout << "CoroutineFunc #3" << std::endl;
    co_return 100; // #1. 코루틴 함수에서 값을 리턴하고 종료합니다.
}

MyCoroutine obj = CoroutineFunc();
EXPECT_TRUE(obj.GetVal() == 0); // #5

obj.Resume(); // CoroutineFunc #1
EXPECT_TRUE(obj.GetVal() == 0); // #5

obj.Resume(); // CoroutineFunc #2
EXPECT_TRUE(obj.GetVal() == 0); // #5

obj.Resume(); // CoroutineFunc #3
EXPECT_TRUE(obj.GetVal() == 100); // #5
```

# co_yield

