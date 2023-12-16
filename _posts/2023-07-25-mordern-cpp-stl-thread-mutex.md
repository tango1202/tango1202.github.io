---
layout: single
title: "#25. [모던 C++ STL] (C++11~) 쓰레드(thread, mutex), (C++14~) shared_timed_mutex, shared_lock, (C++17~) shared_mutex, scoped_lock"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#16] const [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 쓰레드에 안전하게 작성하라(mutex, atomic)
> * [MEC++#37] thread 들을 모든 경로에서 합류 불가능하게 만들어라.(join(), detach()를 사용하라.)

> * (C++11~) [thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)가 추가되어 주어진 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 쓰레드로 실행시킬 수 있습니다. [yield(), sleep_for(), sleep_until()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#%EC%93%B0%EB%A0%88%EB%93%9C-yield-sleep_for-sleep_until)등으로 실행 순서나 속도를 제어할 수 있습니다.
> * (C++11~) [mutex, timed_mutex, recusive_mutex, recusive_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)등이 추가되어 쓰레드간 경쟁 상태를 해결할 수 있습니다.
> * (C++11~)[lock_guard, unique_lock, lock(), try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock) 등이 추가되어 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 잠금 상태를 관리할 수 있습니다.
> * (C++11~) [call_once()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#call_once-once_flag)가 추가되어 주어진 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 여러 쓰레드에서 실행해도 한번만 호출하게 할 수 있습니다. 
> * (C++14~) [shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)가 추가되어 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 소유권을 쓰레드끼리 공유할 수 있습니다.
> * (C++14~) [shared_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)이 추가되어 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 소유권을 쓰레드끼리 공유할 수 있습니다.
> * (C++17~) [[shared_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)가 추가되어 다른 쓰레드들과 공유할 수 있는 `lock_shared()`를 지원하는 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 만들 수 있습니다. 읽고 쓰는 쓰레드 없이, 자원을 읽기만 할때 유용합니다.
> * (C++17~) [scoped_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c17-scoped_lock)이 추가되었습니다. 다수의 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 사용하더라도 데드락(Dead Lock)을 방지할 수 있게 해줍니다.

# 개요

기존에는 멀티 쓰레드 관련 기능들이 표준화 되지 않아 운영체제에 따라 다르게 구현했습니다만, C++11 STL 부터는 이를 표준화 하여 제공합니다.

**쓰레드 실행**

|항목|내용|
|--|--|
|[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread) (C++11~)|주어진 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 쓰레드로 실행시킵니다. [yield(), sleep_for(), sleep_until()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#%EC%93%B0%EB%A0%88%EB%93%9C-yield-sleep_for-sleep_until)등으로 실행 순서나 속도를 제어할 수 있습니다.|
|`jthread` (C++20~)|(작성중)|

**쓰레드 취소**

|항목|내용|
|--|--|
|`stop_token` (C++20~)|(작성중)|
|`stop_source` (C++20~)|(작성중)|
|`stop_callback` (C++20~)|(작성중)|

**캐쉬 크기**

|항목|내용|
|--|--|
|`hardware_destructive_interference_size` (C++17~)<br/>`hardware_constructive_interference_size` (C++17~)|잘못된 공유를 방지하기 위한 최소 크기입니다. CPU L1 데이터 캐쉬 접근에 호환되는 방법을 제공합니다.|

**동기화 개체**

|항목|내용|
|--|--|
|[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex) (C++11~)<br/>`timed_mutex` (C++11~)<br/>`recursive_mutex` (C++11~)<br/>`recursive_timed_mutex` (C++11~)<br/>[shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock) (C++14~)<br/>[shared_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex) (C++17~)|쓰레드간 경쟁 상태를 해결하기 위한 동기화 개체 입니다.|
|`counting_semaphore` (C++ 20)|(작성중)|
|`binary_semaphore` (C++ 20)|(작성중)|
|`latch` (C++ 20)|(작성중)|
|`barrier` (C++ 20)|(작성중)|

**동기화 설정**

|항목|내용|
|--|--|
|[lock_guard](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock_guard---mutex%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0) (C++11~)<br/>`unique_lock` (C++11~)<br/>`lock()` (C++11~)<br/>[try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0) (C++11~)<br/>[shared_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock) (C++14~)<br/>[scoped_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c17-scoped_lock) (C++17~)|[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 잠금 상태를 관리합니다.([lock_guard - mutex가 1개인 경우 데드락(Dead Lock) 해결](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock_guard---mutex%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)과 [try_lock() - mutex가 여러개인 경우 데드락(Dead Lock) 해결](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0) 참고)|
|[call_once()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#call_once-once_flag) (C++11~)|주어진 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 여러 쓰레드에서 실행해도 한번만 호출합니다.|

> *(C++17~) [대부분의 알고리즘에서 병렬 작업을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/)하는 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전이 추가되었습니다. [seq, par, par_unseq, unseq](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/#%EC%8B%A4%ED%96%89-%EC%A0%95%EC%B1%85)으로 병렬 실행 정책을 지정할 수 있습니다.*

# 쓰레드

프로세스(Process)는 프로그램이 컴퓨터 메모리에 로딩되어 실행되고 있는 인스턴스 입니다. 한마디로 실행중인 프로그램입니다.

쓰레드(Thread)는 프로세스 내에서 작동중인 CPU의 실행 단위입니다. 싱글 쓰레드라면, 실행 단위가 하나여서 한가지 작업만 한다는 뜻이고, 멀티 쓰레드라면, 실행 단위가 여러개여서 여러 작업을 동시에 한다는 뜻입니다.

예를 들어, 싱글 쓰레드인 사람은 보고 난 후, 듣고 난 후, 말할 수 있지만, 멀티 쓰레드인 사람은 보면서 듣고 말할 수 있습니다.

아무래도 멀티 쓰레드가 동시에 작업하다 보니 작업 시간은 단축될 수 있죠. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/38aaa574-dcfc-427e-a084-a8480dbc2a63)

다만 헷갈려서 오동작 할 수 있습니다. 본 것을 말해야 하는데, 정신 사나워서 들은걸 말할 수도 있습니다. 

컴퓨터도 마찬가지 입니다. 정신 사나워서 엉뚱한 것을 연산할 수 있습니다. 예를 들어 서로 다른 쓰레드가 동일한 자원을 사용할 때 **경쟁 상태(Race Condition)** 가 되어 잘못된 값을 사용할 수 있습니다. 또는 서로 자원 사용을 양보하다가 **데드락(Dead Lock)** 상태가 되어 시스템이 멈출 수 있습니다.  

# 경쟁 상태(Race Condition)

다음처럼 서로 다른 쓰레드가 동일한 데이터에 접근할때 하나는 값을 저장하고, 하나를 값을 읽는다고 생각해 봅시다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ce0506d4-9d71-4cf4-b416-d1803f518f13)

동시에 저장하고 읽다 보니 쓰레드 B가 읽어온 데이터는 쓰레드 A가 수정하기 전의 값인지, 수정한 후의 값인지 알 수 없습니다.(이런 상태를 경쟁상태(Race Condition)라 합니다.) 이런 경우를 막으려면, 공유하는 데이터에 Lock을 걸어 한개의 쓰레드만 데이터에 접근할 수 있게 하고, 다른 쓰레드는 잠시 대기하도록 해야 합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7f4c7dd4-9b54-4e51-9fda-5a7b857f52a3)

만약 쓰레드 A가 데이터에 먼저 접근하여 Lock을 걸어두면, 쓰레드 A가 Lock을 풀어줄때까지 쓰레드 B는 대기합니다. 이후 쓰레드 A가 작업을 끝내고 Lock을 풀어주면, 그제서야 쓰레드 B가 데이터에 접근하여 작업을 할 수 있습니다. 이때 쓰레드 B도 쓰레드 A가 대기할 수 있도록 Lock을 걸어줘야 합니다.

# 크리티컬 섹션(Critical Section)

쓰레드가 단독으로 실행해야 하는 코드 구간(Lock ~ Unlock 구간)을 크리티컬 섹션이라 합니다.

# 데드락(Dead Lock)

만약 쓰레드 A가 Lock을 걸기만 하고 풀지 않으면 어떻게 될까요? 쓰레드 B는 무한 대기하게 됩니다.(이런 상태를 데드락(Dead Lock)이라 합니다.) 이렇게 대기하면, 원하는 작업이 끝나지 않아 프로그램은 마치 멈춘것처럼 아무 동작도 안하고, 계속 대기하게 됩니다. 결국 프로그램을 강제 종료해야 합니다.

# thread

[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)는 쓰레드를 생성한 뒤 주어진 함수(또는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/))를 실행시킵니다. 이후 `join()`을 이용하여 쓰레드가 종료할 때까지 대기하거나, `detach()`를 이용하여 계속 백그라운드에서 쓰레드가 실행되도록 내버려 두어야 합니다. 만약 `join()`이나 `detach()`를 하지 않으면 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.

[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 다음과 같습니다.

|항목|내용|
|--|--|
|`joinable()` (C++11~)|[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)가 동작중인지 확인합니다.|
|`get_id()` (C++11~)|[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)의 아이디를 구합니다.|
|`native_handle()` (C++11~)|시스템에 따른 구현에 정의된 쓰레드 핸들을 리턴합니다.|
|`hardware_concurrency()` (C++11~)|시스템이 지원하는 동시 쓰레드 수를 리턴합니다.|
|`join()` (C++11~)|[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)가 종료될때까지 기다립니다.|
|`detach()` (C++11~)|[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread)가 실행되도록 내버려 둡니다.|
|`swap()` (C++11~)|바꿔치기 합니다.|

다음은 `Message1()` 과 `Message2()` 를 서로 다른 쓰레드로 호출한 예입니다. `join()`을 사용하여 쓰레드가 끝날때까지 기다립니다.

```cpp
void Message1() {
    for(int i{0};i < 100; ++i) {
        std::cout << "Message1 : " << i << std::endl;
    }        
}
void Message2() {
    for(int i{0};i < 100; ++i) {
        std::cout << "Message2 : " << i << std::endl;
    }        
} 
std::thread worker1{Message1};
std::thread worker2{Message2};

worker1.join(); // worker1이 종료될때까지 기다립니다.
worker2.join(); // worker2가 종료될때까지 기다립니다.
```

다음은 실행 결과입니다. 시스템 상황에 따라 다를 겁니다만, `Message1()` 함수의 출력과 `Message2()` 함수의 출력이 동시에 실행되다 보니 섞여서 나오는 걸 알 수 있습니다. 재밌게도, `Message1()` 함수가 `cout << "Message1 : " << i` 까지 출력하고, ` << std::endl;`를 출력하기 직전에, `Message2()의 ` `cout << "Message2 : " << i << endl;`가 호출됐네요. 

```cpp
Message1 : 0
Message1 : 1Message2 : 0 // Message1()의 << std::endl; 직전에 Message2()가 실행됨
Message2 : 1
Message2 : 2
Message2 : 3
Message2 : 4
Message2 : 5
Message2 : 6
Message2 : 7
Message2 : 8
Message2 : 9
...
```

# 쓰레드 속도 측정

다음은 쓰레드의 속도를 측정하는 코드입니다.

1. [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)에 들어 있는 100개의 정수를 더하고 테스트 합니다.
2. `Sum()` 함수는 주어진 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/) 범위의 요소를 모두 더합니다.
3. `ThreadSum()` 함수를 2개의 쓰레드를 이용하여 `[시작 ~ 중간)` 과 `[중간 ~ 끝)`을 `Sum()`함수를 이용하여 각각 계산한 후 최종적으로 이 둘을 더해 리턴합니다.
4. `Measure()`함수는 전달된 함수를 실행하고 실행 시간을 측정합니다. 실행시킬 함수에 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달하기 위해 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)을 사용합니다.
5. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 유지를 위해 [ref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref)를 사용합니다.
   
```cpp
void Sum(std::vector<int>::iterator itr, std::vector<int>::iterator endItr, int& result) {
    result = 0;

    for(;itr != endItr; ++itr) {
        result += *itr;
    }
}

void ThreadSum(std::vector<int>::iterator itr, std::vector<int>::iterator endItr, int& result) {

    std::vector<int>::iterator middleItr{itr};
    std::advance(middleItr, std::distance(itr, endItr) / 2); // 중간 지점 이터레이터를 구함

    int result1{0};
    std::thread worker1{ // [시작 ~ 중간) 까지의 Sum
        Sum, 
        itr, middleItr, 
        std::ref(result1) // 인수의 참조성 유지
    }; 

    int result2{0};
    std::thread worker2{ // [중간 ~ 끝) 까지의 Sum
        Sum, 
        middleItr, endItr, 
        std::ref(result2) // 인수의 참조성 유지
    }; 

    worker1.join(); // worker1이 종료될때까지 기다립니다.
    worker2.join(); // worker2가 종료될때까지 기다립니다.

    // worker1, worker2 결과를 더합니다.
    result = result1 + result2;
}    

template<typename Func, typename... Params>
std::chrono::microseconds Measure(Func func, Params&&... params) {
    std::chrono::system_clock::time_point start{std::chrono::system_clock::now()};    

    func(std::forward<Params>(params)...);

    std::chrono::system_clock::time_point end{std::chrono::system_clock::now()};
    std::chrono::microseconds val{std::chrono::duration_cast<std::chrono::microseconds>(end - start)};

    return val;
}

std::vector<int> v;

for (int i{0}; i < 100; ++i) {
    v.push_back(i);
}

int result{0};
std::chrono::microseconds duration{Measure(
    Sum, 
    v.begin(), v.end(), 
    std::ref(result) // 인수의 참조성 유지
)};
std::cout << "Sum : " << result << " Duration : " << duration.count() << std::endl; 

int threadResult{0};
std::chrono::microseconds threadDuration{Measure(
    ThreadSum, 
    v.begin(), v.end(), 
    std::ref(threadResult) // 인수의 참조성 유지
)};
std::cout << "ThreadSum : " << threadResult << " Duration : " << threadDuration.count() << std::endl; 
```

실행 결과는 다음과 같습니다. 계산 결과는 `4950`으로 동일하게 잘 나왔습니다. 그런데, 이상하게도 쓰레드를 사용하는 버전이 훨씬 오래 걸립니다.

```cpp
Sum : 4950 Duration : 1
ThreadSum : 4950 Duration : 272 // 쓰레드를 사용하는 버전이 훨씬 오래 걸립니다.
```

이는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)에 들어 있는 100개의 정수를 더하는데 1 마이크로초(1/1,000,000 초) 밖에 걸리지 않는 반면에, 쓰레드를 생성하는 부하가 오히려 더 있기 때문입니다. 이렇게 짧은 시간동안 실행되는 작업을 쓰레드로 만들면 오히려 더 느려집니다.

`Sum()` 함수가 시간이 좀 걸리는 것처럼 시뮬레이션 해서 다시 테스트 해보겠습니다. 

`sleep_for()` 로 잠시 대기하도록 바꿔서 테스트 해보면,

```cpp
void Sum(std::vector<int>::iterator itr, std::vector<int>::iterator endItr, int& result) {
    result = 0;

    for(;itr != endItr; ++itr) {
        result += *itr;
        std::this_thread::sleep_for(std::chrono::milliseconds{1}); // 1 밀리초 만큼 쉽니다.
    }
}
```

실행 결과는 다음과 같이 쓰레드 버전이 2배 정도 빠릅니다. 그래서, 시간이 많이 걸리는 작업을 쓰레드로 만들어야 합니다.

```cpp
Sum : 4950 Duration : 1595827 // 약 1.5초
ThreadSum : 4950 Duration : 784464 // 약 0.7초
```

# 쓰레드 yield(), sleep_for(), sleep_until()

현재 동작하는 쓰레드에 대하여 다음 함수를 사용할 수 있습니다.(`this_thread` [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)에 정의되어 있습니다.)

|항목|내용|
|--|--|
|`yield()` (C++11~)|현 쓰레드를 쓰레드 대기열 뒤로 이동 시켜, 다른 쓰레드들이 먼저 실행되게 합니다.|
|`get_id()` (C++11~)|현 쓰레드의 Id를 구합니다.|
|`sleep_for()` (C++11~)|주어진 시간동안 쓰레드를 쉽니다.([쓰레드 속도 측정](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#%EC%93%B0%EB%A0%88%EB%93%9C-%EC%86%8D%EB%8F%84-%EC%B8%A1%EC%A0%95) 참고)|
|`sleep_until()` (C++11~)|주어진 시간까지 쓰레드를 쉽니다.|

# mutex

[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)는 공유 자원 접근을 하나의 쓰레드에서만 처리할 수 있도록 해주는 동기화 개체입니다. 다음과 같은 개체들이 제공됩니다.

|항목|내용|
|--|--|
|[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex) (C++11~)|[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)는 공유 자원 접근을 하나의 쓰레드에서만 처리할 수 있도록 해주는 동기화 개체입니다.|
|`timed_mutex` (C++11~)|`try_lock_for()`나 `try_lock_until()`등으로 주어진 시간동안(혹은 까지) [try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)을 시도하는 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)입니다.|
|`recursive_mutex` (C++11~)|동일한 쓰레드 내에서 재귀적으로 `lock()`을 할 수 있는 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)입니다.|
|`recursive_timed_mutex` (C++11~)|주어진 시간동안(혹은 까지) [try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)을 시도하는 재귀적 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)입니다.|
|[shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock) (C++14~)|주어진 시간동안(혹은 까지) [try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)을 시도하는 다른 쓰레드들과 공유할 수 있는 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)입니다.|
|[shared_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex) (C++17~)|다른 쓰레드들과 공유할 수 있는 `lock_shared()`를 지원하는 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)입니다. 읽고 쓰는 쓰레드 없이, 자원을 읽기만 할때 유용합니다.|

