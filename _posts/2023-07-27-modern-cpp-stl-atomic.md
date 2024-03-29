---
layout: single
title: "#27. [모던 C++ STL] atomic, memory_order, notify_one, notify_all, wait(C++11, C++20)"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++40] 동시성에는 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)을 사용하고, [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile)은 최적화를 하면 안되는 특별한 메모리에 사용하라.(*[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic), [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile) 참고*)

> * (C++11~) [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)이 추가되어 메모리에서 값을 읽고, 수정하고, 저장하는 작업을 단일 명령 단위로 구성할 수 있습니다. 따라서 [mutex](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex) 없이 쓰레드 경쟁 상태를 해결할 수 있습니다.
> * (C++11~) [memory_order](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#memory_order)가 추가되었습니다. [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)에서 명령을 실행할 때 순차적 일관성 처리 방식을 지정하는 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/) 입니다.
> * (C++20~) [atomic_ref](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-atomic_ref)가 추가되어 참조 타입을 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)으로 사용할 수 있습니다.
> * (C++20~) [notify_one(), notify_all(), wait()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait)가 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)에 추가되어 [condition_variable](https://tango1202.github.io/cpp-stl/modern-cpp-stl-condition_variable)과 같은 인터페이스로 [쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-condition_variable/#%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)를 구현할 수 있습니다.
> * (C++20~) [test(), notify_one(), notify_all(), wait()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic_flag)가 [atomic_flag](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic_flag)에 추가되었습니다.
> * (C++20~) [atomic의 shared_ptr과 weak_ptr의 템플릿 부분 특수화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-atomic%EC%9D%98-shared_ptr%EA%B3%BC-weak_ptr%EC%9D%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94) 버전이 추가되었습니다.
> * (C++20~) [atomic 특수화가 추가](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-atomic-%ED%8A%B9%EC%88%98%ED%99%94-%EC%B6%94%EA%B0%80)되었습니다.(*`float`, `double`, `long double`*)
> * (C++20~) [atomic_signed_lock_free, atomic_unsigned_lock_free](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)가 추가되었습니다.
> * (C++20~) [atomic_init(), ATOMIC_VAR_INIT()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#%EA%B0%9C%EC%9A%94)가 [deprecate](https://tango1202.github.io/cpp-stl/modern-cpp-stl-preview/#deprecateremove) 되었습니다.


# 개요

[쓰레드의 경쟁 상태 해결](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex---%EA%B2%BD%EC%9F%81-%EC%83%81%ED%83%9Crace-condition-%ED%95%B4%EA%B2%B0)을 위해 [mutex](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex)를 이용했는데요,

`m_Val = m_Val + 1`(*혹은 `++m_Val`*) 같은 단순한 연산도 사실은 

1. 메모리에서 `m_Val`의 값을 읽고,
2. 값을 수정하고,
3. 메모리의 `m_Val`의 값을 저장하는

여러 명령 단위로 쪼개져 구성되어 있기 때문에, 여러 쓰레드에서 사용하는 값이 서로 달라질 수 있으므로, [mutex](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex)에 `lock()`을 걸어 한개의 쓰레드만 접근 가능하게 해야 한다고 말씀드렸습니다.

C++11 STL 에서는 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)을 사용하여 메모리에서 값을 읽고, 수정하고, 저장하는 작업을 단일 명령 단위(*더이상 쪼개지지 않는 단위, 원자 단위*)로 구성할 수 있습니다. 따라서 [mutex](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex) 없이 [쓰레드 경쟁 상태 해결](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex---%EA%B2%BD%EC%9F%81-%EC%83%81%ED%83%9Crace-condition-%ED%95%B4%EA%B2%B0)할 수 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e34af791-1282-4f5f-92d3-9150d29464ba)

다음은 원자 단위의 명령을 지원하기 위한 유틸리티 개체와 함수들입니다.

