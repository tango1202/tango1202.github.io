---
layout: single
title: "#11. [모던 C++ STL] (C++11~) unordered_map, unordered_multimap, unordered_set, unordered_multiset"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `forward_list`는 단방향 리스트여서 양방향 리스트인 `list`보다 요소 관리 공간을 작게 차지하며, `push_front()`로 요소의 앞쪽 방향으로 리스트를 구성합니다.

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
2. `set`이나 `map` 등은 이진 탐색을 하기 때문에 O(logN)이 걸리고,
3. 해시 컨테이너는 해시값(Digest)을 인덱스로 해서 찾으면 되므로 O(1)의 시간이 걸립니다. 

다만, 해시값(Digest)을 그대로 컨테이너의 키값으로 사용하면, 해시 컨테이너의 버킷(Bucket)은 모든 해시값(Digest)을 저장할 수 있을 정도로 아주 아주 많아야 하기 때문에(C++에서는 `std::numeric_limits<std::size_t>::max()`), 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/2b14ccb1-dd32-40b7-9474-685cdeaa82c8)

해시값(Digest)이 적당히 분포되도록 해시 키값을 구하여 사용해야 합니다.

```
해시 키값 = 해시값 % 컨테이너 버킷(Bucket) 갯수
```

컨테이너의 버킷(Bucket)이 적다면 해시 키값이 동일해질 확률이 높아집니다. 극단적으로 버킷(Bucket)이 2개 밖에 없다면, 해시 키값은 0 ~ 1 사이의 값이 되고, 대부분의 데이터가 같은 버킷(Bucket)내에 위치하게 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/eb5289a5-88f0-4cce-918f-5800ac8538db)

 이를 해시 충돌이라 하는데요,

해시 충돌이 나면, 같은 버킷(Bucket)내에서 순차 탐색이던, 이진 탐색을 이용하여 검색해야 하므로, 해시 충돌이 최소화되고, 해시 키값이 골고루 분포될 수 있도록 구성해야 합니다.
