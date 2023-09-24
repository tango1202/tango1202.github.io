---
layout: single
title: "#3. [모던 C++ STL] 이터레이터"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `move_iterator` 아답터는 이터레이터가 가리키는 요소를 이동 연산 합니다.

# 이터레이터 기본 요소

|항목|내용|
|--|--|
|`iterator` (~C++17)|(작성중)|
|`iterator_traits`|(작성중)|
|`input_iterator_tag`<br/>`output_iterator_tag`<br/>`forward_iterator_tag`<br/>`bidirectional_iterator_tag`<br/>`random_access_iterator_tag`<br/>`input_iterator_tag`<br/>`contiguous_iterator_tag` (C++20~)|(작성중)|

# 이터레이터 아답터

역방향 이터레이터나 삽입 이터레이터와 같이 이터레이터의 고유 기능인 `*``, `->``, `++`을 재구현하여 다르게 동작하는 이터레이터들입니다.

|항목|내용|
|--|--|
|`reverse_iterator`|역방향으로 이터레이트 합니다.([역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0) 참고)|
|`make_reverse_iterator()` (C++14~)|(작성중)|
|`back_insert_iterator`|컨테이너 끝에 삽입합니다.([삽입 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%82%BD%EC%9E%85-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0) 참고)|
|`back_inserter()`|`back_insert_iterator`를 생성합니다.|
|`front_insert_iterator`|컨테이너 앞에 삽입합니다.|
|`front_inserter()`|`front_insert_iterator`를 생성합니다.|
|`insert_iterator`|컨테이너에 삽입합니다.|
|`inserter()`|`insert_iterator`를 생성합니다.|
|`move_iterator` (C++11~)|이터레이터가 가리키는 요소를 이동 연산 합니다.|
|`make_move_iterator()` (C++11~)|`move_iterator`를 생성합니다.|
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
# 스트림 이터레이터

|항목|내용|
|--|--|
|`istream_iterator`|`basic_istream`에서 사용하는 입력용 이터레이터|
|`ostream_iterator`|`basic_ostream`에서 사용하는 출력용 이터레이터|
|`istreambuf_iterator`|`basic_streambuf`에서 사용하는 입력용 이터레이터|
|`ostreambuf_iterator`|`basic_streambuf`에서 사용하는 출력용 이터레이터|
  
# 이터레이터 유틸리티

|항목|내용|
|--|--|
|`advance()`|이터레이터를 이동시킵니다.|
|`distance()`|이터레이터간 거리를 구합니다.|
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

# 이터레이터 컨셉 

|항목|내용|
|--|--|
|`indirectly_readable` (C++20~)|(작성중)|
|`indirectly_writable` (C++20~)|(작성중)|
|`weakly_incrementable` (C++20~)|(작성중)|
|`incrementable` (C++20~)|(작성중)|
|`input_or_output_iterator` (C++20~)|(작성중)|
|`sentinel_for` (C++20~)|(작성중)|  
|`sized_sentinel_for` (C++20~)|(작성중)|  
|`input_iterator` (C++20~)|(작성중)|  
|`output_iterator` (C++20~)|(작성중)| 
|`forward_iterator` (C++20~)|(작성중)| 
|`bidirectional_iterator` (C++20~)|(작성중)| 
|`random_access_iterator` (C++20~)|(작성중)| 
|`contiguous_iterator` (C++20~)|(작성중)|  

# 이터레이터 연관 타입

|항목|내용|
|--|--|
|`incrementable_traits` (C++20~)|(작성중)|
|`iter_value_t` (C++20~)<br/>`iter_reference_t` (C++20~)<br/>`iter_const_reference_t` (C++23~)<br/>`iter_difference_t` (C++20~)<br/>`iter_rvalue_reference_t` (C++20~)<br/>`iter_common_reference_t` (C++20~)<br/>|(작성중)|

 
# 알고리즘 컨셉 및 유틸리티

|항목|내용|
|--|--|
|`indirectly_unary_invocable` (C++20~)<br/>`indirectly_regular_unary_invocable` (C++20~)|(작성중)|
|`indirect_unary_predicate` (C++20~)|(작성중)|
|`indirect_binary_predicate` (C++20~)|(작성중)|
|`indirect_equivalence_relation` (C++20~)|(작성중)| 
|`indirect_strict_weak_order` (C++20~)|(작성중)|
|`indirectly_movable` (C++20~)|(작성중)|
|`indirectly_movable_storable` (C++20~)|(작성중)|
|`indirectly_copyable` (C++20~)|(작성중)|
|`indirectly_copyable_storable` (C++20~)|(작성중)|
|`indirectly_swappable` (C++20~)|(작성중)|  
|`indirectly_comparable` (C++20~)|(작성중)|  
|`permutable` (C++20~)|(작성중)|  
|`permutable` (C++20~)|(작성중)|  
|`mergeable` (C++20~)|(작성중)|  
|`sortable` (C++20~)|(작성중)| 
|`indirect_result_t` (C++20~)|(작성중)|
|`projected` (C++20~)|(작성중)| 