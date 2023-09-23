---
layout: single
title: "#15. [모던 C++ STL] (C++11~) 이터레이터"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `move_iterator` 아답터는 이터레이터가 가리키는 요소를 이동 연산 합니다.

# 이터레이터 카테고리

contiguous_iterator_tag (C++20~)

# 이터레이터 아답터

|항목|내용|
|--|--|
|`move_iterator` (C++11~)|이터레이터가 가리키는 요소를 이동 연산 합니다.|
|`make_move_iterator()` (C++11~)|`move_iterator`를 생성합니다.|
|`make_reverse_iterator()` (C++14~)|(작성중)|
|`basic_const_iterator` (C++23~)|(작성중)|
|`const_iterator` (C++23~)|(작성중)|
|`const_sentinel` (C++23~)|(작성중)|
|`make_const_iterator()` (C++23~)|(작성중)|
|`move_sentinel` (C++23~)|(작성중)|
|`common_iterator` (C++20~)|(작성중)|
|`default_sentinel_t` (C++20~)|(작성중)|
|`counted_iterator` (C++20~)|(작성중)|
|`unreachable_sentinel_t` (C++20~)|(작성중)|

```cpp
std::vector<std::string> v{"a", "b", "c"};

std::vector<std::string> v1{v.begin(), v.end()};
EXPECT_TRUE(v[0] == "a" && v[1] == "b" && v[2] == "c"); // 요소가 복사됩니다.

std::vector<std::string> v2{std::make_move_iterator(v.begin()), std::make_move_iterator(v.end())};
EXPECT_TRUE(v[0].empty() && v[1].empty() && v[2].empty()); // 요소가 이동되어 비었습니다.
```
  
# 이터레이터 유틸리티

|항목|내용|
|--|--|
|`next()` (C++11~)|뒤로 이동된 이터레이터를 리턴합니다.|
|`prev()` (C++11~)|앞으로 이동된 이터레이터를 리턴합니다.|

```cpp
std::vector<int> v{1, 2, 3, 4};
auto itr{v.begin()};
std::advance(itr, 2); // 인자로 전달한 이터레이터를 뒤로 2번 이동시킴
EXPECT_TRUE(*itr == 3);

itr = v.begin();
EXPECT_TRUE(*std::next(itr, 2) == 3 && *itr == 1); // 뒤로 2번 이동. itr은 수정되지 않음
EXPECT_TRUE(*std::prev(itr, -2) == 3 && *itr == 1); // 앞으로 -2번 이동. itr은 수정되지 않음
```

# 범위 접근

|항목|내용|
|--|--|
|`begin() , end()` (C++11~)|순방향 이터레이터를 리턴합니다.|
|`rbegin(), rend()` (C++14~)|역방향 이터레이터를 리턴합니다.|
|`cbegin(), cend()` (C++14~)|순방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin(), crend()` (C++14~)|역방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`size()` (C++17~)<br/>`ssize()` (C++20~)|(작성중)|
|`empty()` (C++17~)|(작성중)|
|`data()` (C++17~)|(작성중)|

# 이터레이터 사용자 정의

|항목|내용|
|--|--|
|`iter_move()` (C++20~)|(작성중)|
|`iter_swap()` (C++20~)|(작성중)|


# 이터레이터 컨셉 (C++20~)

indirectly_readable 등

# 이터레이터 연관 타입 (C++20~)

# 알고리즘 컨셉 및 유틸리티 (C++20~)

https://en.cppreference.com/w/cpp/iterator