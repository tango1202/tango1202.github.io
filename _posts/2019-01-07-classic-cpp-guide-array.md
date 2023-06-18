---
layout: single
title: "#7. [고전 C++ 가이드] 배열(Array)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 배열보다는 vector를 사용하라. 동적 요소 할당을 지원하며, 여전히 엑세스는 빠르다.

**개요**

|항목|내용|
|--|--|
|정의| `int arr[5];`|
|동적 생성|`int* p = new int[5];`|
|동적 소멸|`delete[] p;`|
|초기화|`int arr[3] = {1, 2, 3};`|
|초기화를 이용한 크기 유추|`int arr[] = {1, 2, 3};`|

배열은 타입(자료형)이 같은 여러 데이터를 집합으로 묶어 연속적인 메모리에 관리할 수 있게 해줍니다. 단, `void` 형과 참조자와 함수 포인터를 데이터로 가질 순 없습니다. 

타입(자료형)이 같은 여러 데이터 집합은 배열 보다는 표준 템플릿 라이브러리의 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)를 사용하는 편이 좋습니다. 동적 요소 할당을 지원하며, 여전히 엑세스는 빠릅니다.

코딩 호환성도 나쁘지 않습니다. 정의와 초기화만 다르고, 요소 접근이나 요소 주소 부분은 동일합니다.

|항목|배열|`vector`|
|--|--|--|
|정의|`int a[5];`|`vector<int> v(20);`|
|초기화|`int a[5] = {};`|X|
|요소 접근|`a[0]`|`v[0]`|
|요소 주소|`&a[0]`|`&v[0]`|

**typedef로 자료형 재정의**

`typedef`를 사용하여 하기와 같이 정의할 수 있습니다.

```cpp
// 자료형 재정의
typedef int MyArray[5]; 
MyArray arr; // int arr[5]; 와 동일 
```

**배열 동적 생성과 삭제**

`new`와 `delete` 대신 `new[]` 와 `delete[]`로 생성하고 소멸시킵니다.

```cpp
int* p = new int[5]; // int[5] 배열 생성
delete[] p; // 삭제. delete가 아님에 유의. delete p;를 하면 int[0] 만 소멸됨
```

