---
layout: single
title: "#2. [모던 C++] (C++11~) 개선된 타입과 리터럴(타입 카테고리, using을 이용한 타입 별칭, nullptr, long long, 유니코드 지원, R 리터럴), (C++14~) 이진 리터럴, 숫자 구분자, (C++17~) 16진수 부동 소수점 리터럴, (C++20~) char8_t, 정수에서 2의 보수 표현"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#8] `0`과 `NULL` 보단 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr)를 선호하라.(*[nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr) 참고*)
> * [MEC++#9] typedef 보다 별칭 선언을 선호하라.(*[클래스 템플릿 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B3%84%EC%B9%AD) 참고*)

> * (C++11~) [타입 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%83%80%EC%9E%85-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)를 수립하여 컴파일 타임 프로그래밍이나 [템플릿 메타 프로그래밍](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-meta-programming/)시의 코딩 계약을 강화할 수 있습니다.
> * (C++11~) [using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)이 추가되어 [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 보다 좀 더 직관적인 표현이 가능해 졌습니다.
> * (C++11~) [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr) 리터럴이 추가되어 좀더 타입에 안전한 코딩 계약이 가능해 졌습니다.
> * (C++11~) 최소 8byte 크기를 보장하는 [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#long-long) 타입이 추가되었습니다.
> * (C++11~) [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#long-long)용 정수형 상수인 `ll`, `ull`, `LL`, `ULL` [리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/)이 추가되었습니다.
> * (C++11~) [char16_t, char32_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t)이 추가되어[UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자와 [UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 지원합니다.
> * (C++11~)  [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [char16_t, char32_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t)용 문자 상수를 제공합니다. 
> * (C++11~) [R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행이나 [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)를 좀더 편하게 입력할 수 있습니다.
> * (C++14~) [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c14-%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.
> * (C++14~) [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어  `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있으며, 가독성이 좋아졌습니다.
> * (C++17~) [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0xA.9p11`과 같이 16진수로 실수를 표현할 수 있습니다.
> * (C++17~) [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 1byte 크기의 문자 상수를 제공합니다.
> * (C++20~) [char8_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c20-char8_t)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 지원합니다.
> * (C++20~) [정수에서 2의 보수 범위를 보장](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c20-%EC%A0%95%EC%88%98%EC%97%90%EC%84%9C-2%EC%9D%98-%EB%B3%B4%EC%88%98-%EB%B2%94%EC%9C%84-%EB%B3%B4%EC%9E%A5)합니다.

# 타입 카테고리

클래스/구조체/[공용체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4)는 [메모리 정렬 방식](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)에 따라서 다른 언어와 데이터가 호환 될 수도 있고, [memcpy()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)등으로 쉽게 복사가 가능할 수도 있습니다.

C++11 부터는 이러한 호환성의 체계를 수립하기 위해, 각 타입의 카테고리를 규정하고 해당 카테고리에 속하는지 확인 할 수 있는 `is_trivial<>`, `is_standard_layout<>`, `is_pod<>`를 제공합니다.(*[타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp/mordern-cpp-type_traits/) 참고*)

# 스칼라 타입

하나의 값만 저장하는 기본 타입들입니다.

1. `int`, `char`, `long`, `float`, `double` 등
2. [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)
3. 포인터
4. 멤버에 대한 포인터
5. [nullptr_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr_t)

# Trivial 타입(간단한 타입)

인접한 메모리 영역에 멤버들이 할당되며, [memcpy()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)로 복사 가능한 타입입니다. 멤버들은 `public`, `protected`, `private`로 [접근 지정](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90)될 수 있습니다.

* [스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85)
* Trivial 클래스
   * 사용자 정의 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/), 사용자 정의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 없음
   * 서로 다른 [접근 지정](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90)이 가능함
   * [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 없음
   * 부모 클래스 없음
   * Trivial이 아닌 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 없음
* [Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85)의 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)

# 표준 레이아웃 타입

다른 언어와 통신하는데 유용한 타입으로서, 인접한 메모리 영역에 멤버들이 할당되며, [memcpy()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)로 복사 가능한 타입입니다. 멤버들은 `public`, `protected`, `private` 중 모두 동일한 것으로 [접근 지정](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90)되어야 합니다.

* [스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85)
* 표준 레이아웃 클래스
  * 사용자 정의 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/), 사용자 정의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 있어도 됨
  * 동일한 [접근 지정](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90)이어야 함
  * [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 없음
  * 부모 클래스 없음
  * 모든 멤버는 [표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85) 이어야 함
* [표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85)의 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)

# POD 타입(Plan Old Data)

[Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85)이면서 [표준 레이아웃인 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85)입니다. 메모리 레이아웃은 연속적이며, C언어와 데이터를 직접 교환하여 사용할 수 있습니다.

# 리터럴 타입

[리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)은 컴파일 타임에 상수로 사용될 수 있는 타입입니다.

* [스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85)
* [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)
* [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)의 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)
* [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)
* [집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)
  * 사용자 정의 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/), 사용자 정의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 없으며 모든 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 `public`인 구조체나 클래스
* (C++14~) `void`
  * C++14 이상부터 `void`도 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)이어서 [constexpr 함수](
https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)가 `void`를 리턴할 수 있습니다.

# 집합 타입

[집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)은 여러 데이터를 저장하는 개체입니다.

* [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)
* 구조체/클래스/[공용체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4)
  * 사용자 정의 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/), 사용자 정의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 없음(*[초기화 리스트](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 없음*)
  * 모든 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)가 `public`
  * 부모 클래스가 없음
  * [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 없음

# using을 이용한 타입 별칭

기존에는 타입 별칭을 위해 [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)를 사용했었는데요(*[타입 별칭](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 참고*),

C++11 부터는 [using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)이 추가되었습니다.

[using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)은 다음과 같이 [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)와 별칭의 순서가 다릅니다만, 좀 더 직관적입니다.

```cpp
typedef unsigned long ulong;
using ulong_11 = unsigned long; // typedef와 순서가 반대입니다.
```

[함수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)인 경우는 훨씬 더 직관적입니다.

```cpp
typedef void (*Func)(int); 
using Func_11 = void (*)(int);
```

# 클래스 템플릿 별칭

[using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)은 [클래스 템플릿 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B3%84%EC%B9%AD)도 지원합니다.

[typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)를 사용하여 별칭을 작성하면, [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)의 경우 구체화된 타입으로만 별칭이 작성되어 향후 다른 타입으로 변경할 수 없지만,

```cpp
// typedef는 템플릿의 구체화된 타입만 지원합니다.
typedef std::vector<int> MyVector;

MyVector v; // std::vector<int> 타입입니다. 
MyVector<char> v2; // (X) 컴파일 오류. 다른 타입으로 정의할 수 없습니다.
```

[using](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)을 사용하면 [클래스 템플릿 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B3%84%EC%B9%AD)을 이용해서 여러가지 타입으로 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)를 할 수 있습니다.

```cpp
// using은 템플릿을 지원합니다.
template<typename T>
using MyVector_11 = std::vector<T>; 

MyVector_11<int> v1; // std::vector<int> 타입입니다.
MyVector_11<char> v2; // std::vector<char> 타입입니다.
```

# nullptr

기존에는 [널 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 표현하기 위해 `0`이나 `NULL`(*`#define NULL 0`*)을 사용했는데요(*[널 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고*),

C++11 부터는 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr) 리터럴이 제공됩니다.

```cpp
int* ptr1 = 0;
int* ptr2 = NULL;
int* ptr3_11 = nullptr; // C++11
```

`0`이나 `NULL`은 사실 포인터가 아니라 정수이기 때문에 [오버로딩된 함수 결정](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)시 `int` 타입이 선택됩니다.

다음의 `ptr1`, `ptr2`, `ptr3`는 모두 `int*`여서 `f(int*)`가 호출되는데요,

```cpp
int f(int) {return 1;} // #1
int f(int*) {return 2;} // #2

int* ptr1 = 0;
int* ptr2 = NULL;
int* ptr3_11 = nullptr;

EXPECT_TRUE(f(ptr1) == 2); // int* 이므로 f(int*) 호출
EXPECT_TRUE(f(ptr2) == 2); // int* 이므로 f(int*) 호출
EXPECT_TRUE(f(ptr3_11) == 2); // int* 이므로 f(int*) 호출
```

[auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용한다면 `0`과 `NULL`은 다음처럼 초기값에 따라 `int`와 같은 정수형으로 추론되어(*[auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 참고*), `f(int)`가 호출되는 문제가 있습니다. 포인터인데, 정수로 추론되니 뭔가 사이드 이펙트가 발생할 우려가 있죠. 따라서, [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr)을 사용하시는게 좋습니다.

```cpp
// auto를 사용하면
auto ptr1 = 0; // (△) 비권장. int
auto ptr2 = NULL; // (△) 비권장. long long
auto ptr3_11 = nullptr; // nullptr_t

EXPECT_TRUE(f(ptr1) == 1); // (△) 비권장. f(int) 호출  
EXPECT_TRUE(f(ptr2) == 1); // (△) 비권장. f(int) 호출
EXPECT_TRUE(f(ptr3_11) == 2); // f(int*) 호출
```

또한 다음과 같은 구문을 만났을때 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr)을 사용하는게 가독성이 좋습니다.

```cpp
auto result_11 = Func();
if (result_11 == 0) {} // (△) 비권장. result_11이 정수인지 포인터인지 불명확 합니다.
if (result_11 == nullptr) {} // result_11은 포인터라는 것이 좀더 명확합니다.
```

# nullptr_t

[nullptr_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr_t)는 [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr)을 저장할 수 있는 타입이며, 어떠한 포인터로도 암시적으로 변환될 수 있는 타입입니다. 크기는 `sizeof(void*)`와 동일합니다.(*[decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)참고*)

```cpp
using nullptr_t = decltype(nullptr);
```

# long long

기존 `long`은 시스템 비트수에 따라 변하는데요(*[기본 타입](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/) 참고*),

C++11 부터는 [long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#long-long)을 제공하며 최소 8byte를 보장합니다.

|항목|내용|용량|
|--|--|--|
|`long`|`int`보다 크거나 같은 정수|16bit : 4byte,<br/>32bit : 4byte,<br/>64bit : 8byte|
|[long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#long-long) (C++11~)|`long`보다 크거나 같은 정수|최소 8byte|

```cpp
unsigned long long val_11{18446744073709550592ull}; 
```

# char16_t 와 char32_t

[유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)는 전 세계의 모든 문자에 고유 숫자를 부여한 코드 체계인데요, 기존 `wchar_t`는 시스템에 따라 2byte 또는 4byte로 가변적이어서(*[기본 타입](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/) 참고*), [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C) 처리가 어려웠습니다.

C++11 부터는 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C) 지원을 위해 [UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 저장할 수 있는 [char16_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t)와 [UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 저장할 수 있는 [char32_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t)가 추가되었습니다.

|항목|내용|용량|
|--|--|--|
|`char`|[1byte 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|1byte|
|`wchar_t`|[와이드 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|시스템의 비트수에 따라 다르며, 대부분 2byte 또는 4byte.<br/>Windows는 2byte|
|[char16_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t) (C++11~)|[UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자|`16bit`(2byte) 보다 크거나 같음|
|[char32_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#char16_t-%EC%99%80-char32_t) (C++11~)|[UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자|`32bit`(4byte) 보다 크거나 같음|

> *(C++20~) [char8_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c20-char8_t)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 지원합니다.*

# 유니코드 리터럴

기존에는 [바이트 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)과 [와이드 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)만 지원했는데요(*[문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98) 참고*),

 C++11 부터는 [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9), [UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9), [UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9)의 [유니코드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C) 문자 및 문자열을 지원합니다.(*[UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열은 C++11에 추가됐지만, [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자는 C++17에 추가되었습니다.*) 

|항목|내용|
|--|--|
|[1byte 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|"abc"|
|[와이드 문자열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|L"abc한글"|
|[UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열 (C++11~)|`u8"abc한글"`|
|[UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열 (C++11~)|`u"abc한글"`|
|[UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자열 (C++11~)|`U"abc한글"`|
|[UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자 (C++17~)|`u8'한'`|
|[UTF-16 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자 (C++11~)|`u'한'`|
|[UTF-32 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자 (C++11~)|`U'한'`|

```cpp
char16_t ch_11 = u'한';
const char16_t* str_11 = u"abc한글";
```

# Raw String 리터럴

기존 문자열은 개행을 하기 위해 다음처럼 [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)(`\r\n`)를 사용해야 했는데요(*[이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90) 참고*),

```cpp
const char* str = "abc\r\ndef";
std::cout << str << std::endl;
```

```cpp
abc
def
```

C++11 부터는 `R"()"`은 `()`안에 표기된 그대로를 문자열로 처리해 줍니다. [이스케이프 문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)도 그대로 출력합니다.

```cpp
// 이스케이프 문자와 개행을 소스코드에 기재된 그대로 출력합니다.
const char* str_11 = R"(abc\r\n
def)";     
std::cout << str_11 << std::endl;
```

```cpp
abc\r\n
def
```

`"()"` 자체를 출력할 때에는 파싱 오류가 날 수 있으므로, 다음 예의 `aaa`처럼 임의 구분자를 지정하여 사용할 수 있습니다.

```cpp
// 임의 구분자 aaa 사용
const char* str_11 = R"aaa(abc"()"
def)aaa";     
std::cout << str_11 << std::endl;
```

```cpp
abc"()"
def
```

# (C++14~) 이진 리터럴

`0b`, `0B` 접두어를 이용하여 이진수 상수를 표현할 수 있습니다.

```cpp
int val1_14 = 0b11;
int val2_14 = 0b1111;
int val3_14 = 0B11111111;

EXPECT_TRUE(val1_14 == 0x03);
EXPECT_TRUE(val2_14 == 0x0F);
EXPECT_TRUE(val3_14 == 0xFF);
```

# (C++14~) 숫자 구분자

작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.

큰 숫자인 경우 가독성이 좋아집니다.

```cpp
int val = 1000000;
int val_14 = 1'000'000;

EXPECT_TRUE(val == val_14);
```

# (C++17~) 16진수 부동 소수점 리터럴

기존에는 16진수 정수 [리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/)만 제공했는데요, 

```cpp
int val = 0x1a; // 16진수. x 또는 X. a~f 또는 A~F
```

C++17 부터는 [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)도 제공합니다.

`0x[소수점 이상 16진수값].[소수점 이하 16진수값]p[십진수 지수]`

가수 `p`또는 `P`는 10진수 `2`입니다.

예를들어 `0xA.9p11`은,

1. `A` : `10진수 10`
2. `9` : `1/16 * 9 = 0.5625` 
3. `p11` : `2의 11승 == 2048`

이어서 

`10.5625 * 2 ^ 11 =  21632`

입니다.

```cpp
// A(10진수 10), 9(1/16 * 9 = 0.5625), (p11은 2의 11승 == 2048)
// 즉, 10.5625 * 2 ^ 11 =  21632
float floatVal_17 = 0xA.9p11; // 21632 
EXPECT_TRUE(floatVal_17 == 21632.0);
```

# (C++20~) char8_t

C++20 부터는 [char8_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c20-char8_t)이 추가되어 [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9)을 지원합니다.

`char`와 크기가 같고(*적어도 1byte입니다.*), 부호 처리도 동일하지만, [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 문자를 처리하는 별개의 타입입니다.

그런데, [UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9)에서 영문자는 1byte 이지만, 한글등 다국어 문자는 여러 바이트를 사용할 수도 있죠. 예를 들어 한글 `가`는 `0xEA`, `0xB0`, `0x80`의 3개의 byte가 필요합니다.(*[UTF-8 인코딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9) 참고*)

따라서 [char8_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#c20-char8_t)은 영문자는 저장할 수 있지만, 한글등 다국어 문자는 저장할 수 없습니다. 

```cpp
// UTF-8 에서 영문 1글자는 1byte 입니다.
char8_t en_20 = u8'a';

// UTF-8에서 한글 1글자는 3byte입니다. 
// 따라서 문자 1개를 1byte로는 저장할 수 없습니다.
char8_t kr_20 = u8'가'; // (X) 컴파일 오류
```

그래서, 안타깝지만, 다국어 문자를 저장하려면 다음과 같이 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 저장해야 합니다.

```cpp
char8_t arr_20[] = u8"가"; // 한글 1글자는 UTF-8로 3byte입니다.
EXPECT_TRUE(sizeof(arr_20) == 4); // 널문자 포함하여 4byte입니다.
```

# (C++20~) 정수에서 2의 보수 범위 보장

기존에는 부호있는 정수의 표현 범위를 1의 보수를 보장하였으나, 실제로는 모든 컴파일러가 2의 보수를 표현하였습니다.

C++20 부터는 정수에서 2의 보수 범위를 보장합니다.

|항목|내용|
|--|--|
|1의 보수|1byte로 -127 ~ 127 표현|
|2의 보수|1byte로 -128 ~ 127 표현|


