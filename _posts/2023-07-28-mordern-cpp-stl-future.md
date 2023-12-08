---
layout: single
title: "#28. [모던 C++ STL] (C++11~) 비동기(future, promise, async())"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#35] 쓰레드 기반 프로그래밍 보다 async 기반 프로그래밍을 사용하라.
>   * 코드가 간결하다.
> * [MEC++#36] 비동기성이 필수일 때에는 std::launch::async를 지정하라.
>   * 그렇지 않은 경우는 쓰레드가 고갈될때 wait_for()로 쓰레드 지연이 일어났는지 검사하고, wait(), get() 등으로 쓰레드를 대기해야 한다.
> * [MEC++#38] 쓰레드 핸들 소멸자들의 다양한 행동 방식을 주의하라.
> * [MEC++#39] 단발성 사건 통신에는 void 미래 개체를 고려하라.

> * (C++11~) [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)는 비동기 처리를 위한 데이터를 저장합니다.
> * (C++11~) [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)는 비동기 함수가 완료될때까지 대기하고, 데이터를 추출합니다.
> * (C++11~) [shared_future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#shared_future)는 여러곳에서 공유할 수 있는 [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future) 입니다.
> * (C++11~) [packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task)는 [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)를 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하여 비동기 함수 설정만 하면 되는 유틸리티 개체 입니다.
> * (C++11~) [async()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#async)는 [packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task)를 쉽게 사용할 수 있도록 만든 유틸리티 함수입니다.

# 개요

일반적으로 함수 호출의 흐름은 함수가 종료하고 난뒤 다음 함수를 실행합니다. 동기적(Synchronous)으로 호출되는데요,

쓰레드를 사용하면, 쓰레드에서 호출하는 함수가 동시에 실행되다 보니 비동기적(Asynchonous)으로 호출됩니다.

그래서 쓰레드에서 호출하는 함수를 동기적으로 호출하기 위해 [condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable)을 사용했었습니다.

다음 예에서 동기 함수인 `Sync()`는 비동기 함수 `Async()` 를 호출한 뒤 `Async()` 함수가 종료될때까지 대기함으로서 동기화를 구현합니다.

1. `condition_variable m_CV;` 로 조건 변수를 생성합니다.
2. `Async()`함수에서 데이터를 설정하기 전에 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 `lock`을 겁니다. 작업이 끝나면, `m_IsAsyncCompleted = true`를 설정하고 `notify_all()`을 호출하여, `wait()`중인 곳에 알립니다.
3. `Sync()` 함수에서는 `Async()`를 [thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)로 호출하여 비동기로 실행시키고, `wait()`를 실행하여 `m_IsAsyncCompleted`가 `true`가 될때까지 대기합니다.

```cpp
class A {
    int m_Data{0};
    std::condition_variable m_CV;
    bool m_IsAsyncCompleted{false}; // true면 Sync가 wait를 멈춥니다.
public:
    void Async(std::mutex& mutex) {

        { // lock
            std::unique_lock<std::mutex> lock{mutex};
            m_Data = 1; // 데이터를 설정합니다.
            m_IsAsyncCompleted = true; 
        } // unlock
        m_CV.notify_all(); 
    }

    void Sync() {
        std::mutex mutex; 

        std::thread worker{std::mem_fn(&A::Async), std::ref(*this), std::ref(mutex)};

        std::unique_lock<std::mutex> lock{mutex};
        m_CV.wait(lock, [&]() -> bool {return m_IsAsyncCompleted;}); // Async가 끝날때까지 기다립니다.
        lock.unlock();

        worker.join(); 

        EXPECT_TRUE(m_Data == 1);
    }           
};

A a{};
a.Sync();
```

[condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable)과 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 이용해서 비동기 처리를 할수 있습니다만, [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future) 개체를 이용하면 좀더 단순하게 코드를 구현할 수 있습니다.

다음은 [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)와 [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)를 이용하여 비동기 함수 구현을 한 예입니다.

1. [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise) 개체로부터 비동기 함수로 미래에 얻을 데이터인 [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future) 개체를 생성합니다. 
2. `wait()` 함수로 데이터가 설정될때까지 기다립니다.
3. 쓰레드로 실행되는 비동기 함수인 `Async()` 에서 `promise.set_value()` 함수로 데이터를 설정합니다.
4. 그러면 대기를 중단합니다.

```cpp
void Async(std::promise<int>& promise) {
    promise.set_value(1); // 데이터를 설정합니다.
}

int Sync() {

    std::promise<int> promise;
    std::future<int> data{promise.get_future()}; // 미래에 data를 주겠다고 약속합니다.

    std::thread worker{&Async, std::ref(promise)};

    data.wait(); // 데이터를 줄때까지 기다립니다.
    worker.join(); 

    return data.get(); // get() 하면 future 개체에서 관리하던 값은 사라집니다.
} 

EXPECT_TRUE(Sync() == 1); 
```

다음은 비동기 처리를 위한 유틸리티 개체와 함수들입니다.

|항목|내용|
|--|--|
|[promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise) (C++11~)|비동기 처리를 위한 데이터를 저장합니다.|
|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future) (C++11~)|비동기 함수가 완료될때까지 대기하고, 데이터를 추출합니다.|
|[shared_future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#shared_future) (C++11~)|
여러곳에서 공유할 수 있는 [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future) 입니다.|
|[packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task) (C++11~)|[promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)를 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하여 비동기 함수 설정만 하면 되는 유틸리티 개체 입니다.|
|[async()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#async) (C++11~)|[packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task)를 쉽게 사용할 수 있도록 만든 유틸리티 함수입니다.|
|`launch` (C++11~)|[async()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#async) 함수에서 사용하며, 비동기 작업에 대한 정책입니다.<br/>* `async` : 새 쓰레드를 생성한뒤 실행합니다.<br/>* `deferred` : 호출된 쓰레드에서 실행합니다.|
|`future_status`|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)개체 `wait_for()`시 사용하는 상태입니다.|
|`future_error` (C++11~)|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)나 [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)의 오류를 보고합니다.|
|`future_category` (C++11~)|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)의 에러 카테고리입니다.|
|`future_errc` (C++11~)|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)의 에러 코드입니다.|

# promise

|항목|내용|
|--|--|
|`operator =` (C++11~)|[이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.<br/>복사 대입은 `delete`되었습니다.|
|`swap()` (C++11~)|바꿔치기 합니다.|
|`get_future()` (C++11~)|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future) 개체를 생성합니다.|
|`set_value()` (C++11~)|데이터를 설정합니다.|
|`set_value_at_thread_exit()` (C++11~)|(작성중)|
|`set_exception()` (C++11~)|예외를 설정합니다. 설정한 예외는 throw로 방출됩니다.|
|`set_exception_at_thread_exit()` (C++11~)|(작성중)|

# future

|항목|내용|
|--|--|
|`operator =` (C++11~)|[이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.<br/>복사 대입은 `delete`되었습니다.|
|`share()` (C++11~)|[shared_future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#shared_future)로 데이터를 전송합니다.|
|`get()` (C++11~)|`wait()` 한뒤 데이터를 추출합니다. 추출한 뒤에는 데이터가 무효화 됩니다. 따라서 여러번 호출하려면 [shared_future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#shared_future)를 이용해야 합니다.|
|`valid()` (C++11~)|아직 `get()`하지 않아 데이터가 유효한지 확인합니다.|
|`wait()` (C++11~)|[promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)에서 데이터를 설정할때까지 대기합니다.|
|`wait_for()` (C++11~)|주어진 기간이 지나면 대기를 해제합니다.|
|`wait_until()` (C++11~)|주어진 시간이 되면 대기를 해제합니다.|
  
# shared_future

[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)의 경우 `get()`을 하고 나면 데이터가 추출되어 무효화됩니다만, [shared_future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#shared_future)는 계속 `get()`을 할 수 있습니다.

|항목|내용|
|--|--|
|`operator =` (C++11~)|[복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)과 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.|
|`get()` (C++11~)|`wait()` 한뒤 데이터를 추출합니다. 이때 데이터는 [future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)처럼 무효화되지 않습니다.|
|`valid()` (C++11~)|데이터가 유효한지 확인합니다.|
|`wait()` (C++11~)|[promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)에서 데이터를 설정할때까지 대기합니다.|
|`wait_for()` (C++11~)|주어진 기간이 지나면 대기를 해제합니다.|
|`wait_until()` (C++11~)|주어진 시간이 되면 대기를 해제합니다.|

# packaged_task

[promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)를 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하여 비동기 함수 설정만 하면 되는 유틸리티 개체 입니다.

다음 예제를 보면,

1. `Async()` 함수는 [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)에 설정될 데이터를 리턴합니다.
2. [thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread) 생성시 `Async()` 함수의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달합니다.

```cpp
int Async(int val) {
    return val + 1; // promise에 설정될 데이터를 리턴합니다.
}

int Sync() {

    std::packaged_task<int(int)> task(&Async);

    std::future<int> data{task.get_future()}; // 미래에 data를 주겠다고 약속합니다.

    std::thread worker{std::move(task), 10}; // thread에 대입시 task는 복사가 안되므로, 이동 생성합니다. Async 함수의 인자를 전달합니다.

    worker.join(); 

    return data.get(); // get() 하면 future 개체에서 관리하던 값은 사라집니다.
} 
EXPECT_TRUE(Sync() == 11); 
```

만약 `Async()` 함수에서 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키면, 다음처럼 예외 핸들링이 가능합니다.

```cpp
int Async(int val) {
    // return val + 1; // promise에 설정될 데이터를 리턴합니다.
    throw "MyError"; // 에외 발생
}

int Sync() {

    std::packaged_task<int(int)> task(&Async);

    std::future<int> data{task.get_future()}; // 미래에 data를 주겠다고 약속합니다.

    std::thread worker{std::move(task), 10}; // thread에 대입시 task는 복사가 안되므로, 이동 생성합니다. Async 함수의 인자를 전달합니다.

    worker.join(); 

    return data.get(); // get() 하면 future 개체에서 관리하던 값은 사라집니다.
} 

try {
    EXPECT_TRUE(Sync() == 11); 
}
catch(const char* err) {
    std::cout << "Task Exception : " << err << std::endl;
    EXPECT_TRUE(err == "MyError");
}

```

|항목|내용|
|--|--|
|`=` (C++11~)|[이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.<br/>[복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)은 `delete`되었습니다.|
|`swap()` (C++11~)|바꿔치기 합니다.|
|`valid()` (C++11~)|데이터가 유효한지 확인합니다.|
|`get_future()` (C++11~)|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future) 개체를 생성합니다.|
|`operator ()` (C++11~)|함수를 실행합니다.|
|`make_ready_at_thread_exit()` (C++11~)|(작성중)|
|`reset()` (C++11~)|(작성중)|

# async()

[async()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#async)는 [packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task)를 쉽게 사용할 수 있도록 만든 유틸리티 함수입니다.


다음 예제와 같이 [packaged_task](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#packaged_task), [thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread) 정의 없이 사용할 수 있습니다.

```cpp
int Async(int val) {
    return val + 1; // promise에 설정될 데이터를 리턴합니다.
}
int Sync() {

    std::future<int> data{std::async(std::launch::async, &Async, 10)}; // 미래에 data를 주겠다고 약속합니다. 내부적으로 쓰레드를 가동하고 join() 합니다.

    return data.get(); // get() 하면 future 개체에서 관리하던 값은 사라집니다.
} 

EXPECT_TRUE(Sync() == 11); 
```