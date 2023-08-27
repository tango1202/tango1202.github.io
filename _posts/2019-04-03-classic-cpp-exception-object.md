---
layout: single
title: "#4. [고전 C++ 가이드] 표준 예외 개체"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

`throw()` 에서 예외를 발생할 때 예외 개체를 만들어 예외에 대한 정보를 담아 전달합니다.(예외 개체를 `catch()`받을 때는 복사 생성을 하지 않도록 참조자로 받는게 좋습니다.)

표준에서 예외 개체는 다음과 같은 상속 구조를 가집니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e180dc05-a0f6-4879-abc1-376061be3162)

# exception

`exception` 는 표준 예외들의 최상위 부모 개체로서 다음의 인터페이스를 제공합니다.

|항목|내용||
|--|--|
|`what()`|예외를 설명하는 문자열을 리턴합니다.|

`exception`을 상속한 모든 자식 개체들은 복사 생성자와 대입 연산자를 구현해야 합니다.

# 표준 예외

|항목|내용|
|--|--|
|`logical_error`|프로그램 내의 논리가 잘못됨|
|`invalid_argument`|잘못된 인자를 전달함|
|`domain_error`|해결하고자 하는 문제 영역(도메인) 에 오류가 있음|
|`length_error`|길이 제한을 초과함|
|`out_of_range`|범위를 벗어난 요소에 접근함|
|`runtime_error`|논리적 문제는 아니나 실행중에 오류가 발생함|
|`range_error`|유효한 범위를 벗어남|
|`overflow_error`|계산 결과가 허용 범위 초과임|
|`underflow_error`|계산 결과가 허용 범위 미만임|
|`bad_typeid`|널포인터를 역참조하여 typeid를 구할때 발생함<br/>T* p = NULL;<br/>typeid(*p).name();|
|`bad_cast`|`dynamic_cast`의 실패시 발생함|
|`bad_alloc`|메모리 할당 실패시 발생함|
|`bad_exception`|동적 예외 사양에 `bad_exception`이 포함되면, `unexpected()` 핸들러가 호출되고, 여기서 `throw;`시 `bad_exception`으로 변환되어 전파됨|

