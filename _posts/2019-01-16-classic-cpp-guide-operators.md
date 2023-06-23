---
layout: single
title: "#16. [고전 C++ 가이드] 연산자들(Operators)과 연산자 오버로딩"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---



**할당 연산자**

할당 연산자는 `=` 와 같은 기본 할당 연산자와 산술연산이나 비트연산의 결과값을 할당하는 연산자가 있습니다.(`a += b` 는 `a = a + b` 와 같습니다.) 

오버로딩시 `T&`와 같이 자기자신의 참조를 리턴하는데요, 이는 `a = b = c;` 와 같이 사용할 때 필요합니다. `b = c;`의 결과 `b`의 참조자를 리턴하고, 이게 `a = b`를 통해 `a`에 대입됩니다.

|항목|내용|오버로딩| 개체내 정의| 개체 외 정의|
|--|--|:--:|:--:|:--:|
|할당|`a = b`|O|T& T::operator =(const T2& b);|N/A|
|추가 할당|`a += b`|O|T& T::operator +=(const T2& b);|T& operator +=(T& a, const T2& b);|
|빼기 할당|`a -= b`|O|T& T::operator -=(const T2& b);|T& operator -=(T& a, const T2& b);|
|곱셈 할당|`a *= b`|O|T& T::operator *=(const T2& b);|T& operator *=(T& a, const T2& b);|
|나누기 할당|`a /= b`|O|T& T::operator /=(const T2& b);|T& operator /=(T& a, const T2& b);|
|나머지 할당|`a %= b`|O|T& T::operator %=(const T2& b);|T& operator %=(T& a, const T2& b);|

산술 연산자

증감 연산자

논리 연산자

비교 연산자

접근 연산자

함수 호출
괄호

콤마 연산자

조건 연산자

형변환 연산자

생성/소멸 연산자

sizeof 연산자

typeid 연산자

연산자 우선순위

연산자 오버로딩







