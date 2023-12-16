---
layout: single
title: "#9. [레거시 C++ STL] 컨테이너"
categories: "legacy-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 삽입/삭제 성능, 검색 성능, 노드 구성 용량 부하를 검토하여 최적의 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)를 선택하라.
> * [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)의 변경이 용이하도록 코딩하라.
> * [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/) 종류나 사용하는 [알고리즘](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/)에 따라 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [비교 연산자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)를 구현하라.

> **모던 C++**
> * (C++11~) [forward_list](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-forward_list/)가 추가되어 단방향 리스트를 제공합니다. 기존 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.
> * (C++11~) [unordered_map, unordered_multimap, unordered_set, unordered_multiset](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/)가 추가되어 [해시값(Digest)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 사용하는 정렬되지 않은 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)를 제공합니다.
> * (C++14~) [연관 컨테이너의 이종 탐색](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/#c14-%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-%EC%9D%B4%EC%A2%85-%ED%83%90%EC%83%89)을 지원하여 **Key**와 다른 타입이더라도 탐색이 가능합니다.

# 개요

타입이 같은 여러개의 요소롤 저장하고 관리하는 개체를 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)라고 합니다.(*Collection 이라고도 합니다.*)

STL의 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)는 다음 특징을 가지고 있습니다.

**동종 컨테이너**

같은 타입만 관리하며, 서로 다른 타입인 경우라도, [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/) 관계를 가지고 있다면 부모 포인터로 관리할 수 있습니다. 

**비 관입형**

관입형은 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/) 요소가 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/) 구성을 위한 노드 정보를 자체적으로 가지고 있는 것을 말하며,

```cpp
class MyClass {
    MyClass* m_Pre; // 이전
    MyClass* m_Next; // 다음
    int m_Value1; // 실제 값
    int m_Value2; // 실제 값
};
```

비 관입형은 노드 정보와 데이터 정보를 분리한 것을 말합니다.

```cpp
class MyClass {
    int m_Value1; // 실제 값
    int m_Value2; // 실제 값
};

class Node {
    Node* m_Pre; // 이전
    Node* m_Next; // 다음
    MyClass m_Value; // 실제 값
};
```

일반적으로 관입형이 실행 속도 성능은 좋으나 일반화의 어려움이 있어 STL은 비 관입형을 사용합니다.

**이터레이터**

[이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)를 통해 요소 접근을 일반화 합니다.

# 시퀀스 컨테이너

요소들을 순차 저장합니다.

|항목|내용|
|--|--|
|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)|타입이 동일한 요소를 연속적인 메모리 공간에 관리합니다.<br/>[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)과 유사하며, 요소 삽입/삭제에 비효율적이고, 랜덤 접근을  지원합니다.|
|`list`|타입이 동일한 요소를 연결된 리스트로 관리합니다.<br/>요소 삽입/삭제에 최적화되어 있으며, 양방향 순차 접근을 지원하고, 랜덤 접근은 불가능합니다.|
|`queue`|타입이 동일한 요소를 선입선출 방식으로 관리합니다.<br/>`front()`시 선입된 요소 반환합니다.<br/>내부적으론 `deque`으로 구현됩니다.|
|`stack`|타입이 동일한 요소를 후입선출 방식으로 관리합니다.|
|`deque`|양방향에서 입출력이 가능한 `queue`입니다. 앞과 뒤에서 요소를 넣거나 뺄 수 있습니다.<br/>양쪽 입력/삭제가 `list`와 효율이 같으며 랜덤 접근은 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 효율이 같습니다.<br/>중간 입력/삭제는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 같이 비효율적입니다.|
|`priority_queue`|우선 순위에 따라 정렬된 `queue`입니다.<br/>`top()`시 가장 큰 요소 반환하며 내부적으론 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)로 구현됩니다.|

# 연관 컨테이너

요소의 대소 비교가 필요하며, **Key**로 정렬하여 관리합니다. 따라서 **Key**로 사용되는 타입은 `bool operator <(const T& other);` 가 구현되어 있어야 합니다.

|항목|내용|**Key** 조건|
|--|--|--|
|`map`|**Key** - **Value** 쌍으로 관리하며, [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)를 요소로 사용합니다.(*[pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/) 참고*)<br/>이진 트리 탐색으로 요소를 탐색(*O(logN)*)하며 요소 삽입시 `<`에 의해 정렬됩니다. 삽입되는 **Key**는 유일하며, 첨자 연산이 지원됩니다.|`<` 구현|
|`multimap`|`map`과 동일합니다.<br/>중복 **Key**를 허용하며, 첨자 연산은 지원하지 않습니다.|`<` 구현|
|`set`|**Key**만 요소로 사용합니다.<br/>이진 트리 탐색으로 요소 탐색(*O(logN)*)하며, 요소 삽입시 `<`에 의해 정렬됩니다. 삽입되는 **Key**는 유일합니다.|`<` 구현|
|`multiset`|`set`과 동일합니다.<br/>중복 **Key**를 허용합니다.|`<` 구현|


