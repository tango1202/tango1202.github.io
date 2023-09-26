---
layout: single
title: "#20. [모던 C++ STL] (C++11~) unordered_map, unordered_multimap, unordered_set, unordered_multiset"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `unordered_map`, `unordered_multimap`, `unordered_set`, `unordered_multiset` 은 정렬되지 않은 컨테이너로서, 해시값(Digest)을 사용하는 해시 컨테이너 입니다.

# 개요

기존의 `map`, `multimap`, `set`, `multiset`은 이진 트리 탐색을 하다보니 정렬등이 자동으로 이루어 졌는데요,

`unordered_map`, `unordered_multimap`, `unordered_set`, `unordered_multiset` 은 정렬되지 않은 컨테이너로서, 해시값(Digest)을 사용하는 해시 컨테이너 입니다.

|항목|내용|**Key** 조건|
|--|--|--|
|`unordered_map` (C++11~)|**Key** - **Value** 쌍으로 관리하며,  `pair`를 사용합니다.([pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/) 참고)<br/>**Key**의 해시값(Digest)으로 요소를 탐색합니다.(O(1))<br/>삽입되는 **Key**는 유일합니다.<br/>첨자 연산을 지원합니다.|`hash()` 함수자 구현<br/>`==` 구현|
|`unordered_multimap` (C++11~)|`map`과 동일하며 중복 **Key**를 허용합니다.<br/>첨자 연산을 지원 하지 않습니다.|`hash()` 함수자 구현<br/>`==` 구현|
|`unordered_set` (C++11~)|**Key**만 요소로 삽입됩니다.<br/>**Key**의 해시값(Digest)으로 요소를 탐색합니다.(O(1))<br/>삽입되는 **Key**는 유일합니다.|`hash()` 함수자 구현<br/>`==` 구현|
|`unordered_multiset` (C++11~)|`set`과 동일하며 중복 **Key**를 허용합니다.|`hash()` 함수자 구현<br/>`==` 구현|

# 해시

일반적으로 비밀번호 등을 저장할때 평문을 그대로 저장하면 데이터 유출시 문제가 발생할 수 있습니다. 그래서 암호화를 하는데요,

암호화는 다음의 두가지로 구분할 수 있습니다.

1. 단방향 : 암호화만 합니다.
2. 양방향 : 암호화하고, 이를 다시 원문으로 복호화 합니다.

일반적으로 비밀번호 등은 단방향만 하면 됩니다. 사용자가 입력한 비밀번호를 암호화해서 데이터베이스에 저장해 두고, 나중에 비밀번호를 입력하면, 다시 암호화해서 암호화된 비밀번호와 비교하면 되니까요. 이런 단방향 암호화를 할때 사용하는 것이 해시 함수이고, 해시 함수의 결과를 해시값(Digest)이라고 합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2acb4e61-94ce-456c-b0c3-8b1df4f007ba)

해시 함수는 동일한 데이터를 암호화하면 반드시 동일한 해시값(Digest)이 나와야 하며, SHA(Secure Hash Algorithm)-256(해시값을 256bit(32byte)로 변환함)이 가장 대표적인 알고리즘입니다.

# 해시 보완

해시값(Digest)으로부터 원래 데이터를 추출하는 것은 어렵지만, 원래 데이터와 해시값(Digest)간의 매핑 테이블인 Rainbow Table은 어렵지 않게 만들 수 있습니다. 그냥 이것 저것 아무 데이터로 해시 함수 돌려보고 기록해 두면 되니까요. 많은 해커들이 이런 정보들을 사용하고 있다고 합니다.

이를 보완하는 방법은 다음과 같은 것이 있습니다.

1. 해시 함수를 여러번 호출합니다.

2. 원래 데이터에 추가 데이터를 강제로 붙여서 해시값(Digest)을 구합니다.(추가 데이터를 소금을 친다는 의미로 Salted 해시 라고 합니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d09989e0-edf0-4373-afaa-17056b5c3d0c)

# 해시 컨테이너

해시 컨테이너는 해시값(Digest)을 이용해서 요소를 관리하는 컨테이너입니다. 요소를 저장하는 공간을 버킷(Bucket)이라 합니다.

속도 성능을 보면,

