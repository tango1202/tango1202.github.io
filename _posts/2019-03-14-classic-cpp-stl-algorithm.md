---
layout: single
title: "#14. [고전 C++ STL] 알고리즘"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

알고리즘은 컨테이너의 요소를 처리하는 표준화된 방법입니다. 만약 컨테이너 멤버 버전이 있다면, 멤버 버전이 효율이 더 좋습니다.

# 검색

|항목|내용|
|--|--|
|`find()`<br/>`find_if()`|시퀀스에서 특정값이 처음으로 나타나는 위치를 찾습니다.|
|`search()`|시퀀스에서 특정 부분 시퀀스가 처음으로 나타나는 위치를 찾습니다.|
|`lower_bound()`|정렬된 시퀀스에서 주어진 값보다 같거나 큰 첫 요소 위치를 찾습니다.|
|`upper_bound()`|정렬된 시퀀스에서 주어진 값보다 큰 요소 위치를 찾습니다.|
|`euqal_range()`|`lower_bound()`, `upper_bound()`를 쌍으로 묶어 리턴합니다. [같거나 큰 요소 ~ 큰 요소]이므로 반개방구조에서 같은 값들의 영역를 표현합니다.|
|`binary_search()`|정렬된 시퀀스에서 이진 탐색하여 특정값이 있으면 `true`를 리턴합니다.|
|`lexicographical_compare()`|첫번째 시퀀스가 두번째 시퀀스 보다 사전식 순서로 먼저 이면 `true`를 리턴합니다.|
|min/max(min_element/max_ele ment) |두 값중 크거나 작은 값 선택|

# 요소 변형

|항목|내용|
|--|--|
|for_each()|시퀀스 안의 요소 각각에 대해 어떤 연산을 수행한다|
|transform|시퀀스 안의 각 요소에 대해 어떤 연산을 적용한다.|
|replace(replace_if, replace_copy, replace_copy_if)|주어진 값을 가진 요소를 다른 값으로 대체한다.|
|fill(fill_n)|모든 요소를 주어진 값을 대체한다|
|generate(generate_n)|어떤 연산의 결과로 모든 요소를 대체한다.|

# 정렬

|항목|내용|
|--|--|
|sort(stable_sort)|대체적으로 좋은 효율로 요소들을 정렬|
|`nth_element()`|시퀀스를 정렬하였을때 n요소가 위치할 곳에 n요소를 배치합니다. 즉 모든 요소를 정렬하지 않고, n요소만 제 위치에 둔다고 볼 수 있습니다. n요소 앞의 요소들은 n요소 뒤의 요소들 보다 작거나 같습니다.|
|partial_sort(partial_sort_copy)|시퀀스의 앞쪽 일부를 정렬|
|partition(stable_partition)|조건에 맞는 요소들을 앞에 놓는다.|

# 시퀀스 관리

|항목|내용|
|--|--|
|copy(copy_backward)|시퀀스를 처음 요소부터 복사한다|
|remove(remove_if, remove_copy, remove_copy_if)|주어진 값을 가진 요소를 제거한다.(컨테이너 버전이 효율적)|
|swap(iter_swap, swap_ranges)|두 요소를 맞바꾼다|
|count(count_if)|시퀀스에서 특정한 값의 출현 빈도를 센다.|
|mismatch|두 시퀀스가 달라지기 시작하는 첫번째 요소를 찾는다|
|equal|두 시퀀스 안의 요소들이 짝을 지어 같으면 참(true)을 낸다|
|includes|어떤 시퀀스가 다른 시퀀스의 부분 시퀀스이면 참을 낸다.|
|unique(unique_copy)|같은 값을 가진 인접한 요소를 제거한다. |

# 시퀀스 변형

|항목|내용|
|--|--|
|reverse(reverse_copy)|요소들의 순서를 뒤집는다.|
|rotate(rotate_copy) |요소들을 순환시킨다.|
|`next_permutation()`<br/>`prev_permutation()`|정렬된 시퀀스에서 다음/이전 순열을 배치합니다. 이때 다음이나 이전 순열이 있다면 `true`를 리턴합니다.|
|random_shuffle|요소들이 균일한 분포를 가지도록 요소들을 흩뜨려 놓는다|
|merge(inplace_merge)|정렬된 두 시퀀스를 병합한다|
|set_union|정렬된 합집합|
|set_intersection|정렬된 교집합|
|set_difference(set_symmetric_difference)|차집합|