|항목|내용|
|--|--|
|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)은 메모리에서 값을 읽고, 수정하고, 저장하는 작업을 단일 명령 단위로 구성합니다.|
|[atomic_flag](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic_flag) (C++11~)|`atomic<bool>`의 특수한 변형입니다.|
|[atomic_ref](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-atomic_ref) (C++20~)|참조 타입을 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)으로 사용할 수 있습니다.|
|`atomic_init()` (C++11~C++20)|C언어 호환성을 유지하며 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)개체를 초기화 합니다.|
|`ATOMIC_VAR_INIT()` (C++11~C++20)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 초기값을 설정합니다.|
|`ATOMIC_FLAG_INIT` (C++11~)|`atomic_flag`의 초기값입니다.|
|[memory_order](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#memory_order) (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)에서 명령을 실행할 때 순차적 일관성 처리 방식을 지정하는 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)입니다.|
|`kill_dependency()` (C++11~)|(작성중)|
|`atomic_thread_fence()` (C++11~)|(작성중)|
|`atomic_signal_fence()` (C++11~)|(작성중)| 
|`_Atomic()` (C++23~)|C언어 호환용 [매크로 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)입니다.| 

# atomic

[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체는 메모리에서 값을 읽고, 수정하고, 저장하는 작업을 단일 명령 단위로 구성합니다. 따라서 [mutex](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex) 없이  [쓰레드 경쟁 상태 해결](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex---%EA%B2%BD%EC%9F%81-%EC%83%81%ED%83%9Crace-condition-%ED%95%B4%EA%B2%B0)를 해결할 수 있습니다.

다음은 [mutex - 경쟁 상태(Race Condition) 해결](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex---%EA%B2%BD%EC%9F%81-%EC%83%81%ED%83%9Crace-condition-%ED%95%B4%EA%B2%B0)에서 소개한 코드를 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)으로 수정한 예입니다.

1. 기존의 `int m_Val{0};` 을 `atomic<int> m_Val{0};` 으로 수정하였습니다.
2. 기존의 `m_Val = m_Val + 1;`을 `++m_Val;`로 수정하였습니다.
3. `int GetVal() const {return m_Val;}` 와 같이 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 값 타입이 [암시적으로 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)됩니다.

그러면, 기존처럼 [mutex](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex)를 사용하지 않고도, 경쟁 상태에 빠지지 않고 `200`을 계산하는 것을 확인할 수 있습니다.

```cpp
class A {
    // int m_Val{0}; // 일반 변수는 mutex를 사용해야 합니다.
    std::atomic<int> m_Val{0};
public:
    int GetVal() const {return m_Val;} // atomic 개체의 값이 형변환되어 리턴됩니다.
    
    // i를 m_Val을 100번 증가시킵니다.
    void Increase() {
        for (int i{0}; i < 100; ++i) {
            // m_Val = m_Val + 1; // atomic은 + 를 지원하지 않습니다. 
            ++m_Val;
            std::this_thread::sleep_for(std::chrono::milliseconds{1}); // 1 밀리초 만큼 쉽니다.
        }
    }
};

A a{};
std::thread worker1{std::mem_fn(&A::Increase), std::ref(a)};
std::thread worker2{std::mem_fn(&A::Increase), std::ref(a)};

worker1.join(); 
worker2.join(); 

EXPECT_TRUE(a.GetVal() == 200); // (O) 경쟁 상태에 빠지지 않고 잘 계산합니다.
```

> *(C++20~) [atomic_ref](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-atomic_ref)가 추가되어 참조 타입을 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)으로 사용할 수 있습니다.*

# atomic 멤버 함수

