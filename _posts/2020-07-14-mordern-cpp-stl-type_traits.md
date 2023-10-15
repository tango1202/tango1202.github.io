---
layout: single
title: "#14. [모던 C++ STL] (C++11~) 컴파일 타임 타입 특성(type_traits)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) `type_traits`가 추가되어 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 할 수 있습니다.

# 개요

C++11 부터 STL 에서는 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)을 위해 다양한 타입 특성들을 제공합니다.

예를 들면 다음과 같이 템플릿 인자로 전달된 `T`가 포인터 타입인지 아닌지에 따라 코딩할 수 있습니다. 

또한 `is_pointer<T>::value`와 같이 열거형 조회를 하는 것은 `is_pointer_v<T>`로 인스턴스화 된 버전을 제공하여 코딩을 조금 더 간편하게 합니다.

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
|`is_array` (C++11~)|배열 타입인지 검사합니다.|
|`is_enum` (C++11~)|열거형 타입인지 검사합니다.|
|`is_union` (C++11~)|공용체 타입인지 검사합니다.|
|`is_class` (C++11~)|구조체, 클래스 타입인지 검사합니다.|
|`is_function` (C++11~)|함수 타입인지 검사합니다.|
|`is_pointer` (C++11~)|포인터 타입인지 검사합니다.|
|`is_lvalue_reference` (C++11~)|좌측값 참조 타입인지 검사합니다.|
|`is_rvalue_reference` (C++11~)|우측값 참조 타입인지 검사합니다.|
|`is_member_object_pointer` (C++11~)|개체 멤버 변수의 포인터 타입인지 검사합니다.|
|`is_member_function_pointer` (C++11~)|개체 멤버 함수의 포인터 타입인지 검사합니다.|
|`is_null_pointer` (C++14~)|`nullptr_t` 타입인지 검사합니다.|

# 복합 타입 카테고리

복합 타입 카테고리는 기본 타입들을 조합하여 검사합니다.

|항목|내용|
|--|--|
|`is_arithmetic` (C++11~)|정수, 실수 타입인지 검사합니다.|
|`is_fundamental` (C++11~)|정수, 실수, `void`, `nullptr_t` 타입인지 검사합니다.|
|`is_scalar` (C++11~)|[스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85)인지 검사합니다.|
|`is_object` (C++11~)|좌측값과 포인터 타입인지 검사합니다.|
|`is_reference` (C++11~)|참조 타입(좌측값 참조와 우측값 참조)인지 검사합니다.|
|`is_compound` (C++11~)|배열, 함수, 개체 포인터, 함수 포인터, 멤버 변수 포인터, 멤버 함수 포인터, 참조자, 구조체/클래스, 공용체, 열거형인지 검사합니다. `int`와 같은 기본 타입은 `false` 입니다.|
|`is_member_pointer` (C++11~)|멤버 변수 포인터, 멤버 함수 포인터 인지 검사합니다.|

# 타입 특성

