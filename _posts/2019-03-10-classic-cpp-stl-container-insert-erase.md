---
layout: single
title: "#10. [고전 C++ STL] 컨테이너 요소 삽입, 삭제"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * (C++11~) [컨테이너의 initializer_list 초기화](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/#c11-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-initializer_list-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 초기값 입력이 간편해 졌습니다. 
> * (C++11~) [emplace() 계열 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)들이 추가되어 요소 삽입시 [완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 이용하여 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 내에서 요소 개체를 직접 생성할 수 있으며, 불필요한 복제본을 생성하지 않습니다.

# 개요

컨테이너에 요소 추가/삽입/삭제 방법은 컨테이너의 특성에 따라 다릅니다.

|항목|[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)|`list`|`map`|내용|
|--|--|--|--|--|
|뒤에 추가|O|O|X|[연관 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)는 추가후 정렬되므로, 뒤에 추가할 수 없음|
|삽입|O|O|O|시퀀스 컨테이너는 어느 위치에 삽입할지 지정해야 하지만, [연관 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)는 **Key** 값으로 정렬하므로 지정할 필요 없음. 또한 [연관 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)는 동일한 **Key** 값이면 삽입에 실패하므로 성공/실패 여부를 리턴함|
|삭제|O|O|O||
|`[]`연산|O|X|O||


# vector 의 삽입과 삭제

[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)는 

1. `push_back()`을 통해 뒤에 추가할 수 있으며,
2. `insert()`로 특정 위치에 삽입할 수 있고,
3. 이터레이터가 랜덤 접근되어 `v.begin() + 1`로 삽입 위치를 지정할 수 있으며,
4. 이터레이터나 `[]`을 통해 인덱스를 지정하여 요소의 값에 접근 할 수 있고,
5. `erase()`를 통해 요소를 삭제 할 수 있습니다.

```cpp
std::vector<int> v;

// 뒤에 추가
v.push_back(0);
v.push_back(1);

// 지정한 위치 앞에 삽입
std::vector<int>::iterator result = v.insert(
    v.begin() + 1, // 1번째 요소 앞에 삽입
    10
);
EXPECT_TRUE(*(v.begin()) == 0 && *(++v.begin()) == 10 && *(++(++v.begin())) == 1); // 이터레이터를 통한 접근
EXPECT_TRUE(v[0] == 0 && v[1] == 10 && v[2] == 1); // [] 연산자를 이용하여 value 값 조회 지원
EXPECT_TRUE(v.size() == 3); // 요소 갯수 3개
EXPECT_TRUE(*result == 10); // 삽입한 요소를 리턴함

// 지정 요소 삭제
result = v.erase(result);
EXPECT_TRUE(v[0] == 0 && v[2] == 1);
EXPECT_TRUE(v.size() == 2); // 요소 갯수 2개
EXPECT_TRUE(*result == 1); // 삭제한 요소의 다음 요소를 리턴함
```

> *(C++11~) [컨테이너의 initializer_list 초기화](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/#c11-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%9D%98-initializer_list-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 초기값 입력이 간편해 졌습니다.*

# list 의 삽입과 삭제

`list`는 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)와 달리 이터레이터가 렌덤 접근되지 않습니다. 따라서 이터레이터의 `++` 연산자를 이용하여 접근합니다. 그외 `push_back()`, `insert()`, `erase()`는 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)와 동일합니다. 

```cpp
std::list<int> l;
l.push_back(0);
l.push_back(1);

// 지정한 위치 앞에 삽입
std::list<int>::iterator result = l.insert(
    ++l.begin(), // 1번째 요소 앞에 삽입, 랜덤 접근 불가하여 ++ 사용
    10
);
EXPECT_TRUE(*(l.begin()) == 0 && *(++l.begin()) == 10 && *(++(++l.begin())) == 1); // 이터레이터를 통한 접근
EXPECT_TRUE(l.size() == 3); // 요소 갯수 3개
EXPECT_TRUE(*result == 10); // 삽입한 요소를 리턴함

// 지정 요소 삭제
result = l.erase(result);
EXPECT_TRUE(*(l.begin()) == 0 && *(++l.begin()) == 1);
EXPECT_TRUE(l.size() == 2); // 요소 갯수 2개
EXPECT_TRUE(*result == 1); // 삭제한 요소의 다음 요소를 리턴함
```

# map 의 삽입과 삭제

`map`은 연관 컨테이너로서 요소가 삽입될때마다 정렬을 수행합니다. 이에 따라 `push_back()`을 지원하지 않으며, `insert()`시 위치를 지정할 필요가 없습니다. 또한 동일 **Key**인 값을 삽입하면, 삽입되지 않기 때문에 `insert()`에서 성공/실패 여부를 리턴합니다.

```cpp
// iterator : 성공시 삽입된 요소, 실패시 동일 키값인 기존 요소
// bool : 성공/실패 여부
std::pair<iterator, bool> insert(_Pair&& __x);
```

```cpp
std::map<int, std::string> m;

// (X) 컴파일 오류. map은 정렬하는 것이라 뒤에 추가를 제공하지 않음
// m.push_back(std::make_pair(0, "data1")); 

// 삽입. 삽입시 key 값으로 정렬됨
std::pair<std::map<int, std::string>::iterator, bool> result = m.insert(std::make_pair(1, "data1"));
result = m.insert(std::make_pair(0, "data0")); 

EXPECT_TRUE((*(m.begin())).first == 0 && (*(m.begin())).second == "data0"); // key 값으로 정렬되어 있음
EXPECT_TRUE((*(++m.begin())).first == 1 && (*(++m.begin())).second == "data1"); // 이터레이터를 통한 접근
EXPECT_TRUE(m[0] == "data0" && m[1] == "data1"); // [] 연산자를 이용하여 value 값 조회 지원

EXPECT_TRUE(m.size() == 2); // 요소 갯수 2개
EXPECT_TRUE((*result.first).first == 0 && (*result.first).second == "data0"); // 삽입한 요소를 리턴함
EXPECT_TRUE(result.second == true); // 삽입 성공

// 동일 key 삽입
result = m.insert(std::make_pair(0, "data2")); 
EXPECT_TRUE((*result.first).first == 0 && (*result.first).second == "data0"); // 기존 동일키 요소를 리턴함
EXPECT_TRUE(result.second == false); // 삽입 실패

// 지정 요소 삭제
std::map<int, std::string>::iterator eraseResult = m.erase(result.first);
EXPECT_TRUE((*(m.begin())).first == 1 && (*(m.begin())).second == "data1"); 
EXPECT_TRUE(m.size() == 1); // 요소 갯수 1개
EXPECT_TRUE((*eraseResult).first == 1); // 삭제한 요소의 다음 요소를 리턴함
```

# 컨테이너의 원본 관리

컨테이너는 삽입된 요소의 복제본을 관리합니다. 만약 원본 개체를 관리하고 싶다면, 포인터로 만들어야 합니다. 그러면 원본 개체의 포인터가 복제되며, 동일한 개체를 가리키게 됩니다.

다음 예에서 `ptrVector`는 원본 개체의 포인터를 관리하며, 원본 개체인 `a`를 수정했을때 `ptrVector[0]`도 수정된 값인 것을 확인할 수 있습니다.

```cpp
class A {
    int m_Val;
public:
    explicit A(int val) : m_Val(val) {}
    int GetVal() const {return m_Val;}
    void SetVal(int val) {m_Val = val;}
};

std::vector<A> v;
A a(1);

v.push_back(a);
EXPECT_TRUE(a.GetVal() == v[0].GetVal());

a.SetVal(2);
EXPECT_TRUE(a.GetVal() != v[0].GetVal()); // vector는 복제본을 저장했기 때문에 a를 수정했다고 값이 변하지는 않습니다.

std::vector<A*> ptrVector; // 포인터를 관리합니다.
ptrVector.push_back(&a);
EXPECT_TRUE(a.GetVal() == ptrVector[0]->GetVal());

a.SetVal(3);
EXPECT_TRUE(a.GetVal() == ptrVector[0]->GetVal()); // vector는 포인터의 복제본을 저장했기 때문에 동일한 a를 가리킵니다.
```

> *(C++11~) [emplace() 계열 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/#c11-emplace-emplace_back-emplace_front-emplace_hint-%EC%82%BD%EC%9E%85)들이 추가되어 요소 삽입시 [완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 이용하여 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/) 내에서 요소 개체를 직접 생성할 수 있으며, 불필요한 복제본을 생성하지 않습니다.*