|항목|내용|
|--|--|
|`T operator =(T desired)` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체는 복사 가능하지 않으며, `store(desired)` 와 같습니다.|
|`is_lock_free()` (C++11~)|잠금이 없는지(`lock-free`) 확인합니다. `lock-free`면 CPU의 지원이 안되므로 [mutex](https://tango1202.github.io/cpp-stl/modern-cpp-stl-thread-mutex/#mutex)등의 `lock()`으로 잠금 처리 됩니다.|
|`is_always_lock_free` (C++17~)|항상 잠금이 없는지(`lock-free`) 확인합니다.|
|[store()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94) (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체에 값을 저장합니다.([atomic 쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94) 참고)|
|[load()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94) (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)개체의 값을 불러옵니다.([atomic 쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94) 참고)|
|`operator T()` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)개체의 값타입으로 [암시적으로 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)합니다.|
|`exchange()` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 값을 바꾸고, 이전 값을 가져옵니다.|
|`compare_exchange_week()` (C++11~)<br/>`compare_exchange_strong()` (C++11~)|(작성중)|
|`fetch_add()` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 값에 주어진 값을 더합니다.|
|`fetch_sub()` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 값에 주어진 값을 뺍니다.|
|`fetch_and()` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 값에 주어진 값을 비트 AND 합니다.|
|`fetch_or()` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 값에 주어진 값을 비트 OR 합니다.|
|`fetch_xor()` (C++11~)|[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 값에 주어진 값을 비트 XOR 합니다.|
|`++, --` (C++11~)|증감합니다.|
|`+=, -=` (C++11~)|증감후 대입합니다.|
|`&=, |=, ^=` (C++11~)|비트 AND, 비트 OR, 비트 XOR 후 대입합니다.|
|[notify_one()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait) (C++20~)|대기중인 한개의 쓰레드에게 알립니다.|
|[notify_all()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait) (C++20~)|대기중인 모든 쓰레드에게 알립니다.|
|[wait(old)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait) (C++20~)|Notify가 왔을때 `old`와 값이 달라지면 대기를 해제합니다.|

# 순차적 일관성(sequential consistency)

다음 코드 조각에서 `a = 1;` 이 먼저 실행될까요? 아니면 `b = 1;` 이 먼저 실행될까요?

```cpp
a = 1;
b = 1;
```

놀랍게도 ***알 수 없다*** 입니다. 컴파일러나 CPU 마음대로 입니다. 이는 컴파일러가 속도 향상을 위해서 명령 순서를 바꿀 수 있고, CPU가 캐쉬 상황에 따라 캐쉬된 정보를 먼저 접근할 수 있기 때문입니다.

보통은 뭐가 먼저 실행되던 상관없습니다. 어쨋든 `a` 와 `b`가 모두 1이 되니까요.

하지만 멀티 쓰레드 환경에서는 문제가 됩니다. `a = 1;` 만 실행되고 쓰레드가 `a`에 접근하느냐, `b = 1;` 만 실행되고 쓰레드가 `b`에 접근하느냐, `a = 1; b = 1;` 이 모두 실행되고 쓰레드가 접근하느냐에 따라 다른 결과가 나옵니다.

따라서 멀티 쓰레드 환경에서는 명령 실행의 순차적 일관성을 확보하며 프로그래밍할 필요가 있습니다.

# memory_order

[memory_order](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#memory_order)는 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)에서 명령을 실행할 때 순차적 일관성 처리 방식을 지정하는 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)입니다.

|항목|내용|
|--|--|
|`memory_order_relaxed` (C++11~)|순서에 관여하지 않고, 원자 단위 명령만 보장됩니다.|
|`memory_order_consume` (C++11~)|(작성중)|
|`memory_order_acquire` (C++11~)|읽기시 이후 명령들이 해당 명령 이전에 오는 것을 금지합니다.([atomic 쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94) 참고)|
|`memory_order_release` (C++11~)|쓰기시 이전 메모리 명령들이 해당 명령 이후로 가는 것을 금지합니다.([atomic 쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94) 참고)|
|`memory_order_acq_rel` (C++11~)|읽기/쓰기/변경시 이후 명령들이 해당 명령 이전에 오는 것을 금지하고, 이전 명령들이 해당 명령 이후로 가는 것을 금지합니다.|
|`memory_order_seq_cst` (C++11~)|기본적으로 사용됩니다.<br/>순차적 일관성을 보장해 줍니다.|

# atomic 쓰레드 동기화

[atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 개체의 순차적 일관성을 조정하면, [condition_variable](https://tango1202.github.io/cpp-stl/modern-cpp-stl-condition_variable) 처럼 쓰레드간 동기화가 가능합니다.

다음 예제에서,

1. `m_IsCompleted.store(true, std::memory_order_release);`을 이용하여 `m_Val1`과 `m_Val2`를 수정하고,
2. `while(!m_IsCompleted.load(std::memory_order_acquire)) {}`을 이용하여 `m_Completed`가 `true`가 될때까지 대기 합니다.
3. `#1`, `#2`는 `#3`, `#5`에 의해 `#6`, `#7` 보다 먼저 실행됨을 보장합니다.

즉, `#1`, `#2`와 `#6`, `#7`이 동기화되어 동작합니다.

```cpp
class A {
    int m_Val1{0};
    int m_Val2{0};
    int m_Val3{0};

    std::atomic<bool> m_IsCompleted{false};
public:
    void Producer() {

        m_Val1 = 1; // #1
        m_Val2 = 2; // #2

        // #1, #2가 나중에 실행되지 않도록 보증합니다.
        m_IsCompleted.store(true, std::memory_order_release); // #3

        m_Val3 = 3; // #4
    }

    void Consumer() {

        // m_IsCompleted가 true가 될때까지 while 문을 반복하며 대기합니다.
        // #6, #7, #8 이 먼저 실행되지 않도록 보증합니다.
        while(!m_IsCompleted.load(std::memory_order_acquire)) {} // #5
        
        EXPECT_TRUE(m_Val1 == 1); // #6
        EXPECT_TRUE(m_Val2 == 2); // #7
        // #4가 #3보다 먼저 실행될 수도 있고, 아닐 수도 있으므로 m_Val3은 0 또는 3입니다.
        EXPECT_TRUE(m_Val3 == 0 || m_Val3 == 3); // #8
    }
};
A a{};
std::thread producer{std::mem_fn(&A::Producer), std::ref(a)};
std::thread consumer{std::mem_fn(&A::Consumer), std::ref(a)};

producer.join(); 
consumer.join(); 
```

> *(C++20~) [notify_one(), notify_all(), wait()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait)가 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)에 추가되어 [condition_variable](https://tango1202.github.io/cpp-stl/modern-cpp-stl-condition_variable)과 같은 인터페이스로 [쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-condition_variable/#%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)를 구현할 수 있습니다.*

# atomic 유틸리티 함수

|항목|내용|
|--|--|
|`atomic_store()` (C++11~)<br/>`atomic_store_explicit()` (C++11~)|(작성중)|
|`atomic_load()` (C++11~)<br/>`atomic_load_explicit()` (C++11~)|(작성중)|
|`atomic_exchange()` (C++11~)<br/>`atomic_exchange_explicit()` (C++11~)|(작성중)|
|`atomic_compare_exchange_weak()` (C++11~)<br/>`atomic_compare_exchange_weak_explicit()` (C++11~)`atomic_compare_exchange_strong()` (C++11~)<br/>`atomic_compare_exchange_strong_explicit()` (C++11~)|(작성중)|
|`atomic_fetch_add()` (C++11~)<br/>`atomic_fetch_add_explicit()` (C++11~)|(작성중)|
|`atomic_fetch_sub()` (C++11~)<br/>`atomic_fetch_sub_explicit()` (C++11~)|(작성중)|
|`atomic_fetch_and()` (C++11~)<br/>`atomic_fetch_and_explicit()` (C++11~)|(작성중)|
|`atomic_fetch_or()` (C++11~)<br/>`atomic_fetch_or_explicit()` (C++11~)|(작성중)|
|`atomic_fetch_xor()` (C++11~)<br/>`atomic_fetch_xor_explicit()` (C++11~)|(작성중)|
|`atomic_notify_one()` (C++20~)|(작성중)|
|`atomic_notify_all()` (C++20~)|(작성중)|
|`atomic_wait()` (C++20~)<br/>`atomic_wait_explicit()` (C++20~)|(작성중)|

# atomic 타입 별칭

`atomic<bool>`과 같은 기본 타입에 대한 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic) 타입들은 다음과 같이 별칭이 선언되어 있습니다.

|항목|내용|
|--|--|
|`atomic_bool` (C++11~)|`atomic<bool>`|
|`atomic_char` (C++11~)|`atomic<char>`|
|`atomic_schar` (C++11~)|`atomic<signed char>`|

그외 다른 별칭들은 [cppreference](https://en.cppreference.com/w/cpp/atomic/atomic)를 참고하시기 바랍니다.

다음의 특수 목적 별칭이 있습니다.

|항목|내용|
|--|--|
|`atomic_signed_lock_free` (C++20~)|(작성중)|
|`atomic_unsigned_lock_free` (C++20~)|(작성중)|

# atomic_flag

`atomic_flag` 는 `true`, `false`를 저장하는 `atomic<bool>`의 특수한 변형이며 `load()`, `store()`를 제공하지 않습니다. 

**atomic_flag 멤버 함수**

|항목|내용|
|--|--|
|`clear()` (C++11~)|플래그를 `false`로 설정합니다.|
|`test_and_set()` (C++11~)|`true`로 설정하고 이전값을 리턴합니다.|
|`test()` (C++20~)|현재값을 리턴합니다.|
|[notify_one()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait) (C++20~)|대기중인 한개의 쓰레드에게 알립니다.|
|[notify_all()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait) (C++20~)|대기중인 모든 쓰레드에게 알립니다.|
|[wait(old)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait) (C++20~)|Notify가 왔을때 `old`와 값이 달라지면 대기를 해제합니다.|

**atomic_flag 유틸리티 함수**

|항목|내용|
|--|--|
|`atomic_flag_test_and_set()` (C++11~)<br/>`atomic_flag_test_and_set_explicit()` (C++11~)<br/>|(작성중)|
|`atomic_flag_clear()` (C++11~)<br/>`atomic_flag_clear_explicit()` (C++11~)<br/>|(작성중)|
|`atomic_flag_test()` (C++20~)<br/>`atomic_flag_test_explicit()` (C++20~)<br/>|(작성중)|
|`atomic_flag_notify_one()` (C++20~)|(작성중)|
|`atomic_flag_notify_all()` (C++20~)|(작성중)|
|`atomic_flag_wait()` (C++20~)<br/>`atomic_flag_wait_explicit()` (C++20~)<br/>|(작성중)|

# (C++20~) atomic_ref

기존의 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)은 복사가 가능한 타입만 가능했는데요, C++20 부터는 [atomic_ref](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-atomic_ref)가 추가되어 참조 타입을 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)으로 사용할 수 있습니다.

```cpp
class A {
    // std::atomic<int&> m_Val; // (X) 컴파일 오류. 복사 가능해야 합니다.
    std::atomic_ref<int> m_Val; // (O) 
public:
    explicit A(int& val) : m_Val{val} {}
    int GetVal() const {return m_Val;}
    
    // i를 m_Val을 100번 증가시킵니다.
    void Increase() {
        for (int i{0}; i < 100; ++i) {
            ++m_Val;
            //!! 테스트 속도 때문에 임시 주석
            std::this_thread::sleep_for(std::chrono::milliseconds{1}); 
        }
    }
};

int val{0};
A a{val};
std::thread worker1{std::mem_fn(&A::Increase), std::ref(a)};
std::thread worker2{std::mem_fn(&A::Increase), std::ref(a)};

worker1.join(); 
worker2.join(); 

EXPECT_TRUE(a.GetVal() == 200); // (O) 경쟁 상태에 빠지지 않고 잘 계산합니다.
EXPECT_TRUE(val == 200); // (O) 참조한 값이 잘 반영되어 있습니다.    
```

# (C++20~) notify_one(), notify_all(), wait()

기존에는 [store()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)와 [load()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)이용하여 [atomic으로 쓰레드를 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94) 했는데요(*[atomic 쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic-%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)참고*),

C++20 부터 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)에 [notify_one(), notify_all(), wait()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait)가 추가되어 [condition_variable](https://tango1202.github.io/cpp-stl/modern-cpp-stl-condition_variable)과 같은 인터페이스로 [쓰레드 동기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-condition_variable/#%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)를 구현할 수 있습니다.

다음을 보면 [notify_one(), notify_all(), wait()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#c20-notify_one-notify_all-wait)이 좀더 직관적입니다.

```cpp
class A {
    int m_Val1{0};
    int m_Val2{0};

    std::atomic<bool> m_IsCompleted{false};
public:
    void Producer() {

        m_Val1 = 1; 
        m_Val2 = 2; 

        // store-load 방식
        // m_IsCompleted.store(true, std::memory_order_release); 
        
        // notify-wait 방식
        m_IsCompleted = true;
        m_IsCompleted.notify_all(); // 값이 수정되었음을 통지합니다.
    }

    void Consumer() {
        // store-load 방식
        // while(!m_IsCompleted.load(std::memory_order_acquire)) {} 

        // notify-wait 방식
        m_IsCompleted.wait(false); // 값이 false 이면 대기합니다.
        
        EXPECT_TRUE(m_Val1 == 1); 
        EXPECT_TRUE(m_Val2 == 2); 
    }
};
A a{};
std::thread producer{std::mem_fn(&A::Producer), std::ref(a)};
std::thread consumer{std::mem_fn(&A::Consumer), std::ref(a)};

producer.join(); 
consumer.join(); 
```

# (C++20~) atomic의 shared_ptr과 weak_ptr의 템플릿 부분 특수화

C++20 부터 [atomic](https://tango1202.github.io/cpp-stl/modern-cpp-stl-atomic/#atomic)의 [shared_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-shared_ptr-weak_ptr/)과 [weak_ptr](https://tango1202.github.io/cpp-stl/modern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr)의 [템플릿 부분 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94) 버전이 추가되었습니다.

```cpp
std::atomic<std::shared_ptr<int>> val{std::make_shared<int>(0)}; // shared_ptr 부분 특수화 버전입니다.

std::thread t1{
    [&val] {
        val.store(std::make_shared<int>(*val.load() + 1));
    }
};
std::thread t2{
    [&val] {
        val.store(std::make_shared<int>(*val.load() + 2));
    }
};
std::thread t3{
    [&val] {
        val.store(std::make_shared<int>(*val.load() + 3));
    }
};

t1.join();
t2.join();
t3.join();

EXPECT_TRUE(*val.load() == 1 + 2 + 3); // 쓰레드 경쟁없이 연산을 수행합니다.
```

# (C++20~) atomic 특수화 추가

`float`, `double`, `long double`의 특수화가 추가되었습니다.
