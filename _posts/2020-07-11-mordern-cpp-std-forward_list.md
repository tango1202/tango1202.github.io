---
layout: single
title: "#11. [모던 C++ STL] (C++11~) forward_list"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `forward_list`는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.

# 개요

기존의 `list`는 양방향 리스트였습니다. 그러다 보니 단방향 리스트가 필요한 경우에는 비효울적이었습니다.

`forward_list`는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다. 이에 따라 가장 마지막에 입력된 요소의 위치가 `begin()`이 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/07c88686-3a16-4f69-91d8-3f9c8a6e6ecc)

1. 역방향 이터레이터가 없으며(`rbegin()`, `rend()`, `crbegin()`, `crend()`),
2. `forward_list`에서 마지막에 입력된 요소 제어를 위해 `before_begin()`이 제공되며,
3. 컨테이너의 끝에 추가하거나 마지막 요소에 접근하는 기능이 없으며(`back()`, `push_back()`, `emplace_back()`, `pop_back()`),
4. 요소 추가 방향과 이터레이터 방향이 달라, 요소 삭제, 삽입, 이동시 별도의 `_after` 버전을 제공합니다.(`erase_after()`, `insert_after()`, `emplace_after()`, `splice_after()`)

|항목|공통|`forward_list`|`list`|
|--|--|--|--|
|첫번째 요소|`front()`|O|O|
|마지막 요소|`forward_list`는 단방향이어서 제공 안함|X|`back()`|
|순방향 이터레이터|`begin()`, `end()`<br/>`cbegin()`, `cend()`<br/>`forward_list`에서는 마지막에 입력된 요소가 `begin()`이고, `list`에서는 처음 입력된 요소가 `begin()`임|O|O|
|역방향 이터레이터|`forward_list`는 단방향이어서 제공 안함|X|`rbegin()`, `rend()`<br/>`crbegin()`, `crend()`|
|`begin()`의 앞 이터레이터|`forward_list`에서 마지막에 입력된 요소 제어를 위해 제공|`before_begin()`|X|
|리스트가 비었는지 검사|`empty()`|O|O|
|요소 갯수|`forward_list`에서는 제공하지 않음|X|`size()`|
|리스트의 최대 요소 갯수|`max_size()`|O|O|
|모든 요소 삭제|`clear()`|O|O|
|이터레이터로 요소 삭제|`forward_list`는 주어진 위치 뒤의 요소를 삭제하고, `list`는 주어진 위치의 요소를 삭제함|`erase_after()`|`erase()`|
|값으로 요소 삭제|`remove()`<br/>`removeif()`|O|O|
|삽입|`forward_list`는 주어진 위치 뒤에 삽입하고, `list`는 앞에 삽입함.|`insert_after()`<br/>`emplace_after()`|`insert()`<br/>`emplace()`|
|앞에 추가/삭제|`push_front()`<br/>`emplace_front()`<br/>`pop_front()`|O|O|
|뒤에 추가/삭제|`forward_list`는 단방향이어서 제공 안함|X|`push_back()`<br/>`emplace_back()`<br/>`pop_back()`|
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