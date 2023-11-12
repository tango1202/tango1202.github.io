---
layout: single
title: "#13. [모던 C++] (C++11~) 범위 기반 for(), (C++17~) 초기식을 포함하는 if(), switch()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~)  [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.
> * (C++17~) [초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch)가 추가되어 함수 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 평가하고 소멸하는 코드가 단순해 졌습니다.

# 범위 기반 for()

기존의 `for()`는 컨테이너와 함께 사용할때 다음과 같이 이터레이터를 이용하여 컨테이너 요소를 탐색해야 했는데요([제어문](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-statements/) 과 [이터레이터를 이용한 요소 접근](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9A%94%EC%86%8C-%EC%A0%91%EA%B7%BC) 참고),

```cpp
std::vector<int> v_11{1, 2, 3};

int sum{0};

// 컨테이너 요소를 탐색 합니다.
auto itr_11{v_11.begin()};
auto endItr_11{v_11.end()};
for (;itr_11 != endItr_11; ++itr_11) {
    sum += *itr_11;
}
EXPECT_TRUE(sum == 1 + 2 + 3);
```

C++11 부터는  [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)를 이용하여 좀더 간단하게 구현할 수 있습니다.

```cpp
std::vector<int> v_11{1, 2, 3};

int sum{0};

for (int val_11 : v_11) { // 혹은 for (auto val_11 : v_11)
    sum += val_11;
}

EXPECT_TRUE(sum == 1 + 2 + 3);
```

또한 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 타입을 이용하면 컨테이너 요소의 값을 쉽게 변경할 수 있습니다.

```cpp
std::vector<int> v_11{1, 2, 3};

for (int& val_11 : v_11) { // 혹은 for (auto& val : v_11)
    ++val_11; // 벡터의 요소가 수정됩니다.
}

EXPECT_TRUE(v_11[0] == 2 && v_11[1] == 3 && v_11[2] == 4);
```

# (C++17~) 초기식을 포함하는 if(), switch()

기존의 `if()`나 `switch()` 는 조건식 만을 평가했습니다. 따라서, 특정 함수의 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 확인하는 경우에는 다음과 같이 표현했는데요,

```cpp
int* result{Func()}; // 결과값을 구한 후
if (result != nullptr) { // 조건 평가
    // Todo
}
```

[리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 만약 스마트 포인터라면 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 짧게하기 위해 다음과 같이 중괄호를 이용했었습니다.

```cpp
{ 
    // result는 중괄호가 끝나면 소멸됩니다.
    std::shared_ptr<int> result{Func()};
    if (result != nullptr) {
        // Todo
    }
}
```

C++17 부터는 `if()` 와 `switch()`에서 초기식을 실행후 조건식을 평가할 수 있어, 다음처럼 코드를 간결하게 작성할 수 있습니다. 초기식에 작성된 변수의 유효 범위는 `if()` 나 `switch()`의 끝입니다.

```cpp
// result는 if 문 이 끝나면 소멸됩니다.
if (std::shared_ptr<int> result_17{Func()}; result_17 != nullptr) {
    // Todo
}
```