1. `vector`는 순차 탐색을 하기 때문에 O(N)의 시간이 걸리고,
2. `set`이나 `map` 등은 이진 트리 탐색을 하기 때문에 O(logN)이 걸리고,
3. 해시 컨테이너는 해시값(Digest)을 인덱스로 해서 찾으면 되므로 O(1)의 시간이 걸립니다. 

다만, 해시값(Digest)을 그대로 컨테이너의 키값으로 사용하면, 해시 컨테이너의 버킷(Bucket)은 모든 해시값(Digest)을 저장할 수 있을 정도로 아주 아주 많아야 하기 때문에(C++에서는 `std::numeric_limits<std::size_t>::max()`), 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2b14ccb1-dd32-40b7-9474-685cdeaa82c8)

해시값(Digest)이 적당히 분포되도록 해시 키값을 구하여 사용해야 합니다.

```
해시 키값 = 해시값 % 컨테이너 버킷(Bucket) 갯수
```

컨테이너의 버킷(Bucket)이 적다면 해시 키값이 동일해질 확률이 높아집니다. 극단적으로 버킷(Bucket)이 2개 밖에 없다면, 해시 키값은 0 ~ 1 사이의 값이 되고, 대부분의 데이터가 같은 버킷(Bucket)내에 위치하게 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d6eda121-11d5-461a-946f-a936fd237175)

 이를 해시 충돌이라 하는데요,

해시 충돌이 나면, 같은 버킷(Bucket)내에서 순차 탐색이나 이진 탐색을 이용하여 검색해야 하므로, 해시 충돌이 최소화되는게 좋습니다.

따라서, 해시 키값이 골고루 버킷(Bucket)에 분포될 수 있도록 해시 키값을 계산하고, 버킷(Bucket)의 갯수도 적당량을 만들어야 합니다.

# unordered_map 멤버 함수

다음 멤버 함수들은 `map`과 동일합니다.

|항목|내용|
|--|--|
|`[]` (C++11~)|주어진 Key의 Value를 구합니다.|
|`at()` (C++11~)|주어진 Key의 Value를 구합니다.|
|`contains()` (C++20~)|(작성중)|
|`begin()` (C++11~), `end()` (C++11~), `cbegin()` (C++11~), `cend()` (C++11~)|이터레이터<br/>`map`은 `rbegin()`, `rend()`, `crbegin()`, `crend()`를 추가로 제공합니다.|
|`empty()` (C++11~)|컨테이너가 비었는지 확인합니다.|
|`size()` (C++11~)|컨테이너의 요소 갯수를 리턴합니다.|
|`max_size()` (C++11~)|컨테이너가 저장할 수 있는 최대 요소 갯수를 리턴합니다.|
|`clear()` (C++11~)|모든 요소를 지웁니다.|
|`erase()` (C++11~)|주어진 위치의 요소를 삭제합니다.|
|`erase_if()` (C++17~)|(작성중)|
|`swap()` (C++11~)|두 컨테이너의 내부 데이터를 바꿔치기 합니다.|
|`insert()` (C++11~)|주어진 위치 앞에 요소를 추가합니다.|
|`insert_range()` (C++23~)|(작성중)|
|`insert_or_assign()` (C++17~)|(작성중)|
|`emplace()` (C++11~), `emplace_hint()` (C++11~)|요소 개체 생성을 위한 인수(`Key`, `Value`)를 전달하여 컨테이너 내에서 요소 개체(`pair<Key, Value>`)를 생성한 뒤 삽입합니다.|
|`extract()` (C++17~)|(작성중)|
|`merge()` (C++17~)|(작성중)|
|`try_emplace()` (C++17~)|(작성중)|
|`count()` (C++11~)|주어진 Key인 요소 갯수를 리턴합니다.|
|`find()` (C++11~)|주어진 Key인 요소를 리턴합니다.| 
|`equal_range()` (C++11~)|주어진 Key인 요소들을 리턴합니다.<br/>`map`은 `lower_bound()`, `upper_bound()`를 추가로 제공합니다.|
|`==` (C++11~)<br/>`!=` (~C++20)|(작성중)|

해시 컨테이너는 다음 멤버 함수를 추가로 제공합니다.

