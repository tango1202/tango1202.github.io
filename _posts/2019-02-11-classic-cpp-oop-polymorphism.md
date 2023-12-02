---
layout: single
title: "#11. [고전 C++ 개체 지향] 다형성"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

다형성은 개체의 속성이나 기능(함수)이 상황에 따라 여러가지 형태로 변할 수 있는 성질을 말합니다.

[함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 통해 전달된 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 따라 다른 기능을 실행하고, 상속 관계를 통해 자식 개체들이 다른 동작을 수행하게 됩니다.

C++에서 다형성을 위해 제공하는 기능들은 다음과 같습니다.

|항목|내용|
|--|--|
|[함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)|전달된 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 따라 다른 함수가 호출됩니다.<br/>* [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고<br/>* [오버로딩 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고<br/>* [오버로딩 함수 탐색 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9-%ED%95%A8%EC%88%98-%ED%83%90%EC%83%89-%EA%B7%9C%EC%B9%99) 참고|
|상속|부모 개체를 재활용하여, 새로운 자식 클래스를 만들어 확장합니다.<br/>* [is-a 관계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#is-a-%EA%B4%80%EA%B3%84) 참고<br/>* [has-a 관계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#has-a-%EA%B4%80%EA%B3%84) 참고<br/>* [상속 강제](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C) 참고<br/>* [상속 제한](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C) 참고|
|추상 클래스|자식 개체의 일반 개념을 제공합니다.<br/>* [추상 클래스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4) 참고<br/>* [public Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고|
|인터페이스|기능 스펙을 제공합니다.<br/>* [인터페이스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4) 참고<br/>* [protected Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고|
|[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)|상속 관계에 따라 다르게 사용합니다.<br/>* [다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8) 참고<br/>* [public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고<br/>* [public Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고<br/>* [protected Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고|
|[가상 함수 오버라이딩](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9)|부모 개체의 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 재구현 합니다.<br/>* [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 참고<br/>* [가상 함수 테이블](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable) 참고<br/>* [순가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 참고<br/>* [순가상 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%88%9C%EA%B0%80%EC%83%81-%EC%86%8C%EB%A9%B8%EC%9E%90) 참고|
|[가상 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EA%B0%80%EC%83%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|개체 타입에 따라 자기 자신을 복제 생성하는 함수입니다.<br/>* [가상 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EA%B0%80%EC%83%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고|
|[템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)|개체 타입에 따라 다르게 동작하는 함수와 클래스|
|dynamic_cast|상속 관계가 있는 개체간의 변환을 제공합니다.<br/>* [명시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 참고<br/>* [Runtime Type Info(RTTI)와 형변환](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#runtime-type-infortti%EC%99%80-%ED%98%95%EB%B3%80%ED%99%98) 참고|
|[Runtime Type Info(RTTI)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#runtime-type-infortti%EC%99%80-%ED%98%95%EB%B3%80%ED%99%98)|개체의 타입 정보를 제공합니다.<br/>* [Runtime Type Info(RTTI)와 형변환](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#runtime-type-infortti%EC%99%80-%ED%98%95%EB%B3%80%ED%99%98) 참고|


