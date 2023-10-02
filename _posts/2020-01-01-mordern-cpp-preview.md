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
    우측값 참조, 이동 생성자, 이동 대입 연산자, `auto`, `decltype`, 중괄호 초기화, 멤버 선언부 초기화, 무제한 공용체, `constexpr`, `static_assert`, 람다 표현식, 가변 템플릿, `nullptr`, `default`, `delete`, `override`, `final`, `noexcept`, 범위 기반 `for`, 범위 있는 열거형, 유니코드 문자열, 사용자 정의 리터럴, 명시적 형변환, 생성자 위임, 생성자 상속 등 런타임 성능 개선과 컴파일 타임 프로그래밍, 코딩 계약 강화, 코딩 편의성을 위해 많은 부분이 추가되었습니다.

# 런타임 성능 개선

|항목|내용|
|--|--|
|[이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/) (C++11~)|이동 연산을 위해 우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자가 추가되어 임시 개체 대입시 속도가 향상되었습니다.|
|[멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/) (C++11~)|멤버 함수에 `&`, `&&` 참조 지정자가 추가되어 좌측값에서 호출될때와 우측값에서 호출될 때를 구분하여 오버로딩 할 수 있습니다.|
|[무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/) (C++11~)|공용체 멤버에서 생성자/소멸자/가상 함수 사용 제한이 풀려 메모리 절약을 위한 코딩 자유도가 높아졌습니다.|
|[extern 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-extern-template/) (C++11~)|`extern` 템플릿을 추가하여 템플릿 선언을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다.| 

# 컴파일 타임 프로그래밍

|항목|내용|
|--|--|
|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/) (C++11~)|`constexpr`이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다.|
|[static_assert](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/) (C++11~)|`static_assert()`가 추가되어 컴파일 타임 진단이 가능해 졌습니다.| 
|[타입 특성(type_traits)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/) (C++11~)|`type_traits`는 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환을 합니다.|
|[noexcept 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) (C++11~)| `noexcept` 연산자를 이용하여 해당 함수가 `noexcept`인지 컴파일 타임에 검사할 수 있습니다.|
|[가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/) (C++11~)|가변 인자를 활용한 가변 템플릿이 추가되어 코딩 자유도가 높아졌습니다.|

# 코딩 계약 강화

