---
layout: single
title: "#23. [모던 C++ STL] (C++11~) 단방향 리스트(forward_list)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.

# 개요

기존의 `list`는 양방향 리스트였습니다. 그러다 보니 단방향 리스트가 필요한 경우에는 비효울적이었습니다.

[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다. 이에 따라 가장 마지막에 입력된 요소의 위치가 `begin()`이 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c411bb39-a809-4321-80e5-7ad00250a337)

1. [역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)가 없으며(`rbegin()`, `rend()`, `crbegin()`, `crend()`),
2. [forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)에서 마지막에 입력된 요소 제어를 위해 `before_begin()`이 제공되며,
3. [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)의 끝에 추가하거나 마지막 요소에 접근하는 기능이 없으며(`back()`, `push_back()`, `emplace_back()`, `pop_back()`),
4. 요소 추가 방향과 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/) 방향이 달라, 요소 삭제, 삽입, 이동시 별도의 `_after` 버전을 제공합니다.(`erase_after()`, `insert_after()`, `emplace_after()`, `splice_after()`)

|항목|공통|`forward_list`|`list`|
|--|--|--|--|
|첫번째 요소|`front()`|O|O|
|마지막 요소|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 단방향이어서 제공 안합니다.|X|`back()`|
|순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)|`begin()`, `end()`<br/>`cbegin()`, `cend()`<br/>[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)에서는 마지막에 입력된 요소가 `begin()`이고, `list`에서는 처음 입력된 요소가 `begin()`입니다.|O|O|
|[역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 단방향이어서 제공 안합니다.|X|`rbegin()`, `rend()`<br/>`crbegin()`, `crend()`|
|`begin()`의 앞 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)에서 마지막에 입력된 요소 제어를 위해 제공합니다.|`before_begin()`|X|
|리스트가 비었는지 검사|`empty()`|O|O|
|요소 갯수|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)에서는 제공하지 않습니다.|X|`size()`|
|리스트의 최대 요소 갯수|`max_size()`|O|O|
|모든 요소 삭제|`clear()`|O|O|
|[이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)로 요소 삭제|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 주어진 위치 뒤의 요소를 삭제하고, `list`는 주어진 위치의 요소를 삭제함|`erase_after()`|`erase()`|
|값으로 요소 삭제|`remove()`<br/>`remove_if()`|O|O|
|삽입|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 주어진 위치 뒤에 삽입하고, `list`는 앞에 삽입합니다.|`insert_after()`<br/>`emplace_after()`|`insert()`<br/>`emplace()`|
|앞에 추가/삭제|`push_front()`<br/>`emplace_front()`<br/>`pop_front()`|O|O|
|뒤에 추가/삭제|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 단방향이어서 제공 안합니다.|X|`push_back()`<br/>`emplace_back()`<br/>`pop_back()`|
|요소 크기 재설정|`resize()`|O|O|
|바꿔치기|`swap()`|O|O|
|리스트 병합|`merge()`|O|O|
|요소 이동||`splice_after()`|`splice()`|
|요소 순서 반전|`reverse()`|O|O|
|중복 요소 제거|`unique()`|O|O|
|요소 정렬|`sort()`|O|O|

```cpp
std::forward_list<int> list;
for(int i{0};i < 4; ++i) { // 0, 1, 2, 3의 순서로 삽입합니다.
    list.push_front(i); 
}

// 가장 마지막에 넣은 요소가 begin입니다.
std::vector<int> v;
for(auto val : list) {
    v.push_back(val);
}
EXPECT_TRUE(v[0] == 3 && v[1] == 2 && v[2] == 1 && v[3] == 0);

// insert after는 주어진 위치 뒤에 삽입합니다.
list.insert_after(list.begin(), 10);
v.clear();
for(auto val : list) {
    v.push_back(val);
}
EXPECT_TRUE(v[0] == 3 && v[1] == 10 && v[2] == 2 && v[3] == 1 && v[4] == 0);

// erase_after는 주어진 위치 뒤를 삭제합니다.
list.erase_after(list.begin());
v.clear();
for(auto val : list) {
    v.push_back(val);
}
EXPECT_TRUE(v[0] == 3 && v[1] == 2 && v[2] == 1 && v[3] == 0);  

// before_begin()은 begin()의 앞 이터레이터입니다. erase_after()는 주어진 위치 뒤를 삭제하므로 begin()이 삭제됩니다.
list.erase_after(list.before_begin());
v.clear();
for(auto val : list) {
    v.push_back(val);
}
EXPECT_TRUE(v[0] == 2 && v[1] == 1 && v[2] == 0);


// slice_after()는 주어진 요소 뒤부터 이동합니다.
std::forward_list<int> temp;
list.splice_after(list.begin(), temp); 
for(auto val : list) {
    v.push_back(val);
}
EXPECT_TRUE(v[0] == 2); // begin 다음부터 이동했으므로 2만 남았습니다.
```

# forward_list 멤버 함수

|항목|내용|
|--|--|
|`=` (C++11~)|(작성중)|
|`assign()` (C++11~)|(작성중)|
|`assign_range()` (C++23~)|(작성중)|
|`get_allocator()` (C++11~)|(작성중)| 
|`front()` (C++11~)|(작성중)|
|`before_begin(), cbefore_begin()` (C++11~)|(작성중)|
|`begin(), end()` (C++11~)|(작성중)|
|`cbegin(), cend()` (C++11~)|(작성중)|
|`clear()` (C++11~)|(작성중)|
|`insert_after()` (C++11~)|(작성중)|
|`emplace_after()` (C++11~)|(작성중)|
|`insert_range_after()` (C++23~)|(작성중)|
|`erase_after()` (C++11~)|(작성중)|
|`push_front()` (C++11~)|(작성중)|
|`emplace_front()` (C++11~)|(작성중)|
|`prepend_range()`(C++23~)|(작성중)|
|`pop_front()` (C++11~)|(작성중)|
|`resize()` (C++11~)|(작성중)|
|`swap()` (C++11~)|(작성중)|
|`merge()` (C++11~)|(작성중)|
|`splice_after()` (C++11~)|(작성중)|
|`remove()` (C++11~)<br/>`remove_if()` (C++11~)|(작성중)|
|`reverse()` (C++11~)|(작성중)|
|`unique()` (C++11~)|(작성중)|
|`sort()` (C++11~)|(작성중)|
|`==` (C++11~)<br/>`!=` (C++11~C++20)|(작성중)|
|`<, <=, >, >=` (C++11~C++20)<br/>`<=>` (C++20~)|(작성중)|
|`erase()` (C++20~)<br/>`erase_if()` (C++20~)|(작성중)|