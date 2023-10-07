---
layout: single
title: "#9. [모던 C++] (C++11~) 범위 기반 for(), (C++17~) 초기식을 포함하는 if(), switch()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 범위 기반 `for()`가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.
> * (C++17~) 초기식을 포함하는 `if()`, `switch()` 가 추가되어 함수 리턴값을 평가하고 소멸하는 코드가 단순해 졌습니다.

# 범위 기반 for()

기존에는 `for()`는 컨테이너와 함께 사용할때 다음과 같이 이터레이터를 이용하여 컨테이너 요소를 탐색해야 했는데요([제어문](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-statements/) 과 [이터레이터를 이용한 요소 접근](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9A%94%EC%86%8C-%EC%A0%91%EA%B7%BC) 참고),

```cpp
std::vector<int> v{1, 2, 3};

int sum{0};
// 컨테이너 요소를 탐색 합니다.
auto itr{v.begin()};
auto endItr{v.end()};
for (;itr != endItr; ++itr) {
    sum += *itr;
}
EXPECT_TRUE(sum == 1 + 2 + 3);
```

C++11 부터는 범위 기반 `for()`를 이용하여 좀더 간단하게 구현할 수 있습니다.

```cpp
std::vector<int> v{1, 2, 3};

int sum{0};

// for (auto val : v) { // val == int
// for (const int& val : v) {  // val == const int&
for (int val : v) { // 혹은 for (auto val : v)
    sum += val;
}

EXPECT_TRUE(sum == 1 + 2 + 3);
```

또한 참조자 타입을 이용하면 컨테이너 요소의 값을 쉽게 변경할 수 있습니다.

```cpp
std::vector<int> v{1, 2, 3};

for (int& val : v) { // 혹은 for (auto& val : v)
    ++val; // 벡터의 요소가 수정됩니다.
}

EXPECT_TRUE(v[0] == 2 && v[1] == 3 && v[2] == 4);
```

# (C++17~) 초기식을 포함하는 if(), switch()

기존에는 `if()`나 `switch()` 는 조건식 만을 평가했습니다. 따라서, 특정 함수의 리턴값을 확인하는 경우에는 다음과 같이 표현했는데요,

```cpp
int* result{Func()}; // 결과값을 구한 후
if (result != nullptr) { // 조건 평가
    // Todo
}
```

만일 리턴값이 스마트 포인터라면 유효 범위를 짧게하기 위해 다음과 같이 중괄호를 이용했었습니다.

```cpp
{ // result는 중괄호가 끝나면 소멸됩니다.
    std::shared_ptr<int> result{Func()};
    if (result != nullptr) {
        // Todo
    }
}
```

C++17 부터는 `if()` 와 `switch()`에서 초기식을 실행후 조건문을 평가할 수 있어, 다음처럼 코드를 간결하게 작성할 수 있습니다. 초기식에 작성된 변수의 유효 범위는 `if()` 나 `switch()`의 끝입니다.

```cpp
// result는 if 문 이 끝나면 소멸됩니다.
if (std::shared_ptr<int> result{Func()}; result != nullptr) {
    // Todo
}
```
