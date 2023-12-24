---
layout: single
title: "#14. [모던 C++ STL] (C++11~) 컴파일 타임 타입 특성(type_traits)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [type_traits](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/)가 추가되어 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 할 수 있습니다.
> * (C++14~) [is_null_pointer](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%EA%B8%B0%EB%B3%B8-%ED%83%80%EC%9E%85-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC), [is_final](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1)이 추가되었습니다.
> * (C++17~) [bool_constant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#helper), [has_unique_object_representations, is_aggregate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1), [is_swappable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%EB%B3%B5%EC%82%AC%EC%9D%B4%EB%8F%99%EB%8C%80%EC%9E%85%EC%86%8C%EB%A9%B8-%EC%86%8D%EC%84%B1), [is_invocable](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%ED%83%80%EC%9E%85-%EA%B4%80%EA%B3%84), [invoke_result, void_t, type_identity](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%EA%B8%B0%ED%83%80-%EB%B3%80%ED%99%98),  [conjunction, disjunction, negation](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#c17-traits-%EC%97%90%EC%84%9C%EC%9D%98-%EC%97%B0%EC%82%B0)이 추가되었습니다.
> * (C++20~) [is_constant_evaluated()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#c20-is_constant_evaluated)가 추가되어 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)가 컴파일 타임 함수인지 런타임 함수인지 검사할 수 있습니다.
> * (C++20~) [is_bounded_array, is_unbounded_array](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1)가 추가되었습니다.
> * (C++20~) [remove_cvref, common_reference](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%EA%B8%B0%ED%83%80-%EB%B3%80%ED%99%98)가 추가되었습니다.

# 개요

C++11 부터 STL 에서는 [템플릿 메타 프로그래밍](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-meta-programming/)을 위해 다양한 타입 특성들을 제공합니다.

예를 들면 다음과 같이 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 전달된 `T`가 포인터 타입인지 아닌지에 따라 코딩할 수 있습니다. 

또한 `is_pointer<T>::value`와 같이 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/) 조회를 하는 것은 `is_pointer_v<T>`로 인스턴스화 된 버전을 제공하여 코딩을 조금 더 간편하게 합니다.

```cpp
template<typename T>
void Func(T t) { 
    if (std::is_pointer<T>::value) { // std::is_pointer_v<T> 와 동일
        // T가 포인터 타입인 경우
    } 
    else {
        // T가 포인터 타입이 아닌 경우
    }
}
```

# Helper

|항목|내용|
|--|--|
|`integral_constant`(C++11~)|정수형 타입을 생성합니다.|
|`true_type`(C++11~)|`integral_constant<bool, true>`의 별칭입니다.|
|`false_type`(C++11~)|`integral_constant<bool, false>`의 별칭입니다.|
|`bool_constant` (C++17~)|`integral_constant<bool, true 나 false>`|

```cpp
typedef std::integral_constant<int, 2> TwoType;
typedef std::integral_constant<int, 3> ThreeType;

TwoType two;
ThreeType three;
```

# 기본 타입 카테고리

|항목|내용|
|--|--|
|`is_void` (C++11~)|`void` 타입인지 검사합니다.|
|`is_integral` (C++11~)|정수형 타입인지 검사합니다.|
|`is_floating_point` (C++11~)|실수형 타입인지 검사합니다.|
|`is_array` (C++11~)|[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 타입인지 검사합니다.|
|`is_enum` (C++11~)|[열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)인지 검사합니다.|
|`is_union` (C++11~)|[공용체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 타입인지 검사합니다.|
|`is_class` (C++11~)|구조체, 클래스 타입인지 검사합니다.|
|`is_function` (C++11~)|함수 타입인지 검사합니다.|
|`is_pointer` (C++11~)|포인터 타입인지 검사합니다.|
|`is_lvalue_reference` (C++11~)|[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 타입인지 검사합니다.|
|`is_rvalue_reference` (C++11~)|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0) 타입인지 검사합니다.|
|`is_member_object_pointer` (C++11~)|개체 [멤버 변수의 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EC%82%AC%EC%9A%A9%EB%B2%95) 타입인지 검사합니다.|
|`is_member_function_pointer` (C++11~)|개체 [멤버 함수의 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 타입인지 검사합니다.|
|`is_null_pointer` (C++14~)|[nullptr_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr_t) 타입인지 검사합니다.|

# 복합 타입 카테고리

복합 타입 카테고리는 기본 타입들을 조합하여 검사합니다.

|항목|내용|
|--|--|
|`is_arithmetic` (C++11~)|정수, 실수 타입인지 검사합니다.|
|`is_fundamental` (C++11~)|정수, 실수, `void`, [nullptr_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#nullptr_t) 타입인지 검사합니다.|
|`is_scalar` (C++11~)|[스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85)인지 검사합니다.|
|`is_object` (C++11~)|좌측값과 포인터 타입인지 검사합니다.|
|`is_reference` (C++11~)|참조 타입(*[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)*)인지 검사합니다.|
|`is_compound` (C++11~)|[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/), 함수, 개체 포인터, [함수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0), [멤버 변수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EC%82%AC%EC%9A%A9%EB%B2%95), [멤버 함수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0), 참조자, 구조체/클래스, [공용체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4), [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)인지 검사합니다. `int`와 같은 기본 타입은 `false` 입니다.|
|`is_member_pointer` (C++11~)|[멤버 변수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EC%82%AC%EC%9A%A9%EB%B2%95), [멤버 함수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 인지 검사합니다.|

# 타입 특성

|항목|내용|
|--|--|
|`is_const` (C++11~)|[const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/) 타입인지 검사합니다.|
|`is_volatile` (C++11~)|[volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile) 타입인지 검사합니다.|
|`is_trivial` (C++11~)|[Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`is_trivially_copyable` (C++11~)|[스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85) 이나 [Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85) 처럼 간단하게 복사 가능한지 검사합니다.|
|`is_standard_layout` (C++11~)|[표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`is_pod`(C++11~C++20)|[POD 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#pod-%ED%83%80%EC%9E%85plan-old-data) 인지 검사합니다.||
|`is_literal_type`(C++11~C++17)|[리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`is_empty` (C++11~)|[멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/), [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98), 비어있지 않은 부모 개체가 없는 빈 개체인지 검사합니다.|
|`is_polymorphic` (C++11~)|[가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있거나, [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 상속한 [다형성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-polymorphism/) 타입인지 검사합니다.|
|`is_abstract` (C++11~)|적어도 1개 이상의 [순가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있는 추상 클래스 타입인지 검사합니다.|
|`is_signed` (C++11~)|양수/음수 처리를 지원하는 타입인지 검사합니다.|
|`is_unsigned` (C++11~)|양수/음수 처리를 지원하는 않는 타입인지 검사합니다.|
|`is_final` (C++14~)|[final](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#final) 클래스 타입인지 검사합니다.|
|`has_unique_object_representations` (C++17~)|(작성중)|
|`is_aggregate` (C++17~)|(작성중)|
|`is_bounded_array` (C++20~)|요소의 갯수가 알려진 배열 타입인지 검사합니다.|
|`is_unbounded_array` (C++20~)|요소의 갯수가 알려지지 않은 배열 타입인지 검사합니다.|
|`is_scoped_enum` (C++23~)|(작성중)|
|`is_implicit_lifetime` (C++23~)|(작성중)|

다음은 사용예입니다.

```cpp

```

# 복사/이동/대입/소멸 속성 

|항목|내용|
|--|--|
|`is_constructible` (C++11~)<br/>`is_trivially_constructible` (C++11~)<br/>`is_nothrow_constructible` (C++11~)|열거된 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입으로 생성될 수 있는지 검사합니다.|
|`is_default_constructible` (C++11~)<br/>`is_trivially_default_constructible` (C++11~)<br/>`is_nothrow_default_constructible` (C++11~)|[기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성될 수 있는지 검사합니다.|
|`is_copy_constructible` (C++11~)<br/>`is_trivially_copy_constructible` (C++11~)<br/>`is_nothrow_copy_constructible` (C++11~)|[복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성될 수 있는지 검사합니다.|
|`is_move_constructible` (C++11~)<br/>`is_trivially_move_constructible` (C++11~)<br/>` is_nothrow_move_constructible` (C++11~)|[이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성될 수 있는지 검사합니다.|
|`is_assignable` (C++11~)<br/>`is_trivially_assignable` (C++11~)<br/>` is_nothrow_assignable` (C++11~)|주어진 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입으로 대입될 수 있는지 검사합니다.|
|`is_copy_assignable` (C++11~)<br/>`is_trivially_copy_assignable` (C++11~)<br/>` is_nothrow_copy_assignable` (C++11~)|[복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)될 수 있는지 검사합니다.|
|`is_move_assignable` (C++11~)<br/>`is_trivially_move_assignable` (C++11~)<br/>` is_nothrow_move_assignable` (C++11~)|[이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)될 수 있는지 검사합니다.|
|`is_destructible` (C++11~)<br/>` is_trivially_destructible` (C++11~)<br/>` is_nothrow_destructible` (C++11~)|소멸 가능한 타입인지 검사합니다.|
|`has_vitual_destuctor` (C++11~)|[가상 소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EB%8B%A4%ED%98%95-%EC%86%8C%EB%A9%B8)인지 검사합니다.|
|`is_swappable_with` (C++17~)<br/>`is_swappable` (C++17~)<br/>`is_nothrow_swappable_with` (C++17~)<br/>`is_nothrow_swappable` (C++17~)|(작성중)|

# 속성 질의

|항목|내용|
|--|--|
|`alignment_of` (C++11~)|[메모리 정렬](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)에 필요한 크기를 구합니다.<br/>[alignof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-alignas-alignof) 와 동일하며, C++11 이전 컴파일러와 호환성을 유지합니다.|
|`rank` (C++11~)|다차원 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)의 차원수를 구합니다.|
|`extent` (C++11~)|[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/) 차원의 요소 수를 구합니다.|

# 타입 관계

|항목|내용|
|--|--|
|`is_same` (C++11~)|두개의 타입이 동일한지 검사합니다. 이때 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)와 [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile)을 고려합니다. 즉 `int`와 `const int`는 다른 타입입니다.|
|`is_base_of` (C++11~)|`Base`가 `Derived`의 부모인지 검사합니다.([CloneTraits 구현](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-meta-programming/#clonetraits-%EA%B5%AC%ED%98%84) 의 `IsDerivedFrom` 참고)|
|`is_convertible` (C++11~)<br/>`is_nothrow_convertible` (C++20~)|`From` 타입이 `To`로 변환 가능한지 검사합니다.|
|`is_invocable` (C++17~)<br/>`is_invocable_r` (C++17~)<br/>`is_nothrow_invocable` (C++17~)<br/>`is_nothrow_invocable_r` (C++17~)|(작성중)|
|`is_layout_compatible` (C++20~)|[표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85) 등 메모리 레이아웃이 호환되는지 검사합니다.|
|`is_pointer_interconvertible_base_of` (C++20~)|(작성중)|

# const-volatile

|항목|내용|
|--|--|
|`remove_cv` (C++11~)<br/>`remove_const` (C++11~)<br/>`remove_volatile` (C++11~)|[const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)나 [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile) 한정자를 제거합니다.|
|`add_cv` (C++11~)<br/>`add_const` (C++11~)<br/>`add_volatile` (C++11~)|[const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)나 [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile) 한정자를 추가합니다.|

# 참조자

|항목|내용|
|--|--|
|`remove_reference` (C++11~)|타입에서 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)의 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거합니다.|
|`add_lvalue_reference` (C++11~)<br/>`add_rvalue_reference` (C++11~)|타입에서 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)의 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 추가합니다.|

# 포인터

|항목|내용|
|--|--|
|`remove_pointer` (C++11~)|타입에서 포인터성을 제거합니다.|
|`add_pointer` (C++11~)|타입에서 포인터성을 추가합니다.|

# 부호 수정

|항목|내용|
|--|--|
|`make_signed` (C++11~)|양수/음수 처리를 지원하는 타입으로 변경합니다.|
|`make_unsigned` (C++11~)|양수/음수 처리를 지원하지 않는 타입으로 변경합니다.|

# 배열

|항목|내용|
|--|--|
|`remove_extent` (C++11~)|[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)의 첫번째 차원을 제거합니다. 즉, `int[][][]` 는 `int[][]`가 됩니다.|
|`remove_all_extents` (C++11~)|[배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)의 모든 차원을 제거합니다.|
  
# 기타 변환

|항목|내용|
|--|--|
|`aligned_storage`(C++11~C++23)|(작성중)|
|`aligned_union`(C++11~C++23)|(작성중)|
|`decay` (C++11~)|[참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/), [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile)등의 속성을 떼고, 타입으로 만듭니다. 즉 `const int&`이던 `int&&`이던 모두 `int`가 됩니다. 또한 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)은 포인터로 만듭니다. <br/>이와는 반대로 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 붙여서 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달하는 것은 [ref(), cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref)를 참고하세요.|
|[enable_if](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#enable_if) (C++11~)|지정한 조건이 참인 경우만 [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)을 활성화 합니다.|
|`conditional` (C++11~)|(작성중)|
|`common_type` (C++11~)|(작성중)|
|`underlying_type` (C++11~)|(작성중)|
|`result_of`(C++11~C++20)<br/>`invoke_result` (C++17~)|(작성중)|
|`void_t` (C++17~)|(작성중)|
|`type_identity` (C++17~)|(작성중)|
|`remove_cvref` (C++20~)|[const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/), [volatile](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%B5%9C%EC%A0%81%ED%99%94-%EC%A0%9C%ED%95%9C-%ED%95%9C%EC%A0%95%EC%9E%90volatile), [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)를 제거한 타입을 구합니다.|
|`common_reference` (C++20~)<br/>`basic_common_reference` (C++20~)|주어진 타입들의 공통된 참조 형식을 구합니다.|

# enable_if

주어진 조건이 참인 경우만 템플릿을 활성화 시킵니다. 내부적으로 [SFINAE](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)를 이용합니다. 즉, [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter), 리턴 타입, [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)등에 사용하여 조건이 참이어서 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)인 `type`이 정의되어 있으면 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보군으로 활성화 시키고, 정의되어 있지 않으면 비활성 시킵니다.

```cpp
template<bool B, class T = void>
struct enable_if {}; // 조건이 거짓이면 아무 정의 안합니다.
 
template<class T>
struct enable_if<true, T> { typedef T type; }; // 조건이 참이면 `T`와 동일한 종속 타입 `type`을 만듭니다.
```

예를 들어 다음은 `int`타입이나 `double`타입이나 심지어 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)까지 `operator +()`가 정의되었다면 모두 실행 가능한데요,

```cpp
template<typename T> 
T Add(T a, T b) {
    return a + b;
}

EXPECT_TRUE(Add(10, 20) == 30); // 정수 합
EXPECT_TRUE(Add(std::string("Hello"), std::string("World")) == std::string("HelloWorld")); 
```

다음처럼 정수 타입만 실행되게 수정할 수 있습니다.

1. `typename U = enable_if<조건>::type`으로 조건이 거짓이면 [SFINAE](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)에 의해 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 목록에서 제외되게 합니다.(*이때 `U`는 사용되지 않으므로, `typename = enable_if<조건>::type` 와 같이 생략하는게 좋습니다.*)

2. `std::is_integral<T>::value` 로 `T`가 정수 타입인지 검사합니다.

```cpp
template<
    typename T, 
    typename U = typename std::enable_if< // U 는 사용하지 않으므로 생략 가능합니다. typename = typename std::enable_if<
        std::is_integral<T>::value // 조건. 정수형 타입이면 true 입니다.
    >::type // 조건이 true인 경우에만 enable_if<>::type이 정의됩니다.
> 
T Add(T a, T b) {
    return a + b;
}

EXPECT_TRUE(Add(10, 20) == 30); // 정수 합
EXPECT_TRUE(Add(std::string("Hello"), std::string("World")) == std::string("HelloWorld")); // (X) 컴파일 오류. 정수 타입이 아니어서 enable_if<>::type이 정의되지 않고, SFINIE에 의해 오버로딩된 함수 후보 목록에서 제외됩니다. 따라서 함수가 없습니다. 
```       

> *(C++20~) [컨셉(concept)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항(requires)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 추가되어 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/mordern-cpp/mordern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 줄 수 있습니다.*

# (C++17~) traits 에서의 연산

|항목|내용|
|--|--|
|`conjunction` (C++17~)|(작성중)|
|`disjunction` (C++17~)|(작성중)|
|`negation` (C++17~)|(작성중)|

# (C++20~) 함수

|항목|내용|
|--|--|
|`is_pointer_interconvertible_with_class()` (C++20~)|(작성중)|
|`is_corresponding_member()` (C++20~)|(작성중)|
|[is_constant_evaluated()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#c20-is_constant_evaluated) (C++20~)|컴파일 타임에 실행중인지, 런타임에 실행중인지 평가합니다.|
|`is_within_lifetime()` (C++26~)|(작성중)|

# (C++20~) is_constant_evaluated()

[constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 컴파일 타임 함수나 런타임 함수로 동작할 수 있는데요, 

C++20 부터는 [is_constant_evaluated()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#c20-is_constant_evaluated)가 추가되어 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)가 컴파일 타임 함수인지 런타임 함수인지 검사할 수 있습니다.

다음 예의 `Factorial()`함수는 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)인데요, [is_constant_evaluated()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#c20-is_constant_evaluated)를 이용하여 런타임 함수로 동작할때만 메시지를 출력합니다.

```cpp
constexpr int Factorial(int val) {
    
    int result{1}; // 초기화된 지역 변수 정의

    if (val < 1) {
        return 1; // 2개 이상의 리턴문
    }

    for (int i{val}; 0 < i; --i) { // 제어문
        result *= i;
    }
    bool isConstant = std::is_constant_evaluated();

    if (!std::is_constant_evaluated()) {
        std::cout << "Factorial Result : " << result << std::endl; // 컴파일 타임 함수가 아니라면, 메시지를 출력합니다.
    }
    return result;
} 

static_assert(Factorial(5) == 1 * 2 * 3 * 4 * 5); // 컴파일 타임 함수입니다.

int val{5};
int result{Factorial(val)}; // 런타임 함수입니다. 메시지를 출력합니다.
EXPECT_TRUE(result == 1 * 2 * 3 * 4 * 5);
```    




