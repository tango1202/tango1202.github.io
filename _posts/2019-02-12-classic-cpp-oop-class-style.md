---
layout: single
title: "#12. [고전 C++ 개체 지향] 클래스 스타일"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 유틸리티 클래스

* 클래스의 public 함수는 최소화 할 것
* 필수 함수와 유틸리티를 구분하라. 

```cpp
class Date { 
    public: string toString() const; };class Date { public: static string toString(const Date& d); };class DateUtil { public: static string toString(const Date& d); };namespace DateUtil { string toString(const Date&d); }
```

클래스의 멤버 데이터를 읽어와 문
자열로 만든다.

Date의 멤버를 get하는 함수가 있다
면 굳이 toString을 멤버 함수로 만
들 필요는 없다. 이미 있는 public 함수로 구현할 수
있는 유틸리티 함수군을 분리하여
클래스를 간결히 유지하라.

멤버함수보단 비멤버 비프렌드 함수가 낫다.(EC++ 3판 23항목) ->캡슐화 정도가 높다.(private 멤버 접근 함수 갯수를 늘리지 않음)

# 구체 타입

구체적인 동작, 구성요소들이 구현
된 클래스
파생클래스를 고려하지 않음
have a, use a 관계로 재사용

# 추상 타입

추상화된 동작 정의
클래스간 연관을 느슨하게. 단일 개념을 구현하라(단위 전략)
흔히 우리가 말하는 인터페이스

# 노드

추상 타입이나 어느정도의 데이터
타입을 취하는 클래스. 혹은 파생클래스에 대한 약간의 서
비스

# 동작 실행자

action, command, 함수자

# 인터페이스(TPL 1005page, 용어의
혼동)

일종의 class 아답터. 인터페이스
충돌 해결

# handle

auto_ptr 같은 데이터 객체 관리자.

# 유틸리티

클래스에서 제공하는 기능을 활용
하여 새로운 기능을 만든다.

# traits

취급특성. 객체의 타입에 따라 다르게 동작할
때 해당 동작에 대한 정의. 혹은 공통 동작을 위한 타입명 정의
(binary_function등)

# 거대 클래스

여러가지 기능들이 혼합된 클래스. 여러 인터페이스가 공존하며, 모든
기능이 서비스 되나, 원하는 서비스
를 쉽게 찾을 수 없다.