|항목|내용|
|--|--|
|`is_const` (C++11~)|`const` 타입인지 검사합니다.|
|`is_volatile` (C++11~)|`volatile` 타입인지 검사합니다.|
|`is_trivial` (C++11~)|[Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`is_trivially_copyable` (C++11~)|[스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85) 이나 [Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85) 처럼 간단하게 복사 가능한지 검사합니다.|
|`is_standard_layout` (C++11~)|[표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`is_pod`(C++11~C++20)|[POD 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#pod-%ED%83%80%EC%9E%85plan-old-data) 인지 검사합니다.||
|`is_literal_type`(C++11~C++17)|[리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`is_empty` (C++11~)|멤버 변수, 가상 함수, 비어있지 않은 부모 개체가 없는 빈 개체인지 검사합니다.|
|`is_polymorphic` (C++11~)|가상 함수가 있거나, 가상 함수를 상속한 다형성 타입인지 검사합니다.|
|`is_abstract` (C++11~)|적어도 1개 이상의 순가상 함수가 있는 추상 클래스 타입인지 검사합니다.|
|`is_signed` (C++11~)|양수/음수 처리를 지원하는 타입인지 검사합니다.|
|`is_unsigned` (C++11~)|양수/음수 처리를 지원하는 않는 타입인지 검사합니다.|
|`is_final` (C++14~)|`final`클래스 타입인지 검사합니다.|
|`has_unique_object_representations` (C++17~)|(작성중)|
|`is_aggregate` (C++17~)|(작성중)|
|`is_bounded_array` (C++20~)|(작성중)|
|`is_unbounded_array` (C++20~)|(작성중)|
|`is_implicit_lifetime` (C++23~)|(작성중)|
|`is_scoped_enum` (C++20~)|(작성중)|

# 복사/이동/대입/소멸 속성 

|항목|내용|
|--|--|
|`is_constructible` (C++11~)<br/>`is_trivially_constructible` (C++11~)<br/>`is_nothrow_constructible` (C++11~)|열거된 인자 타입으로 생성될 수 있는지 검사합니다.|
|`is_default_constructible` (C++11~)<br/>`is_trivially_default_constructible` (C++11~)<br/>`is_nothrow_default_constructible` (C++11~)|기본 생성자로 생성될 수 있는지 검사합니다.|
|`is_copy_constructible` (C++11~)<br/>`is_trivially_copy_constructible` (C++11~)<br/>`is_nothrow_copy_constructible` (C++11~)|복사 생성자로 생성될 수 있는지 검사합니다.|
|`is_move_constructible` (C++11~)<br/>`is_trivially_move_constructible` (C++11~)<br/>` is_nothrow_move_constructible` (C++11~)|이동 생성자로 생성될 수 있는지 검사합니다.|
|`is_assignable` (C++11~)<br/>`is_trivially_assignable` (C++11~)<br/>` is_nothrow_assignable` (C++11~)|주어진 인자 타입으로 대입될 수 있는지 검사합니다.|
|`is_copy_assignable` (C++11~)<br/>`is_trivially_copy_assignable` (C++11~)<br/>` is_nothrow_copy_assignable` (C++11~)|복사 대입될 수 있는지 검사합니다.|
|`is_move_assignable` (C++11~)<br/>`is_trivially_move_assignable` (C++11~)<br/>` is_nothrow_move_assignable` (C++11~)|이동 대입될 수 있는지 검사합니다.|
|`is_destructible` (C++11~)<br/>` is_trivially_destructible` (C++11~)<br/>` is_nothrow_destructible` (C++11~)|소멸 가능한 타입인지 검사합니다.|
|`has_vitual_destuctor` (C++11~)|가상 소멸자인지 검사합니다.|
|`is_swappable_with` (C++17~)<br/>`is_swappable` (C++17~)<br/>`is_nothrow_swappable_with` (C++17~)<br/>`is_nothrow_swappable` (C++17~)|(작성중)|

# 속성 질의

|항목|내용|
|--|--|
|`alignment_of` (C++11~)|메모리 정렬에 필요한 크기를 구합니다.<br/>`alignof()` 와 동일하며, C++11 이전 컴파일러와 호환성을 유지합니다.|
|`rank` (C++11~)|다차원 배열의 차원수를 구합니다.|
|`extent` (C++11~)|배열 차원의 요소 수를 구합니다.|

# 타입 관계

|항목|내용|
|--|--|
|`is_same` (C++11~)|두개의 타입이 동일한지 검사합니다. 이때 `const`와 `volatile`을 고려합니다. 즉 `int`와 `const int`는 다른 타입입니다.|
|`is_base_of` (C++11~)|`Base`가 `Derived`의 부모인지 검사합니다.([CloneTraits 구현](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/#clonetraits-%EA%B5%AC%ED%98%84) 의 `IsDerivedFrom` 참고)|
|`is_convertible` (C++11~)<br/>`is_nothrow_convertible` (C++20~)|`From` 타입이 `To`로 변환 가능한지 검사합니다.|
|`is_invocable` (C++17~)<br/>`is_invocable_r` (C++17~)<br/>`is_nothrow_invocable` (C++17~)<br/>`is_nothrow_invocable_r` (C++17~)|(작성중)|
|`is_layout_compatible` (C++20~)|[표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85) 등 메모리 레이아웃이 호환되는지 검사합니다.|
|`is_pointer_interconvertible_base_of` (C++20~)|(작성중)|

# const-volatile

|항목|내용|
|--|--|
|`remove_cv` (C++11~)<br/>`remove_const` (C++11~)<br/>`remove_volatile` (C++11~)|`const`나 `volatile` 한정자를 제거합니다.|
|`add_cv` (C++11~)<br/>`add_const` (C++11~)<br/>`add_volatile` (C++11~)|`const`나 `volatile` 한정자를 추가합니다.|

# 참조자

|항목|내용|
|--|--|
|`remove_reference` (C++11~)|타입에서 좌측값 참조, 우측값 참조의 참조성을 제거합니다.|
|`add_lvalue_reference` (C++11~)<br/>`add_rvalue_reference` (C++11~)|타입에서 좌측값 참조, 우측값 참조의 참조성을 추가합니다.|

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
|`remove_extent` (C++11~)|배열의 첫번째 차원을 제거합니다. 즉, `int[][][]` 는 `int[][]`가 됩니다.|
|`remove_all_extents` (C++11~)|배열의 모든 차원을 제거합니다.|
  
# 기타 변환

|항목|내용|
|--|--|
|`aligned_storage`(C++11~C++23)|(작성중)|
|`aligned_union`(C++11~C++23)|(작성중)|
|`decay` (C++11~)|참조성을 떼고, 타입으로 만듭니다. 즉 `int &`는 `int`가 됩니다. 또한 배열은 포인터로 만듭니다.이와는 반대로 참조성을 붙여서 인자 전달하는 것은 [ref(), cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref)를 참고하세요.|
|`enable_if` (C++11~)|(작성중)|
|`conditional` (C++11~)|(작성중)|
|`common_type` (C++11~)|(작성중)|
|`underlying_type` (C++11~)|(작성중)|
|`result_of`(C++11~C++20)<br/>`invoke_result` (C++17~)|(작성중)|
|`void_t` (C++17~)|(작성중)|
|`type_identity` (C++17~)|(작성중)|
|`remove_cvref` (C++20~)|(작성중)|
|`common_reference` (C++20~)<br/>`basic_common_reference` (C++20~)|(작성중)|

# (C++17~) traits 에서의 연산

|항목|내용|
|--|--|
|`conjunction` (C++17~)|(작성중)|
|`disjunction` (C++17~)|(작성중)|
|`negation` (C++17~)|(작성중)|

# 함수

|항목|내용|
|--|--|
|`is_pointer_interconvertible_with_class` (C++20~)|(작성중)|
|`is_corresponding_member` (C++20~)|(작성중)|
|`is_constant_evaluated` (C++20~)|(작성중)|
|`is_within_lifetime` (C++20~)|(작성중)|
