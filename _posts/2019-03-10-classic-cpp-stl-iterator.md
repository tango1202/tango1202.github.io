---
layout: single
title: "#10. [고전 C++ STL] 이터레이터(반복자)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이터레이터는 전위 증가 연산자를 사용하라.

# 개요

이터레이터(반복자)는 컨테이너에서 지금 가리키는 요소나 다음 요소를 찾아가는 방법을 제공합니다.

# 반개방구조

이터레이터의 시작(`begin()`)과 끝(`end()`)은 다음 그림과 같이 반개방 구조를 가집니다.

`begin()`은 요소의 시작 항목을 가리키고, `end()`는 마지막 요소의 다음 위치를 가리킵니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/06a41744-f5a9-4dfa-b50d-05df31b812ba)

이에 따라, 하기 연산이 가능합니다.

```cpp
// 컨테이너가 텅 비었습니다.
container.begin() == container.end();

// 컨테이너에 요소가 있습니다.
container.begin() != container.end();
```

# 이터레이터를 이용한 요소 접근

이터레이터는 다음의 연산자를 통해 요소에 접근합니다.

|항목|내용|
|`*`, `->`|이터레이터가 가리키는 요소|
|`++`|다음 요소를 가리키는 이터레이터|

`++`를 이용하여 이터레이터를 사용하는 경우에는 꼭 전위형을 사용해야 합니다. 후위형을 사용하면, 증가 시키기 전의 값을 복제해서 리턴하기 때문에 불필요한 복사 부하가 생깁니다.([증감 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A6%9D%EA%B0%90-%EC%97%B0%EC%82%B0%EC%9E%90)와 [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고)

```cpp
std::vector<int> v(5); // 5개의 요소 생성(클래스면 생성자를 호출함)
EXPECT_TRUE(v[0] == 0 && v[1] == 0 && v[2] == 0 && v[3] == 0 && v[4] == 0);

std::vector<int>::iterator itr = v.begin(); // 요소의 시작
std::vector<int>::iterator endItr = v.end(); // 마지막 요소의 다음
for (int i = 0; itr != endItr; ++itr, ++i) { // 이터레이터는 복사 부하가 없도록 전위 증가 연산 사용
    *itr = i; // 요소에 값 할당
}

itr = v.begin();
for (int i = 0; itr != endItr; ++itr, ++i) { 
    EXPECT_TRUE(*itr == i);
}       

EXPECT_TRUE(v[0] == 0 && v[1] == 1 && v[2] == 2 && v[3] == 3 && v[4] == 4);


// 랜덤 접근이 가능하다면, 포인터 연산이랑 비슷한 형태로 접근      
*(v.begin() + 1) = 10; 
EXPECT_TRUE(v[1] == 10); 
```

# 이터레이터 카테고리

이터레이터는 그 특성에 따라 5가지 카테고리로 분류됩니다. 컨테이너는 요소 처리 특성에 맞게 이터레이터를 사용합니다.

|항목|내용|컨테이너|
|--|--|--|
|`input`|읽기 전용<br/>`++`, `*`, `->`, `=`, `==`, `!=`, 복사 생성자||
|`output`|쓰기 전용<br/>`++`, `*`, `=`, 복사 생성자||
|`forward`|단방향 탐색<br/>`input`과 연산자 동일||
|`bidirectional`|양방향 탐색<br/>`forward` 에서 `--`추가|`list`<br/>`map`|
|`random access`|임의 접근 탐색<br/> `bidirectional`에서 `+`, `-`, `+=`, `-=`, `<`, `>`, `<=`, `>=`, `[]` 추가|`vector`|

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5d7a3f38-3c6f-4321-a6d0-b5ddc75792db)

# 삽입 이터레이터

# 역방향 이터레이터