> *(C++14~) [연관 컨테이너의 이종 탐색](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/#c14-%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-%EC%9D%B4%EC%A2%85-%ED%83%90%EC%83%89)을 지원하여 **Key**와 다른 타입이더라도 탐색이 가능합니다.*

# 컨테이너 선정

보통은 `map`과 `set`이 검색에 유리하지만, 연속 메모리를 가진 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)도 데이터 갯수가 적으면 속도가 빠릅니다. 요소 갯수에 따라 가변적이기 때문에 최초 설계 단계에서 어느 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)가 적합하다 단정 짓기 쉽지 않습니다.

그래서,

1. 기본적으로 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)를 사용하고,
2. 속도 개선이 필요할 정도로 프로그램의 성능이 떨어진다면 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)를 이것 저것 바꾸며 프로파일러로 속도를 측정하고,(*검색 속도가 빠르다 해서 `set`을 사용했는데, 의외로 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)가 더 빠른 경우가 있을 겁니다. 용량도 더 작게 차지하고요.*)
3. 측정 결과를 바탕으로 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)를 재선정하는게 좋습니다.

따라서 코딩시 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)의 변경이 용이하도록 작성하여야 합니다.

|항목|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)|`list`|`map`, `set`|
|--|--|--|--|
|[컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)의 끝에 데이터 추가가 빈번한 경우|O|O|X|
|빈번한 삽입/삭제|X|O|X|
|랜덤 접근|O|X|O|
|비교적 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)가 작은 경우 빈번한 검색|O|X|O|
|비교적 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)가 큰 경우 빈번한 검색|X|X|O|

# 컨테이너 요소 규칙

[컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)는 요소 삽입/삭제/검색/비교등을 위해 다음 항목들을 지원해야 합니다.

**복사 생성자**

[시퀀스 컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/#%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)의 요소는 기본적으로 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 정의해야 합니다.

```cpp
class A {
public:
    int m_Val;
public:
    explicit A(int val) : m_Val(val) {}
    // 복사 생성자를 구현해야 합니다.
    A(const A& other) : m_Val(other.m_Val) {}
private:
    // 복사 대입 연산자는 필수는 아닙니다.
    A& operator =(const A& other) {
        m_Val = other.m_Val;
        return *this;
    }
};

std::vector<A> v;
v.push_back(A(0)); // vector의 끝에 요소를 추가합니다.
v.push_back(A(1));
```

**알고리즘과의 결합을 위한 연산자**

[알고리즘](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/)과 결합하여 사용할 때에는 [알고리즘](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-algorithm/)에서 사용하는 연산자가 지원되어야 합니다.

예를들어 `sort()`는 요소들의 정렬을 위해 대소 비교 후 대입하므로, [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)와 `<` 연산자를 구현해야 합니다.(*`<` 연산자는 [대소 비교의 논리 조건](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EB%8C%80%EC%86%8C-%EB%B9%84%EA%B5%90%EC%9D%98-%EB%85%BC%EB%A6%AC-%EC%A1%B0%EA%B1%B4)을 충족해야 합니다.*)

```cpp
class A {
public:
    int m_Val;
public:
    explicit A(int val) : m_Val(val) {}
    // 복사 생성자
    A(const A& other) : m_Val(other.m_Val) {}
    // 복사 대입 연산자
    A& operator =(const A& other) {
        m_Val = other.m_Val;
        return *this;
    }
    // 비교 연산자
    bool operator <(const A& other) const {
        return m_Val < other.m_Val;
    }
};

std::vector<A> v;
v.push_back(A(1));
v.push_back(A(0));

// 정렬합니다. 
// 내부적으로 요소의 복사 대입 연산자와 비교 연산자를 사용합니다.
std::sort(v.begin(), v.end()); 
EXPECT_TRUE(v[0].m_Val == 0 && v[1].m_Val == 1); // 크기순으로 정렬됩니다.
```

**연관 컨테이너의 Key**

[연관 컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)의 **Key**는 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)에 삽입시 대소 비교하여 정렬하는데 사용되므로, [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 `<` 연산자를 구현해야 합니다.(*`<` 연산자는 [대소 비교의 논리 조건](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EB%8C%80%EC%86%8C-%EB%B9%84%EA%B5%90%EC%9D%98-%EB%85%BC%EB%A6%AC-%EC%A1%B0%EA%B1%B4)을 충족해야 합니다.*)

```cpp
class A {
public:
    int m_Val;
public:
    explicit A(int val) : m_Val(val) {}
    // 복사 생성자
    A(const A& other) : m_Val(other.m_Val) {}
private:    
    // 복사 대입 연산자는 필수는 아닙니다.
    A& operator =(const A& other) {
        m_Val = other.m_Val;
        return *this;
    }
public:
    // 비교 연산자
    bool operator <(const A& other) const {
        return m_Val < other.m_Val;
    }
};        

std::map<A, std::string> m;

// insert시 내부적으로 key를 비교 연산하여 정렬하여 삽입합니다. 
m.insert(std::make_pair(A(0), "data0")); // map에 key-value 쌍을 추가
m.insert(std::make_pair(A(1), "data1"));
```