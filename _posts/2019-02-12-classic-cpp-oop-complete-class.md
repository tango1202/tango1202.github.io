---
layout: single
title: "#13. [고전 C++ 개체 지향] 완전한 클래스(작성중)"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게 구현하라.
> * 함수 호출시 복사 부하가 없도록 설계하라.
> * 함수 작성시 상수성을 준수하도록 하라.
> * 예외 안정적으로 구현하라.(예외 발생시 이전 상태로 전환하라.)

# 개요

클래스는 단단한 **코딩 계약**에 의해 잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게 구현해야 합니다.([캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 참고)

# 암시적 정의 차단

컴파일러가 은근슬쩍 암시적으로 정의한 것들은 의도한대로 동작하지 않을 수도 있고, 현재는 의도한 대로 동작하지만, 누군가가 기능 추가나 리팩토링하면서 바뀔 수 있습니다.

따라서, 현재 사용하지 않는다면, 모두 차단하십시오.

|항목|내용|
|--|--|
|[암시적 기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)|접근 지정자 `private`또는 `protected` 사용<br/>또는 다른 생성자 정의|
|[암시적 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|접근 지정자 `private`또는 `protected` 사용|
|[암시적 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|접근 지정자 `private`또는 `protected` 사용|
|[암시적 소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%86%8C%EB%A9%B8%EC%9E%90)|암시적 소멸자와 호환되도록 멤버 변수 정의([스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)를 사용)|
|[형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)|[`explicit`로 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)|

# 최소 public

가시성 최소화

# 완전한 생성자

개체가 생성될때 완전한 상태로 생성합니다. 

완전하지 않은 생성자는 다음과 같은 문제가 있습니다.

1. 별도로 Setter를 호출해야 하므로 사용하기 번거롭습니다.
2. 기본값으로 대충 생성후 값을 세팅하면 불필요한 대입 연산 오버헤드가 발생합니다.
3. 개체 사용자는 개체 내부 구조를 완전히 파악해야만 제대로 사용할 수 있습니다. **코딩 계약** 을 투명하게 하여 잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게 구현해야 합니다.([캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 참고)
4. 불완전하게 생성된 개체를 사용했을때 발생하는 버그는 예측하기 힘듭니다.
5. 불완전하게 생성된 개체에 별도 Setter를 호출하여 완전하게 만드는 중에 예외가 발생하면, 이미 생성된 예외 안정 처리를 위해 소멸시켜야 합니다. 혹시나 이미 이 개체를 참조하는 곳이 있다면, 처리가 곤란합니다.

따라서,
1. 개체 생성에 필요한 모든 인자를 생성자에서 나열하고,   
2. 생성중 오류가 발생하면 예외를 발생시켜 그동안 만들어 둔건 소멸시켜 버려야 합니다.

|항목|내용|
|--|--|
|[인자 나열](https://tango1202.github.io/principle/principle-explicit-dependencies/)|생성할 때 필요한 모든 인자를 나열|
|[초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)|초기화 리스트로 멤버 변수 초기화|
|시스템 종속성이 높은 개체|생성자를 `private`로 만들고 별도의 `Create()`함수 제공([생성자 접근 차단 - private 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A0%91%EA%B7%BC-%EC%B0%A8%EB%8B%A8---private-%EC%83%9D%EC%84%B1%EC%9E%90) 참고)|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|[스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)와 같은 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여, 생성자등에서 예외가 발생했을때 자동 소멸되도록 함|

# 완전한 함수

함수 구현은 다음 사항을 준수하여야 합니다.

1. 생성자와 마찬가지로 필요한 인자를 모두 전달하여야 합니다. **코딩 계약** 을 투명하게 하여 잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게 구현해야 합니다.([캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 참고)
2. 인자 전달이나 함수 리턴값 전달시 복사 부하를 최소화 해야 합니다.
3. [상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)에 위배되지 않도록 작성해야 합니다.
4. 함수내에서 예외가 발생하면 그동안 만들어 둔건 소멸시켜 버려야 합니다.

|항목|내용|
|--|--|
|[인자 나열](https://tango1202.github.io/principle/principle-explicit-dependencies/)|함수에 필요한 모든 인자를 나열|
|인자 복사 부하|기본 자료형인 경우 값 복사로, 클래스 타입인 경우 참조자로 작성([Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98) 참고)|
|리턴값 복사 부하|RVO가 쉽도록 임시 개체로 생성([리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 참고)<br/>기본 자료형인 경우 값 복사로, 클래스 타입인 경우 참조자로 작성([Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98) 참고)|
|상수성|멤버 변수를 수정하지 않는다면, 상수 멤버 함수로 작성하고, 상수 참조를 리턴([Getter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98) 참고)<br/>[논리적 상수성](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-logical-const/)인 것은 최대한 예외가 발생하지 않도록 구현|
|함수내 지역 변수|[스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여 획득된 자원은 유효 범위가 벗어났을때 자동 소멸 되도록 함([RAII와 Holder 와 Restorer - 자원 획득과 안전한 소멸(복원)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/))|

# 소유권 분쟁 차단

포인터 멤버 변수 사용시에는 [소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)이 발생합니다.

소유권 분쟁이 발생하지 않도록 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 사용하여,

1. 소유권 이전을 하거나,
2. 깊은 복제를 하거나,
3. 자원을 공유하거나,
4. 유일한 자원으로 대체해서 사용

하여야 합니다.

|항목|내용|
|--|--|
|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|복사 생성시 소유권을 처리할 수 있는 스마트 포인터 사용([복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)참고)|
|[대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/)|대입 연산시 소유권을 처리할 수 있는 스마트 포인터 사용([대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고)|

# 자원 획득은 초기화

획득된 자원은 반드시 소멸되어야 합니다.([RAII(Resource Acquisition Is Initialization)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 참고) 소멸자에서 명시적으로 `delete` 하거나 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 이용할 수 있습니다.

|항목|내용|
|--|--|
|[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)|멤버 변수에 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 사용하여, 암시적 소멸자와 호환되어 자동 소멸 시키거나, 명시적으로 `delete` 호출|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|[스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)를 사용하여 암시적 소멸자에서 자동 소멸시키도록 함([포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 참고)|

# 예외 안정

예외가 발생하면, 예외가 발생하기 전의 상태로 돌아가야 합니다. 개체의 일부 내용만 복원하는게 아니라, 완전히 예외 발생 전의 상태로 되돌아 가야 합니다. 

따라서,

1. **완전한 생성자**로 생성하여 생성중 오류가 발생하면 예외를 발생시켜 그동안 만들어 둔건 소멸시켜 버리고,
2. 대입 연산중 예외가 발생하지 않도록 [nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94)을 이용해서 대입 연산을 구현하고, 
3. 함수 실행중 예외가 발생하면, [**스택 풀기**](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding) 메카니즘에 맞게 이전 상태로 되돌리고,
4. **스택 풀기**가 꼬이지 않게 소멸자에서는 예외를 발생하지 않도록

구현해야 합니다.

|항목|내용|
|--|--|
|생성자|완전한 생성자로 구현하고, 생성중 오류가 발생하면 예외를 발생시킴.|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|[스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)와 같은 [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여, 생성자등에서 예외가 발생했을때 자동 소멸되도록 함|
|[대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/)|멤버 변수가 1개인 경우 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 사용하고, 2개 이상이면 [swap을 이용한 예외 안정 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%95-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 사용|
|[nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94)|`swap` 구현시 복사 부하나 예외 발생이 없도록, 포인터 멤버 변수를 이용하여 구현([nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94). [PImpl 이디엄](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-pimpl/) 참고) 
|함수내 지역 변수|[스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 개체를 사용하여 획득된 자원은 유효 범위가 벗어났을때 자동 소멸 되도록 함([RAII와 Holder 와 Restorer - 자원 획득과 안전한 소멸(복원)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/))|
|[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)|기본적으로 소멸자에서 정리하되, 예외 발생의 소지가 있으면 `Release()`와 같은 별도의 정리 함수 제공([소멸자에서 예외 발생 금지](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90%EC%97%90%EC%84%9C-%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D-%EA%B8%88%EC%A7%80) 참고)|

# 형변환 차단

암시적인 형변환은 제공하지 않습니다. 필요하다면 명시적으로 형변환 함수를 제공합니다.

|항목|내용|
|--|--|
|[형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)|정의하지 않음|
|[형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)|[`explicit`로 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)|
|명시적 형변환 함수|형변환이 꼭 필요하다면, 명시적으로 구현([형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고)|
