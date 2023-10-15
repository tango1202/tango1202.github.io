---
layout: single
title: "#11. [고전 C++ STL] 이터레이터(반복자)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이터레이터는 전위 증가 연산자를 사용하라.
> * for를 이용하여 이터레이터를 순차 탐색 할때 `itr < endItr`보다는 `itr != endItr`을 사용하라.

> **모던 C++**
> * (C++11~) (C++11~) [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for) 가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.
> * (C++11~) [move_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/) 아답터는 이터레이터가 가리키는 요소를 이동 연산 합니다.
> * (C++11~) [begin(), end()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)가 추가되었습니다.
> * (C++14~) [rbegin(), rend(), cbegin(), cend(), crbegin(), crend()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)가 추가되었습니다.
> * (C++17~) [size(), empty(), data()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC) 가 추가되었습니다.



# 개요

이터레이터(반복자)는 컨테이너에서 지금 가리키는 요소나 다음 요소를 찾아가는 방법을 제공합니다.

# 반개방구조

이터레이터의 시작(`begin()`)과 끝(`end()`)은 다음 그림과 같이 반개방 구조(`[begin - end)` 로 표기합니다.)를 가집니다.

`begin()`은 요소의 시작 항목을 가리키고, `end()`는 마지막 요소의 다음 위치를 가리킵니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/06a41744-f5a9-4dfa-b50d-05df31b812ba)

이에 따라, 다음과 같이 컨테이너에 요소가 있는지 없는지 확인할 수 있습니다.

```cpp
// 컨테이너가 텅 비었습니다.
container.begin() == container.end();

// 컨테이너에 요소가 있습니다.
container.begin() != container.end();
```

# 이터레이터를 이용한 요소 접근

이터레이터는 다음의 연산자를 통해 요소에 접근합니다.

|항목|내용|
|--|--|
|`*`, `->`|이터레이터가 가리키는 요소|
|`++`|다음 요소를 가리키는 이터레이터|

`++`를 이용하여 이터레이터를 사용하는 경우에는 꼭 전위형을 사용해야 합니다. 후위형을 사용하면, 증가 시키기 전의 값을 복제해서 리턴하기 때문에 불필요한 복사 부하가 생깁니다.([증감 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A6%9D%EA%B0%90-%EC%97%B0%EC%82%B0%EC%9E%90)와 [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고)

또한, `for` 문의 조건문에서 `itr < endItr`이 아니라 `itr != endItr`을 사용합니다. 이는 랜덤 접근이 가능한 이터레이터만 `<`이 가능하기 때문입니다. 향후 다른 컨테이너로 변경하더라도 수정을 최소화하기 위해 관습적으로 `<` 보다는 `!=` 사용하는게 좋습니다.


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

> *(C++11~) [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for) 가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.*

# 이터레이터 카테고리

이터레이터는 그 특성에 따라 5가지 카테고리로 분류됩니다. 컨테이너는 요소 처리 특성에 맞게 이터레이터를 사용합니다.

|항목|내용|컨테이너|
|--|--|--|
|`input`|읽기 전용<br/>`++`, `*`, `->`, `=`, `==`, `!=`, 복사 생성자||
|`output`|쓰기 전용<br/>`++`, `*`, `=`, 복사 생성자||
|`forward`|단방향 탐색<br/>`input`과 연산자 동일||
|`bidirectional`|양방향 탐색<br/>`forward` 에서 `--`추가|`list`<br/>`map`|
|`random access`|랜덤 접근 탐색<br/> `bidirectional`에서 `+`, `-`, `+=`, `-=`, `<`, `>`, `<=`, `>=`, `[]` 추가|`vector`|

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5d7a3f38-3c6f-4321-a6d0-b5ddc75792db)

# 역방향 이터레이터

역방향 이터레이터는 `++`시 요소의 끝에서 처음으로 이동하는 이터레이터 입니다.

```cpp
std::vector<int> v(5); 

// 순방향
{
    std::vector<int>::iterator itr = v.begin(); // 요소의 시작
    std::vector<int>::iterator endItr = v.end(); // 요소의 끝
    for (int i = 0; itr != endItr; ++itr, ++i) { 
        *itr = i; 
    }
    EXPECT_TRUE(v[0] == 0 && v[1] == 1 && v[2] == 2 && v[3] == 3 && v[4] == 4);
}
// 역방향
{
    std::vector<int>::reverse_iterator itr = v.rbegin(); // 요소의 끝
    std::vector<int>::reverse_iterator endItr = v.rend(); // 요소의 시작
    for (int i = 0; itr != endItr; ++itr, ++i) { 
        *itr = i; 
    }
    EXPECT_TRUE(v[0] == 4 && v[1] == 3 && v[2] == 2 && v[3] == 1 && v[4] == 0);
}
```

