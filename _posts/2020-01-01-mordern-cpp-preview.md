---
layout: single
title: "#1. [모던 C++] 개요"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

모던 C++ 은,

1. 이동 연산을 지원하여 런타임 성능을 향상시키고, 
2. 컴파일 타임 프로그래밍이 가능하게 하여, 런타임에 했던 작업을 최대한 컴파일 타임으로 옮겨 런타임 성능을 향상시키고,
3. 느슨했던 코딩 규약을 좀더 강화하고,
4. 코딩 편의 기능들을 보강하거나 추가하여, 좀더 쉽게 코딩할 수 있도록

현대적인 언어 특성에 발맞춰 여러 항목이 추가되고, 수정되었습니다.

1. C++11

    
    [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto), [decltype](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype), [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/), [멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/), [무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/), [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/), [static_assert](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/), [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/), [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/), [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/), [default](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete), [delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete), [override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override), [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final), [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/), [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for), [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/), [유니코드 문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/), [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4), [명시적 형변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/), [생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84), [생성자 상속](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%83%81%EC%86%8D) 등 런타임 성능 개선과 컴파일 타임 프로그래밍, 코딩 계약 강화, 코딩 편의성을 위해 많은 부분이 추가되었습니다.

2. C++14

    [constexpr 함수 제약이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94), [비정적 멤버 변수의 멤버 선언부 초기화시 집합 초기화를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/#c14-%EB%B9%84%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EB%A9%A4%EB%B2%84-%EC%84%A0%EC%96%B8%EB%B6%80-%EC%B4%88%EA%B8%B0%ED%99%94%EC%8B%9C-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94), [변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF), [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto), [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0), [[[deprecated]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-attribute) 속성, [람다 캡쳐 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90-%EC%B4%88%EA%B8%B0%ED%99%94), [일반화된 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98), [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4), [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되었습니다. 

3. C++17
고전 C++ 개선

    임시 구체화와 복사 생략 보증
    
    단순한 중첩 네임스페이스

    if, switch의 초기식(selection statement width initializer)

    inline variable : inline 함수와 동일. 정적 멤버 변수 초기화

    

모던 C++ 개선    
 
    static_assert에서 설명 옵션
    
    람다 캡쳐 *this 값 복사

    u8 문자 리터럴(u8 문자열 리터럴은 C++11~)

    if constexpr

    constexpr 람다

    noexcept가 함수 유형에 포함됨
    
    클래스 템플릿 인자 추론(Class template argument deduction (CTAD))
    
    auto 템플릿 인자(declareing non-type template prameter with auto)
    
    가변 템플릿을 위한 Fold 표현식(folding expressions)



    중괄호 초기화시 auto 추론의 새로운 규칙(new rules for auto deduction from braced-init-list)

    어트리뷰트 네임스페이스

    어트리뷰트 추가 fallthrough, nodiscard, maybe_unused

    열거형 초기화(direct-list-initialization of enums)

모던 C++ 추가
    
    구조화된 바인딩 structured binding

    __has_include

STL

# Utility types

std::tuple:
std::apply
std::make_from_tuple
deduction guides
std::any
std::optional
std::variant
searchers
std::as_const
std::not_fn

# 기타

string_view
invoke
filesystem
sample(https://github.com/AnthonyCalandra/modern-cpp-features#stdsample)

https://github.com/AnthonyCalandra/modern-cpp-features#prefix-sum-algorithms

https://github.com/AnthonyCalandra/modern-cpp-features#string-conversion-tofrom-numbers

# Memory management

uninitialized memory algorithms
std::destroy_at
std::destroy
std::destroy_n
std::uninitialized_move
std::uninitialized_value_construct
weak_from_this
std::pmr::memory_resource and std::polymorphic_allocator
std::aligned_alloc
transparent std::owner_less
array support for std::shared_ptr
allocation functions with explicit alignment

# Compile-time programming

std::byte
std::conjunction/std::disjunction/std::negation
type trait variable templates (xxx_+v)
std::is_swappable
is_invocable
is_aggregate
std::has_unique_object_representations

# Algorithms
std::clamp
parallel algorithms and execution policies
std::inclusive_scan
std::exclusive_scan
std::gcd
std::lcm
std::reduce

# Iterators and containers

map/set extract and map/set merge(slicing for maps and sets https://github.com/AnthonyCalandra/modern-cpp-features#splicing-for-maps-and-sets)
map/unordered_map try_emplace and insert_or_assign
contiguous iterators (LegacyContiguousIterator)
non-member std::size/std::empty/std::data
# Numerics
mathematical special functions
3D std::hypot
# Others
cache line interface
std::launder
std::uncaught_exceptions
std::to_chars/std::from_chars
std::atomic<T>::is_always_lock_free
std::scoped_lock
std::timespec_get
rounding functions for std::chrono::duration and std::chrono::time_point


deprecate
noexcept 관련 throw() - deprecate ~C++17
the obsolete iostreams aliases ~C++17
trigraphs ~C++17
the register keyword ~C++17
bool increment ~C++17


std::iterator,
std::raw_storage_iterator,
std::get_temporary_buffer,
std::is_literal_type,
std::result_of,
all of <codecvt>






wiki
    Allow typename (as an alternative to class) in a template template parameter
    
    네임스페이스와 열거형에서 어트리뷰트 허용

    Hexadecimal floating-point literals

    Constant evaluation for all non-type template arguments

# 런타임 성능 개선

|항목|내용|
|--|--|
|[이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/) (C++11~)|**(C++11~)**<br/>이동 연산을 위해 우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자가 추가되어 임시 개체 대입시 속도가 향상되었습니다.|
|[무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/) (C++11~)|**(C++11~)**<br/>공용체 멤버에서 생성자/소멸자/가상 함수 사용 제한이 풀려 메모리 절약을 위한 코딩 자유도가 높아졌습니다.|
|[임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/) (C++17~)|**(C++17~)**<br/>임시 구체화와 복사 생략 보증을 통해 임시 개체가 불필요하게 복사되지 않음을 문법적으로 보증합니다. 기존에는 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)들이 표준화 되었습니다.|

# 컴파일 타임 프로그래밍

|항목|내용|
|--|--|
|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/) (C++11~)|**(C++11~)**<br/>`constexpr`이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다.<br/><br/>**(C++14~)**<br/>`constexpr` 함수 제약이 완화되어 지역 변수, 2개 이상의 리턴문, `if()`, `for()`, `while()` 등을 사용할 수 있습니다.([constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94) 참고)<br/><br/>**(C++17~)**<br/>[if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr)을 이용하면 조건에 맞는 부분만 컴파일하고, 그렇지 않으면 컴파일 하지 않습니다.|
|[static_assert](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/) (C++11~)|**(C++11~)**<br/>`static_assert()`가 추가되어 컴파일 타임 진단이 가능해 졌습니다.<br/><br/>**(C++17~)** [static_assert의 메시지 생략](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/#c17-static_assert%EC%9D%98-%EB%A9%94%EC%8B%9C%EC%A7%80-%EC%83%9D%EB%9E%B5)을 지원합니다.| 
|[noexcept 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) (C++11~)|**(C++11~)**<br/>`noexcept` 연산자를 이용하여 해당 함수가 `noexcept`인지 컴파일 타임에 검사할 수 있습니다.<br/><br/>**(C++17~)**<br/>`noexcept`가 함수 유형에 포함되어 예외 처리에 대한 코딩 계약을 좀더 단단하게 할 수 있습니다.([함수 유형에 포함된 noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/#c17-%ED%95%A8%EC%88%98-%EC%9C%A0%ED%98%95%EC%97%90-%ED%8F%AC%ED%95%A8%EB%90%9C-noexcept) 참고)|
|개선된 템플릿 (C++11~)|**(C++11~)**<br/>가변 인자를 활용한 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)이 추가되어 코딩 자유도가 높아졌습니다.<br/>[sizeof...()](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 가변 템플릿의 가변 인자인 파라메터 팩의 인자수를 구할 수 있습니다.<br/>`extern`으로 템플릿 선언을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다.([extern 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#extern-%ED%85%9C%ED%94%8C%EB%A6%BF) 참고)<br/><br/>**(C++14~)**<br/>[변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 변수도 템플릿으로 만들 수 있습니다.<br/>[클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 템플릿 함수처럼 타입을 생략할 수 있습니다.|

# 코딩 계약 강화

|항목|내용|
|--|--|
|[타입 형식](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/) (C++11~)|**(C++11~)**<br/>타입 형식 체계를 수립하여 컴파일 타임 프로그래밍이나 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)시의 코딩 계약을 강화할 수 있습니다.|
|[noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) (C++11~)|**(C++11~)**<br/>`noexcept` 를 이용하여 예외 발생이 없는 함수인 nothrow 보증을 할 수 있습니다.<br/>소멸자는 기본적으로 `noexcept`로 동작합니다.|
|[명시적 형변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/) (C++11~)|**(C++11~)**<br/>`explicit` 형변환 연산자를 추가하여 명시적으로 형변환 할 수 있습니다.|
|[alignas, alignof](https://tango1202.github.io/mordern-cpp/mordern-cpp-align/) (C++11~)|**(C++11~)**<br/>`alignas()` 와 `alignof()` 를 이용하여 메모리 정렬 방식을 표준화 했습니다.|
|[attribute](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/) (C++11~)|**(C++11~)**<br/>`attribute`가 추가되어 컴파일러에게 부가 정보를 전달하는 방식을 표준화 했습니다.<br/><br/>**(C++14~)**<br/>[[deprecated]]가 추가되었습니다.([표준 attribute](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-attribute) 참고)|

# 코딩 편의성

|항목|내용|
|--|--|
|[auto<br/>decltype<br/>후행 리턴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/) (C++11~)|**(C++11~)**<br/>값으로부터 타입을 추론하는 `auto` 타입과 `decltype()`이 추가되어 코딩이 간편해 졌습니다.<br/>함수 인자에 의존하여 리턴 타입을 결정하는 후행 리턴이 추가되어 좀더 동적인 함수 설계가 가능해 졌습니다.<br/><br/>**(C++14~)**<br/>[decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)를 이용하여 `decltype()`의 `()`내 표현식이 복잡할 경우 `decltype(auto)` 와 같이 좀더 간결하게 작성할 수 있습니다.<br/>[리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)이 가능하여 후행 리턴 대신 `auto`나 `decltype(auto)` 를 사용할 수 있습니다.|
|[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/) (C++11~)|**(C++11~)**<br/>`{}` 초기화가 추가되어 클래스, 배열, 구조체를 일관성 있게 초기화 할 수 있습니다.<br/>`{}` 복사 초기화로 함수 인수 전달, 리턴문 작성을 간소화할 수 있습니다.<br/>`{}` 초기화시 인자의 암시적 형변환을 일부 차단하여, 코딩 계약이 개선되었습니다.<br/>`initializer_list` 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다.|
|[멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/) (C++11~)|**(C++11~)**<br/>비정적 멤버 변수도 멤버 선언부에서 초기화를 할 수 있어 초기화 작성이 쉬워졌습니다.<br/><br/>**(C++14~)**<br/>[비정적 멤버 변수의 멤버 선언부 초기화시 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/#c14-%EB%B9%84%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EB%A9%A4%EB%B2%84-%EC%84%A0%EC%96%B8%EB%B6%80-%EC%B4%88%EA%B8%B0%ED%99%94%EC%8B%9C-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 허용합니다.|
|개선된 리터럴 (C++11~)|**(C++11~)**<br/>[nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) 리터럴이 추가되어 좀더 타입에 안전한 코딩 계약이 가능해 졌습니다.<br/>`int operator ""_km(long double val);`와 같은 [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 단위계 처리가 쉬워졌습니다.<br/><br/>**(C++14~)**<br/>[이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.<br/>가독성을 위한 [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어 `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.|
|[개선된 제어문](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/) (C++11~)|**(C++11~)**<br/>[범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.<br/><br/>**(C++17~)**<br/>[초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch) 가 추가되어 함수 리턴값을 평가하고 소멸하는 코드가 단순해 졌습니다.|
|개선된 변수 및, 함수 및 클래스 표현 (C++11~)|**(C++11~)**<br/>[멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 추가되어 멤버 함수에 `&`, `&&` 로 좌측값에서 호출될때와 우측값에서 호출될 때를 구분하여 오버로딩 할 수 있습니다.<br/>[default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete)가 추가되어 암시적으로 생성되는 멤버 함수의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.<br/>[override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override)가 추가되어 가상 함수 상속의 코딩 규약이 좀더 단단해졌습니다.<br/>[final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final)이 추가되어 가상 함수를 더이상 오버라이딩 못하게 할 수 있고, 강제적으로 상속을 제한할 수 있습니다.<br/>[생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84)이 추가되어 생성자의 초기화 리스트 코드가 좀더 간결해 졌습니다.<br/>[생성자 상속](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%83%81%EC%86%8D)이 추가되어 부모 개체의 생성자도 상속받아 사용할 수 있어 자식 개체의 생성자 재정의 코드가 좀더 간결해 졌습니다.<br/><br/>**(C++17~)**<br/>[인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 `include` 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, 클래스 정적 멤버 변수 정의 및 초기화가 쉬워졌습니다.|
|[개선된 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/) (C++11~)|**(C++11~)**<br/>[인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4) 가 추가되어 API 버전 구성이 편리해 졌습니다.<br/><br/>**C++14**<br/> [단순한 중첩 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#c17-%EB%8B%A8%EC%88%9C%ED%95%9C-%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4) 표현이 추가되어 `::` 로 중첩 네임스페이스를 표현할 수 있습니다.|
|[범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/) (C++11~)|**(C++11~)**<br/>이름 범위를 한정하는 범위 있는 열거형이 추가되어 이름 충돌 회피가 쉬워졌습니다.|
|[람다 표현식, 클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) (C++11~)|**(C++11~)**<br/>람다 표현식이 추가되어 1회용 익명 함수를 만들 수 있습니다.<br/><br/>**(C++14~)**<br/>[람다 캡쳐 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90-%EC%B4%88%EA%B8%B0%ED%99%94)로 람다내에서 사용하는 임의 변수를 정의하여 사용할 수 있습니다.<br/>`auto`를 인자로 받아 마치 템플릿 함수처럼 동작하는 [일반화된 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98)가 추가되었습니다.<br/><br/>**(C++17~)**<br/>람다 캡쳐시 `*this` 를 이용하여 개체 자체를 복제하여 사용합니다.([캡쳐](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90) 참고)<br/>람다 함수는 요구사항이 충족되면 암시적으로 컴파일 타임 함수로 만들어지고,`constexpr` 을 지정하여 명시적으로 컴파일 타임 함수로 만들 수 있습니다.([constexpr 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c17-constexpr-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98) 참고)| 
|[using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/) (C++11~)|**(C++11~)**<br/>`using`을 이용한 타입 별칭이 추가되어 `typedef` 보다 좀 더 직관적인 표현이 가능해 졌습니다.|
|[문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) (C++11~)|**(C++11~)**<br/>유니코드를 지원하는 `char16_t`, `char32_t`, `u16string`, `u32string` 이 추가되었습니다.<br/>유니코드를 지원하는 `u8""`, `u""`, `U""`, `'u'`(문자), `'U'`(문자) 리터럴이 추가되었습니다.<br/>`R"()"`리터럴이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.<br/><br/>**(C++17~)**<br/>유니코드를 지원하는 `'u8'`(문자) 리터럴이 추가되었습니다.|
|[멤버 sizeof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-sizeof/) (C++11~)|**(C++11~)**<br/>`sizeof()`가 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있도록 개선되었습니다.|
|[템플릿 오른쪽 꺽쇠 괄호](https://tango1202.github.io/mordern-cpp/mordern-cpp-right-angle-bracket/) (C++11~)|**(C++11~)**<br/>파싱을 개선하여 템플릿 인스턴스화시 `>`가 중첩되어 `>>`와 같이 되더라도 공백을 추가할 필요가 없습니다.|

# 타입

|항목|내용|
|--|--|
|[long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/) (C++11~)|**(C++11~)**<br/>최소 8byte 크기를 보장하는 `long long` 타입이 추가되었습니다.<br/>`long long`용 정수형 상수인 `ll`, `ull`, `LL`, `ULL` 리터럴이 추가되었습니다.|
|[char8_t<br/>char16_t<br/>char32_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) (C++11~)|**(C++11~)**<br/>유니코드를 위한 타입이 추가되었습니다.|

# C언어 호환

|항목|내용|
|--|--|
|[가변 매크로](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-macro/) (C++11~)|**(C++11~)**<br/>가변 인자를 활용한 가변 매크로가 추가되어 C언어와의 호환성이 높아졌습니다.|

# deprecate/remove

|항목|내용|
|--|--|
|[동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) (~C++11)| 동적 예외 사양은 deprecate 되었습니다. 예외를 나열하는 것보다 `noexcept`로 예외가 발생하느냐 안하느냐만 관심을 둡니다.| 
|[export 템플릿](??) (~C++11)|`export` 템플릿은 제대로 구현한 컴파일러는 드물고, 세부사항에 대한 의견이 일치하지 않아 remove 되었습니다.|


