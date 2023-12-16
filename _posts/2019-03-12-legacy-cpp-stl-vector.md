---
layout: single
title: "#12. [레거시 C++ STL] vector"
categories: "legacy-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [swap()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#clear%EC%99%80-swap)을 이용하여 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 할당한 메모리 영역을 해제할 수 있다.
> * [`vector<bool>`](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#vectorbool)은 사용하지 마라.

> **모던 C++**
> * (C++11~) [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 추가되어 기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)를 제공합니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.

# 개요

[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 타입이 동일한 요소를 연속적인 메모리 공간에 관리하는 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)입니다. 

1. [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)과 유사합니다.(*[배열과 vector](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/#%EB%B0%B0%EC%97%B4%EA%B3%BC-vector) 참고*)
2. 랜덤 접근을 지원합니다.
3. 요소 삽입/삭제시 요소들을 순서대로 재배치합니다. 이에 따라 선형 시간이 필요하며, 요소 삽입/삭제는 비효율적입니다.(*요소 삽입/삭제가 빈번하면 `list`가 유리합니다.*)
4. 요소가 추가되어 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)의 용량을 늘려야 하는 경우, 재할당이 수행됩니다. 재할당 시에는 모든 요소가 새롭게 할당된 공간에 이동되어야 하므로 속도 부하가 있고, 요소의 참조나 [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)가 무효화됩니다. 따라서 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 재할당 시도를 최소화 하기 위해, 재할당시 실제 추가되는 크기가 아니라 좀 더 넉넉한 크기로 재할당합니다.

# vector 멤버 함수

**생성자**

정의|내용|
|--|--|
|`vector();`|요소가 없는 빈 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)를 생성합니다.|
|`explicit vector(size_type count);`|`count` 갯수 만큼 요소를 생성합니다.|
|`vector(size_type count, const Type& value);`|`count` 갯수 만큼 요소를 생성하고, `value`로 초기화 합니다.|
|`vector(const vector& other);`|[복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.|
|`template<class InputIterator>`<br/>`vector(InputIterator first, InputIterator last);`|`first`부터 `last` 직전까지의 요소(*반개방 구조*)를 복사합니다.|

상기 외에 `allocator`를 사용하는 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)가 있으며, 자세한 내용은 [할당자(*Allocator*)](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-allocator/)를 참고하시기 바랍니다.

**할당과 vector 관리**

|항목|정의|내용|
|--|--|--|
|`=`|`vector& operator =(const vector& other);`|[복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 합니다. <br/>[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 기존 요소를 지우고 `other`요소들을 복사합니다.|
|`=` (C++11~)|`vector& operator =(const vector&& other) notexcept;`|[이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 합니다.|
|`assign()`|`void assign(size_type count, const Type& value)`<br/>`template<typename InputIterator>`<br/>`void assign(InputIterator first, InputIterator last)`|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 기존 요소를 지우고 `value`를 `count` 만큼 복사합니다.<br/>[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 기존 요소를 지우고 `first`부터 `last`직전까지 복사합니다.|
|`resize()`|`void resize(size_type new_size)`<br/>`void resize(size_type new_size, Type value)`|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 새 크기를 지정하며, `value`로 채웁니다. `value`가 없는 경우 요소의 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.|
|[size()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)|`size_type size() const;`|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 요소 갯수를 리턴합니다.|
|`empty()`|`bool empty() const;`|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 비었는지 확인합니다.|
|[capacity()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)|`size_type capacity() const;`|메모리를 더 할당하지 않고 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)에 저장할 수 있는 요소 갯수를 리턴합니다.(*[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 메모리 할당이 빈번히 발생하지 않도록 실제 요소 갯수보다 큰 크기를 할당합니다.*)|
|`shrink_to_fit()`||(작성중)|
|[reserve()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#%EC%A0%80%EC%9E%A5-%EA%B3%B5%EA%B0%84-%EC%98%88%EC%95%BD)|`void reserve(size_type count);`|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 최소한 `count` 갯수 만큼 저장할 수 있도록 메모리 공간(*[capacity()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)*)을 할당합니다.<br/>|
|`max_size()`|`size_type max_size() const;`|[컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)가 저장할 수 있는 최대 요소 갯수를 리턴합니다.|
|[swap()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#clear%EC%99%80-swap)|`void swap(vector<Type, Allocator>& other);`|두 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)의 내부 데이터를 바꿔치기 합니다.|
|`==`<br/>`!=` (~C++20)|(작성중)|
|`<, <=, >, >=` (~C++20)<br/>`<=>` (C++20~)|(작성중)|
|`<<, >>`(C++20~)|(작성중)|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

**요소 검색**

|항목|정의|내용|
|--|--|--|
|[[]](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#-%EA%B3%BC-at)|`reference operator [](size_type position)`|`position`위치의 요소 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. `position`이 잘못된 위치라면 아무 생각없이 실행되어 오동작 합니다.|
|[at()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#-%EA%B3%BC-at)|`reference at(size_type position);`<br/>`const_reference at(size_type position) const;`|`position`위치의 요소 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. `position`이 잘못된 위치이면 [[]](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#-%EA%B3%BC-at) 과 달리 [예외를 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`begin()`|`iterator begin();`<br/>`const_iterator begin() const;`|첫번째 요소의 [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)를 리턴합니다.|
|`end()`|`iterator end();`<br/>`const_iterator end() const;`|마지막 요소의 다음 위치의 [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)를 리턴합니다.|
|`rbegin()`|`reverse_iterator rbegin();`<br/>`const_reverse_iterator rbegin() const;`|반전된 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 첫번째 요소의 [역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`rend()`|`reverse_iterator rend();`<br/>`const_reverse_iterator rend() const;`|반전된 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 마지막 요소의 다음 위치의 [역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`cbegin()` (C++11~)|`const_iterator cbegin() const;`|첫번째 요소의 [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)를 리턴합니다.|
|`cend()` (C++11~)|`const_iterator cend() const;`|마지막 요소의 다음 위치의 [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)를 리턴합니다.|
|`crbegin()` (C++11~)|`const_reverse_iterator crbegin() const;`|반전된 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 첫번째 요소의 [역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`crend()` (C++11~)|`const_reverse_iterator crend() const;`|반전된 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 마지막 요소의 다음 위치의 [역방향 이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 리턴합니다.|
|`data()`|`pointer data();`<br/>`const_pointer data() const;`|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 관리하는 메모리 블록을 리턴합니다.(*첫번째 요소의 포인터를 리턴합니다.*)|
|`front()`|`reference front();`<br/>`const_reference front() const;`|첫번째 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`back()`|`reference back();`<br/>`const_reference back() const;`|마지막 요소의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 비었다면 아무 생각없이 실행되어 오동작 합니다.|

**요소 삽입/삭제**

|항목|정의|내용|
|--|--|--|
|[clear()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#clear%EC%99%80-swap)|`void clear();`|모든 요소를 지웁니다. 이때 메모리 영역은 그대로 입니다.|
|[erase()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98-erase%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-remove-%ED%95%A8%EC%88%98)|`iterator erase(const_iterator position);`<br/>`iterator erase(const_iterator first, const_iterator last);`|`position`위치의 요소를 삭제하거나 `first`와 `last` 직전까지의 요소(*반개방 구조*)를 삭제합니다. [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)가 유효하지 않다면, 아무 생각없이 실행되어 오동작 합니다.|
|`erase_if()` (C++20~)||(작성중)|
|`pop_back()`|`void pop_back();`|마지막 요소를 삭제합니다. [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 비었다면 아무 동작 안합니다.|
|[push_back()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C)|`void push_back(const T& value);`|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 끝에 요소를 추가합니다.|
|[insert()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C)|`iterator insert(const_iterator position, const Type& value);`<br/>`iterator insert(const_iterator position, Type&& value);`<br/>`void insert(const_iterator position, size_type count, const Type& value);`<br/>`template<class InputIterator>`<br/>`void insert(const_iterator position,  InputIterator first, InputIterator last);`|`position`으로 지정한 위치 앞에 요소를 삽입합니다.|
|`insert_range()` (C++23~)||(작성중)||
|`emplace(), emplace_back()` (C++11~)||(작성중)|
|`append_range()` (C++23~)||(작성중)|

# 배열과 vector

[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 연속된 메모리 공간에 요소들을 순차적으로 관리하기 때문에 포인터 연산을 통해서도 요소 접근이 가능합니다.

```cpp
std::vector<int> v;

v.push_back(10); // 요소 생성
v.push_back(20);
EXPECT_TRUE(v[0] == 10);
EXPECT_TRUE(v[1] == 20);

EXPECT_TRUE(*(&v[0] + 0) == 10);
EXPECT_TRUE(*(&v[0] + 1) == 20); // 연속된 메모리여서 포인터 연산으로도 접근 가능합니다.
```

> *(C++11~) [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 추가되어 기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)를 제공합니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.*

# [] 과 at()

[[]](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#-%EA%B3%BC-at)는 별다른 검사없이 주어진 인덱스에 접근하여, 범위 바깥이면 오동작을 하게 되지만, [at()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#-%EA%B3%BC-at)은 다음과 같이 범위 검사를 하므로 좀더 [예외에 안전](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)합니다. 하지만, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.

```cpp
reference at(size_type position) {
    if (this->size() < position) { // 검사 코드가 추가되어 있습니다.
        throw std::out_of_range();
    }
    return (*this)[position];
}
```

# size()와 capacity()

[size()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)는 저장하고 있는 요소의 갯수이며, [capacity()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)는 메모리를 더 할당하지 않고 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)에 저장할 수 있는 요소의 갯수입니다.

보통의 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)는 이 둘이 서로 같은데요, [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 경우는 저장하고 있는 요소보다 좀 더 많이 저장할 수 있도록 메모리를 할당합니다. 메모리 할당 횟수를 최소화 하기 위해서요. 

즉, [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 요소 추가시마다 매번 해당 크기만큼 메모리 확보를 하지 않고, 적당한 시점에 미리 메모리 영역을 크게 확보합니다.

다음 코드를 실행시켜 보면,

```cpp
std::vector<int> v;

for (int i = 0; i < 16; ++i) {
    v.push_back(0);
    std::cout << "size:" << v.size() << " capacity:" << v.capacity() << std::endl;
}
```

5개째의 요소를 추가할때 메모리는 8개를 저장할 수 있는 공간을 확보하고, 9개째의 요소를 추가할때 메모는 16개를 저장할 수 있는 공간을 확보하여, 메모리 할당을 최소화하는 걸 알 수 있습니다.

```cpp
size:1 capacity:1
size:2 capacity:2
size:3 capacity:4
size:4 capacity:4
size:5 capacity:8 // 5개의 요소이지만 8개를 저장할 수 있게 메모리를 확보합니다.
size:6 capacity:8
size:7 capacity:8
size:8 capacity:8
size:9 capacity:16 // 9개의 요소이지만, 16개를 저장할 수 있게 메모리를 확보합니다.
size:10 capacity:16
size:11 capacity:16
size:12 capacity:16
size:13 capacity:16
size:14 capacity:16
size:15 capacity:16
size:16 capacity:16
```

# 저장 공간 예약 

[reserve()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#%EC%A0%80%EC%9E%A5-%EA%B3%B5%EA%B0%84-%EC%98%88%EC%95%BD)를 통해 미리 [capacity()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)를 설정할 수 있습니다.

```cpp
std::vector<int> v;   
v.reserve(100);
EXPECT_TRUE(v.capacity() == 100); // 100개를 저장할 수 있는 메모리 공간이 확보됨
v[0] = 0; // (X) 컴파일 오류. 아직 요소는 아직 생성된게 아니기에 접근할 수 없습니다.
v.push_back(0); // (O) 
```

# clear()와 swap()

[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 요소 삭제를 하더라도 메모리 할당 횟수를 최소화 하기 위해 확보한 메모리 영역을 다시 조정하지 않습니다.

다음 코드에서 100개의 요소를 생성하고, [clear()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#clear%EC%99%80-swap)했지만, [capacity()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#size%EC%99%80-capacity)는 그대로 인걸 알 수 있습니다.

```cpp
std::vector<int> v(100);
size_t old = v.capacity();
v.clear();
EXPECT_TRUE(v.capacity() == old); // clear를 했지만 메모리 용량은 그대로 입니다.
```

하지만, 계속 내버려 두면 쓸데없이 메모리만 차지할 수 있습니다. 따라서, 필요없다고 판단되면, 다음처럼 [swap()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#clear%EC%99%80-swap)을 이용한 바꿔치기로 메모리를 깔끔하게 해제할 수 있습니다.(*[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 pImpl로 구현되어 [swap()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#clear%EC%99%80-swap)시 복사 부하가 거의 없습니다. [PImpl 이디엄](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-pimpl/) 참고*)

```cpp
std::vector<int> v(100);
EXPECT_TRUE(v.size() == 100);
EXPECT_TRUE(v.capacity() == 100);

std::vector<int> temp; // 크기가 0인 텅빈 vector입니다.
v.swap(temp); // vector는 pImpl로 구현되어 swap시 복사 부하가 없습니다.

EXPECT_TRUE(v.size() == 0); // 크기가 0인 vector와 바꿔치기해서 요소가 없습니다.
EXPECT_TRUE(v.capacity() == 0); // 크기가 0인 vector와 바꿔치기해서 할당된 메모리가 없습니다.
```

# `vector<bool>`

[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 [bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/) [템플릿 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전은, [bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/)타입을 저장하는 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)가 아니라, 용량 최적화를 위해 [bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/) 8개를 1byte에 저장하여 사용하는 특수 개체입니다. [[]](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#-%EA%B3%BC-at)는 특정 항목에 접근하는 [프록시](https://tango1202.github.io/pattern/pattern-proxy/)로 구현되어 있고, `&v[0]`과 같은 포인터 연산이나 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)를 위한 일부 알고리즘들과 호환되지 않습니다.(*실제 사용하기 위해서 만들었다기 보다는 [프록시](https://tango1202.github.io/pattern/pattern-proxy/)를 활용한 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/) 구현 실험차 만들었다는 설도 있습니다.*) 

따라서,
 
1. [bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/)을 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)로 사용하고 싶다면, `deque<bool>`을 사용하고,
2. 비트 단위로 처리하고 싶다면, [bitset](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-bitset/)을 사용하는게 좋습니다. 

```cpp
std::vector<bool> v; // 내부적으로는 bool 타입이 아니라 비트 필드를 사용합니다.
v.push_back(true);
v.push_back(false);

EXPECT_TRUE(v[0] == true && v[1] == false);
EXPECT_TRUE(*(&v[0]) == true); // (X) 컴파일 오류. &v[0]와 같은 포인터 연산과 호환되지 않습니다.
```
