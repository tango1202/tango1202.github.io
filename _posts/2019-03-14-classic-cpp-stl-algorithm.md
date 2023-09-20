---
layout: single
title: "#14. [고전 C++ STL] 알고리즘(작성중)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

알고리즘은 컨테이너의 요소를 처리하는 표준화된 방법입니다. 만약 컨테이너 멤버 버전이 있다면, 멤버 버전이 효율이 더 좋습니다.

다음은 시퀀스 안의 요소들에 대해 `f()`를 실행하는 `for_each()` 함수입니다.(사용하는 STL 라이브러리에 따라 다르겠지만, 대략 다음과 같이 정의되어 있습니다.)

```cpp
template<typename InputIterator, typename Function>
Function for_each(InputIterator first, InputIterator last, Function f) {
    // first~last 직전까지 요소에 대해 f() 호출
    for (; first != last; ++first) { 
        f(*first);
    }
    return f; 
}
```

다음은 `std::for_each()`를 사용한 예입니다. `Setter()`함수를 이용하여 각 요소의 값을 `10`으로 변경합니다.

```cpp
// 10으로 세팅하는 함수
void Setter(int& val) {
    val = 10;
}

// 0으로 초기화 되어 생성
std::vector<int> v(3);

EXPECT_TRUE(v[0] == 0 && v[1] == 0 && v[2] == 0);

// 모든 요소에 대해 Setter 적용
std::for_each(v.begin(), v.end(), Setter);
EXPECT_TRUE(v[0] == 10 && v[1] == 10 && v[2] == 10); 
```

# 수정되지 않는 시퀀스 작업

|항목|내용|
|--|--|
|`for_each()`<br/>`for_each_n()` (C++17~)|시퀀스 안의 요소들에 대해 `f()`를 실행합니다.|
|`count()`<br/>`count_if()`|시퀀스에서 지정한 값인 요소의 갯수를 리턴합니다.|
|`mismatch()`|두 시퀀스가 달라지기 시작하는 첫번째 요소를 찾습니다.|
|`find()`<br/>`find_if()`<br/>`find_if_not()` (C++11~)|시퀀스에서 특정값의 위치를 찾습니다.|
|`find_end()`|시퀀스에서 특정값의 마지막 위치를 찾습니다.|
|`find_first_of()`|지정된 요소들중 일치하는 값이 있는 첫번째 위치를 찾습니다.|
|`find_last_of()`|지정된 요소들중 일치하는 값이 있는 마지막 위치를 찾습니다.|
|`adjacent_find()`|중복된 요소의 위치를 찾습니다.|
|`search()`<br/>`search_n()`|시퀀스에서 특정 부분 시퀀스의 위치를 찾습니다.|
|`all_of()` (C++11~)<br/>`any_of()` (C++11~)<br>`none_of()` (C++11~)|시컨스 안의 요소들에 대해 모든 조건이 참인지, 1개 이상 참인지, 모두 거짓인지 검사합니다.|

# 수정되는 시퀀스 작업

|항목|내용|
|--|--|
|`copy()`<br/>`copy_backward()`<br/>`copy_if()` (C++11~)<br/>`copy_n()` (C++11~)|시퀀스의 요소들을 다른 곳에 저장합니다.|
|`fill()`<br/>`fill_n()`|모든 요소에 `value`값을 대입합니다.|
|`transform()`|시퀀스 안의 각 요소들에 대해 `op()`를 실행후 `*result`에 대입합니다.|
|`generate`<br/>`generate_n()`|모든 요소에 `gen()`값을 대입합니다.|
|`remove()`<br/>`remove_if()`<br/>`remove_copy()`<br/>`remove_copy_if()`|시퀀스에서 주어진 값인 요소를 삭제합니다.(컨테이너 버전이 효율적)|
|`replace()`<br/>`replace_if()`<br/>`replace_copy()`<br/>`replace_copy_if()`|주어진 값을 가진 요소를 다른 값으로 대체합니다.|
|`swap()`<br/>`iter_swap()`<br/>`swap_ranges()`|두 요소나 시퀀스의 각 요소들을 바꿔치기 합니다.|
|`reverse()`<br/>`reverse_copy()`|요소들의 순서를 뒤집습니다.|
|`rotate()`<br/>`rotate_copy()`|요소들을 순환시킵니다.|
|`random_shuffle()` (~C++17)<br/>`shuffle()` (C++11~)|요소들이 뒤섞어 재배치 합니다.|
|`unique()`<br/>`unique_copy()`|같은 값을 가진 요소를 제거하여 유일한 값만 시퀀스에 남도록 만듭니다.|
|`move()` (C++11~), `move_backward()` (C++11~)|시퀀스의 요소들을 다른 곳에 이동시킵니다.|
|`sample()` (C++17~)|시퀀스에서 요소를 랜덤으로 추출합니다.|

# 분할 작업