[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 다음과 같습니다. `lock()`과 `unlock()`을 이용하여 다른 쓰레드들의 접근을 차단/해제합니다. 

|항목|내용|
|--|--|
|`lock()` (C++11~)|현 쓰레드만 접근 가능하고, 다른 쓰레드는 대기시킵니다.([mutex - 경쟁 상태(Race Condition) 해결](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex---%EA%B2%BD%EC%9F%81-%EC%83%81%ED%83%9Crace-condition-%ED%95%B4%EA%B2%B0) 참고)|
|[try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0) (C++11~)|`lock()`을 시도하고, 만약 다른 쓰레드에 의해 대기된다면, `false`를 리턴합니다.|
|`unlock()` (C++11~)|`lock()`을 해제하여 다른 쓰레드에서도 자원을 사용할 수 있게 합니다.([mutex - 경쟁 상태(Race Condition) 해결](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex---%EA%B2%BD%EC%9F%81-%EC%83%81%ED%83%9Crace-condition-%ED%95%B4%EA%B2%B0) 참고)|
|`native_handle()` (C++11~)|시스템에 따른 구현에 정의된 핸들을 리턴합니다.|

# lock

[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)는 `lock()`을 호출하면, 반드시 `unlock()`을 호출해야 합니다. 그렇지 않으면 데드락(Dead Lock)에 빠지니까요. 이에 따라 [스택 풀기](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 의해 자연스럽게 `unlock()`이 호출되도록 [lock_guard](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock_guard---mutex%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)를 제공합니다. 그외에도 다음과 같은 유틸리티 개체와 함수들이 있습니다.

|항목|내용|
|--|--|
|[lock_guard](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock_guard---mutex%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0) (C++11~)|개체 생성시 `lock()`을 하고, 개체 소멸시 `unlock()` 합니다.|
|`unique_lock` (C++11~)|[lock_guard](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock_guard---mutex%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0) 처럼 생성시 자동으로 잠글 수도 있고, 별도로 `lock()`을 호출해야 잠글 수도 있습니다.|
|lock 옵션 (C++11~)|`defer_lock` : `lock()` 호출시 잠금이 됩니다.<br/>`try_to_lock` : 잠금시 [try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)을 사용합니다.<br/>`adopt_lock` : 해당 쓰레드가 이미 `lock()`을 했다고 가정하고, `unlock()`만 합니다.|
|`lock()` (C++11~)|여러개의 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 `lock()`합니다.|
|[try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0) (C++11~)|여러개의 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 [try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)합니다.|
|[shared_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock) (C++14~)|공유할 수 있는 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 `unique_lock` 처럼 생성시 자동으로 잠글 수도 있고, 별도로 `lock()`을 호출해야 잠글 수도 있습니다.|
|[scoped_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c17-scoped_lock) (C++17~)|여러개의 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 `lock()`하며 데드락을 방지합니다.|

# mutex - 경쟁 상태(Race Condition) 해결

다음은 경쟁 상태(Race Condition)가 발생하는 예입니다. 서로 다른 두 쓰레드가 `A`개체의 `m_Val`을 100번씩 증가시킵니다. 두개가 100번씩 증가시켰으므로 200이 되야 할 것 같지만, 다른 값이 나올 수 있습니다. 

```cpp
class A {
    int m_Val{0};
public:
    int GetVal() const {return m_Val;}
    
    // i를 m_Val을 100번 증가시킵니다.
    void Increase() {
        for (int i{0}; i < 100; ++i) {
            m_Val = m_Val + 1;
            std::this_thread::sleep_for(std::chrono::milliseconds{1}); // 1 밀리초 만큼 쉽니다.
        }
    }
};

A a{};
std::thread worker1{std::mem_fn(&A::Increase), std::ref(a)};
std::thread worker2{std::mem_fn(&A::Increase), std::ref(a)};

worker1.join(); 
worker2.join(); 

std::cout << "Non Mutex : " << a.GetVal() << std::endl;
```

제 경우엔 198이 나왔습니다. 

이는 `A`개체의 `m_Val`이 두개의 쓰레드에 공유됨으로써 경쟁 상태(Race Condition)에 빠졌기 때문입니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9355f39a-945f-41a8-ab93-702eb90c1741)


이를 해결하기 위해서는 다음처럼 자원에 접근할때 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 이용하여 `lock()` 을 걸어두면 됩니다.

```cpp
class A {
    int m_Val{0};
    
public:
    int GetVal() const {return m_Val;}
    
    void Increase(std::mutex& mutex) {
        for (int i{0}; i < 100; ++i) {
            mutex.lock(); // 값을 읽고 쓰기 전에 다른 쓰레드를 기다리게 합니다.
            m_Val = m_Val + 1;
            mutex.unlock(); // lock을 해제 합니다.
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
};

A a{};
std::mutex mutex; // mutex 개체
std::thread worker1{std::mem_fn(&A::Increase), std::ref(a), std::ref(mutex)};
std::thread worker2{std::mem_fn(&A::Increase), std::ref(a), std::ref(mutex)};

worker1.join(); 
worker2.join(); 

std::cout << "Mutex : " << a.GetVal() << std::endl;
EXPECT_TRUE(a.GetVal() == 200);
```

그림으로 보면 다음과 같습니다. 먼저 접근한 쓰레드가 `lock()` 을 걸어 다른 쓰레드를 대기시키기 때문에 정상적으로 동작합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7a7a5391-877c-4dc4-91a6-dbc13dca0d2d)

