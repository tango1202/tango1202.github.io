---
layout: single
title: "#5. [모던 C++ STL] 컨테이너"
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#13] `iterator` 보다 [const_iterator](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-const_iterator-%EC%A7%80%EC%9B%90)를 선호하라.(*[const_iterator 지원](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-const_iterator-%EC%A7%80%EC%9B%90) 참고*)
> * [MEC++#42] 기존 `push_back()`등의 삽입 대신 [emplace_back()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)등의 삽입을 고려하라.(*[emplace(), emplace_back(), emplace_front(), emplace_hint() 삽입](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85) 참고*)

> * (C++11~) [array](https://tango1202.github.io/cpp-stl/modern-cpp-stl-array/)가 추가되어 기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)를 제공합니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.
> * (C++11~) [forward_list](https://tango1202.github.io/cpp-stl/modern-cpp-stl-forward_list/)가 추가되어 단방향 리스트를 제공합니다. 기존 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.
> * (C++11~) [unordered_map, unordered_multimap, unordered_set, unordered_multiset](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/)가 추가되어 [해시값(Digest)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 사용하는 정렬되지 않은 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)를 제공합니다.
> * (C++11~) [const_iterator](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-const_iterator-%EC%A7%80%EC%9B%90) 지원이 강화되어 [cbegin(), cend(), crbegin(), crend()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#vector-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 추가되었고, `insert()`, `erase()`등의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/)에서 사용할 수 있습니다.
> * (C++11~) [컨테이너의 initializer_list 초기화](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-initializer_list-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 초기값 입력이 간편해 졌습니다. 
> * (C++11~) [emplace() 계열 함수](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)들이 추가되어 요소 삽입시 [완벽한 전달](https://tango1202.github.io/cpp/modern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 이용하여 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 내에서 요소 개체를 직접 생성할 수 있으며, 불필요한 복제본을 생성하지 않습니다.
> * (C++14~) [연관 컨테이너의 이종 탐색](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c14-%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-%EC%9D%B4%EC%A2%85-%ED%83%90%EC%83%89)을 지원하여 **Key**와 다른 타입이더라도 탐색이 가능합니다. 
> * (C++20~) [컨테이너 멤버 함수의 constexpr 지원이 개선](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c20-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98%EC%9D%98-constexpr-%EA%B0%9C%EC%84%A0)되어 대부분의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)들이 [constexpr 함수](https://tango1202.github.io/cpp/modern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)로 변경되었습니다.
> * (C++20~) [vector와 string의 constexpr 지원이 개선](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c20-vector%EC%99%80-string%EC%9D%98-constexpr-%EA%B0%9C%EC%84%A0)되어 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이 [constexpr](https://tango1202.github.io/cpp/modern-cpp-constexpr/) [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)로 변경되었습니다.
> * (C++20~) [erase(), erase_if()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c20-erase-erase_if)가 추가되어 값으로 요소를 삭제할 수 있습니다.
> * (C++20~) [contains()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c20-contains)가 추가되어 [연관 컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)에서 주어진 Key가 있는지 검사할 수 있습니다.

# 시퀀스 컨테이너

요소들을 순차 저장합니다.

|항목|내용|
|--|--|
|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)|타입이 동일한 요소를 연속적인 메모리 공간에 관리합니다.<br/>[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)과 유사하며, 요소 삽입/삭제에 비효율적이고, 랜덤 접근을  지원합니다.|
|`list`|타입이 동일한 요소를 연결된 리스트로 관리합니다.<br/>요소 삽입/삭제에 최적화되어 있으며, 양방향 순차 접근 지원하고, 랜덤 접근은 불가능합니다.|
|`queue`|타입이 동일한 요소를 선입선출 방식으로 관리합니다.<br/>`front()`시 선입된 요소 반환합니다.<br/>내부적으론 `deque`으로 구현됩니다.|
|`stack`|타입이 동일한 요소를 후입선출 방식으로 관리합니다.|
|`deque`|양방향에서 입출력이 가능한 `queue`입니다. 앞과 뒤에서 요소를 넣거나 뺄 수 있습니다.<br/>양쪽 입력/삭제가 `list`와 효율이 같으며 랜덤 접근은 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 효율이 같습니다.<br/>중간 입출력은 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 같이 비효율적입니다.|
|`priority_queue`|우선 순위에 따라 정렬된 `queue`입니다.<br/>`top()`시 가장 큰 요소 반환하며 내부적으론 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)로 구현됩니다.|
|[array](https://tango1202.github.io/cpp-stl/modern-cpp-stl-array/) (C++11~)|기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 입니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.|
|[forward_list](https://tango1202.github.io/cpp-stl/modern-cpp-stl-forward_list/) (C++11~)|단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.|

# 연관 컨테이너

요소의 대소 비교가 필요하며, **Key**로 정렬하여 관리합니다. 따라서 **Key**로 사용되는 타입은 `bool operator <(const T& other);` 가 구현되어 있어야 합니다.

|항목|내용|**Key** 조건|
|--|--|--|
|`map`|**Key** - **Value** 쌍으로 관리하며, [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)를 요소로 사용합니다.(*[pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/) 참고*)<br/>이진 트리 탐색으로 요소를 탐색(*O(logN)*)하며 요소 삽입시 `<`에 의해 정렬됩니다. 삽입되는 **Key**는 유일하며, 첨자 연산이 지원됩니다.|`<` 구현|
|`multimap`|`map`과 동일합니다.<br/>중복 **Key**를 허용하며, 첨자 연산은 지원하지 않습니다.|`<` 구현|
|`set`|**Key**만 요소로 사용합니다.<br/>이진 트리 탐색으로 요소 탐색(*O(logN)*)하며, 요소 삽입시 `<`에 의해 정렬됩니다. 삽입되는 **Key**는 유일합니다.|`<` 구현|
|`multiset`|`set`과 동일합니다.<br/>중복 **Key**허용합니다.|`<` 구현|
|[unordered_map, unordered_multimap, unordered_set, unordered_multiset](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/) (C++11~)|정렬되지 않은 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)로서, [해시값(Digest)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 사용하는 [해시 컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88) 입니다.|`==` 구현<br/>[hash()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-hash/)|

# (C++11~) const_iterator 지원

기존의 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)에서 [const_iterator](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-const_iterator-%EC%A7%80%EC%9B%90)를 지원했습니다만, 다른 함수에서 지원하지 않았기에 사용성이 좀 떨어졌습니다.

예를들어 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 `insert()`함수는 다음처럼 `iterator`를 전달받습니다.

```cpp
iterator insert(iterator position, const value_type& x);
```

따라서, 다음처럼 [find()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#find)한 [const_iterator](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-const_iterator-%EC%A7%80%EC%9B%90)를 `insert()`함수에 전달할 수 없습니다.

```cpp
std::vector<int> v;
v.push_back(1);
v.push_back(2);

std::vector<int>::const_iterator const_itr = std::find(v.begin(), v.end(), 1); // v를 수정하지 않으므로 const_iterator로 받습니다.
v.insert(const_itr, 0); // (X) 컴파일 오류. const_itr 앞에 0을 삽입하려고 했지만 iterator를 전달해야 합니다.
```

그래서 어쩔수 없이 그냥 `iterator`를 사용할 수 밖에 없었습니다.

```cpp
std::vector<int> v;
v.push_back(1);
v.push_back(2);

std::vector<int>::iterator itr = std::find(v.begin(), v.end(), 1); // (△) 비권장. v를 수정하지 않아 const_iterator로 받고 싶지만, insert 함수에서 사용하기 위해 iterator로 받습니다.
v.insert(itr, 0); // (△) 비권장. insert() 함수가 const_iterator가 아닌 iterator를 사용하므로, 어쩔수 없이 iterator를 전달합니다.
```

C++11 부터는 [const_iterator](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-const_iterator-%EC%A7%80%EC%9B%90) 지원이 강화되어 [cbegin(), cend(), crbegin(), crend()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#vector-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 추가되었고, `insert()`, `erase()`등의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/)에서 사용할 수 있습니다.

따라서 상기 코드를 다음과 같이 [cbegin(), cend()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/#vector-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)과 `insert()`를 이용하여 구현할 수 있습니다.

```cpp
std::vector<int> v;
v.push_back(1);
v.push_back(2);

std::vector<int>::const_iterator const_itr = std::find(v.cbegin(), v.cend(), 1); // v를 수정하지 않으므로 const_iterator로 받습니다.
v.insert(const_itr, 0); // const_itr 앞에 0을 삽입합니다.

EXPECT_TRUE(v[0] == 0 && v[1] == 1 && v[2] == 2);
```

# (C++11~) 컨테이너의 initializer_list 초기화

기존에는 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)에 요소들을 추가할때 일일이 추가해야 했습니다.(*[컨테이너 요소 삽입/삭제](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/) 참고*)

```cpp
std::vector<int> v;
v.push_back(1); // 이전에는 push_back으로 데이터를 삽입했습니다.
v.push_back(2);
v.push_back(3);
```

C++11 부터는 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)의 [initializer_list](https://tango1202.github.io/cpp/modern-cpp-initialization/#initializer_list) 초기화가 추가되어 초기값 입력이 간편해 졌습니다. 

```cpp
std::vector<int> v{1, 2, 3}; // C++11. 중괄호 초기화로 초기값을 넣을 수 있습니다.
```

단, [기존 생성자와 initializer_list 생성자와의 충돌](https://tango1202.github.io/cpp/modern-cpp-initialization/#%EA%B8%B0%EC%A1%B4-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-initializer_list-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80%EC%9D%98-%EC%B6%A9%EB%8F%8C)에서 말씀드린 것처럼  [initializer_list](initializer_list)를 사용한 버전이 비교적 우선적으로 선택되기 때문에 [중괄호 초기화](https://tango1202.github.io/cpp/modern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)시 기존 버전 초기화를 사용할때는 `()`버전을 사용해야 합니다.

```cpp
std::vector<int> v1(2); // 요소 갯수가 2개인 vector 생성
EXPECT_TRUE(v1.size() == 2 && v1[0] == 0 && v1[1] == 0);

std::vector<int> v2{2}; // 요소값이 2인 vector 생성
EXPECT_TRUE(v2.size() == 1 && v2[0] == 2);  
```

# (C++11~) emplace(), emplace_back(), emplace_front(), emplace_hint() 삽입

기존에는 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 바깥에서 개체를 생성해서 `push_back()`등으로 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)에 전달했는데요, 이때 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)는 삽입된 개체의 복제본을 관리합니다.(*[컨테이너의 원본 관리](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-%EC%9B%90%EB%B3%B8-%EA%B4%80%EB%A6%AC) 참고*)

C++11 부터는 [emplace() 계열 함수](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)들이 추가되어 요소 삽입시 [완벽한 전달](https://tango1202.github.io/cpp/modern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 이용하여 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 내에서 요소 개체를 직접 생성할 수 있으며, 불필요한 복제본을 생성하지 않습니다.

요소 개체를 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 밖에서 생성하던, [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 안에서 생성하던 생성하는건 똑같지만, 바깥에서 생성된 것은 관리를 위해 복제본을 만들어야 하기 때문에 복사 부하가 있습니다.

다음 예에서 

* `push_back(a)`는 기존에 생성한 `a`개체를 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하여 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)에서 관리합니다. 즉 2회 생성합니다.

* `push_back(A{1, 2})`는 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)인 `A{1, 2}`를 생성하고, 이를 [이동 생성](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)하여 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)에서 관리합니다. 즉 1회 생성 / 1회 이동합니다.

* `emplace_back(1, 2)`는 `1` 과 `2`를 가지고 `A`개체를 생성해서 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)에서 관리합니다. 즉 1회 생성합니다.

따라서, [emplace_back()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)이 불필요한 복제본을 만들지 않으니 `push_back()` 보다는 [emplace_back()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)을 사용하는게 효율적입니다.

```cpp
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) {std::cout << "A::Value Constructor" << std::endl;}
    A(const A& other) {std::cout << "A::Copy Constructor" << std::endl;}
    A(A&& other) noexcept {std::cout << "A::Move Constructor" << std::endl;}

    A& operator =(const A& other) = delete;
    A& operator =(A&& other) noexcept = delete;
};

std::vector<A> v;
v.reserve(10); // push_back이나 emplace_back 중 재할당이 없게끔 공간을 널널하게 만듭니다.

A a{1, 2}; 
v.push_back(a); // (△) 비권장. 복제본을 생성하면서 좌측값이므로 복사 생성 1회.
v.push_back(A{1, 2}); // (△) 비권장. 복제본을 생성하면서 임시개체인 우측값이므로 이동 생성 1회.
v.push_back({1, 2}); // (△) 비권장. 중괄호 복사 초기화로 v.push_back(A{1, 2}); 과 동일

v.emplace_back(1, 2); // (O) A개체 생성을 위한 데이터를 전달합니다. 개체 생성 1회
// v.emplace_back({1, 2}); // (X) 컴파일 오류. A는 {1, 2} 를 전달받는 생성자가 없습니다.
```

또한, [initializer_list](https://tango1202.github.io/cpp/modern-cpp-initialization/#initializer_list) 보다 [emplace_back()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)을 사용하는게 효과적입니다.

다음 예를 보면, [initializer_list](https://tango1202.github.io/cpp/modern-cpp-initialization/#initializer_list)는 [값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90) 2회, [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 2회를 호출하지만, [emplace_back()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)은 [값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90) 2회만 호출합니다.

```cpp
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) {std::cout << "A::Value Constructor" << std::endl;}
    A(const A& other) {std::cout << "A::Copy Constructor" << std::endl;}
    A(A&& other) noexcept {std::cout << "A::Move Constructor" << std::endl;}

    A& operator =(const A& other) = delete;
    A& operator =(A&& other) noexcept = delete;
};

// 값 생성자 2회 
// initializer_list 요소로 만들기 위해 이동 생성자 2회 -> 컴파일러 최적화에 의해 생략됨
// 복사 생성자 2회 - vector에서 복제본 관리
std::vector<A> v1{A{1, 2}, A{3, 4}}; 
                                        
std::vector<A> v2;
v2.reserve(2);
v2.emplace_back(1, 2); // 값 생성자 1회
v2.emplace_back(3, 4); // 값 생성자 1회
```

# (C++14~) 연관 컨테이너의 이종 탐색

기존에는 `map`과 `set`등의 [연관 컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)에서 **Key**와 동일한 타입으로만 탐색이 가능했습니다.

다음 예에서는 `Lee`의 데이터를 탐색하고 싶어 `Data{"Lee", 10}`와 같이 **Key**와 동일한 `Data` 타입 개체를 만들어 `find()`함수를 호출합니다. 

```cpp
class Data {
public:
    std::string m_Name;
    int m_Val;
    bool operator <(const Data& other) const {
        if (m_Val < other.m_Val) return true;
        if (other.m_Val < m_Val) return false;

        return m_Name < other.m_Name;
    }
};

std::set<Data> dataSet{
    Data{"Lee", 10},
    Data{"Park", 20}
};
auto result{
    dataSet.find(
        Data{"Lee", 10} // (△) 비권장. 같은 타입만 탐색이 가능하므로 "Lee"를 탐색하고 싶어도 Data 개체를 동일하게 만들어 탐색해야 합니다.
    )
}; 
EXPECT_TRUE((*result).m_Name == "Lee" && (*result).m_Val == 10);
```

이때 탐색할 `"Lee"`만 알고 있고, `10`은 모르고 있다면, 그냥 요소들을 다 뒤져서 값비교를 해야 합니다. [연관 컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)의 장점을 살리지 못하니, 이럴게 사용할 바엔 차라리 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 낫죠. 

C++14 부터는 [연관 컨테이너의 이종 탐색](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c14-%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-%EC%9D%B4%EC%A2%85-%ED%83%90%EC%83%89)을 지원하여 **Key**와 다른 타입이더라도 탐색이 가능합니다.

1. 이종 타입에 대해 비교할 수 있게 정적 비교 함수를 제공합니다.
2. [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 선언시 `find()` 에서 **Key** 타입외에 다른 것을 사용하려면 `less<>`를 사용합니다.
3. `find()` 함수에 탐색하고 싶은 이종 타입의 개체를 전달합니다.

다음 예에서는 `Data` 개체를 생성하지 않고 `"Lee"`로 검색합니다.

```cpp
class Data {
public:
    std::string m_Name;
    int m_Val;
    bool operator <(const Data& other) const {
        if (m_Val < other.m_Val) return true;
        if (other.m_Val < m_Val) return false;

        return m_Name < other.m_Name;
    }
};
// 이종 타입에 대해 비교할 수 있게 정적 비교 함수를 제공합니다.
bool operator <(const Data& left, const char* right) {
    return left.m_Name < right;
}
bool operator <(const char* left, const Data& right) {
    return left < right.m_Name;
}

// find()에서 키 타입외에 다른 것을 사용하려면 std::less<>를 사용합니다.
std::set<Data, std::less<>> dataSet{
    Data{"Lee", 10},
    Data{"Park", 20}
};
auto result{
    dataSet.find(
        "Lee" // find() 함수에 탐색하고 싶은 이종 타입의 개체를 전달합니다.
    )
}; 

EXPECT_TRUE((*result).m_Name == "Lee" && (*result).m_Val == 10);
```

# (C++20~) 컨테이너 멤버 함수의 constexpr 개선

[컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 멤버 함수의 [constexpr](https://tango1202.github.io/cpp/modern-cpp-constexpr/) 지원이 개선되어 대부분의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)들이 [constexpr 함수](https://tango1202.github.io/cpp/modern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)로 변경되었습니다.

# (C++20~) vector와 string의 constexpr 개선

[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)이 [constexpr](https://tango1202.github.io/cpp/modern-cpp-constexpr/) [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)로 변경되었습니다.

또한 대부분의 [알고리즘에서 constexpr 지원이 개선](https://tango1202.github.io/cpp-stl/modern-cpp-stl-algorithm/#c20-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%98-constexpr-%EA%B0%9C%EC%84%A0)되었기 때문에 다음과 같이 컴파일 타임에 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)를 정렬하여 사용할 수 있습니다.

```cpp
constexpr int maxVal { 
    [] {
        std::vector<int> v{3, 2, 1};
        std::sort(v.begin(), v.end()); // 컴파일 타임에 정렬됩니다.
        
        return v.back(); // 가장 뒤의 값을 리턴합니다.
    }()
};
static_assert(maxVal == 3); // 컴파일 타임 상수입니다.
```

# (C++20~) erase(), erase_if()

각 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/)의 [erase()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98-erase%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-remove-%ED%95%A8%EC%88%98)는 [이터레이터](https://tango1202.github.io/cpp-stl/modern-cpp-stl-iterator/)를 전달받아 요소를 삭제하고, [알고리즘](https://tango1202.github.io/cpp-stl/modern-cpp-stl-algorithm/)의 [remove()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/#remove)는 값을 전달받아 삭제하지 않을 요소를 앞으로 옮깁니다. 따라서 값으로 요소를 삭제하려면 다음과 같이 이 둘을 결합해서 사용해야 했습니다.(*[컨테이너 멤버 함수 erase()와 알고리즘 remove() 함수](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container-insert-erase/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98-erase%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-remove-%ED%95%A8%EC%88%98) 참고*)

```cpp
std::vector<int> v{1, 1, 2, 1, 1, 3};
std::vector<int>::iterator result{std::remove(v.begin(), v.end(), 1)}; // 삭제하지 않을 요소를 컨테이너의 앞으로 옮기고 erase할 위치를 리턴합니다.
v.erase(result, v.end()); // 요소를 실제로 삭제합니다.
EXPECT_TRUE(v.size() == 2 && v[0] == 2 && v[1] == 3);
```

C++20 부터는 [erase(), erase_if()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c20-erase-erase_if)가 추가되어 값으로 요소를 삭제할 수 있습니다.

```cpp
std::vector<int> v{1, 1, 2, 1, 1, 3};
std::erase(v, 1); // 값이 1인 요소를 삭제합니다.
EXPECT_TRUE(v.size() == 2 && v[0] == 2 && v[1] == 3);
```

# (C++20~) contains()

기존에는 연관 컨테이너에서 주어진 Key가 있는지 검사하려면 `find()`를 사용했는데요,

```cpp
std::set<int> s{1, 2, 3}; // C++17 부터는 템플릿 인수 추론을 하므로 std::set s{1, 2, 3}; 도 됩니다.
if (s.find(2) != s.end()) {
    // s에 2가 있을때의 코드
}
```

C++20 부터는 [contains()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#c20-contains)가 추가되어 [연관 컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)에서 주어진 Key가 있는지 검사할 수 있습니다.

```cpp
std::set s{1, 2, 3};
if (s.contains(2)) {
    // s에 2가 있을때의 코드   
}
```
