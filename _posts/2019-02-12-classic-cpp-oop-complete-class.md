---
layout: single
title: "#12. [고전 C++ 개체 지향] 완전한 클래스"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** 구현하라.
> > * [단일 책임 원칙(Single Responsibility Principle)](https://tango1202.github.io/principle/principle-single-responsibility/)을 준수하여 사용하기 쉽게 만들어라.
> > * [암시적 정의를 차단](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98-%EC%B0%A8%EB%8B%A8)하여 의도한 동작만 하게 하라.
> > * [최소 public](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%B5%9C%EC%86%8C-public)으로 구현하여 사용하기 쉽게 하라.
> > * [완전한 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90)로 작성하여 사용하기 쉽고, 에외에 안전하게 하라.
> > * [완전한 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%ED%95%A8%EC%88%98)로 코딩 계약과 [상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)을 만들어 사용자 실수를 최소화 하라.
> * [소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81-%EC%B0%A8%EB%8B%A8)과 복사 부하가 없도록 구현하라.
> * [예외에 안전](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-safe/)하게 구현하라.(*[예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시 이전 상태를 복원하라.*)
> * 새로운 타입을 설계하듯 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/), [형변환 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98), 연산자의 가시성을 조정하고, `vitual` 여부를 결정하라.

> **모던 C++**
> * (C++11~) [우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 지원하며, [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다. [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)와 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 암시적으로 생성됩니다.
> * (C++11~) [default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.
> * (C++11~) [explicit 형변환 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/)가 추가되어 [명시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 할 수 있습니다.
> * (C++11~) [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#final)이 추가되어 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 더이상 [오버라이딩](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 못하게 할 수 있고, 강제적으로 [상속을 제한](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C)할 수 있습니다.

# 개요

클래스는 [단일 책임 원칙(Single Responsibility Principle)](https://tango1202.github.io/principle/principle-single-responsibility/)에 따라 단 하나의 책임만 갖도록 설계하고, 단단한 **코딩 계약**에 의해 ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** 구현해야 합니다.(*[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 참고*)

# 암시적 정의 차단

컴파일러가 은근슬쩍 암시적으로 정의한 것들은 의도한대로 동작하지 않을 수도 있고, 현재는 의도한 대로 동작하지만, 누군가가 기능 추가나 리팩토링하면서 바뀔 수 있습니다.

따라서, 현재 사용하지 않는다면, 모두 차단하십시오.

|항목|내용|
|--|--|
|[암시적 기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)|[접근 지정자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90) `private`또는 `protected` 사용<br/>또는 다른 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 정의|
|[암시적 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|[접근 지정자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90) `private`또는 `protected` 사용|
|[암시적 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|[접근 지정자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90) `private`또는 `protected` 사용|
|[암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)|[암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)와 호환되도록 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 정의(*스마트 포인터([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)를 사용*)|
|[형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)|[explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)로 정의하여 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 차단|

> *(C++11~) [우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 지원하며, [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다. [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)와 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 암시적으로 생성되므로 사용하지 않는다면 차단해야 합니다.*<br/>
> *(C++11~) [default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다. `private`보다는 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)를 사용하는 편이 더 낫습니다.*


# 최소 public

가시성이 최소화되어 접근할 수 있는 항목이 적을 수록 사용하기 쉽습니다.

|항목|내용|
|--|--|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|모두 `private`|
|[멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)|외부에서 사용하는 것만 `public`|
|[상속](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/)|부모 개체의 `public`을 외부로 노출해야 하는 경우만 `public` 상속|
|[PImpl 이디엄](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-pimpl/)|구현의 상세 정보를 은닉|

# 완전한 생성자

개체가 생성될때 완전한 상태로 생성합니다. 

완전하지 않은 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)는 다음과 같은 문제가 있습니다.

1. 별도로 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)를 호출해야 하므로 사용하기 번거롭습니다.(*[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 참고*)
2. 기본값으로 대충 생성후 값을 세팅하면 불필요한 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산 오버헤드가 발생합니다.(*[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 참고*)
3. 개체 사용자는 개체 내부 구조를 완전히 파악해야만 제대로 사용할 수 있습니다. **코딩 계약** 을 투명하게 하여 ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** 구현해야 합니다.(*[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 참고*)
4. 불완전하게 생성된 개체를 사용했을때 발생하는 버그는 예측하기 힘듭니다.
5. 불완전하게 생성된 개체에 별도 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)를 호출하여 완전하게 만드는 중에 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, 이미 생성된 [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/) 처리를 위해 소멸시켜야 합니다. 혹시나 이미 이 개체를 참조하는 곳이 있다면, 처리가 곤란합니다.(*[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 참고*)

따라서,
1. 개체 생성에 필요한 모든 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)에서 나열하고,   
2. 생성중 오류가 발생하면 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜 그동안 만들어 둔건 소멸시켜 버려야 합니다.

|항목|내용|
|--|--|
|[인자 나열](https://tango1202.github.io/principle/principle-explicit-dependencies/)|생성할 때 필요한 모든 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 나열|
|[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)|[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)로 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 초기화|
|시스템 종속성이 높은 개체|[생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 `private`로 만들고 별도의 `Create()`함수 제공(*[생성자 접근 차단 - private 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A0%91%EA%B7%BC-%EC%B0%A8%EB%8B%A8---private-%EC%83%9D%EC%84%B1%EC%9E%90) 참고*)|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|스마트 포인터(*[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)와 같은 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)등에서 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했을때 자동 소멸되도록 함|

# 완전한 함수

함수 구현은 다음 사항을 준수하여야 합니다.

1. [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)와 마찬가지로 필요한 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 모두 전달하여야 합니다. **코딩 계약** 을 투명하게 하여 ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** 구현해야 합니다.(*[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 참고*)
2. [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 전달이나 함수 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 전달시 복사 부하를 최소화 해야 합니다.
3. [상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)에 위배되지 않도록 작성해야 합니다.
4. 함수내에서 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면 그동안 만들어 둔건 소멸시켜 버려야 합니다.

|항목|내용|
|--|--|
|[인자 나열](https://tango1202.github.io/principle/principle-explicit-dependencies/)|함수에 필요한 모든 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 나열|
|[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 복사 부하|기본 자료형인 경우 값 복사로, 클래스 타입인 경우 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 작성(*[Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98) 참고*)|
|[리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 복사 부하|[리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)가 가능하도록 값 타입으로 리턴<br/>* 기본 자료형인 경우 값 타입<br/>* 생성한 개체인 경우 값 타입(*[값 타입 리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%92-%ED%83%80%EC%9E%85-%EB%A6%AC%ED%84%B4%EA%B0%92) 참고*)<br/>* 클래스 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)인 경우 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)(*[Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98) 참고*)|
|[상수성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 수정하지 않는다면, [상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)로 작성하고, 상수 참조를 리턴([Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98) 참고)<br/>[논리적 상수성](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-logical-const/)인 것은 최대한 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않도록 구현|
|함수내 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)|[스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여 획득된 자원은 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)가 벗어났을때 자동 소멸되도록 함(*[RAII와 Holder 와 Restorer - 자원 획득과 안전한 소멸(복원)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)*)|

# 소유권 분쟁 차단

[포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 사용시에는 [소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 발생합니다.

[소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 발생하지 않도록 스마트 포인터(*[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 사용하여,

1. 소유권 이전을 하거나([auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/)),
2. 깊은 복제를 하거나,
3. 자원을 공유하거나(*[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/)*),
4. 유일한 자원으로 대체해서 사용

하여야 합니다.

|항목|내용|
|--|--|
|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|[복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)시 소유권을 처리할 수 있는 스마트 포인터 사용(*[복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)참고*)|
|[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|[복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산시 소유권을 처리할 수 있는 스마트 포인터 사용(*[복사 대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고*)|

# 자원 획득은 초기화

획득된 자원은 반드시 소멸되어야 합니다.(*[RAII(Resource Acquisition Is Initialization)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 참고*) [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 명시적으로 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 하거나 스마트 포인터(*[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 이용할 수 있습니다.

|항목|내용|
|--|--|
|[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)에 스마트 포인터(*[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 사용하여, [암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)와 호환되어 자동 소멸되도록 하거나, 명시적으로 [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 호출|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|스마트 포인터(*[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)를 사용하여 [암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)에서 자동 소멸되도록 함(*[포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 참고*)|

# 예외 안전에 좋은 클래스 설계

[예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하기 전의 상태로 돌아가야 합니다. 개체의 일부 내용만 복원하는게 아니라, 완전히 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 전의 상태로 되돌아 가야 합니다. (*[예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/) 참고*) 

따라서, 예외에 대해 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)을 해주어야 하며, 다음을 준수하시기 바랍니다.

1. [완전한 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성하여 생성중 오류가 발생하면 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜 그동안 만들어 둔건 소멸시켜 버리고,
2. [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산중 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않도록 [nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94)을 이용해서 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 연산을 구현하고, 
3. 함수 실행중 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90) 메카니즘에 맞게 이전 상태로 되돌리고,
4. [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)가 꼬이지 않게 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서는 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않도록 구현해야 합니다.

|항목|내용|
|--|--|
|[생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)|[완전한 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90)로 구현하고, 생성중 오류가 발생하면 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시킴.|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|스마트 포인터(*[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)와 같은 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)등에서 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)했을때 자동 소멸되도록 함|
|[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 1개인 경우 스마트 포인터(*[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 사용하고, 2개 이상이면 [swap을 이용한 예외 보증 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 사용|
|[nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94)|`swap()` 구현시 복사 부하나 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)이 없도록, [포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)로 구현(*[nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94)과 [PImpl 이디엄](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-pimpl/) 참고*) 
|함수내 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)|[스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여 획득된 자원은 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 벗어났을때 자동 소멸되도록 함(*[RAII와 Holder 와 Restorer - 자원 획득과 안전한 소멸(복원)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)*)|
|[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)|기본적으로 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)없이 정리하되, 예외 방출의 소지가 있으면 `Release()`와 같은 별도의 정리 함수 제공(*[소멸자에서 예외 방출 금지](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90%EC%97%90%EC%84%9C-%EC%98%88%EC%99%B8-%EB%B0%A9%EC%B6%9C-%EA%B8%88%EC%A7%80) 참고*)|

# 형변환 차단

[암시적인 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)은 제공하지 않습니다. 필요하다면 [명시적으로 형변환 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)를 제공합니다.

|항목|내용|
|--|--|
|[형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)|정의하지 않음|
|[형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)|[explicit로 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)|
|[명시적 형변환 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)|형변환이 꼭 필요하다면, 명시적으로 구현|

> *(C++11~) [explicit 형변환 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/)가 추가되어 [명시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 할 수 있습니다.*

# 부모 개체의 생성자와 복사 대입 연산자

상속 특성에 맞게 `protected`나 `private`으로 제공합니다.

|항목|내용|
|--|--|
|[기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)|[상속 강제](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C)가 필요하므로 [protected 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%83%81%EC%86%8D-%EC%A0%84%EC%9A%A9-%EA%B8%B0%EB%B0%98-%ED%81%B4%EB%9E%98%EC%8A%A4---protected-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용합니다.|
|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|[인터페이스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)라면 사용못하게 `private`로 막고, [추상 클래스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)라면 상황에 따라 `protected`나 `private`를 사용하고, 필요하면 `Clone()`[가상 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EA%B0%80%EC%83%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 구현합니다.(*[가상 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EA%B0%80%EC%83%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고*)|
|[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|사용하지 못하게 `private`로 막습니다.(*[부모 개체의 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고*)|

# 부모 개체의 소멸자

상속 특성에 맞게 적합한 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 사용합니다.

|항목|내용|
|--|--|
|부모 개체로 사용하지 않는 개체|상속하지 않음<br/>[public Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 사용(*[상속 제한](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C) 참고*)|
|[is-a 관계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#is-a-%EA%B4%80%EA%B3%84)|[다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 하려면 [public Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 사용|
|[has-a 관계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#has-a-%EA%B4%80%EA%B3%84)|[protected Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 사용|
|[추상 클래스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)|[다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)을 하려면 [public Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#public-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 사용<br/>[다형 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)이 필요 없으면 [protected Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 사용|
|[인터페이스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)|[protected Non-Virtual 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#protected-non-virtual-%EC%86%8C%EB%A9%B8%EC%9E%90) 사용|


> *(C++11~) [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#final)이 추가되어 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 더이상 [오버라이딩](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 못하게 할 수 있고, 강제적으로 [상속을 제한](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C)할 수 있습니다.*


