---
layout: single
title: "#9. [모던 C++ STL] (C++11~) hash"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

`hash`는 각 타입별로 해시값을 리턴하는 함수자 입니다.

1. 리턴되는 해시값은 `std::size_t` 입니다.(4byte보다 크거나 같으며, `int`와 동일한 크기입니다.)
2. 동일한 데이터는 동일한 해시값이 나와야 합니다.
3. 서로 다른 데이터라도 해시값은 충돌될 수 있습니다. 확률적으로는 `1.0 / std::numeric_limits<std::size_t>::max()` 입니다.

STL에서는 `bool`, `int`등의 기본 타입과 `string`등의 STL 타입에 대해 모두 정의되어 있어 이를 사용할 수 있습니다.


```cpp
int a{1};
int b{1};
int c{2};

size_t hashA{std::hash<int>{}(a)};
size_t hashB{std::hash<int>{}(b)};
size_t hashC{std::hash<int>{}(c)};

EXPECT_TRUE(hashA == hashB); // 같은 값은 해시값도 같습니다.
EXPECT_TRUE(hashA != hashC); // 다른값은 해시값도 다릅니다.

std::string str1{"Hello"};
std::string str2{"Hello"};
std::string str3{"Hi"};

size_t hashStr1{std::hash<std::string>{}(str1)};
size_t hashStr2{std::hash<std::string>{}(str2)};
size_t hashStr3{std::hash<std::string>{}(str3)};

EXPECT_TRUE(hashStr1 == hashStr2); // 같은 값은 해시값도 같습니다.
EXPECT_TRUE(hashStr1 != hashStr3); // 다른값은 해시값도 다릅니다.
```

# 해시

일반적으로 비밀번호 등을 저장할때 평문을 그대로 저장하면 데이터 유출시 문제가 발생할 수 있습니다. 그래서 암호화를 하는데요,

암호화는 다음의 두가지로 구분할 수 있습니다.

1. 단방향 : 암호화만 합니다.
2. 양방향 : 암호화하고, 이를 다시 원문으로 복호화 합니다.

일반적으로 비밀번호 등은 단방향만 하면 됩니다. 사용자가 입력한 비밀번호를 암호화해서 데이터베이스에 저장해 두고, 나중에 비밀번호를 입력하면, 다시 암호화해서 암호화된 비밀번호와 비교하면 되니까요. 이런 단방향 암호화를 할때 사용하는 것이 해시 함수이고, 해시 함수의 결과를 Digest라고 합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9c8cda51-00ac-46c0-820c-3e2813a4e9a8)

해시 함수는 동일한 데이터를 암호화하면 반드시 동일한 값이 나와야 하며, SHA(Secure Hash Algorithm)-256(해시값을 256bit(32byte)로 변환함)이 가장 대표적인 알고리즘이라고 합니다.

# 해시 보완

Digest로부터 원래 데이터를 추출하는 것은 어렵지만, 원래 데이터와 Digest간의 매핑 테이블인 Rainbow Table은 어렵지 않게 만들 수 있습니다. 그냥 이것 저것 아무 데이터로 해시 함수 돌려보고 기록해 두면 되니까요. 많은 해커들이 이런 정보들을 사용하고 있다고 합니다.

이를 보완하는 방법은 다음과 같은 것이 있습니다.

1. 해시 함수를 여러번 호출합니다.

2. 원래 데이터에 추가 데이터를 강제로 붙여서 해시값을 구합니다.(추가 데이터를 소금을 친다는 의미로 Salted 해시 라고 합니다.)

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/67694d12-fbb2-4edc-a8b1-ff1cb142092f)

# 헤시 컨테이너

1. `vector`는 순차 탐색을 하기 때문에 O(N)의 시간이 걸리고,
2. `set`이나 `map` 등은 이진 탐색을 하기 때문에 O(N)이 걸리고,
3. 해시 컨테이너는 해시값을 인덱스로 해서 찾으면 되므로 O(1)의 시간이 걸립니다. 

다만, 해시값을 그대로 컨테이너의 키값으로 사용하면, 해시 컨테이너가 모든 해시값을 저장할 수 있을 정도로 무한히 커야 하기 때문에, 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/284fcae0-8cc4-4ee9-b85c-fdd5d8a0ac82)

해시값이 적당히 분포되도록 해시 키값을 구하여 사용해야 합니다.

```
해시 키값 = 해시값 % 컨테이너 공간
```

컨테이너의 공간이 작다면 해시 키값이 동일해질 확률이 높아집니다. 극단적으로 공간이 2개 밖에 없다면, 해시 키값은 0 ~ 1 사이의 값이 되고, 대부분의 데이터가 같은 공간내에 위치하게 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/c9de3147-81a6-439b-b9bc-ea6411f5e316)

 이를 해시 충돌이라 하는데요,

해시 충돌이 나면, 같은 공간내에서 순차 탐색이던, 이진 탐색을 이용하여 검색해야 하므로, 해시 충돌이 최소화되고, 해시 키값이 골고루 분포될 수 있도록 구성해야 합니다.