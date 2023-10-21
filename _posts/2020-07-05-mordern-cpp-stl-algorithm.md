---
layout: single
title: "#5. [모던 C++ STL] 알고리즘"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# C스타일 알고리즘 함수

|항목|내용|
|--|--|
|`qsort()`|(작성중)|
|`bsearch()`|(작성중)|

# 수정되지 않는 시퀀스 작업

|항목|내용|
|--|--|
|[for_each()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-algorithm/)<br/>`for_each_n()` (C++17~)|시퀀스 안의 요소들에 대해 `f()`를 실행합니다.|
|`count()`<br/>`count_if()`|시퀀스에서 지정한 값인 요소의 갯수를 리턴합니다.|
|`mismatch()`|두 시퀀스가 달라지기 시작하는 첫번째 요소를 찾습니다.|
|`find()`<br/>[find_if()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)<br/>`find_if_not()` (C++11~)|시퀀스에서 특정값의 위치를 찾습니다.|
|`find_end()`|시퀀스에서 특정값의 마지막 위치를 찾습니다.|
|`find_first_of()`|지정된 요소들중 일치하는 값이 있는 첫번째 위치를 찾습니다.|
|`find_last_of()`|지정된 요소들중 일치하는 값이 있는 마지막 위치를 찾습니다.|
|`adjacent_find()`|중복된 요소의 위치를 찾습니다.|
|`search()`<br/>`search_n()`|시퀀스에서 특정 부분 시퀀스의 위치를 찾습니다.|
|`all_of()` (C++11~)<br/>`any_of()` (C++11~)<br>`none_of()` (C++11~)|시퀀스 안의 요소들에 대해 모든 조건이 참인지, 1개 이상 참인지, 모두 거짓인지 검사합니다.|

# 수정되는 시퀀스 작업

|항목|내용|
|--|--|
|`copy()`<br/>`copy_backward()`<br/>`copy_if()` (C++11~)<br/>`copy_n()` (C++11~)|시퀀스의 요소들을 다른 곳에 저장합니다.|
|`fill()`<br/>`fill_n()`|모든 요소에 `value`값을 대입합니다.|
|`transform(InputIt first1, InputIt last1, OutputIt d_first, UnaryOperation unary_op)`|시퀀스 안의 각 요소들(`first1~last1`)에 대해 `op()`를 실행후 `*d_first~`에 대입합니다.|
|`generate()`<br/>`generate_n()`|모든 요소에 `gen()`값을 대입합니다.|
|`remove()`<br/>`remove_if()`<br/>`remove_copy()`<br/>`remove_copy_if()`|시퀀스에서 주어진 값인 요소를 삭제합니다.(컨테이너 버전이 효율적)|
|`replace()`<br/>`replace_if()`<br/>`replace_copy()`<br/>`replace_copy_if()`|주어진 값을 가진 요소를 다른 값으로 대체합니다.|
|`swap()`<br/>`iter_swap()`<br/>`swap_ranges()`|두 요소나 시퀀스의 각 요소들을 바꿔치기 합니다.|
|`reverse()`<br/>`reverse_copy()`|요소들의 순서를 뒤집습니다.|
|`rotate()`<br/>`rotate_copy()`|요소들을 순환시킵니다.|
|`random_shuffle()` (~C++17)<br/>`shuffle()` (C++11~)|요소들이 뒤섞어 재배치 합니다.|
|`unique()`<br/>`unique_copy()`|같은 값을 가진 요소를 제거하여 유일한 값만 시퀀스에 남도록 만듭니다.|
|`move()` (C++11~)<br/>`move_backward()` (C++11~)|시퀀스의 요소들을 다른 곳에 이동시킵니다.|
|`sample()` (C++17~)|시퀀스에서 요소를 랜덤으로 추출합니다.|
|`shift_left()` (C++20~)<br/>`shift_right()` (C++20~)|(작성중)|

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
|`clamp(const T& value, const T& min, const T& max)` (C++17~)|주어진 값(`value`)을 최대(`max`)/최소(`min`) 범위로 조정된 값으로 리턴합니다.|

# 비교 작업