|항목|내용|
|--|--|
|`partition()`<br/>`stable_partition()`|주어진 조건이 참인 요소들을 앞에 배치하고, 거짓인 요소들을 뒤에 분리합니다.|
|`is_partitioned()` (C++11~)|주어진 조건에 맞게 `partition`되었는지 검사합니다.|
|`partition_copy()` (C++11~)|주어진 조건이 참인 요소들과 거짓인 요소들을 분리합니다.|
|`partition_point()` (C++11~)|`partition`된 경계 위치(거짓인 요소중 첫번째 위치)를 리턴합니다.|

# 정렬 작업

|항목|내용|
|--|--|
|`sort()`<br/>`stable_sort()`|요소들을 정렬합니다.|
|`nth_element()`|시퀀스를 정렬하였을때 n요소가 위치할 곳에 n요소를 배치합니다. 즉 모든 요소를 정렬하지 않고, n요소만 제 위치에 둔다고 볼 수 있습니다. n요소 앞의 요소들은 n요소 뒤의 요소들 보다 작거나 같습니다.|
|`partial_sort()`<br/>`partial_sort_copy()`|지정한 중간값까지 앞쪽 시퀀스만 정렬합니다.|
|`is_sorted()` (C++11~)<br/>`is_sorted_until()` (C++11~)|졍렬되었는지 검사합니다.|


# 이진 검색 작업

|항목|내용|
|--|--|
|`lower_bound()`|정렬된 시퀀스에서 주어진 값보다 같거나 큰 첫 요소 위치를 찾습니다.|
|`upper_bound()`|정렬된 시퀀스에서 주어진 값보다 큰 요소 위치를 찾습니다.|
|`euqal_range()`|`lower_bound()`, `upper_bound()`를 쌍으로 묶어 리턴합니다. [같거나 큰 요소 ~ 큰 요소]이므로 반개방구조에서 같은 값들의 영역를 표현합니다.|
|`binary_search()`|정렬된 시퀀스에서 이진 탐색하여 특정값이 있으면 `true`를 리턴합니다.|

# 정렬된 범위에서의 작업

|항목|내용|
|--|--|
|`includes()`|시퀀스가 다른 부분 시퀀스를 포함하면 `true`를 리턴합니다.|
|`merge()`<br/>`inplace_merge()`|두 시퀀스를 정렬하여 병합합니다.|
|`set_union()`|정렬된 합집합을 만듭니다.`merge()`와 동일하나, 중복된 요소는 제거됩니다.|
|`set_difference()`<br/>`set_symmetric_difference()`|정렬된 차집합을 만듭니다. 중복된 요소는 제거됩니다.|
|`set_intersection()`|정렬된 교집합을 만듭니다. 중복된 요소는 제거됩니다.|

# 최대/최소 작업

|항목|내용|
|--|--|
|`min()`<br/>/`max()`<br/>`min_element()`<br/>`max_element()`|두 값 중에 최소값이나 최대값을 리턴하거나, 시퀀스에서 최소값이나 최대값을 리턴합니다.|
|`minmax()` (C++11~)<br/>`minmax_element()` (C++11~)|최대값과 최소값을 리턴합니다.|
|`clamp()` (C++17~)|주어진 값을 최대/최소 범위로 조정된 값으로 리턴합니다.|

# 비교 작업

|항목|내용|
|--|--|
|`equal()`|두 시퀀스의 각 요소가 서로 같으면 `true`를 리턴합니다.|
|`lexicographical_compare()`|첫번째 시퀀스가 두번째 시퀀스 보다 사전식 순서로 먼저면 `true`를 리턴합니다.|

# 순열 작업

|항목|내용|
|--|--|
|`next_permutation()`<br/>`prev_permutation()`|정렬된 시퀀스에서 다음/이전 순열(요소들을 중복없이 순서를 변경하여 나열)을 배치합니다. 이때 다음이나 이전 순열이 있다면 `true`를 리턴합니다.|
|`is_permutation()` (C++11~)|순열인지 검사합니다.|

# 힙 작업

|항목|내용|
|--|--|
|`make_heap()`||
|`push_heap()`||
|`pop_heap()`||
|`sort_heap()`||
|`is_heap_until()` (C++11~)||

# 수학 작업

|항목|내용|
|--|--|
|`accumulate()`||
|`inner_product()`||
|`adjacent_difference()`||
|`partial_sum()`||
|`iota()` (C++11~)||
|`reduce()` (C++17~)||
|`exclusive_scan()` (C++17~)||
|`inclusive_scan()` (C++17~)||
|`transform_reduce()` (C++17~)||
|`transform_exclusive_scan()` (C++17~)||
|`transform_inclusive_scan()` (C++17~)||

# 메모리 유틸리티 작업

|항목|내용|
|--|--|
|`uninitialized_copy()`<br/>`uninitialized_copy_n()` (C++11~)|
|`uninitialized_fill()`<br/>`uninitialized_fill_n()`||
|`uninitialized_move()` (C++17~)<br/>`uninitialized_move_n()` (C++17~)||
|`uninitialized_default_construct()` (C++17~)<br/>`uninitialized_default_construct_n()` (C++17~)|| 
|`uninitialized_value_construct()` (C++17~)<br/>`uninitialized_value_construct_n()` (C++17~)|| 
|`destroy()` (C++17~)<br/>`destroy_n()` (C++17~)|| 
|`destroy_at()` (C++17~)|| 




