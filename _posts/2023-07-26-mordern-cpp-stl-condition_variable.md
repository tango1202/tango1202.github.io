---
layout: single
title: "#26. [모던 C++ STL] (C++11~) 조건 변수(condition_variable)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable)이 추가되어 쓰레드간 동기화를 위해 쓰레드를 `wait()`시킨뒤, 특정 조건이 되었을때 활성화 시킬 수 있습니다.

# 쓰레드 동기화

쓰레드를 동시에 실행시키는 것도 필요하지만, 상황에 따라서 쓰레드를 순차적으로 실행하는 [쓰레드 동기화](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable/#%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)를 하는 것도 필요합니다.

예를 들어 

1. 인터넷에서 파일을 다운로드 받는 쓰레드가 종료되었을때
2. UI 쓰레드에서 다운로드 완료 버튼이 나와야 합니다.

STL 에서는 [쓰레드 동기화](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable/#%EC%93%B0%EB%A0%88%EB%93%9C-%EB%8F%99%EA%B8%B0%ED%99%94)를 위해 [condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable/#condition_variable)를 사용할 수 있습니다.

|항목|내용|
|--|--|
|[condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable/#condition_variable) (C++11~)|쓰레드간 동기화를 위해 쓰레드를 `wait()`시킨뒤, 특정 조건이 되었을때 활성화 시켜 주는 개체입니다.|
|`condition_variable_any` (C++11~)|(작성중)|
|`notify_all_at_thread_exit()` (C++11~)|(작성중)|
|`cv_status` (C++11~)|`no_timeout`, `timeout`이 정의된 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)입니다.|

# condition_variable

쓰레드가 종료된 시점을 알기 위해 매 시간마다 쓰레드의 상태를 검사하는 방법은 CPU 자원을 많이 사용하기 때문에 좋지 않습니다.

멀티 쓰레드 프로그램에서는 이런 경우 생산자(Producer)-소비자(Consumer) 패턴을 많이 이용하는데요,

이때 

1. 생산자(Producer)는 각 작업을 생성 및 관리하고,
2. 소비자(Consumer)는 각 작업을 수행합니다.
3. 소비자는 작업을 대기(Wait)하고 있다가, 생산자가 작업하라 알려주면(Notify), 그제서야 작업을 시작합니다. 

즉, 생산자의 데이터 생성이 완료되면, 소비자가 데이터를 사용해야 하므로, 생산자와 소비자의 작업은 동기화되어야 합니다.

[condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable)은 쓰레드간 동기화를 위해 쓰레드를 `wait()`시킨뒤, 특정 조건이 되었을때 `notify_one()` 이나 `notify_all()`로 활성화 시켜 주는 개체입니다. 

[condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable)의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 다음과 같습니다. 

|항목|내용|
|--|--|
|`notify_one()` (C++11~)|대기중인 한개의 쓰레드에게 알립니다.|
|`notify_all()` (C++11~)|대기중인 모든 쓰레드에게 알립니다.|
|`wait()` (C++11~)|Notify가 왔을때 주어진 [조건자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)가 참이면 대기를 해제합니다.|
|`wait_for()` (C++11~)|Notify가 왔을때 주어진 [조건자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)가 참이거나 주어진 기간이 지나면 대기를 해제합니다.|
|`wait_until()` (C++11~)|Notify가 왔을때 주어진 [조건자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)가 참이거나 주어진 시간이 되면 대기를 해제합니다.|
|`native_handle()` (C++11~)|시스템에 따른 구현에 정의된 핸들을 리턴합니다.|

예를 들어 다음의 `wait()`는 

1. 누군가가 `m_IsRun1`을 `true`로 설정하고, 
2. `m_CV.notify_one()` 또는 `m_CV.notify_all()`을 호출할때 까지 대기합니다.

```cpp
std::condition_variable m_CV;

std::unique_lock<std::mutex> lock{mutex};
m_CV.wait(lock, [&]() -> bool {return m_IsRun1;}); 
```

다음은 [condition_variable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-condition_variable) 사용 예입니다.

`Build()` -> `Part1()` -> `Build()` -> `Part2()` -> `Build()` -> `Part1()` -> `Build()` 의 순서로 동기화 하여 실행하고 있습니다.

```cpp
class A {
    std::string m_Str;
    std::condition_variable m_CV;
    bool m_IsRunBuild{false}; // true면 Build가 wait를 멈춥니다.
    bool m_IsRun1{false}; // true면 Part1이 wait를 멈춥니다.
    bool m_IsRun2{false}; // true면 Part2가 wait를 멈춥니다.
public:
    const std::string& GetStr() const {return m_Str;}

    void Part1(std::mutex& mutex) {

        std::unique_lock<std::mutex> lock{mutex};
        m_CV.wait(lock, [&]() -> bool {return m_IsRun1;}); // 누군가가 m_IsRun1 == true로 하고 m_CV에서 notify할때까지 대기

        std::cout << "#2. Part1()" << std::endl;

        m_Str += "Hello";

        m_IsRunBuild = true; // Build를 깨움
        m_IsRun1 = false;
        lock.unlock();
        m_CV.notify_all(); 

        // 아직 할일이 더 있어 wait
        lock.lock();
        m_CV.wait(lock, [&]() -> bool {return m_IsRun1;}); // 누군가가 m_IsRun1 == true로 하고 m_CV에서 notify할때까지 대기
        
        std::cout << "#6. Part1()" << std::endl;
        m_Str += "!!";

        m_IsRunBuild = true; // Build를 깨움
        m_IsRun1 = false;
        lock.unlock();
        m_CV.notify_all(); 
    }
    void Part2(std::mutex& mutex) {
        std::unique_lock<std::mutex> lock{mutex};
        m_CV.wait(lock, [&]() -> bool {return m_IsRun2;});// 누군가가 m_IsRun2 == true로 하고 m_CV에서 notify할때까지 대기

        std::cout << "#4. Part2()" << std::endl;

        m_Str += "World";

        m_IsRunBuild = true; // Build를 깨움
        m_IsRun2 = false;
        lock.unlock();
        m_CV.notify_all(); 
    } 

    void Build() {
        std::mutex mutex; // mutex 개체

        std::thread worker1{std::mem_fn(&A::Part1), std::ref(*this), std::ref(mutex)};
        std::thread worker2{std::mem_fn(&A::Part2), std::ref(*this), std::ref(mutex)};

        std::cout << "#1. Build Start" << std::endl;
        m_IsRun1 = true; // Part1을 깨움
        m_CV.notify_all(); 

        std::unique_lock<std::mutex> lock{mutex};
        m_CV.wait(lock, [&]() -> bool {return m_IsRunBuild;});

        std::cout << "#3. Build Wake Up" << std::endl;
        m_IsRunBuild = false;
        m_IsRun1 = false;
        m_IsRun2 = true; // Part2를 깨움
        lock.unlock();
        m_CV.notify_all(); 

        lock.lock();
        m_CV.wait(lock, [&]() -> bool {return m_IsRunBuild;});

        std::cout << "#5. Build Wake Up" << std::endl;
        m_IsRunBuild = false;
        m_IsRun1 = true; // Part1를 깨움
        m_IsRun2 = false; 
        lock.unlock();
        m_CV.notify_all(); 

        lock.lock();
        m_CV.wait(lock, [&]() -> bool {return m_IsRunBuild;});

        std::cout << "#7. Build Completed" << std::endl;

        worker1.join(); 
        worker2.join();
    }           
};

A a{};
a.Build();
std::cout << "Make String : " << a.GetStr() << std::endl; // HelloWorld!!를 출력함
```

실행 결과는 다음과 같습니다.

```cpp
#1. Build Start
#2. Part1()
#3. Build Wake Up
#4. Part2()
#5. Build Wake Up
#6. Part1()
#7. Build Completed
Make String : HelloWorld!!
```