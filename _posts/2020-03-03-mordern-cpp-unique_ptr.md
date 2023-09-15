---
layout: single
title: "#33. [모던 C++] (C++11~) std::unique_ptr"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `std::unique_ptr`을 추가하여 소유권 이전용 스마트 포인터인 `std::auto_ptr`의 문제점을 보완하였습니다.

# 개요

기존 `std::auto_ptr`은 복사/대입시 소유권을 이전하고, 소멸시 관리하는 포인터를 `delete`하는 스마트 포인터입니다.([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/) 참고)

하지만, 다음 문제로 인해 C++11에서 deprecate 되었습니다.

1. 배열을 `delete[]`가 아닌 `delete`로 삭제합니다.(이러면 배열 요소들이 제대로 소멸되지 않습니다. [개체 생성/소멸과 배열 생성/소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8%EA%B3%BC-%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 참고)
2. 대입 연산자에서 소유권을 이전합니다.(의도한 코드입니다만, 이게 암시적으로 동작하면서 런타임 오류가 많이 발생합니다.)

C++11 부터는 상기 문제를 보완한 `std::unique_ptr`이 제공됩니다.

`std::auto_ptr`과 동일하게 소유권을 이전하는 포인터이며, 다음이 개선되었습니다.

1. 일반 포인터는 `delete`하고, 배열은 `delete[]`합니다.
2. 복사 생성자와 `operator =(const T&)`는 제공하지 않고, 이동 생성자와 `operator =(const T&&)`만 제공합니다. 즉 이동 연산만 제공합니다.

다음은 사용예 입니다.

1. `std::unique_ptr<T[]>` 와 같이 배열도 관리할 수 있으며,
2. `c = d;` 대신 `c = std::move(d);`를 하여 소유권을 이동시킵니다.
   
```cpp
class T {
public:
    ~T() {std::cout<<"T : Destructor"<<std::endl;}    
};

std::unique_ptr<T> a(new T); // 단일 개체. delete를 호출하여 1개가 소멸됩니다.
std::unique_ptr<T[]> b(new T[2]); // 배열 개체. delete[] 를 호출하여 2개가 소멸됩니다.

std::unique_ptr<int> c(new int{0});
std::unique_ptr<int> d(new int{1});

// 소유권 이전으로 c는 d의 값을 갖고, d는 nullptr 이 됩니다.
// c = d; // (X) 컴파일 오류.
c = std::move(d); // 소유권 이전시 이동 연산을 사용합니다.
EXPECT_TRUE(*c == 1 && d == nullptr);
```