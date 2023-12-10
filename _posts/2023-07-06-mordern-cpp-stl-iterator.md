---
layout: single
title: "#6. [모던 C++ STL] 이터레이터"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/) 어뎁터가 추가되어 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)가 가리키는 요소를 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 할 수 있습니다.
> * (C++11~) [make_move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%96%B4%EB%8E%81%ED%84%B0)가 추가되었습니다.
> * (C++11~) [next()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)와 [prev()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)가 추가되었습니다.
> * (C++11~) [begin(), end()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#c11-%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)가 추가되었습니다.
> * (C++14~) [make_reverse_iterator()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%96%B4%EB%8E%81%ED%84%B0)가 추가되었습니다.
> * (C++14~) [rbegin(), rend(), cbegin(), cend(), crbegin(), crend()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#c11-%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)가 추가되었습니다.
> * (C++17~) [size(), empty(), data()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#c11-%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)가 추가되었습니다.

# 이터레이터 기본 요소

|항목|내용|
|--|--|
|`iterator` (~C++17)|(작성중)|
|`iterator_traits`|(작성중)|
|`input_iterator_tag`<br/>`output_iterator_tag`<br/>`forward_iterator_tag`<br/>`bidirectional_iterator_tag`<br/>`random_access_iterator_tag`<br/>`input_iterator_tag`<br/>`contiguous_iterator_tag` (C++20~)|(작성중)|

# 이터레이터 어뎁터

[역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)나 [삽입 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%82%BD%EC%9E%85-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)와 같이 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)의 고유 기능인 `*``, `->``, `++`을 재구현하여 다르게 동작하는 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)들입니다.

|항목|내용|
|--|--|
|[reverse_iterator](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)|역방향으로 이터레이트 합니다.|
|`make_reverse_iterator()` (C++14~)|(작성중)|
|`back_insert_iterator`|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 끝에 삽입합니다.([삽입 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%82%BD%EC%9E%85-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0) 참고)|
|`back_inserter()`|`back_insert_iterator`를 생성합니다.|
|`front_insert_iterator`|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 앞에 삽입합니다.|
|`front_inserter()`|`front_insert_iterator`를 생성합니다.|
|`insert_iterator`|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)에 삽입합니다.|
|`inserter()`|`insert_iterator`를 생성합니다.|
|[move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%95%84%EB%8B%B5%ED%84%B0) (C++11~)|[이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)가 가리키는 요소를 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 합니다.|
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

다음은 [move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%95%84%EB%8B%B5%ED%84%B0) 사용 예입니다. [move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%95%84%EB%8B%B5%ED%84%B0)를 통해 `v2`를 생성하면, 요소가 이동되는 것을 알 수 있습니다.

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
|`istream_iterator`|`basic_istream`에서 사용하는 입력용 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)|
|`ostream_iterator`|`basic_ostream`에서 사용하는 출력용 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)|
|`istreambuf_iterator`|`basic_streambuf`에서 사용하는 입력용 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)|
|`ostreambuf_iterator`|`basic_streambuf`에서 사용하는 출력용 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)|
  
# 이터레이터 유틸리티

|항목|내용|
|--|--|
|`advance()`|[이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 이동시킵니다.|
|`distance()`|[이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)간 거리를 구합니다.|
|[next()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0) (C++11~)|뒤로 이동된 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다.|
|[prev()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0) (C++11~)|앞으로 이동된 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다.|

다음 예제는 `advance()`와 [next()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0), [prev()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)의 차이를 보여줍니다.

`advance()` 은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달한 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 직접 수정하지만, [next()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)와 [prev()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)는 수정하지 않습니다.

```cpp
std::vector<int> v{1, 2, 3, 4};
auto itr{v.begin()};
std::advance(itr, 2); // 인자로 전달한 이터레이터를 뒤로 2번 이동시킴
EXPECT_TRUE(*itr == 3);

itr = v.begin();
EXPECT_TRUE(*std::next(itr, 2) == 3 && *itr == 1); // 뒤로 2번 이동. itr은 수정되지 않음
EXPECT_TRUE(*std::prev(itr, -2) == 3 && *itr == 1); // 앞으로 -2번 이동. itr은 수정되지 않음
```

# (C++11~) 범위 접근

|항목|내용|
|--|--|
|`begin(), end()` (C++11~)|순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다.|
|`rbegin(), rend()` (C++14~)|[역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`cbegin(), cend()` (C++14~)|순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin(), crend()` (C++14~)|[역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`size()` (C++17~)<br/>`ssize()` (C++20~)|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)의 크기를 구합니다.|
|`empty()` (C++17~)|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)가 비었는지 검사합니다.|
|`data()` (C++17~)|[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)가 관리하는 메모리 블록을 리턴합니다.|

# (C++20~) 이터레이터 사용자 정의

|항목|내용|
|--|--|
|`iter_move()` (C++20~)|(작성중)|
|`iter_swap()` (C++20~)|(작성중)|

# (C++20~) 이터레이터 연관 타입

|항목|내용|
|--|--|
|`incrementable_traits` (C++20~)|(작성중)|
|`iter_value_t` (C++20~)<br/>`iter_reference_t` (C++20~)<br/>`iter_const_reference_t` (C++23~)<br/>`iter_difference_t` (C++20~)<br/>`iter_rvalue_reference_t` (C++20~)<br/>`iter_common_reference_t` (C++20~)<br/>|(작성중)|

 
