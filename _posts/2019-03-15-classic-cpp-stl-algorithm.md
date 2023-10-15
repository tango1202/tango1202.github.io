---
layout: single
title: "#15. [고전 C++ STL] 알고리즘"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * (C++17~) [대부분의 알고리즘에서 병렬 작업을 지원](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-parallel-algorithm/)하는 오버로딩 버전이 추가되었고, `seq, par, par_unseq, unseq`으로 병렬 실행 정책을 지정할 수 있습니다.

# 개요

알고리즘은 컨테이너의 요소를 처리하는 표준화된 방법입니다. 만약 컨테이너 멤버 버전이 있다면, 멤버 버전이 효율이 더 좋습니다.

다음은 시퀀스 안의 요소들에 대해 `f()`를 실행하는 `for_each()` 함수입니다.(사용하는 STL 라이브러리에 따라 다르겠지만, 대략 다음과 같이 정의되어 있습니다.)

```cpp
template<typename InputIterator, typename Function>
Function for_each(InputIterator first, InputIterator last, Function f) {
    // first~last 직전까지 요소에 대해 f() 호출
    for (; first != last; ++first) { 
        f(*first);
    }
    return f; 
}
```

다음은 `for_each()`를 사용한 예입니다. `Setter()`함수를 이용하여 각 요소의 값을 `10`으로 변경합니다.

```cpp
// 10으로 세팅하는 함수
void Setter(int& val) {
    val = 10;
}

// 0으로 초기화 되어 생성
std::vector<int> v(3);

EXPECT_TRUE(v[0] == 0 && v[1] == 0 && v[2] == 0);

// 모든 요소에 대해 Setter 적용
std::for_each(v.begin(), v.end(), Setter);
EXPECT_TRUE(v[0] == 10 && v[1] == 10 && v[2] == 10); 
```

알고리즘의 종류에 대해서는 모던 C++ STL의 [알고리즘](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-algorithm/) 을 참고하기 바랍니다.

