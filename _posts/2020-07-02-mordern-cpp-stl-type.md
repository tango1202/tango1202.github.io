---
layout: single
title: "#2. [모던 C++ STL] 타입"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EB%9F%B0%ED%83%80%EC%9E%84-%ED%83%80%EC%9E%85)는 [type_info](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)의 래퍼로서 [type_info](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)를 컨테이너에서 관리할 수 있게 합니다.
> * (C++11~) [고정 너비 정수 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#c11-%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%A0%95%EC%88%98-%ED%83%80%EC%9E%85)이 추가되었습니다.
> * (C++17~) [byte](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EA%B8%B0%EB%B3%B8-%ED%83%80%EC%9E%85)는 순수한 1byte 크기를 갖는 타입입니다.(*`char`, `unsigned char`는 문자 처리 의미가 포함된 타입입니다.*)
> * (C++23~) [고정 너비 실수 타입](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#c23-%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%8B%A4%EC%88%98-%ED%83%80%EC%9E%85)이 추가되었습니다.

# 기본 타입

|항목|내용|
|--|--|
|`size_t`|`unsigned int` 으로서 크기나 갯수를 나타냅니다.|
|`ptrdiff_t`|`signed int` 으로서 포인터간의 차를 나타냅니다.|
|[nullptr_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr) (C++11~)|[nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr)을 저장하는 타입입니다.|
|NULL|널포인터의 값이며, `#define NULL 0` 입니다.|
`max_align_t` (C++11~)|(작성중)|
|`offsetof()`|(작성중)|
|`byte` (C++17~)|순수한 1byte 크기를 갖는 타입입니다.(`char`, `unsigned char`는 문자 처리 의미가 포함된 1byte 크기의 타입입니다.) |

# 수학 타입

|항목|내용|
|--|--|
|`div_t`|(작성중)|
|`ldiv_t`|(작성중)|
|`lldiv_t` (C++11)|(작성중)|
|`imaxdiv_t` (C++11)|(작성중)|
|`float_t` (C++11)|(작성중)|
|`double_t` (C++11)|(작성중)|

# (C++11~) 고정 너비 정수 타입

|항목|내용|
|--|--|
|`int8_t` (C++11~)<br/>`int16_t` (C++11~)<br>`int32_t` (C++11~)<br/>`int64_t` (C++11~)|(작성중)|
|`int_fast8_t` (C++11~)<br/>`int_fast16_t` (C++11~)<br>`int_fast32_t` (C++11~)<br/>`int_fast64_t` (C++11~)|(작성중)|
|`int_least8_t` (C++11~)<br/>`int_least16_t` (C++11~)<br>`int_least32_t` (C++11~)<br/>`int_least64_t` (C++11~)|(작성중)|
|`intmax_t` (C++11~)|(작성중)|
|`intptr_t` (C++11~)|(작성중)|
|`uint8_t` (C++11~)<br/>`uint16_t` (C++11~)<br>`uint32_t` (C++11~)<br/>`uint64_t` (C++11~)|(작성중)|
|`uint_fast8_t` (C++11~)<br/>`uint_fast16_t` (C++11~)<br>`uint_fast32_t` (C++11~)<br/>`uint_fast64_t` (C++11~)|(작성중)|
|`uint_least8_t` (C++11~)<br/>`uint_least16_t` (C++11~)<br>`uint_least32_t` (C++11~)<br/>`uint_least64_t` (C++11~)|(작성중)|
|`uintmax_t` (C++11~)|(작성중)|
|`uintptr_t` (C++11~)|(작성중)|

# (C++23~) 고정 너비 실수 타입

|항목|내용|
|--|--|
|`float16_t` (C++23~)|(작성중)|
|`float32_t` (C++23~)|(작성중)|
|`float64_t` (C++23~)|(작성중)|
|`float128_t` (C++23~)|(작성중)|
|`bfloat16_t` (C++23~)|(작성중)|

# 타입 최대/최소값

|항목|내용|
|--|--|
|[numeric_limits](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%B5%9C%EB%8C%80%EC%B5%9C%EC%86%8C%EA%B0%92)| 타입의 최대, 최소값을 알 수 있습니다.|

# C스타일 최대/최소값

|항목|내용|
|--|--|
|`CHAR_BIT`|(작성중)|
|`MB_LEN_MAX`|(작성중)|
|`CHAR_MIN`|(작성중)|
|`CHAR_MAX`|(작성중)|
|`SCHAR_MIN`<br/>`SHRT_MIN`<br/>`INT_MIN`<br/>`LONG_MIN`<br/>`LLONG_MIN` (C++11~)|(작성중)|
|`SCHAR_MAX`<br/>`SHRT_MAX`<br/>`INT_MAX`<br/>`LONG_MAX`<br/>`LLONG_MAX` (C++11~)|(작성중)|
|`UCHAR_MAX`<br/>`USHRT_MAX`<br/>`UINT_MAX`<br/>`ULONG_MAX`<br/>`ULLONG_MAX` (C++11~)|(작성중)|
|`WCHAR_MIN` (C++11~)|(작성중)|
|`WCHAR_MAX` (C++11~)|(작성중)|
|`PTRDIFF_MIN` (C++11~)|(작성중)| 
|`PTRDIFF_MAX` (C++11~)|(작성중)| 
|`SIZE_MAX` (C++11~)|(작성중)|  
|`SIG_ATOMIC_MIN` (C++11~)|(작성중)|  
|`SIG_ATOMIC_MAX` (C++11~)|(작성중)| 
|`WINT_MIN` (C++11~)|(작성중)|   
|`WINT_MAX` (C++11~)|(작성중)|   
 
# 런타임 타입

|항목|내용|
|--|--|
|[type_info](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)|개체의 타입 정보입니다.|
|[bad_typeid](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%ED%91%9C%EC%A4%80-%EC%98%88%EC%99%B8)|널포인터를 역참조하여 typeid를 구할때 발생합니다.<br/>`T* p = NULL;`<br/>`typeid(*p).name();`|
|[bad_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|[dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)의 실패시 발생합니다.|
|[type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EB%9F%B0%ED%83%80%EC%9E%84-%ED%83%80%EC%9E%85) (C++11~)|[type_info](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)의 래퍼로서 [type_info](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)를 컨테이너에서 관리할 수 있게 합니다.|


[typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)는 `const type_info&`를 리턴하는데요, [컨테이너 요소 규칙](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9A%94%EC%86%8C-%EA%B7%9C%EC%B9%99)인 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 [연관 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%97%B0%EA%B4%80-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)를 위한 대소 비교를 제공하지 않아 컨테이너에 추가할 수 없습니다.

[type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#%EB%9F%B0%ED%83%80%EC%9E%84-%ED%83%80%EC%9E%85)는 `const type_info&`를 컨테이너의 요소로 사용할 수 있게끔 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 대소 비교를 구현한 래퍼입니다. 

이를 이용하면 `const type_info&`를 관리하는 컨테이너를 만들 수 있습니다. 

```cpp
class A {};
class B {};

// const std::type_info& 여서 컨테이너에서 사용할 수 없습니다.
// const std::type_info& a{typeid(A)};
// const std::type_info& b{typeid(B)};

std::type_index a{typeid(A)};
std::type_index b{typeid(B)};

std::map<std::type_index, int> m;

m.insert(std::make_pair(a, 0)); // A 타입의 추가 정보를 저장할 수 있습니다.
m.insert(std::make_pair(b, 1)); // B 타입의 추가 정보를 저장할 수 있습니다.

EXPECT_TRUE(m[typeid(A)] == 0 && m[typeid(B)] == 1);
```