# lock_guard - mutex가 1개인 경우 데드락(Dead Lock) 해결

다음은 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex) 개체를 1개 사용했을때 발생할 수 있는 전형적인 데드락(Dead Lock)입니다. `lock`을 하고 실수로 `unlock()`을 빼먹은 경우죠. 

```cpp
class A {
public:
    void Func(std::mutex& mutex) {
        for (int i{0}; i < 100; ++i) {
            mutex.lock(); 

            // Todo

            // mutex.unlock(); // (X) 오동작. 실수로 빼먹으면 다른 쓰레드가 무한 대기합니다.
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
};

A a{};
std::mutex mutex; // mutex 개체

std::thread worker1{std::mem_fn(&A::Func), std::ref(a), std::ref(mutex)};
std::thread worker2{std::mem_fn(&A::Func), std::ref(a), std::ref(mutex)};

worker1.join(); 
worker2.join(); 
```

혹은 `unlock()`을 꼼꼼하게 했더라도, Todo에서 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키면, `unlock`이 호출 되지 않습니다.

```cpp
{
    mutex.lock(); 
    // lock()~unlock() 구간에서 예외를 발생시킬 수 있습니다.
    ...
    throw "error";
    ...
    mutex.unlock(); // (X) 오동작. 예외를 발생시키면 호출되지 않습니다.
}
```