|항목|내용|
|--|--|
|`begin(size_type)` (C++11~), `end(size_type)` (C++11~)<br/>`begin(size_type)` (C++11~), `end(size_type)` (C++11~)|버킷(Bucket) 이터레이터|
|`bucket_count()` (C++11~)|버킷 갯수|
|`max_bucket_count()` (C++11~)|최대 버킷 갯수|
|`bucket_size()` (C++11~)|주어진 버킷의 요소 갯수|
|`bucket()` (C++11~)|주어진 Key의 버킷|
|`load_factor()` (C++11~)|버킷당 평균 요소 수|
|`max_load_factor()` (C++11~)|버킷당 최대 평균 요소 수|
|`rehash()` (C++11~)|버킷 갯수를 조정하고, 기존 요소들을 재배치|
|`reserve()` (C++11~)|요소 수에 맞게 버킷 갯수를 조정하고, 기존 요소들을 재배치|

요소 수에 맞게 버킷 갯수는 내부적으로 조정됩니다.

```cpp
std::unordered_map<int, std::string> m;

// 예약된 요소 수에 맞게 버킷은 자동 조정 됩니다.
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 1개
m.reserve(2);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 2개
m.reserve(3);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 3개
m.reserve(4);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 5개
m.reserve(5);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 5개
m.reserve(6);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 7개
m.reserve(7);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 7개
m.reserve(8);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 11개
m.reserve(9);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 11개
m.reserve(100);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 103개
m.reserve(1000);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 1031개
m.reserve(10000);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 10273개        
    m.reserve(100000);
std::cout<<"bucket count : "<<m.bucket_count()<<std::endl; // 107897개  
```

기존 `map`과 사용법은 동일합니다.

```cpp
std::unordered_map<int, std::string> m;

// 기본적인 사용 방식은 map과 동일합니다.
m.insert(std::make_pair(0, "data0"));
m.insert(std::make_pair(1, "data1"));
m[0] = "changed data";
EXPECT_TRUE(m[0] == "changed data");
```

# unordered_map의 Key

[컨테이너 요소 규칙](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9A%94%EC%86%8C-%EA%B7%9C%EC%B9%99) 에서 개체를 `set`이나 `map`의 Key로 사용하려면, 복사 생성자와 `bool operator <(const T& other);`을 구현해야 한다고 말씀드렸는데요,

`unordered_map`과 `unordered_set`의 Key는 

1. 복사 생성자를 구현해야 하고, 대입 연산자는 불필요하며,
2. 대소 비교를 하지 않으므로, `bool operator <(const T& other);` 구현은 필요 없고,
3. 버킷(Bucket) 내에서 탐색하기 위해 `bool operator ==(const T& other);` 구현이 필요하고,
4. 해당 개체의 해시값(Digest)를 구하기 위해 `hash()`함수자를 구현하여 `unordered_map` 정의시 전달해야 합니다.

```cpp
class A {
private:
    int m_Val;
    std::string m_Str;
public:
    A(int val, const char* str) : 
        m_Val{val}, 
        m_Str{str} {}
    A(const A& other) = default; // 복사 생성자 필요
    A& operator =(const A& other) = delete; // 대입 연산자 불필요
public:
    // == 비교 연산자 필요
    bool operator ==(const A& other) const {
        if (m_Val != other.m_Val) return false;

        return m_Str == other.m_Str;
    }

    // < 비교 연산자 불필요
    // bool operator <(const A& other) const {
    //     if (m_Val < other.m_Val) return true;
    //     if (other.m_Val < m_Val) return false;

    //     return m_Str < other.m_Str;
    // }

    int GetVal() const {return m_Val;}
    const std::string& GetStr() const {return m_Str;}
}; 

// 해시 함수. 반드시 const 함수이어야 합니다.
struct AHash {
    std::size_t operator ()(const A& obj) const {
        size_t h1{std::hash<int>{}(obj.GetVal())};
        size_t h2{std::hash<std::string>{}(obj.GetStr())};

        return h1 ^ (h2 << 1);
    }
}; 

// A 개체의 해시 함수를 전달합니다.
std::unordered_map<A, std::string, AHash> m;

// insert시 내부적으로 key를 비교 연산하여 정렬하여 삽입합니다. 
m.insert(std::make_pair(A{0, "a"}, "data0")); 
m.insert(std::make_pair(A{1, "b"}, "data1"));
```   