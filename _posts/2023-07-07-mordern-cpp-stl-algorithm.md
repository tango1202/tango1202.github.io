---
layout: single
title: "#7. [모던 C++ STL] 알고리즘"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [all_of(), any_of(), none_of(), find_if_not()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)이 추가되었습니다.
> * (C++11~) [minmax(), minmax_element()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C-%EC%9E%91%EC%97%85)가 추가되었습니다.
> * (C++11~) [is_heap(), is_heap_until()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%ED%9E%99-%EC%9E%91%EC%97%85)이 추가되었습니다.
> * (C++11~) [iota()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)가 추가되었습니다.
> * (C++11~) [copy_if(), copy_n(), shuffle()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)이 추가되었습니다.
> * (C++11~) [move(), move_backward()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)가 추가되었습니다.
> * (C++11~) [is_partitioned(), partition_copy(), partition_point()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EB%B6%84%ED%95%A0-%EC%9E%91%EC%97%85)가 추가되었습니다.
> * (C++11~) [is_sorted(), is_sorted_until()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%A0%95%EB%A0%AC-%EC%9E%91%EC%97%85)이 추가되었습니다.
> * (C++11~) [is_permutation()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%9C%EC%97%B4-%EC%9E%91%EC%97%85)이 추가되었습니다.
> * (C++17~) [sample()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)이 추가되어 시퀀스에서 요소를 랜덤으로 추출할 수 있습니다.
> * (C++17~) [clamp()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C-%EC%9E%91%EC%97%85)가 추가되어 주어진 값을 최대/최소 범위로 조정할 수 있습니다.
> * (C++17~) [for_each_n()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%EC%A0%95%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%9E%91%EC%97%85)이 추가되었습니다.
> * (C++17~) [gcd()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)가 추가되었습니다. `m`과 `n`의 최대 공약수를 계산합니다.
> * (C++17~) [lcm()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)이 추가되었습니다. `m`과 `n`의 최소 공배수를 계산합니다.
> * (C++17~) [reduce()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)가 추가되었습니다. 임의의 순서로 [accumulate()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)를 처리합니다.
> * (C++17~) [transform_reduce()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)가 추가되었습니다. [inner_product()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)를 병렬로 적용합니다.
> * (C++17~) [inclusive_scan(), exclusive_scan(), transform_inclusive_scan(), transform_exclusive_scan()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#%EC%88%98%ED%95%99-%EC%9E%91%EC%97%85)가 추가되었습니다. `partial_sum()`을 병렬로 적용합니다.
> * (C++17~) [대부분의 알고리즘에서 병렬 작업을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/)하는 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전이 추가되었습니다. [seq, par, par_unseq, unseq](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/#%EC%8B%A4%ED%96%89-%EC%A0%95%EC%B1%85)으로 병렬 실행 정책을 지정할 수 있습니다.
> * (C++20~) [대부분의 알고리즘에서 constexpr을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#c20-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%98-constexpr-%EA%B0%9C%EC%84%A0)합니다.
> * (C++20~) [midpoint()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#c20-midpoint)가 추가되어 중간점인 `a + (b - a) / 2`를 쉽게 구할 수 있습니다.

# C스타일 알고리즘 함수

|항목|내용|
|--|--|
|`qsort()`|(작성중)|
|`bsearch()`|(작성중)|

# 수정되지 않는 시퀀스 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|[for_each()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#for_each)<br/>`for_each_n()` (C++17~)|시퀀스 안의 요소들에 대해 `f()`를 실행합니다.|(C++20~)|
|[count()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#count)<br/>`count_if()`|시퀀스에서 `value`인 요소의 갯수를 리턴합니다.|(C++20~)|
|`mismatch()`|두 시퀀스가 달라지기 시작하는 첫번째 요소를 찾습니다.|(C++20~)|
|[find()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#find)<br/>[find_if()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)<br/>`find_if_not()` (C++11~)|시퀀스에서 특정값의 위치를 찾습니다.|(C++20~)|
|`find_end()`|시퀀스에서 특정값의 마지막 위치를 찾습니다.|(C++20~)|
|`find_first_of()`|지정된 요소들중 일치하는 값이 있는 첫번째 위치를 찾습니다.|(C++20~)|
|`find_last(), find_last_if(), find_last_if_not()` (C++23~)|지정된 요소들중 일치하는 값이 있는 마지막 위치를 찾습니다.|(C++23~)|
|`adjacent_find()`|중복된 요소의 위치를 찾습니다.|(C++20~)|
|[search()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#search)<br/>`search_n()`|시퀀스에서 특정 부분 시퀀스의 위치를 찾습니다.|(C++20~)|
|`all_of()` (C++11~)<br/>`any_of()` (C++11~)<br>`none_of()` (C++11~)|시퀀스 안의 요소들에 대해 모든 조건이 참인지, 1개 이상 참인지, 모두 거짓인지 검사합니다.|(C++20~)|

# 수정되는 시퀀스 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`copy()`<br/>`copy_backward()`<br/>`copy_if()` (C++11~)<br/>`copy_n()` (C++11~)|시퀀스의 요소들을 다른 곳에 저장합니다.||
|`fill()`<br/>`fill_n()`|모든 요소에 `value`값을 대입합니다.|(C++20~)|
|`transform(InputIt first1, InputIt last1, OutputIt d_first, UnaryOperation unary_op)`|시퀀스 안의 각 요소들(`first1~last1`)에 대해 `op()`를 실행후 `*d_first~`에 대입합니다.|(C++20~)|
|`generate()`<br/>`generate_n()`|모든 요소에 `gen()`값을 대입합니다.|(C++20~)|
|[remove()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#remove)<br/>`remove_if()`<br/>`remove_copy()`<br/>`remove_copy_if()`|`value`가 아닌 요소는 시퀀스의 앞으로 옮기고 삭제할 위치를 리턴합니다.(*[컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 버전이 효율적입니다.*)|(C++20~)|
|`replace()`<br/>`replace_if()`<br/>`replace_copy()`<br/>`replace_copy_if()`|주어진 값을 가진 요소를 다른 값으로 대체합니다.|(C++20~)|
|`swap()`<br/>`iter_swap()`<br/>`swap_ranges()`|두 요소나 시퀀스의 각 요소들을 바꿔치기 합니다.|(C++20~)|
|`reverse()`<br/>`reverse_copy()`|요소들의 순서를 뒤집습니다.|(C++20~)|
|`rotate()`<br/>`rotate_copy()`|요소들을 순환시킵니다.|(C++20~)|
|`random_shuffle()` (~C++17)<br/>`shuffle()` (C++11~)|요소들이 뒤섞어 재배치 합니다.||
|`unique()`<br/>`unique_copy()`|같은 값을 가진 요소를 제거하여 유일한 값만 시퀀스에 남도록 만듭니다.|(C++20~)|
|`move()` (C++11~)<br/>`move_backward()` (C++11~)|시퀀스의 요소들을 다른 곳에 이동시킵니다.|(C++20~)|
|`sample()` (C++17~)|시퀀스에서 요소를 랜덤으로 추출합니다.||
|`shift_left()` (C++20~)<br/>`shift_right()` (C++20~)|(작성중)|(C++20~)|

# 분할 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`partition()`<br/>`stable_partition()`|주어진 조건이 참인 요소들을 앞에 배치하고, 거짓인 요소들을 뒤에 분리합니다.|(C++20~)|
|`is_partitioned()` (C++11~)|주어진 조건에 맞게 `partition`되었는지 검사합니다.|(C++20~)|
|`partition_copy()` (C++11~)|주어진 조건이 참인 요소들과 거짓인 요소들을 분리합니다.|(C++20~)|
|`partition_point()` (C++11~)|`partition`된 경계 위치(거짓인 요소중 첫번째 위치)를 리턴합니다.|(C++20~)|

# 정렬 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`sort()`<br/>`stable_sort()`|요소들을 정렬합니다.|(C++20~)|
|`nth_element()`|시퀀스를 정렬하였을때 n요소가 위치할 곳에 n요소를 배치합니다. 즉 모든 요소를 정렬하지 않고, n요소만 제 위치에 둔다고 볼 수 있습니다. n요소 앞의 요소들은 n요소 뒤의 요소들 보다 작거나 같습니다.||
|`partial_sort()`<br/>`partial_sort_copy()`|지정한 중간값까지 앞쪽 시퀀스만 정렬합니다.|(C++20~)|
|`is_sorted()` (C++11~)<br/>`is_sorted_until()` (C++11~)|졍렬되었는지 검사합니다.|(C++20~)|

# 이진 검색 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`lower_bound()`|정렬된 시퀀스에서 주어진 값보다 같거나 큰 첫 요소 위치를 찾습니다.|(C++20~)|
|`upper_bound()`|정렬된 시퀀스에서 주어진 값보다 큰 요소 위치를 찾습니다.|(C++20~)|
|`euqal_range()`|`lower_bound()`, `upper_bound()`를 쌍으로 묶어 리턴합니다. [같거나 큰 요소 ~ 큰 요소]이므로 반개방구조에서 같은 값들의 영역를 표현합니다.|(C++20~)|
|`binary_search()`|정렬된 시퀀스에서 이진 탐색하여 특정값이 있으면 `true`를 리턴합니다.|(C++20~)|

# 정렬된 범위에서의 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`includes()`|시퀀스가 다른 부분 시퀀스를 포함하면 `true`를 리턴합니다.|(C++20~)|
|`merge()`<br/>`inplace_merge()`|두 시퀀스를 정렬하여 병합합니다.|(C++20~)|
|`set_union()`|정렬된 합집합을 만듭니다.`merge()`와 동일하나, 중복된 요소는 제거됩니다.|(C++20~)|
|`set_difference()`<br/>`set_symmetric_difference()`|정렬된 차집합을 만듭니다. 중복된 요소는 제거됩니다.|(C++20~)|
|`set_intersection()`|정렬된 교집합을 만듭니다. 중복된 요소는 제거됩니다.|(C++20~)|

# 최대/최소 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`min()`<br/>/`max()`|두 값 중에 최소값이나 최대값을 리턴합니다.|(C++14~)|
|`min_element()`<br/>`max_element()`|시퀀스에서 최소값이나 최대값을 리턴합니다.|(C++17~)|
|`minmax()` (C++11~)|최대값과 최소값을 리턴합니다.|(C++14~)|
|`minmax_element()` (C++11~)|최대값과 최소값을 리턴합니다.|(C++17~)|
|`clamp(const T& value, const T& min, const T& max)` (C++17~)|주어진 값(`value`)을 최대(`max`)/최소(`min`) 범위로 조정된 값으로 리턴합니다.|(C++17~)|

# 비교 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`equal()`|두 시퀀스의 각 요소가 서로 같으면 `true`를 리턴합니다.|(C++20~)|
|`lexicographical_compare()`|첫번째 시퀀스가 두번째 시퀀스 보다 사전식 순서로 먼저면 `true`를 리턴합니다.|(C++20~)|
|`lexicographical_compare_three_way()` (C++20~)|(작성중)|(C++20~)|

# 순열 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`next_permutation()`<br/>`prev_permutation()`|정렬된 시퀀스에서 다음/이전 순열(요소들을 중복없이 순서를 변경하여 나열)을 배치합니다. 이때 다음이나 이전 순열이 있다면 `true`를 리턴합니다.|(C++20~)|
|`is_permutation()` (C++11~)|순열인지 검사합니다.|(C++20~)|

# 힙 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`make_heap()`|(작성중)|(C++20~)|
|`push_heap()`|(작성중)|(C++20~)|
|`pop_heap()`|(작성중)|(C++20~)|
|`sort_heap()`|(작성중)|(C++20~)|
|`is_heap()` (C++11~)<br/>`is_heap_until()` (C++11~)|(작성중)|(C++20~)|

# 수학 작업

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`accumulate(InputIt first, InputIt last, T init)`|주어진 시퀀스의 값들(`[first - last)`)을 누적합니다.|(C++20~)|
|`inner_product(InputIt1 first1, InputIt1 last1, InputIt2 first2, T init)`|주어진 시퀀스의 값들(`[first1 - last1)`) 과 다른 시퀀스(`first1~`)의 각 요소를 곱하고, `init`에 누적합니다.|(C++20~)|
|`adjacent_difference()`|(작성중)|(C++20~)|
|`partial_sum(InputIt first, InputIt last, OutputIt d_first)`|주어진 시퀀스의 값들(`[first - last)`)을 누적하여 각각 `d_first`에 저장합니다.<br/>`*(d_first + 0) = *first`<br/>`*(d_first + 1) = *first + *(first + 1)`<br/>`*(d_first + 2) = *first + *(first + 1) + *(first + 2)`|(C++20~)|
|`iota(ForwardIt first, ForwardIt last, T init)` (C++11~)|`[first ~ last)` 구간에 `init`부터 `1`씩 증가한 값을 저장합니다.|(C++20~)|
|`reduce()` (C++17~)|`reduce()`는 임의의 순서로 `accumulate()`를 처리합니다.(`accumulate()`를 병렬로 적용합니다.)|(C++20~)|
|`transform_reduce()` (C++17~)|`inner_product()`를 병렬로 적용합니다.|(C++20~)|
|`inclusive_scan()` (C++17~)|`partial_sum()`을 병렬로 적용합니다.|(C++20~)|
|`exclusive_scan()` (C++17~)|`inclusive_scan()` 과 유사하며, `i`번째 요소는 포함하지 않습니다.|(C++20~)|
|`transform_inclusive_scan()` (C++17~)|주어진 시퀀스에 `unary_op()`를 적용한 결과를 `inclusive_scan()`합니다.|(C++20~)|
|`transform_exclusive_scan()` (C++17~)|주어진 시퀀스에 `unary_op()`를 적용한 결과를 `exclusive_scan()`합니다.|(C++20~)|
|`gcd(m, n)` (C++17~)|`m`과 `n`의 최대 공약수를 계산합니다.|(C++17~)|
|`lcm(m, n)` (C++17~)|`m`과 `n`의 최소 공배수를 계산합니다.|(C++17~)|
|[midpoint(a, b)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#c20-midpoint) (C++20~)|중간점인 `a + (b - a) / 2`를 계산합니다.<br/>포인터인 경우 `a` ~ `b`의 중간 위치를 가리키는 포인터를 리턴합니다.|(C++20~)|
|`lerp()` (C++20~)|(작성중)|(C++20~)|

다음은 `accumulate()`와 `reduce()`의 사용예입니다. 내부적으로 `+`연산을 사용하므로, 수행 순서와 상관없이 동일한 결과를 리턴합니다.

```cpp
std::vector<int> v{1, 2, 3, 4};
int init{0};

EXPECT_TRUE(std::accumulate(v.begin(), v.end(), init) == init + v[0] + v[1] + v[2] + v[3]); // 왼쪽에서 오른쪽으로 순서대로 계산합니다.
EXPECT_TRUE(std::reduce(v.begin(), v.end(), init) == init + v[3] + v[2] + v[0] + v[1]); // (C++17~) 순서는 뒤죽박죽이지만 계산 결과는 같습니다.
```

다만, 다음 그림에서 볼 수 있듯이 순서를 지키는 `accumulate()`는 병렬화 할 수 있는 여지가 없지만,

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/80a6815c-bb8b-4c40-a468-c669a2ef90f8)

순서를 지키지 않는 `reduce()`는 병렬화할 여지가 있습니다. `3`과 `7`은 동시에 수행해도 결과에 영향이 없으니까요.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/bd1f572d-e27c-4a11-b656-3d3bce062153)

C++17 부터는 [대부분의 알고리즘에서 병렬 작업을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/)하는 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전이 추가되었습니다. [seq, par, par_unseq, unseq](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/#%EC%8B%A4%ED%96%89-%EC%A0%95%EC%B1%85)으로 병렬 실행 정책을 지정할 수 있습니다.

# (C++20~) 알고리즘의 constexpr 개선

C++20 부터는 [대부분의 알고리즘에서 constexpr을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#c20-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%98-constexpr-%EA%B0%9C%EC%84%A0)합니다.

따라서 다음과 같이 [알고리즘](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/)을 활용하여 컴파일 타임에 다양한 연산을 수행할 수 있습니다.

```cpp
constexpr int arr[]{1, 2, 3};
constexpr int sum{std::accumulate(arr, arr + 3, 0)}; // 시퀀스의 값을 누적합니다.
static_assert(sum == 1 + 2 + 3); // 컴파일 타임 상수로 구합니다.
```

# (C++20~) midpoint()

[midpoint()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/#c20-midpoint)는 중간점인 `a + (b - a) / 2`를 구합니다. 정수인 경우 나누어 떨어지지 않으면 내림합니다.

```cpp
static_assert(std::midpoint(1.0, 2.0) == 1.5); // 실수

static_assert(std::midpoint(1, 3) == 2); 
static_assert(std::midpoint(1, 2) == 1); // 나누어 떨어지지 않으면 내림을 합니다.
static_assert(std::midpoint(1, 4) == 2); // 나누어 떨어지지 않으면 내림을 합니다.  
```
