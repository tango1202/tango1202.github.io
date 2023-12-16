---
layout: single
title: "#8. [Legacy C++ 가이드] 배열(Array)"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 속도 성능에 영향이 없다면 유지보수시 확장성을 위하여 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)보다는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)를 사용하라. 동적 요소 할당을 지원하며, 여전히 엑세스는 빠르다.
> * [new[]](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete[]](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 쌍을 준수하라.

> **모던 C++**
> * (C++11~)[array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 추가되어 기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)를 제공합니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.

# 개요

[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)은 타입이 같은 여러 데이터를 집합으로 묶어 연속적인 메모리에 관리할 수 있게 해줍니다. 각각의 데이터는 **[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 요소**라고 하고요, `void` 형과 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [함수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)는 **[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 요소**가 될 수 없습니다.

|항목|내용|
|--|--|
|정의| `int arr[5];`|
|동적 생성|`int* p = new int[5];`|
|동적 소멸|`delete[] p;`|
|초기화|`int arr[3] = {1, 2, 3};`,<br/>`char str[] = "abc";`|
|초기화를 이용한 크기 유추|`int arr[] = {1, 2, 3};`|

# 배열 요소의 갯수

[배열 요소의 갯수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)는 [sizeof()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)를 이용하여 다음처럼 구할 수 있습니다.

```cpp
int arr[3];
EXPECT_TRUE(sizeof(arr) / sizeof(int) == 3); // 배열의 전체 크기 / 배열 요소 크기
```

[템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)을 이용하는 방법도 있습니다. [함수 템플릿 인수 추론](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)을 참고하세요.

# 배열 초기화

[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 정의시 [배열 요소의 갯수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)가 유추될 수 있어야 합니다. 

* [배열 요소의 갯수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)를 명시적으로 지정하거나, 
* [중괄호 집합 초기화를 이용한 배열 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94)에서 초기화 항목을 1개 이상 지정해 주면 됩니다.(*갯수보다 초기화 값을 적게 제공하면, 나머지는 [자동 제로 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94) 됩니다.*)

```cpp
int arr1[3]; // (△) 비권장. int 형 3개 정의. 초기화 되지 않아 비권장 
// int arr1[]; // (X) 컴파일 오류. 갯수가 지정되지 않음. 오류
// int arr1[] = {}; // (X) 컴파일 오류. 갯수가 지정되지 않았고, 중괄호 집합초기화도 비었음. 오류
int arr2[] = {0, 1, 2}; // (O) 중괄호 집합 갯수만큼 초기화
int arr3[3] = {}; // (O) 3개 모두 0으로 초기화
int arr4[3] = {0, 1, }; // (O) 갯수가 적거나 같아야 함. 모자라면 0

EXPECT_TRUE(arr2[2] == 2);
EXPECT_TRUE(arr3[0] == 0 && arr3[1] == 0 && arr3[2] == 0);
EXPECT_TRUE(arr4[2] == 0);
```

문자 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)의 경우 특별히 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 초기화 할 수 있습니다. 이때 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)의 마지막 요소에 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)가 추가됩니다. 따라서 `"abc"`의 배열 갯수는 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)를 포함하여 `4`이며, 문자열의 길이는 `3`입니다.

```cpp
char str1[] = "abc"; // (O) {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str1[0] == 'a');
EXPECT_TRUE(str1[1] == 'b');
EXPECT_TRUE(str1[2] == 'c');
EXPECT_TRUE(str1[3] == '\0'); // 널문자가 추가됨
EXPECT_TRUE(sizeof(str1) / sizeof(char) == 4); // 배열 갯수는 널문자를 포함하여 4
EXPECT_TRUE(strlen(str1) == 3); // 문자열의 길이는 3

wchar_t str2[] = L"abc"; // (O) {L'a', L`b`, L'c', L'\0'};
EXPECT_TRUE(str2[0] == L'a');
EXPECT_TRUE(str2[1] == L'b');
EXPECT_TRUE(str2[2] == L'c');
EXPECT_TRUE(str2[3] == L'\0'); // 널문자가 추가됨
EXPECT_TRUE(sizeof(str2) / sizeof(wchar_t) == 4); // 배열 갯수는 널문자를 포함하여 4
EXPECT_TRUE(wcslen(str2) == 3); // 문자열의 길이는 3
```

# 배열 대입

[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)끼리는 대입할 수 없습니다. [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)의 각 요소를 일일이 복사해야 합니다.(*포인터형 변수를 이용하여 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)을 대입 받는 것은 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)을 대입하는 것이 아니라 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)을 가리키는 것입니다.*)

```cpp
int arr1[] = {1, 2, 3};

// int arr2[] = arr1; // (X) 컴파일 오류. 배열끼리는 대입되지 않습니다.
int arr2[3];
for (int i = 0; i < sizeof(arr1) / sizeof(int); ++i) { // 배열의 각 요소를 복사합니다.
    arr2[i] = arr1[i];
} 
EXPECT_TRUE(arr2[0] == 1 && arr2[1] == 2 && arr2[2] == 3);

int* ptr = arr1; // 포인터형 변수가 배열을 가리킵니다.

ptr[1] = 20;
EXPECT_TRUE(arr1[1] == 20);
```

# 배열과 vector

속도 성능에 영향이 없다면 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 보다는 표준 템플릿 라이브러리의 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)를 사용하는 편이 유지보수시 확장성 측면에서 좋습니다. 동적 요소 할당을 지원하며, 랜덤 접근을 하므로 여전히 엑세스는 빠릅니다.

[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)와 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)간 코딩 호환성도 나쁘지 않습니다. 정의와 초기화만 다르고, 요소 접근하는 방법이나 요소 주소를 얻는 것은 동일합니다.

|항목|[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)|[vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)|
|--|--|--|
|정의|`int a[5];`|`vector<int> v(5);`|
|초기화|`int a[5] = {};`|X|
|요소 접근|`a[0]`|`v[0]`|
|요소 주소|`&a[0]`|`&v[0]`|

단, [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)는 동적 할당을 사용하기 때문에, [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)보다는 메모리 할당 속도가 현저히 느립니다.(*[배열, array, vector 속도 비교](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/#%EB%B0%B0%EC%97%B4-array-vector-%EC%86%8D%EB%8F%84-%EB%B9%84%EA%B5%90) 참고*)

> *(C++11~) [array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-array/)가 추가되어 기존 [C스타일의 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 연속된 메모리를 사용하는 [컨테이너](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-container/)를 제공합니다. [C스타일 배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)처럼 컴파일 타임에 크기가 결정되어 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 할당되므로, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 할당되는 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/) 보다 성능이 좋습니다.*

# typedef로 타입 재정의

[typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)로 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 타입을 재정의할 수 있습니다.

```cpp
typedef int MyArray[5]; 
MyArray arr; // int arr[5]; 와 동일 
arr[0] = 10; // 첫번째 요소에 값 대입
EXPECT_TRUE(arr[0] == 10);
```

# 배열 동적 생성과 소멸

[new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)와 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 대신 [new[]](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 와 [delete[]](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 생성하고 소멸시킵니다. 간단한 규칙입니다만, 많이들 실수하는 부분입니다. [new[]](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)-[delete[]](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 쌍을 꼭 지켜주세요.

```cpp
int* p = new int[5]; // int[5] 배열 생성
delete[] p; // 소멸. delete가 아님에 유의. delete p;를 하면 배열 요소 갯수만큼 소멸자를 호출하지 않고 한번만 호출합니다.
```
