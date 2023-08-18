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
|find(find_if)|시퀀스에서 어떤 값이 처음으로 출현한 위치를 찾는다|
|search|어떤 시퀀스가 부분시퀀스로서 처음으로 나타나는 위치를 찾는다.|
|nth_element|n 번째 요소를 적절한 위치에 놓는다.|
|lower_bound|어떤 값이 처음 출현한 위치를 찾는다|
|upper_bound|주어진 값보다 큰 요소가 처음 출현한  위치를 찾는다|
|euqal_range|주어진 값을 가진 부분시퀀스를 찾는다.|
|binary_search|정렬된 시퀀스 안에서 어떤 값이 있으면 참을 낸다|
|lexicographical_compare|두 시퀀스중 사전식 순서로 먼저 오는 것을 골라 준다. |
|next_permutation(prev_permutation)|사전식 순서의 다음/이전 순열을 내준다.|
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
|random_shuffle|요소들이 균일한 분포를 가지도록 요소들을 흩뜨려 놓는다|
|merge(inplace_merge)|정렬된 두 시퀀스를 병합한다|
|set_union|정렬된 합집합|
|set_intersection|정렬된 교집합|
|set_difference(set_symmetric_difference)|차집합|