# 삽입 이터레이터

다음과 같이 `Fill()` 함수를 구현했다고 합시다. 

전달된 `Iterator`의 

1. `++`을 이용하여 다음 요소로 이동하고,
2. `*`을 이용하여 실제 요소를 참조하며,
3. `=`을 이용하여 실제 요소에 값을 대입합니다.

```cpp
// Iterator가 가르키는 곳부터 n개를 value로 채웁니다. 
// Iterator는 ++, *, = 연산자를 구현해야 합니다.
template<typename Iterator>
void Fill(Iterator first, size_t n, const int& value) {
    for (size_t i = n; 0 < n; --n, ++first) {
        *first = value;
    }
}
```

다음과 같이 사용할 수 있습니다.

```cpp
std::vector<int> v(5); // 5개의 요소 생성(클래스면 생성자를 호출함) 
Fill(v.begin(), 5, 7); // v[0] ~ v[4] 에 7 대입.
```

하지만 다음은 `vector`가 할당되지 않아 오류가 발생합니다.

```cpp
std::vector<int> v; // 빈 벡터
Fill(v.begin(), 5, 7); // (X) 예외 발생. v가 5개 할당되지 않았다면 예외 발생 
```

`Fill()` 함수에서는 이터레이터를 반복하면서 `*first = value;` 로 실제 요소에 값을 대입하기 때문에, 벡터에 요소가 없어 예외를 발생하게 됩니다.

이런 경우 값을 대입하지 않고, 컨테이너에 `push_back()` 하도록 연산자를 재정의한 것을 삽입 이터레이터라고 합니다.

구현 방법은 다음과 같습니다.

1. 값 생성자에서는 Container를 전달받습니다.
2. 복사 생성자에서는 컨테이너 참조자를 복사합니다.
3. 복사 대입 연산자는 사용하지 않으므로 `private`로 정의합니다.
4. 복사 대입 연산자중 컨테이너 값 타입을 전달하면, `push_back()`을 하여 추가합니다.
5. 무조건 끝에 추가하는 것이므로, `++`은 아무 동작 안하게 합니다.
6. `*` 시 자기 자신을 리턴하여 `*first = value;`시 #4가 호출되게 합니다.

```cpp
template<typename ContainerT>
class BackInsertIterator {
    ContainerT& m_Container;
public:
    // 값 생성자에서는 Container를 전달받습니다.
    explicit BackInsertIterator(ContainerT& container) : // #1
        m_Container(container) {}

    // 복사 생성자에서는 컨테이너 참조자를 복사합니다.
    BackInsertIterator(const BackInsertIterator& other) : // #2
        m_Container(other.m_Container) {}
private:   
    // 복사 대입 연산자는 사용하지 않습니다.
    BackInsertIterator& operator =(const BackInsertIterator& other) {return *this;} // #3

public:
    // = 에서 컨테이너 값 타입을 전달하면, push_back()을 하여 추가합니다.
    BackInsertIterator& operator =(const typename ContainerT::value_type& value) { // #4
        m_Container.push_back(value);
        return *this;
    }

    // 무조건 끝에 추가하므로 ++는 별다른 동작하지 않습니다.
    BackInsertIterator& operator ++() { // #5
        return *this;
    }

    // * 시 자기 자신을 리턴하여 *first = value;시 #4가 호출되게 합니다.
    BackInsertIterator& operator *() { //# 6
        return *this;
    }
};
```

다음과 같이 테스트 합니다.

```cpp
std::vector<int> v; 
Fill(BackInsertIterator<std::vector<int>>(v), 5, 7); // 현 컨테이너 뒤 5 개에 7 삽입. BackInsertIterator에서 operator = 을 push_back() 으로 구현

EXPECT_TRUE(v[0] == 7 && v[1] == 7 && v[2] == 7 && v[3] == 7 && v[4] == 7);
```

표준에서는 `back_inserter()` 유틸리티 함수로 삽입 이터레이터를 생성해 줍니다.

```cpp
std::vector<int> v; 
Fill(std::back_inserter(v), 5, 7); // 표준 유틸리티 함수 사용

EXPECT_TRUE(v[0] == 7 && v[1] == 7 && v[2] == 7 && v[3] == 7 && v[4] == 7);
```

# 이터레이터 아답터

역방향 이터레이터나 삽입 이터레이터와 같이 이터레이터의 고유 기능인 `*`, `->`, `++`을 재구현하여 다르게 동작하는 이터레이터들입니다. 자세한 사항은 모던 C++ STL의 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/) 를 참고하기 바랍니다.