|항목|내용|
|--|--|
|`equal()`|두 시퀀스의 각 요소가 서로 같으면 `true`를 리턴합니다.|
|`lexicographical_compare()`|첫번째 시퀀스가 두번째 시퀀스 보다 사전식 순서로 먼저면 `true`를 리턴합니다.|
|`lexicographical_compare_three_way()` (C++20~)|(작성중)|

# 순열 작업

|항목|내용|
|--|--|
|`next_permutation()`<br/>`prev_permutation()`|정렬된 시퀀스에서 다음/이전 순열(요소들을 중복없이 순서를 변경하여 나열)을 배치합니다. 이때 다음이나 이전 순열이 있다면 `true`를 리턴합니다.|
|`is_permutation()` (C++11~)|순열인지 검사합니다.|

# 힙 작업

|항목|내용|
|--|--|
|`make_heap()`|(작성중)|
|`push_heap()`|(작성중)|
|`pop_heap()`|(작성중)|
|`sort_heap()`|(작성중)|
|`is_heap()` (C++11~)<br/>`is_heap_until()` (C++11~)|(작성중)|

# 수학 작업

|항목|내용|
|--|--|
|`accumulate(InputIt first, InputIt last, T init)`|주어진 시퀀스의 값들(`first~last`)을 누적합니다.|
|`inner_product(InputIt1 first1, InputIt1 last1, InputIt2 first2, T init)`|주어진 시퀀스의 값들(`first1~last1`) 과 다른 시퀀스(`first1~`)의 각 요소를 곱하고, `init`에 누적합니다.|
|`adjacent_difference()`|(작성중)|
|`partial_sum(InputIt first, InputIt last, OutputIt d_first)`|주어진 시퀀스의 값들(`first~last`)을 누적하여 각각 `d_first`에 저장합니다.<br/>`*(d_first + 0) = *first`<br/>`*(d_first + 1) = *first + *(first + 1)`<br/>`*(d_first + 2) = *first + *(first + 1) + *(first + 2)`|
|`iota()` (C++11~)|(작성중)|
|`reduce()` (C++17~)|`reduce()`는 임의의 순서로 `accumulate()`를 처리합니다.(`accumulate()`를 병렬로 적용합니다.)|
|`transform_reduce()` (C++17~)|`inner_product()`를 병렬로 적용합니다.|
|`inclusive_scan()` (C++17~)|`partial_sum()`을 병렬로 적용합니다.|
|`exclusive_scan()` (C++17~)|`inclusive_scan()` 과 유사하며, `i`번째 요소는 포함하지 않습니다.|
|`transform_inclusive_scan()` (C++17~)|주어진 시퀀스에 `unary_op()`를 적용한 결과를 `inclusive_scan()`합니다.|
|`transform_exclusive_scan()` (C++17~)|주어진 시퀀스에 `unary_op()`를 적용한 결과를 `exclusive_scan()`합니다.|
|`gcd(m, n)` (C++17~)|`m`과 `n`의 최대 공약수를 계산합니다.|
|`lcm(m, n)` (C++17~)|`m`과 `n`의 최소 공배수를 계산합니다.|
|`midpoint()` (C++20~)|(작성중)|
|`lerp()` (C++20~)|(작성중)|

다음은 `accumulate()`와 `reduce()`의 사용예입니다. 내부적으로 `+`연산을 사용하므로, 수행 순서와 상관없이 동일한 결과를 리턴합니다.

```cpp
std::vector<int> v{1, 2, 3, 4};
int init{0};

EXPECT_TRUE(std::accumulate(v.begin(), v.end(), init) == init + v[0] + v[1] + v[2] + v[3]); // 왼쪽에서 오른쪽으로 순서대로 계산합니다.
EXPECT_TRUE(std::reduce(v.begin(), v.end(), init) == init + v[3] + v[2] + v[0] + v[1]); // (C++17~) 순서는 뒤죽박죽이지만 계산 결과는 같습니다.
```

다만, 다음 그림에서 볼 수 있듯이 순서를 지키는 `accumulate()`는 병렬화 할 수 있는 여지가 없지만,

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/80a6815c-bb8b-4c40-a468-c669a2ef90f8)

순서를 지키지 않는 `reduce()`는 병렬화할 여지가 있습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/bd1f572d-e27c-4a11-b656-3d3bce062153)


