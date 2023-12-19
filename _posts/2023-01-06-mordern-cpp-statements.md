---
layout: single
title: "#6. [모던 C++] (C++11~) 범위 기반 for(), (C++17~) 초기식을 포함하는 if(), switch(), (C++20~) 범위 기반 for()에서 초기식"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~)  [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)가 추가되어 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 요소의 탐색 처리가 쉬워졌습니다.
> * (C++17~) [초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch)가 추가되어 함수 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 평가하고 소멸하는 코드가 단순해 졌습니다.
> * (C++20~) [범위 기반 for()에서 초기식](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c20-%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for%EC%97%90%EC%84%9C-%EC%B4%88%EA%B8%B0%EC%8B%9D)이 추가되었습니다.

# 범위 기반 for()

기존의 `for()`는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)와 함께 사용할때 다음과 같이 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 이용하여 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 요소를 탐색해야 했는데요(*[제어문](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-statements/) 과 [이터레이터를 이용한 요소 접근](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9A%94%EC%86%8C-%EC%A0%91%EA%B7%BC) 참고*),

```cpp
std::vector<int> v_11{1, 2, 3};

int sum{0};

// 컨테이너 요소를 탐색 합니다.
std::vector<int>::iterator itr{v_11.begin()};
std::vector<int>::iterator endItr{v_11.end()};
for (;itr != endItr; ++itr) {
    sum += *itr;
}
EXPECT_TRUE(sum == 1 + 2 + 3);
```

C++11 부터는  [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)를 이용하여 좀더 간단하게 구현할 수 있습니다.

```cpp
std::vector<int> v_11{1, 2, 3};

int sum{0};

for (int val : v_11) { // 혹은 for (auto val : v_11)
    sum += val;
}

EXPECT_TRUE(sum == 1 + 2 + 3);
```

또한 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 타입을 이용하면 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 요소의 값을 쉽게 변경할 수 있습니다.

```cpp
std::vector<int> v_11{1, 2, 3};

for (int& val : v_11) { // 혹은 for (auto& val : v_11)
    ++val; // 벡터의 요소가 수정됩니다.
}

EXPECT_TRUE(v_11[0] == 2 && v_11[1] == 3 && v_11[2] == 4);
```

> *(C++20~) [범위 기반 for()에서 초기식](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c20-%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for%EC%97%90%EC%84%9C-%EC%B4%88%EA%B8%B0%EC%8B%9D)이 추가되었습니다.*

# (C++17~) 초기식을 포함하는 if(), switch()

기존의 `if()`나 `switch()` 는 조건식 만을 평가했습니다. 따라서, 특정 함수의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 확인하는 경우에는 다음과 같이 표현했는데요,

```cpp
int* result{Func()}; // 결과값을 구한 후
if (result != nullptr) { // 조건 평가
    // Todo
}
```

[리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 만약 스마트 포인터(*[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)라면 [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)를 짧게하기 위해 다음과 같이 중괄호를 이용했었습니다.

```cpp
{ // 유효 범위를 짧게 하기 위해 중괄호를 사용했습니다. 
    // result는 중괄호가 끝나면 소멸됩니다.
    std::shared_ptr<int> result{Func()};
    if (result != nullptr) {
        // Todo
    }
} // 여기서 result가 소멸됩니다.
```

C++17 부터는 `if()` 와 `switch()`에서 초기식을 실행후 조건식을 평가할 수 있어, 다음처럼 코드를 간결하게 작성할 수 있습니다. 초기식에 작성된 변수의 [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)는 `if()` 나 `switch()`의 끝입니다.

```cpp
// result_17은 if 문 이 끝나면 소멸됩니다.
if (std::shared_ptr<int> result_17{Func()}; result_17 != nullptr) {
    // Todo
}
```

# (C++20~) 범위 기반 for()에서 초기식

기존의 [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)에서는 초기식을 제공하지 않았는데요(*[범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for) 참고*),

C++20 부터는 [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)에서 초기식을 사용할 수 있습니다.

```cpp
int sum{0};

// 초기식을 이용하여 v_20 값을 초기화 합니다.
for (std::vector<int> v_20{1, 2, 3}; int val : v_20) { 
    sum += val;
}

EXPECT_TRUE(sum == 1 + 2 + 3);
```