|항목|내용|
|--|--|
|[타입 형식](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/) (C++11~)|타입 형식 체계를 수립하여 컴파일 타임 프로그래밍이나 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)시의 코딩 계약을 강화할 수 있습니다.|
|[noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) (C++11~)|`noexcept` 를 이용하여 예외 발생이 없는 함수인 nothrow 보증을 할 수 있습니다.<br/>소멸자는 기본적으로 `noexcept`로 동작합니다.|
|[nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) (C++11~)|`nullptr` 리터럴이 추가되어 좀더 타입에 안전한 코딩 계약이 가능해 졌습니다.|
|[명시적 형변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/) (C++11~)|`explicit` 형변환 연산자를 추가하여 명시적으로 형변환 할 수 있습니다.|
|[default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete) (C++11~)| `default`와 `delete`가 추가되어 암시적으로 생성되는 멤버 함수의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.|
|[override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override) (C++11~)|`override`가 추가되어 가상 함수 상속의 코딩 규약이 좀더 단단해졌습니다.|
|[final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final) (C++11~)| `final` 이 추가되어 가상 함수를 더이상 오버라이딩 못하게 할 수 있고, 강제적으로 상속을 제한할 수 있습니다.|
|[alignas, alignof](https://tango1202.github.io/mordern-cpp/mordern-cpp-align/) (C++11~)|`alignas()` 와 `alignof()` 를 이용하여 메모리 정렬 방식을 표준화 했습니다.|
|[attribute](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/) (C++11~)|`attribute`가 추가되어 컴파일러에게 부가 정보를 전달하는 방식을 표준화 했습니다.|

# 코딩 편의성

|항목|내용|
|--|--|
|[auto<br/>decltype<br/>후행 리턴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/) (C++11~)|값으로부터 타입을 추론하는 `auto` 타입과 `decltype()`이 추가되어 코딩이 간편해 졌습니다.<br/>함수 인자에 의존하여 리턴 타입을 결정하는 후행 리턴이 추가되어 좀더 동적인 함수 설계가 가능해 졌습니다.|
|리턴 타입 추론|(작성중)|
|[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/) (C++11~)|`{}` 초기화가 추가되어 클래스, 배열, 구조체를 일관성 있게 초기화 할 수 있습니다.<br/>`{}` 복사 초기화로 함수 인수 전달, 리턴문 작성을 간소화할 수 있습니다.<br/>`{}` 초기화시 인자의 암시적 형변환을 일부 차단하여, 코딩 계약이 개선되었습니다.<br/>`initializer_list` 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다.|
|[멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/) (C++11~)|비정적 멤버 변수도 멤버 선언부에서 초기화를 할 수 있어 초기화 작성이 쉬워졌습니다.|
|[범위 기반 for](https://tango1202.github.io/mordern-cpp/mordern-cpp-range-for/) (C++11~)|범위 기반 `for()`가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.|
|[범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/) (C++11~)|이름 범위를 한정하는 범위 있는 열거형이 추가되어 이름 충돌 회피가 쉬워졌습니다.|
|[람다 표현식, 클로져](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) (C++11~)|람다 표현식이 추가되어 1회용 익명 함수를 만들 수 있습니다.| 
|[인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-namespace/) (C++11~)|`inline namespace` 가 추가되어 API 버전 구성이 편리해 졌습니다.|
|[using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/) (C++11~)|`using`을 이용한 타입 별칭이 추가되어 `typedef` 보다 좀 더 직관적인 표현이 가능해 졌습니다.|
|[문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) (C++11~)|유니코드를 지원하는 `char8_t`, `char16_t`, `char32_t`, `u8string`, `u16string`, `u32string` 이 추가되었습니다.<br/>유니코드를 지원하는 `u8""`, `u""`, `U""` 리터럴이 추가되었습니다.<br/>`R"()"`리터럴이 추가되어 개행된 문자열이나 확장된 기호 표시를 좀더 편하게 입력할 수 있습니다.|
|[사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-user-literal/) (C++11~)|`int operator ""_km(long double val);`와 같은 사용자 정의 리터럴이 추가되어 단위계 처리가 쉬워졌습니다.|
|[생성자 위임, 생성자 상속](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/) (C++11~)|생성자 위임이 추가되어 생성자의 초기화 리스트 코드가 좀더 간결해 졌습니다.<br/>생성자 상속이 추가되어 부모 개체의 생성자도 상속받아 사용할 수 있어 자식 개체의 생성자 재정의 코드가 좀더 간결해 졌습니다.|
|[sizeof...()](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90) (C++11~)|`sizeof...()`이 추가되어 가변 템플릿의 가변 인자인 파라메터 팩의 인자수를 구할 수 있습니다.|
|[멤버 sizeof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-sizeof/) (C++11~)|`sizeof()`가 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있도록 개선되었습니다.|
|[템플릿 오른쪽 꺽쇠 괄호](https://tango1202.github.io/mordern-cpp/mordern-cpp-right-angle-bracket/) (C++11~)|파싱을 개선하여 템플릿 인스턴스화시 `>`가 중첩되어 `>>`와 같이 되더라도 공백을 추가할 필요가 없습니다.|

# 타입

|항목|내용|
|--|--|
|[long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/) (C++11~)|최소 8byte 크기를 보장하는 `long long` 타입이 추가되었습니다.<br/>`long long`용 정수형 상수인 `ll`, `ull`, `LL`, `ULL` 리터럴이 추가되었습니다.|
|[char8_t<br/>char16_t<br/>char32_t](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) (C++11~)|유니코드를 위한 타입이 추가되었습니다.|

# C언어 호환

|항목|내용|
|--|--|
|[가변 매크로](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-macro/) (C++11~)|가변 인자를 활용한 가변 매크로가 추가되어 C언어와의 호환성이 높아졌습니다.|

# deprecate/remove

|항목|내용|
|--|--|
|[동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) (~C++11)| 동적 예외 사양은 deprecate 되었습니다. 예외를 나열하는 것보다 `noexcept`로 예외가 발생하느냐 안하느냐만 관심을 둡니다.| 
|[export template](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#export-template) (~C++11)|`export template` 은 제대로 구현한 컴파일러는 드물고, 세부사항에 대한 의견되 일하지 않아 remove 되었습니다.|


