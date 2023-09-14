---
layout: single
title: "#31. [모던 C++] (C++11~) type_traits"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

C++11부터 STL 에서는 템플릿 메타 프로그래밍을 위해 다양한 타입 특성들을 제공합니다.

예를 들면 다음과 같이 템플릿 인자로 전달된 `T`가 포인터 타입인지 아닌지에 따라 코딩할 수 있습니다. 또한 `std::is_pointer<T>::value`와 같이 열거형 조회를 하는 것은 `std::is_pointer_v<T>`로 인스턴스화 되어 있어 코딩을 조금더 간편하게 합니다.

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
|`std::integral_constant`|정수형 타입을 생성합니다.|

```cpp
typedef std::integral_constant<int, 2> TwoType;
typedef std::integral_constant<int, 3> ThreeType;

TwoType two;
ThreeType three;
```

# 기본 타입 카테고리

|항목|내용|
|--|--|
|`std::is_void`|`void` 타입인지 검사합니다.|
|`std::is_integral`|정수형 타입인지 검사합니다.|
|`std::is_floating_point`|실수형 타입인지 검사합니다.|
|`std::is_array`|배열 타입인지 검사합니다.|
|`std::is_enum`|열거형 타입인지 검사합니다.|
|`std::is_union`|공용체 타입인지 검사합니다.|
|`std::is_class`|구조체, 클래스 타입인지 검사합니다.|
|`std::is_function`|함수 타입인지 검사합니다.|
|`std::is_pointer`|포인터 타입인지 검사합니다.|
|`std::is_lvalue_reference`|좌측값 참조 타입인지 검사합니다.|
|`std::is_rvalue_reference`|우측값 참조 타입인지 검사합니다.|
|`std::is_member_object_pointer`|개체 멤버 변수의 포인터 타입인지 검사합니다.|
|`std::is_member_function_pointer`|개체 멤버 함수의 포인터 타입인지 검사합니다.|

# 복합 타입 카테고리

복합 타입 카테고리는 기본 타입들을 조합하여 검사합니다.

|항목|내용|
|--|--|
|`std::is_arithmetic`|정수, 실수 타입인지 검사합니다.|
|`std::is_fundamental`|정수, 실수, `void`, `nullptr_t` 타입인지 검사합니다.|
|`std::is_scalar`|[스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85)인지 검사합니다.|
|`std::is_object`|좌측값과 포인터 타입인지 검사합니다.|
|`std::is_reference`|참조 타입(좌측값 참조와 우측값 참조)인지 검사합니다.|
|`std::is_compound`|배열, 함수, 개체 포인터, 함수 포인터, 멤버 변수 포인터, 멤버 함수 포인터, 참조자, 구조체/클래스, 공용체, 열거형인지 검사합니다. `int`와 같은 기본 타입은 `false` 입니다.|
|`std::is_member_pointer`|멤버 변수 포인터, 멤버 함수 포인터 인지 검사합니다.|

# 타입 특성

