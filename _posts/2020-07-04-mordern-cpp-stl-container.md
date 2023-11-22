---
layout: single
title: "#4. [모던 C++ STL] 컨테이너"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)는 기존 [C스타일의 배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)처럼 연속된 메모리를 사용하는 컨테이너 입니다. [C스타일 배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/) 보다 성능이 좋습니다.
> * (C++11~) [forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.
> * (C++11~) [unordered_map, unordered_multimap, unordered_set, unordered_multiset](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/)은 정렬되지 않은 컨테이너로서, [해시값(Digest)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 사용하는 [해시 컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88) 입니다.

# 시퀀스 컨테이너

요소들을 순차 저장합니다.

|항목|내용|
|--|--|
|[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)|타입이 동일한 요소를 연속적인 메모리 공간에 관리합니다.<br/>[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)과 유사하며, 요소 삽입/삭제에 비효율적이고, 랜덤 접근을  지원합니다.|
|`list`|타입이 동일한 요소를 연결된 리스트로 관리합니다.<br/>요소 삽입/삭제에 최적화되어 있으며, 양방향 순차 접근 지원하고, 랜덤 접근은 불가능합니다.|
|`queue`|타입이 동일한 요소를 선입선출 방식으로 관리합니다.<br/>`front()`시 선입된 요소 반환합니다.<br/>내부적으론 `deque`으로 구현됩니다.|
|`stack`|타입이 동일한 요소를 후입선출 방식으로 관리합니다.|
|`deque`|양방향에서 입출력이 가능한 `queue`입니다. 앞과 뒤에서 요소를 넣거나 뺄 수 있습니다.<br/>양쪽 입력/삭제가 `list`와 효율이 같으며 랜덤 접근은 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)와 효율이 같습니다.<br/>중간 입출력은 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)와 같이 비효율적입니다.|
|`priority_queue`|우선 순위에 따라 정렬된 `queue`입니다.<br/>`top()`시 가장 큰 요소 반환하며 내부적으론 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)로 구현됩니다.|
|[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/) (C++11~)|기존 [C스타일의 배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)처럼 연속된 메모리를 사용하는 컨테이너 입니다. [C스타일 배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/) 보다 성능이 좋습니다.|
|[forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/) (C++11~)|단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.|

# 연관 컨테이너

요소의 대소 비교가 필요하며, **Key**로 정렬하여 관리합니다. 따라서 **Key**로 사용되는 타입은 `bool operator <(const T& other);` 가 구현되어 있어야 합니다.

|항목|내용|**Key** 조건|
|--|--|--|
|`map`|**Key** - **Value** 쌍으로 관리하며, [pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/)를 요소로 사용합니다.(*[pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/) 참고*)<br/>이진 트리 탐색으로 요소를 탐색(O(logN))하며 요소 삽입시 `<`에 의해 정렬됩니다. 삽입되는 **Key**는 유일하며, 첨자 연산이 지원됩니다.|`<` 구현|
|`multimap`|`map`과 동일합니다.<br/>중복 **Key**를 허용하며, 첨자 연산은 지원하지 않습니다.|`<` 구현|
|`set`|**Key**만 요소로 사용합니다.<br/>이진 트리 탐색으로 요소 탐색(O(logN))하며, 요소 삽입시 `<`에 의해 정렬됩니다. 삽입되는 **Key**는 유일합니다.|`<` 구현|
|`multiset`|`set`과 동일합니다.<br/>중복 **Key**허용합니다.|`<` 구현|
|[unordered_map, unordered_multimap, unordered_set, unordered_multiset](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/) (C++11~)|정렬되지 않은 컨테이너로서, [해시값(Digest)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 사용하는 [해시 컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88) 입니다.|`==` 구현<br/>[hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-hash/)|

# (C++11~) 컨테이너의 initializer_list 초기화

기존에는 컨터이너에 요소들을 추가할때 일일이 추가해야 했습니다.(*[컨테이너 요소 삽입/삭제](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/) 참고*)

```cpp
std::vector<int> v;
v.push_back(1); // 이전에는 push_back으로 데이터를 삽입했습니다.
v.push_back(2);
v.push_back(3);
```

C++11 부터는 컨테이너의 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list) 초기화가 추가되어 초기값 입력이 간편해 졌습니다. 

```cpp
std::vector<int> v{1, 2, 3}; // C++11. 중괄호 초기화로 초기값을 넣을 수 있습니다.
```

단, [기존 생성자와 initializer_list 생성자와의 충돌](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EA%B8%B0%EC%A1%B4-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-initializer_list-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80%EC%9D%98-%EC%B6%A9%EB%8F%8C)에서 말씀드린 것처럼  [initializer_list](initializer_list)를 사용한 버전이 비교적 우선적으로 선택되기 때문에 [중괄호 초기화](??)시 기존 버전 초기화를 사용할때는 `()`버전을 사용해야 합니다.

```cpp
std::vector<int> v1(2); // 요소 갯수가 2개인 vector 생성
EXPECT_TRUE(v1.size() == 2 && v1[0] == 0 && v1[1] == 0);

std::vector<int> v2{2}; // 요소값이 2인 vector 생성
EXPECT_TRUE(v2.size() == 1 && v2[0] == 2);  
```

# (C++11~) emplace(), emplace_back(), emplace_front(), emplace_hint() 삽입

기존에는 [컨테이너](??) 바깥에서 개체를 생성해서 `push_back()`등으로 컨테이너에 삽입했는데요, 이때 [컨테이너](??)는 삽입된 개체의 복제본을 저장했습니다.

C++11 부터 [emplace()](??) 계열 함수들이 추가되어 요소 삽입시 [완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 이용하여 [컨테이너](??) 내에서 요소 개체를 직접 생성할 수 있으며, 불필요한 복제본을 생성하지 않습니다.

요소 개체를 [컨테이너](??) 밖에서 생성하던, [컨테이너](??) 안에서 생성하던 생성하는건 똑같다라고 생각하실 수도 있는데요, [컨테이너](??)는 요소 개체의 복제본을 내부에서 관리하므로 다릅니다.

예를들어 다음과 같은 `A`개체를 관리하는 `vector<V>`는 `push_back()`에 전달된 개체의 복제본을 관리하기 때문에 [복사 생성](??)이나 [이동 생성](??)을 합니다. 하지만, `emplace_back()`의 경우는 전달된 [인자](??)로 생성한 개체를 직접 관리하기 때문에 [복사 생성](??)이나 [이동 생성](??)을 하지 않습니다.

```cpp
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) {std::cout << "A::Value Constructor" << std::endl;}
    A(const A& other) {std::cout << "A::Copy Constructor" << std::endl;}
    A(A&& other) noexcept {std::cout << "A::Move Constructor" << std::endl;}

    A& operator =(const A& other) = delete;
    A& operator =(A&& other) = delete;
};

std::vector<A> v;
v.reserve(10); // push_back이나 emplace_back 중 재할당이 없게끔 공간을 널널하게 만듭니다.

A a{1, 2}; 
v.push_back(a); // (△) 비권장. 복제본을 생성하면서 좌측값이므로 복사 생성 1회.
v.push_back(A{1, 2}); // (△) 비권장. 복제본을 생성하면서 임시개체인 우측값이므로 이동 생성 1회.
v.push_back({1, 2}); // (△) 비권장. v.push_back(A{1, 2}); 과 동일

v.emplace_back(1, 2); // (O) A개체 생성을 위한 데이터를 전달합니다. 개체 생성 1회
// v.emplace_back({1, 2}); // (X) 컴파일 오류. A는 {1, 2} 를 전달받는 생성자가 없습니다.
```


