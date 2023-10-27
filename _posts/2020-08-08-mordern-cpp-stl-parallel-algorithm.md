---
layout: single
title: "#38. [모던 C++ STL] (C++17~) 병렬 알고리즘과 실행 정책(동작 확인중)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [대부분의 알고리즘에서 병렬 작업을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/)하는 오버로딩 버전이 추가되었고, [seq, par, par_unseq, unseq](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/#%EC%8B%A4%ED%96%89-%EC%A0%95%EC%B1%85)으로 병렬 실행 정책을 지정할 수 있습니다.

# 개요

[대부분의 알고리즘에서 병렬 작업을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/)하는 오버로딩 버전이 추가되었고, [seq, par, par_unseq, unseq](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/#%EC%8B%A4%ED%96%89-%EC%A0%95%EC%B1%85)으로 병렬 실행 정책을 지정할 수 있습니다. 단, 여러 쓰레드를 통한 병렬 작업을 반드시 수행하지는 않고, 병렬 작업이 가능하도록 허용합니다.

*(아쉽게도 제 환경에서는 단일 쓰레드로 동작하네요. MinGW-w64(GCC 12.3.0)의 문제일지 다른 문제일지는 좀더 확인해 봐야 할 듯합니다.)*

> **확인 필요사항**
> * GCC 옵션 : [gcc.gnu.org](https://gcc.gnu.org/onlinedocs/libstdc++/manual/parallel_mode_using.html) 에서 `-fopenmp`, `-march=native`, `D_GLIBCXX_PARALLEL`에 대한 언급이 있는데, 이게 C++17 병렬 알고리즘을 활성화 하는 옵션인지는 잘 모르겠습니다. 어쨌던, 사용해도 단일 쓰레드로 동작합니다.
> * STL 구현 : STL 실제 구현이 안되어 있을 수도 있습니다. GCC에서 병렬 알고리즘 함수의 세부 구현 목록을 찾아봐야 합니다.(샘플로 `for_each()`, [reduce()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85), `sort()`를 해봤습니다만 모두 단일 쓰레드로 동작합니다.)
> * 다른 STL 사용 : [https://cukic.co/2018/03/29/cxx17-and-parallel-algorithms-in-stl/](https://cukic.co/2018/03/29/cxx17-and-parallel-algorithms-in-stl/)에서 Intel과 HP STL 사용 예가 있습니다. 

![image](https://www.modernescpp.com/wp-content/uploads/2021/07/allAlgorithm-1024x477.png)
*[https://www.modernescpp.com/index.php/parallel-algorithms-of-the-stl-with-gcc/](https://www.modernescpp.com/index.php/parallel-algorithms-of-the-stl-with-gcc/) 참고*


# 실행 정책

실행 정책은 알고리즘 실행시의 어떻게 병렬화할지를 지정합니다.

`<execution>` 헤더 파일을 포함해야 하며, `std::execution` 네임스페이스를 사용합니다.

실행 정책을 지원하는 알고리즘 함수는 다음과 같이 실행 정책을 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받습니다.

```cpp
// 실행 정책을 인자로 받습니다.
template<typename ExecutionPolicy, typename ForwardIt, typename T>
ForwardIt find(
    ExecutionPolicy&& policy,
    ForwardIt first, ForwardIt last, 
    const T& value
);
```

|항목|인스턴스|내용|
|--|--|--|
|`sequenced_policy` (C++17~)|`seq`|단일 쓰레드에서 순차적으로 실행합니다.|
|`parallel_policy` (C++17~)|`par`|여러 쓰레드에서 병렬로 실행합니다. 각 요소는 순차적으로 실행합니다.|
|`parallel_unsequenced_policy` (C++17~)|`par_unseq`|여러 쓰레드에서 병렬로 실행합니다. 각 요소가 비 순차적으로 실행되며, 메모리 할당/해제, 뮤텍스 획득 등을 사용할 수 없습니다.|
|`unsequenced_policy` (C++20~)|`unseq`|(작성중)|

또한, `is_execution_policy`를 이용하여 주어진 클래스가 실행 정책을 나타내는지 확인합니다.

다음 예는 GCC 12.3.0에서 일반 `Sum()`함수와 병렬 처리를 하는 `SumPararell()` 함수간의 속도 비교입니다.

```cpp
template<typename Func, typename... Params>
std::chrono::microseconds Measure(Func func, Params... params) {
    std::chrono::system_clock::time_point start{std::chrono::system_clock::now()};    

    func(params...);

    std::chrono::system_clock::time_point end{std::chrono::system_clock::now()};
    std::chrono::microseconds val{std::chrono::duration_cast<std::chrono::microseconds>(end - start)};

    return val;
}


void Sum() {
    std::vector<int> v(100, 1); // 100 개를 생성하고 1로 초기화 합니다.

    int total{0};

    std::for_each(
        v.begin(), v.end(), 
        [&](int item) {
            total += item; // 각 요소의 값을 누적합니다.
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    );

    EXPECT_TRUE(total == 100);       
}

void SumPararell() { 
    std::vector<int> v(100, 1); // 100 개를 생성하고 1로 초기화 합니다.

    int total{0};
    std::mutex mutex;

    std::for_each(
        std::execution::par, 
        v.begin(), v.end(), 
        [&](int item) {
            {
                std::lock_guard<std::mutex> lock(mutex); // total에 접근할 때만 lock 합니다.
                total += item;
            }
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    );

    EXPECT_TRUE(total == 100);       
}

// 일반 알고리즘
std::chrono::microseconds duration1{Measure(
    Sum
)};
std::cout << "Sum Duration : " << duration1.count() << std::endl; 


// parallel_policy
std::chrono::microseconds duration3{Measure(
    SumPararell
)};
std::cout << "SumPararell Duration : " << duration3.count() << std::endl; 
```

실행 결과를 보면 병렬버전이 좀더 빨라야 하는데 그렇지 않습니다. 확인해 보니 단일 쓰레드로 동작하고 있네요. 컴파일러 탓일지, 코드가 잘못된 것인지는 좀더 확인해봐야 겠습니다.

```cpp
Sum Duration : 1555822
SumPararell Duration : 1604355
```










