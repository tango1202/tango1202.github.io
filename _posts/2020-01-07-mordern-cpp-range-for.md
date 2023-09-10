---
layout: single
title: "#7. [모던 C++] (C++11~) 범위 기반 for"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 범위 기반 `for()`가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.

# 개요

기존 `for()`는 컨테이너와 함께 사용할때 다음과 같이 이터레이터를 이용하여 컨테이너 요소를 탐색해야 합니다.

```cpp
std::vector<int> v{1, 2, 3};

int sum = 0;
// 컨테이너 요소를 탐색 합니다.
auto itr = v.begin();
auto endItr = v.end();
for (;itr != endItr; ++itr) {
    sum += *itr;
}
EXPECT_TRUE(sum == 1 + 2 + 3);
```

C++11에서는 범위 기반 `for()`를 이용하여 좀더 간단하게 구현할 수 있습니다.

```cpp
std::vector<int> v{1, 2, 3};

int sum = 0;

// for (auto val : v) { // val == int
// for (const int& val : v) {  // val == const int&
for (int val : v) {
    sum += val;
}

EXPECT_TRUE(sum == 1 + 2 + 3);
```

또한 참조자 타입을 이용하면 컨테이너 요소의 값을 쉽게 변경할 수 있습니다.

```cpp
std::vector<int> v{1, 2, 3};

for (int& val : v) {
    ++val; // 벡터의 요소가 수정됩니다.
}

EXPECT_TRUE(v[0] == 2 && v[1] == 3 && v[2] == 4);
```


