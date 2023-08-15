---
layout: single
title: "#11. [고전 C++ STL] Vector"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

타입이 동일한 요소를 연속적인 메모리 공간에 관리하는 컨테이너입니다. 

1. 배열과 유사합니다.([배열과 vector](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4%EA%B3%BC-vector) 참고)
2. 랜덤 접근을 지원합니다.
3. 요소 삽입/삭제시 연속적인 메모리 공간에 관리하기 위해 나머지 요소들을 재배치합니다.이에 따라 선형 시간이 필요하며, 비효율적입니다.(요소 삽입/삭제가 빈번하면 `list`가 유리합니다.)
4. 요소가 추가되어 컨테이너의 용량을 늘려야 하는 경우, 재할당이 수행됩니다. 재할당 시에는 모든 요소가 새롭게 할당된 공간에 이동되어야 하므로 속도 부하가 있고, 요소의 참조나 이터레이터가 무효화됩니다. 따라서 `vector`는 재할당 시도를 최소화 하기 위해, 재할당시 실제 추가/삭제되는 크기가 아니라 좀 넉넉한 크기로 재할당합니다.

# vector 레퍼런스

**생성자**

|항목|내용|
|--|--|
|`vector();`|요소가 없는 빈 벡터를 생성합니다.|
|`explicit vector(size_type count);`|`count` 갯수 만큼 요소를 생성합니다.|
|`vector(size_type count, const Type& value);`|`count` 갯수 만큼 요소를 생성하고, `value`로 초기화 합니다.|
|`vector(const vector& other);`|복사 생성합니다.|
|`template <class InputIterator>`<br/>`vector(InputIterator first, InputIterator last);`|`first`와 `last` 직전까지의 요소(반개방 구조)를 복사합니다.|

상기 외에 `allocator`를 사용하는 생성자가 있으며, 자세한 내용은 [할당자(Allocator)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-allocator/) 를 참고하기 바랍니다.

**할당과 벡터 관리**

|항목|내용|
|--|--|
|`assign(size_type count, const Type& value)`|벡터의 기존 요소를 지우고 `value`를 `count` 만큼 복사합니다.|
|`template <typename InputIterator>`<br/>`assign(InputIterator first, InputIterator last)`|벡터의 기존 요소를 지우고 `first`부터 `last`직전까지 복사합니다.|
|resize||
|size||
|capacity||
|max_size||
|swap||

**요소 검색**

|항목|내용|
|--|--|
|begin||
|end||
|rbegin||
|rend||
|cbegin||
|cend||
|crbegin||
|crend||
|data||
|front||
|back||
|at||

**요소 추가/삭제**

|항목|내용|
|--|--|
|clear||
|erase||
|empty||
|pop_back||
|push_back||
|insert||

# 배열과 vector

```cpp
vector<int> v(20); *(&v[0]) == 벡터의 첫번째 요소
*(&v[0] + 1) == 벡터의 두번째 요소
```

# at() 과 []

at() : v[idx] 보다 속도는 느리지만, 범위를 점검하므로 안전합니다.


# size와 capacity

```cpp
v.size(); // 원소의 갯수
v.capacity(); // 예약된 크기. 일반적으로 메모리 관리 효율을 위해
// 원소의 추가시 늘어난 용량을 삭제시 줄이지 않는
다.
```

# 저장 공간 예약 

```cpp
vector<int> v; // 별다른 공간 확보 없음
vector<int> v(20); // 20개 원소 공간을 할당하여 생성
v.resize(40); // 이전 20개의 원소 공간을 유지하고 추가로 20개의 연속 메모리 공간확
보
v[39] = 20;
for (int i = 0; i < 100000; ++i) { v.push_back(10); // v[40] 부터 추가.할당한 공간보다 더 많이 사용하면 알아서 grow
}
vector의 크기와 용량
v.size(); // 원소의 갯수
v.capacity(); // 예약된 크기. 일반적으로 메모리 관리 효율을 위해
// 원소의 추가시 늘어난 용량을 삭제시 줄이지 않는
다.
vector<int> v; v.reserve(1000); // 뭐 대략 예약 가능하다. resize 처럼 실제 원소를 할당하지는 않는다. v[0] = i; // (X) 메모리 예약만 했다.
```
# clear와 swap

```cpp
바꿔치기 묘수(vector 의 불필요한 용량 제거) vector<int> temp = v; v.swap(temp);
```

# vector<bool>

