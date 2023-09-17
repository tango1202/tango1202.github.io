---
layout: single
title: "#2. [모던 C++] (C++11~) 타입 형식(스칼라 타입, Trivial 타입, 표준 레이아웃 타입, POD 타입, 리터럴 타입, 집합 타입)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 타입 형식 체계를 수립하여 컴파일 타임 프로그래밍이나 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)시의 코딩 계약을 강화할 수 있습니다.

# 개요

클래스/구조체/공용체는 [메모리 정렬 방식](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)에 따라 다른 언어와 데이터가 호환 될 수도 있고, `memcpy()` 로 쉽게 복사가 가능할 수도 있습니다. 

C++11 부터는 각 타입의 형식을 규정하고 해당 범주에 속하는지 확인 할 수 있는 `is_trivial<>`, `is_standard_layout<>`, `is_pod<>`를 제공합니다.([타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp/mordern-cpp-type_traits/) 참고)

# 스칼라 타입

하나의 값만 저장하는 기본 타입들입니다.

1. `int`, `char`, `long`, `float`, `double` 등
2. 열거형
3. 포인터
4. 멤버에 대한 포인터
5. `std::nullptr_t`

# Trivial 타입(간단한 타입)

인접한 메모리 영역에 멤버들이 할당되며, `memcpy()`로 복사 가능한 타입입니다. 멤버들은 `public`, `protected`, `private`로 접근 지정될 수 있습니다.

* 스칼라 타입
* trivial 클래스
   * 사용자 정의 생성자, 소멸자 없음
   * 서로 다른 접근 지정이 가능함
   * 가상 함수 없음
   * 부모 클래스 없음
   * trivial이 아닌 멤버 변수가 없음
* trivial 타입의 배열

# 표준 레이아웃 타입

다른 언어와 통신하는데 유용한 타입으로서, 인접한 메모리 영역에 멤버들이 할당되며, `memcpy()`로 복사 가능한 타입입니다. 멤버들은 `public`, `protected`, `private` 중 모두 동일한 것으로 접근 지정되어야 합니다.

* 스칼라 타입
* 표준 레이아웃 클래스
  * 사용자 정의 생성자, 소멸자가 있어도 됨
  * 동일한 접근 지정이어야 함
  * 가상 함수 없음
  * 부모 클래스 없음
  * 모든 멤버는 표준 레이아웃 이어야 함
* 표준 레이아웃 타입의 배열

# POD 타입(Plan Old Data)

travial 이면서 표준 레이아웃인 타입입니다. 메모리 레이아웃은 연속적이며, C언어와 데이터를 직접 교환하여 사용할 수 있습니다.

# 리터럴 타입

**리터럴 타입**은 컴파일 타임에 상수로 사용될 수 있는 타입입니다.

* void
* 스칼라 타입
* 참조자
* 리터럴 타입의 배열
* 람다 함수
* 집합 타입
  * 사용자 정의 생성자, 소멸자가 없으며 모든 멤버 변수가 `public`인 구조체나 클래스

# 집합 타입

**집합 타입** 은 타입이 같거나 다른 여러 데이터를 저장하는 개체입니다.

* 배열
* 구조체/클래스/공용체
  * 사용자 정의 생성자, 소멸자가 없음
  * 모든 멤버 변수가 `public`
  * 부모 클래스가 없음
  * 가상 함수 없음
  * 멤버 변수 초기화 없음