|항목|내용|
|--|--|
|`std::is_const`|`const` 타입인지 검사합니다.|
|`std::is_volatile`|`volatile` 타입인지 검사합니다.|
|`std::is_trivial`|[Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`std::is_trivially_copyable`|[스칼라 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%83%80%EC%9E%85) 이나 [Trivial 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#trivial-%ED%83%80%EC%9E%85%EA%B0%84%EB%8B%A8%ED%95%9C-%ED%83%80%EC%9E%85) 처럼 간단하게 복사 가능한지 검사합니다.|
|`std::is_standard_layout`|[표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`std::is_pod`(C++11~C++20)|[POD 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#pod-%ED%83%80%EC%9E%85plan-old-data) 인지 검사합니다.||
|`std::is_literal`(C++11~C++17)|[리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85) 인지 검사합니다.|
|`std::is_empty`|멤버 변수, 가상 함수, 비어있지 않은 부모 개체가 없는 빈 개체인지 검사합니다.|
|`std::is_polymorphic`|가상 함수가 있거나, 가상 함수를 상속한 다형성 타입인지 검사합니다.|
|`std::is_abstract`|적어도 1개 이상의 순가상 함수가 있는 추상 클래스 타입인지 검사합니다.|
|`std::is_signed`|양수/음수 처리를 지원하는 타입인지 검사합니다.|
|`std::is_unsigned`|양수/음수 처리를 지원하는 않는 타입인지 검사합니다.|

# 복사/이동/대입/소멸 속성 

|항목|내용|
|--|--|
|`std::is_constructible`<br/>`std::is_trivially_constructible`<br/>`std::is_nothrow_constructible`|열거된 인자 타입으로 생성될 수 있는지 검사합니다.|
|`std::is_default_constructible`<br/>`std::is_trivially_default_constructible`<br/>`std::is_nothrow_default_constructible`|기본 생성자로 생성될 수 있는지 검사합니다.|
|`std::is_copy_constructible`<br/>`std::is_trivially_copy_constructible`<br/>`std::is_nothrow_copy_constructible`|복사 생성자로 생성될 수 있는지 검사합니다.|
|`std::is_move_constructible`<br/>`std::is_trivially_move_constructible`<br/>` std::is_nothrow_move_constructible`|이동 생성자로 생성될 수 있는지 검사합니다.|
|`std::is_assignable`<br/>`std::is_trivially_assignable`<br/>` std::is_nothrow_assignable`|주어진 인자 타입으로 대입될 수 있는지 검사합니다.|
|`std::is_copy_assignable`<br/>`std::is_trivially_copy_assignable`<br/>` std::is_nothrow_copy_assignable`|복사 대입될 수 있는지 검사합니다.|
|`std::is_move_assignable`<br/>`std::is_trivially_move_assignable`<br/>` std::is_nothrow_move_assignable`|이동 대입될 수 있는지 검사합니다.|
|`std:: is_destructible`<br/>`std:: is_trivially_destructible`<br/>`std:: is_nothrow_destructible`|소멸 가능한 타입인지 검사합니다.|
|`std:: has_vitual_destuctor`|가상 소멸자인지 검사합니다.|

# 속성 질의

|항목|내용|
|--|--|
|`std::alignment_of`|메모리 정렬 크기를 구합니다.(`alignof()`와 동일)|
|`std::rank`|다차원 배열의 차원수를 구합니다.|
|`std::extent`|배열 차원의 요소 수를 구합니다.|

# 타입 관계

|항목|내용|
|--|--|
|`std::is_same`|두개의 타입이 동일한지 검사합니다. 이때 `const`와 `volatile`을 고려합니다. 즉 `int`와 `const int`는 다른 타입입니다.|
|`std::is_base_of`|`Base`가 `Derived`의 부모인지 검사합니다.[CloneTraits 구현](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/#clonetraits-%EA%B5%AC%ED%98%84) 의 `IsDerivedFrom` 참고|
|`std::is_convertible`|`From` 타입이 `To`로 변환 가능한지 검사합니다.|

# const-volatile

|항목|내용|
|--|--|
|`std::remove_cv`<br/>`std::remove_const`<br/>`std::remove_volatile`|`const`나 `volatile` 한정자를 제거합니다.|
|`std::add_cv`<br/>`std::add_const`<br/>`std::add_volatile`|`const`나 `volatile` 한정자를 추가합니다.|

# 참조자

|항목|내용|
|--|--|
|`std::remove_reference`|타입에서 좌측값 참조, 우측값 참조의 참조성을 제거합니다.|
|`std::add_lvalue_reference`<br/>`std::add_rvalue_reference`|타입에서 좌측값 참조, 우측값 참조의 참조성을 추가합니다.|

# 포인터

|항목|내용|
|--|--|
|`std::remove_pointer`|타입에서 포인터성을 제거합니다.|
|`std::add_pointer`|타입에서 포인터성을 추가합니다.|

# 부호 수정

|항목|내용|
|--|--|
|`std::make_signed`|양수/음수 처리를 지원하는 타입으로 변경합니다.|
|`std::make_unsigned`|양수/음수 처리를 지원하지 않는 타입으로 변경합니다.|

# 배열

|항목|내용|
|--|--|
|`std::remove_extent`|배열의 첫번째 차원을 제거합니다. 즉, `int[][][]` 는 `int[][]`가 됩니다.|
|`remove_all_extents`|배열의 모든 차원을 제거합니다.|
  
# 기타 변환

|항목|내용|
|--|--|
|`std::aligned_storage`(C++11~C++23)|(작성중)|
|`std::aligned_union`(C++11~C++23)|(작성중)|
|`std::decay`|(작성중)|
|`std::enable_if`|(작성중)|
|`std::conditional`|(작성중)|
|`std::common_type`|(작성중)|
|`std::underlying_type`|(작성중)|
|`std::result_of`(C++11~C++20)|(작성중)|

# traits 에서의 연산


# 함수

# (C++14~)

|항목|내용|
|--|--|
|`std::is_null_pointer`|`std::nullptr_t` 타입인지 검사합니다.|
|`std::is_final`|`final`클래스 타입인지 검사합니다.|

# (C++17~)

|항목|내용|
|--|--|
|`std::has_unique_object_representations`|(작성중)|
|`std::is_aggregate`|(작성중)|
|`std::is_swappable_with`<br/>`std::is_swappable`<br/>`std::is_nothrow_swappable_with`<br/>`std::is_nothrow_swappable`|(작성중)|
|`std::is_invocable`<br/>`std::is_invocable_r`<br/>`std::is_nothrow_invocable`<br/`std::is_nothrow_invocable_r`|(작성중)|
|`std::invoke_result`|(작성중)|
|`std::void_t`|(작성중)|
|`std::type_identity`|(작성중)|
|`std::conjunction`|(작성중)|
|`std::disjunction`|(작성중)|
|`std::negation`|(작성중)|

# (C++20~)

|항목|내용|
|--|--|
|`std::is_bounded_array`|(작성중)|
|`std::is_unbounded_array`|(작성중)|
|`std::is_nothrow_convertible`|`From` 타입이 `To`로 변환 가능한지 검사합니다.|
|`std::is_layout_compatible`|[표준 레이아웃 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%ED%91%9C%EC%A4%80-%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83-%ED%83%80%EC%9E%85) 등 메모리 레이아웃이 호환되는지 검사합니다.|
|`std::is_pointer_interconvertible_base_of`|(작성중)|
|`std::remove_cvref`|(작성중)|
|`std::common_reference`|(작성중)|
|`std::is_pointer_interconvertible_with_class`|(작성중)|
|`std::is_corresponding_member`|(작성중)|
|`std::is_constant_evaluated`|(작성중)|

# (C++23~)

|항목|내용|
|--|--|
|`std::is_implicit_lifetime`|(작성중)|
|`std::is_scoped_enum`|(작성중)|