따라서 `new - delete`와 같이 `lock() - unlock()` 도 [스택 풀기](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 의해 자연스럽게 호출되어야 합니다. 스마트 포인터 처럼요. [lock_guard](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#lock_guard---mutex%EA%B0%80-1%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)는 생성시 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 `lock()` 하고 소멸시 `unlock()`해줍니다.

```cpp
{
    std::lock_guard<std::mutex> lock(mutex); // 유효 범위를 벗어나면 unlock을 호출합니다.
    // lock()~unlock() 구간에서 예외를 발생시켜도 안전합니다.
    ...
    throw "error";
    ... 
}
```

# try_lock() - mutex가 여러개인 경우 데드락(Dead Lock) 해결 

다음 코드를 보면, 

1. `worker1` 쓰레드에서 `myMutex`를 `lock()` 한뒤, 
2. `worker2` 쓰레드에서 `yourMutex`를 `lock()` 하면,
3. `worker1` 쓰레드에서 `yourMutex`를 대기하고, `worker2` 쓰레드에서 `myMutex`를 대기하게 됩니다.

서로 대기하는 데드락(Dead Lock)입니다. 

```cpp
class A {
public:
    void Init(std::mutex& myMutex, std::mutex& yourMutex) {
        for (int i{0}; i < 100; ++i) {
            std::lock_guard<std::mutex> lock1{myMutex}; // myMutex 후 yourMutex 를 lock 합니다.
            std::lock_guard<std::mutex> lock2{yourMutex}; 
            // Todo

            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
    void Reset(std::mutex& myMutex, std::mutex& yourMutex) {

        for (int i{0}; i < 100; ++i) {
            std::lock_guard<std::mutex> lock1{yourMutex}; // yourMutex후 myMutex 를 lock 합니다. 
            std::lock_guard<std::mutex> lock2{myMutex}; 
            // Todo
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
};

A a{};
std::mutex myMutex; // mutex 개체
std::mutex yourMutex; 

std::thread worker1{std::mem_fn(&A::Init), std::ref(a), std::ref(myMutex), std::ref(yourMutex)};
std::thread worker2{std::mem_fn(&A::Reset), std::ref(a), std::ref(myMutex), std::ref(yourMutex)};

worker1.join(); 
worker2.join(); 
```

상기의 데드락(Dead Lock)은 동일한 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 사용한다던지, [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 호출 순서를 잘 통제하여 개선할 수도 있으나, 여의치 않은 경우 [try_lock()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)을 이용하여, 데드락(Dead Lock) 상황이 될 것 같으면, 다른 쓰레드에 양보하여 데드락(Dead Lock) 상황을 피할 수 있습니다.

```cpp
class A {
public:
    void Init(std::mutex& myMutex, std::mutex& yourMutex) {
        for (int i{0}; i < 100; ++i) {
            myMutex.lock(); // myMutex 후 yourMutex 를 lock 합니다.
            yourMutex.lock();
            // Todo
            yourMutex.unlock();
            myMutex.unlock();
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
    void Reset(std::mutex& myMutex, std::mutex& yourMutex) {

        for (int i{0}; i < 100; ++i) {
            yourMutex.lock(); // yourMutex 후  myMutex를 lock 합니다.
            if (!myMutex.try_lock()) { // myMutex가 다른곳에서 벌써 사용중이라면, yourMutex lock을 다른 쓰레드가 사용하도록 양보합니다.
                yourMutex.unlock();
                continue;
            }
            // Todo
            myMutex.unlock();
            yourMutex.unlock();
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
};

A a{};
std::mutex myMutex; // mutex 개체
std::mutex yourMutex; 

std::thread worker1{std::mem_fn(&A::Init), std::ref(a), std::ref(myMutex), std::ref(yourMutex)};
std::thread worker2{std::mem_fn(&A::Reset), std::ref(a), std::ref(myMutex), std::ref(yourMutex)};

worker1.join(); 
worker2.join(); 
```

# call_once(), once_flag

주어진 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 여러 쓰레드에서 실행해도 한번만 호출합니다. 

```cpp
class A {
    std::once_flag m_OnceFlag; // call_once() 시에 사용되는 개체입니다. 이 개체에 호출 정보가 담겨 있어 다음번 호출시 비교됩니다.
public:
    void OnceFunc() {
        // this->Func() 함수를 여러 쓰레드에서 호출해도 한번만 호출합니다.
        std::call_once(m_OnceFlag, std::mem_fn(&A::Func), std::ref(*this));
    } 
    void Func() {
        std::cout << "A : Func()" << std::endl;
    }
};

A a;
std::thread worker1{std::mem_fn(&A::OnceFunc), std::ref(a)};
std::thread worker2{std::mem_fn(&A::OnceFunc), std::ref(a)};

worker1.join(); 
worker2.join();

a.OnceFunc(); // 더이상 Func()을 호출하지 않습니다.
```

# (C++14~) shared_timed_mutex 와 shared_lock

[mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)는 공유 자원 접근을 하나의 쓰레드에서만 독점하여 처리할 수 있도록 해주는 동기화 개체입니다만,

C++14에서는 [shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)를 이용하여 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)의 소유권을 쓰레드끼리 공유할 수 있습니다. 

공유 자원을 단순히 읽거나, 동일한 값으로 세팅한다면, 하나의 쓰레드에서 독점할 필요가 없으므로  [shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)를 사용하는게 효율적입니다.

다음 예에서 `UniqueWrite()`와 `UniqueFunc()` 은 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 이용하여 자원을 독점하며, `SharedWrite()`와 `SharedFunc_14()` 는 [shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)를 이용하여 자원을 공유하고 있습니다.

```cpp
void UniqueWrite(std::vector<int>::iterator itr, std::vector<int>::iterator endItr, std::mutex& mutex) {
    for(int i = 0; itr != endItr; ++itr, ++i) {
        std::unique_lock<std::mutex> lock(mutex); // mutex를 독점합니다.
        *itr = 1;
        std::this_thread::sleep_for(std::chrono::milliseconds{1}); 
        lock.unlock();
    }
}
void UniqueFunc(std::vector<int>::iterator itr, std::vector<int>::iterator endItr) {


    std::mutex mutex; // mutex 개체
    std::thread worker1{UniqueWrite, itr, endItr, std::ref(mutex)};
    std::thread worker2{UniqueWrite, itr, endItr, std::ref(mutex)};
    std::thread worker3{UniqueWrite, itr, endItr, std::ref(mutex)};
    std::thread worker4{UniqueWrite, itr, endItr, std::ref(mutex)};
    worker1.join(); 
    worker2.join(); 
    worker3.join();
    worker4.join();
}
void SharedWrite_14(std::vector<int>::iterator itr, std::vector<int>::iterator endItr, std::shared_timed_mutex& mutex) {
    for(int i = 0; itr != endItr; ++itr, i++) {
        std::shared_lock<std::shared_timed_mutex> lock(mutex); // mutex를 공유합니다.
        *itr = 1;
        std::this_thread::sleep_for(std::chrono::milliseconds{1}); 
        lock.unlock();
    }
}
void SharedFunc_14(std::vector<int>::iterator itr, std::vector<int>::iterator endItr) {

    std::shared_timed_mutex mutex; // mutex 개체
    std::thread worker1{SharedWrite_14, itr, endItr, std::ref(mutex)};
    std::thread worker2{SharedWrite_14, itr, endItr, std::ref(mutex)};
    std::thread worker3{SharedWrite_14, itr, endItr, std::ref(mutex)};
    std::thread worker4{SharedWrite_14, itr, endItr, std::ref(mutex)};
    worker1.join(); 
    worker2.join(); 
    worker3.join();
    worker4.join();
}  
template<typename Func, typename... Params>
std::chrono::microseconds Measure(Func func, Params&&... params) {
    std::chrono::system_clock::time_point start{std::chrono::system_clock::now()};    

    func(std::forward<Params>(params)...);

    std::chrono::system_clock::time_point end{std::chrono::system_clock::now()};
    std::chrono::microseconds val{std::chrono::duration_cast<std::chrono::microseconds>(end - start)};

    return val;
}
std::vector<int> v;

for (int i{0}; i < 100; ++i) {
    v.push_back(i);
}

std::chrono::microseconds uniqueDuration{Measure(
    UniqueFunc, 
    v.begin(), v.end()
)};
std::cout << "UniqueFunc : " << uniqueDuration.count() << std::endl; 
for (int i = 0; i < 100; ++i) {
    EXPECT_TRUE(v[i] == 1);
    v[i] = i; // 다시 값을 초기화 해둡니다.
}

std::chrono::microseconds sharedDuration{Measure(
    SharedFunc_14, 
    v.begin(), v.end() 
)};
std::cout << "SharedFunc : " << sharedDuration.count() << std::endl;
for (int i = 0; i < 100; ++i) {
    EXPECT_TRUE(v[i] == 1);
} 
```

실행시켜보면 [shared_timed_mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c14-shared_timed_mutex-%EC%99%80-shared_lock)가 훨씬 빠른 것을 알 수 있습니다.

```cpp
UniqueFunc : 6360136
SharedFunc : 1559724 //shared_timed_mutex 가 훨씬 빠릅니다.
```

# (C++17~) scoped_lock

[try_lock() - mutex가 여러개인 경우 데드락(Dead Lock) 해결](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#try_lock---mutex%EA%B0%80-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%B8-%EA%B2%BD%EC%9A%B0-%EB%8D%B0%EB%93%9C%EB%9D%BDdead-lock-%ED%95%B4%EA%B2%B0)에서 다수의 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)가 서로 대기하는 데드락(Dead Lock) 상황을 예시하였는데요, C++17 부터는 [scoped_lock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#c17-scoped_lock)이 추가되었습니다. 다수의 [mutex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#mutex)를 사용하더라도 데드락(Dead Lock)을 방지할 수 있게 해줍니다.

```cpp
class A {
public:
    void Init(std::mutex& myMutex, std::mutex& yourMutex) {
        for (int i{0}; i < 100; ++i) {
            std::scoped_lock{myMutex, yourMutex}; // 내부적으로 데드락을 방지합니다. 
            // Todo
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
    void Reset(std::mutex& myMutex, std::mutex& yourMutex) {

        for (int i{0}; i < 100; ++i) {
            std::scoped_lock{myMutex, yourMutex}; // 내부적으로 데드락을 방지합니다. 
            // Todo
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }
};

A a{};
std::mutex myMutex; // mutex 개체
std::mutex yourMutex; 

std::thread worker1{std::mem_fn(&A::Init), std::ref(a), std::ref(myMutex), std::ref(yourMutex)};
std::thread worker2{std::mem_fn(&A::Reset), std::ref(a), std::ref(myMutex), std::ref(yourMutex)};

worker1.join(); 
worker2.join(); 
```