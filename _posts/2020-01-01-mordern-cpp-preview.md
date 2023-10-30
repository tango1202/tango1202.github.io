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

1. [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)을 지원하여 런타임 성능을 향상시키고, 
2. 런타임에 했던 작업을 최대한 컴파일 타임으로 전환하여 런타임 성능을 향상시키며,
3. 느슨했던 코딩 규약을 좀더 강화하고,
4. 좀더 쉽게 코딩할 수 있도록 코딩 편의 기능들을 추가하여

현대적인 언어 특성에 발맞춰 좀더 단순하게 코딩할 수 있도록 개션되었습니다.

STL의 변경 내용은 [[모던 C++ STL] 개요](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/)를 참고하기 바랍니다.

1. C++11

    런타임 성능 개선과 컴파일 타임 프로그래밍을 위해 많은 부분을 추가하였습니다. C++11 이전을 고전 C++이라 하고, 이후를 모던 C++으로 구분할 만큼 많이 변했습니다. 특히 [우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto), [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)은 혁신적인 변화라 할 수 있습니다.
    
    [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto), [decltype](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype), [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/), [멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/), [무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/), [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/), [static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/), [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/), [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/), [nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/), [default](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete), [delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete), [override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override), [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final), [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/), [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for), [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/), [유니코드 문자](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/), [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4), [명시적 형변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/), [생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84), [생성자 상속](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%83%81%EC%86%8D) 등 런타임 성능 개선과 컴파일 타임 프로그래밍, 코딩 계약 강화, 코딩 편의성을 위해 많은 부분이 추가되었습니다.

2. C++14

    C++11에 추가된 내용을 일부 보강하였습니다. 특히 [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)으로 C++11 의 [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 개념을 깔끔하게 보강했고, [constexpr 함수 제약을 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)하여 컴파일 타임 함수 작성 편의성이 향상됐습니다.

    [변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF), [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto), [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0), [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94), [비정적 멤버 변수의 멤버 선언부 초기화시 집합 초기화를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/#c14-%EB%B9%84%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EB%A9%A4%EB%B2%84-%EC%84%A0%EC%96%B8%EB%B6%80-%EC%B4%88%EA%B8%B0%ED%99%94%EC%8B%9C-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94), [[[deprecated]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-%EC%86%8D%EC%84%B1), [람다 캡쳐 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90-%EC%B4%88%EA%B8%B0%ED%99%94), [일반화된 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98), [이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4), [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되었습니다. 

3. C++17

    그동안 컴파일러에 의존했던 최적화 부분이 [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)으로 표준화 되었고, [if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr)과 [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)으로 컴파일 타임 프로그래밍을 좀더 강화 했으며, [초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch)와 [구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/)으로 컨테이너 관련 처리 코드가  깔끔해 졌습니다.

    [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/), [if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr), [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0), [초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch), [구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/), [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/), [auto의 중괄호 초기화 특수 추론 규칙 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c17-auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99-%EA%B0%9C%EC%84%A0), [열거형의 중괄호 직접 초기화를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#c17-%EC%97%B4%EA%B1%B0%ED%98%95%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%97%88%EC%9A%A9), [람다 캡쳐시 *this 를 이용](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90), [constexpr 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c17-constexpr-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98), [static_assert()의 메시지 생략](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/#c17-static_assert%EC%9D%98-%EB%A9%94%EC%8B%9C%EC%A7%80-%EC%83%9D%EB%9E%B5), [noexcept 함수 유형 포함](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/#c17-noexcept-%ED%95%A8%EC%88%98-%EC%9C%A0%ED%98%95-%ED%8F%AC%ED%95%A8), [Fold 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D), [템플릿 인자로 auto를 사용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-auto-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90),  [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4), [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4),[[[fallthrough]], [[nodiscard]], [[maybe_unused]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-%EC%86%8D%EC%84%B1)가 추가되었습니다.

4. C++20

    * (O) Concept : 공통된 성질끼리 묶는기능. 제약조건(?)
        * https://github.com/AnthonyCalandra/modern-cpp-features#concepts

    * (O) 코루틴 : 함수를 yield 위치에서 일시 정지후 재진행
        * https://github.com/AnthonyCalandra/modern-cpp-features#coroutines       

    * (O) 모듈 : include 기법외 방법. 컴파일 시간 개선         

    * (O) 3중 비교 <=>
        https://en.cppreference.com/w/cpp/language/operator_comparison#Three-way_comparison

    * (O)지정된 초기화(Designated initalizers) A a{ .x = 1 };
        * https://github.com/AnthonyCalandra/modern-cpp-features#designated-initializers
        * 지정자 순서는 정의 순서와 같아야 함. 구조체 안의 구조체 초기화 안됨. 지정자를 사용하면 모두 지정자로 초기화 해야 함.

        ```cpp
        struct A { int x, y; };
        struct B { struct A a; };
        
        struct A a = {.y = 1, .x = 2}; // valid C, invalid C++ (out of order)
        int arr[3] = {[1] = 5};        // valid C, invalid C++ (array)
        struct B b = {.a.x = 0};       // valid C, invalid C++ (nested)
        struct A a = {.x = 1, 2};      // valid C, invalid C++ (mixed)
        ```
    * 템플릿 개선
        * (O) 탬플릿의 비타입 인수에서 클래스 사용
            * https://github.com/AnthonyCalandra/modern-cpp-features#class-types-in-non-type-template-parameters

            * https://en.cppreference.com/w/cpp/language/template_parameters#Non-type_template_parameter
            
            * 클래스는 operator== 구현해야 함        
        
        * (O) 축약된 기능 템플릿
            * https://en.cppreference.com/w/cpp/language/function_template#Abbreviated_function_template

        * (O) initializer 사용시 템플릿 인수 추론
          cppreference(Initializer list constructors in class template argument deduction)
            * 기존에는 initializer 사용시 안됐으나, 이제는 됨. std::vector v{ 1, 2, 3, 4, 5 };    

    * 람다 개선

        * (O)템플릿 람다 auto f = []<typename T>(std::vector<T>vector) { ...};
            * https://github.com/AnthonyCalandra/modern-cpp-features#template-syntax-for-lambdas

        * (O) 람다 캡쳐에서 파라메터 팩 확장 지원 ..., &... 
            * https://github.com/AnthonyCalandra/modern-cpp-features#lambda-capture-of-parameter-pack

        * (O) 람다 캡쳐시 명시적 this
            * https://github.com/AnthonyCalandra/modern-cpp-features#deprecate-implicit-capture-of-this
            * 람다 캡쳐에서 [=, this] 허용. 기존에는 [=] 만해도 this가 캡쳐되어 [=,this] 는 오류였음. 
            * 암시적 this 캡쳐 중단
                * https://en.cppreference.com/w/cpp/language/lambda#Lambda_capture


                ```cpp
                void S2::f(int i)
                {
                    [=] {};        // OK: by-copy capture default
                    [=, &i] {};    // OK: by-copy capture, except i is captured by reference
                    [=, *this] {}; // until C++17: Error: invalid syntax
                                // since C++17: OK: captures the enclosing S2 by copy
                    [=, this] {};  // until C++20: Error: this when = is the default
                                // since C++20: OK, same as [=]
                }
                ```
    * (O) 기능 테스트 매크로 : C++11 이상의 기능이 있는지 컴파일 타임 검사 __has_cpp_attributes
 
    * (O) constexpr 완화
        * (O) constexpr virtual https://github.com/AnthonyCalandra/modern-cpp-features#constexpr-virtual-functions
        * (O) constexpr에서 런타임에서 동작시 try - catch 허용, throw는 안됨
        * (O) constexpr 함수에서 asm : 런타임만 가능
   
    * (O) consteval 즉시 평가 함수
        * https://github.com/AnthonyCalandra/modern-cpp-features#immediate-functions
        * constexpr 함수는 런타임에도 동작함. consteval은 컴파일에만 동작함

    * (O) constinit 컴파일 타임에 static 변수가 초기화되도록 강제함 static 생성 순서가 명확해짐
        * https://github.com/AnthonyCalandra/modern-cpp-features#constinit


    * (O) Ranged for 에서 초기화 문
        * https://github.com/AnthonyCalandra/modern-cpp-features#range-based-for-loop-with-initializer

    * (O) using enum : 열거형 이름을 생략하고 사용.
        * https://github.com/AnthonyCalandra/modern-cpp-features#using-enum

    * (O) char8_t
        * https://github.com/AnthonyCalandra/modern-cpp-features#char8_t

    * (O) 부호있는 정수의 표현범위 변경 2의 보수로 저장
        * 기존 보장 범위 1의 보수 -2^(n-1) - 1 ~ 2^(n-1) - 1(-127~127)
        * 2의 보수 -2^(n-1) ~ 2^(n-1) - 1 (-128~127)

    * (O) new 연산자의 배열 크기 추론
      
        * double* p1 = new double[]{ 1, 2, 3 }; // new double[3]과 동치

    * () 크기가 정해진 배열 포인터를 크기가 알수없는 배열 포인터로 변환(경계를 알 수 없는 배열로의 허용)
        * https://wg21.link/P0388R4

        ```cpp
        void f(int(&)[]);
        int arr[1];

        f(arr);
        int(&r)[] = arr;
        ```
     * (O) explicit(bool) : true인 경우만 명시적으로 동작함
        * https://github.com/AnthonyCalandra/modern-cpp-features#explicitbool 
        * https://en.cppreference.com/w/cpp/language/explicit

    * (O) 비트 필드 생성자에서 초기화
        * https://en.cppreference.com/w/cpp/language/bit_field#Cpp20_Default_member_initializers_for_bit_fields

    * (O) inline 네임스페이스 중첩 : namespace A::inline B {}

    * (O) Bitwise shift operators unified behavior 

    * (O) __VA_OPT__ 가변 인수가 있을 경우에는 x로 치환되고, 없을 경우에는 그냥 빈 토큰으로 처리됩니다.     

    * (O) 컴파일 타임 상수식에서 dynamic_cast와 typeid 사용(https://wg21.link/P1327R1)
        * 상수 표현식 의 Dynamic_cast 및 다형성 typeid

    (O) 어트리뷰트 : likly, unlikely, no_unique_address, nodiscard(reason), 
        * https://github.com/AnthonyCalandra/modern-cpp-features#likely-and-unlikely-attributes


const&-멤버에 대한 한정된 포인터 const&-qualified pointers to members(https://wg21.link/P0704R1)

평가되지 않은 컨텍스트의 람다(https://en.cppreference.com/w/cpp/language/lambda#Lambdas_in_unevaluated_contexts)

암시적 람다 캡쳐 단순화 Simplifying implicit lambda capture( https://wg21.link/P0588R1)

기본 구성 가능 및 할당 가능 상태 비저장 람다 Default constructible and assignable stateless lambdas https://en.cppreference.com/w/cpp/language/lambda

const기본 복사 생성자와 불일치 const mismatch with defaulted copy constructor https://wg21.link/P0641R2

전문 분야에 대한 액세스 확인 Access checking on specializations https://wg21.link/P0692R1

표시되지 않는 ADL 및 기능 템플릿 ADL and function templates that are not visible https://wg21.link/P0846R0


상수 평가를 위해constexpr 함수 정의가 필요한 시기 지정 Specify when constexpr function definitions are needed for constant evaluation https://wg21.link/P0859R0

typename을 더 선택적으로 만듭니다 . Make typename more optional  https://wg21.link/P0634R3

조건부로 사소한 특수 멤버 함수 Conditionally Trivial Special Member Functions https://wg21.link/P0848R3

구조적 바인딩 사용자 정의 지점 찾기 규칙 완화 Relaxing the structured bindings customization point finding rules  https://wg21.link/P0961R1

range-for 루프 사용자 정의 지점 찾기 규칙 완화  Relaxing the range-for loop customization point finding rules https://wg21.link/P0962R1

액세스 가능한 멤버에 대한 구조적 바인딩 허용 Allow structured bindings to accessible members https://wg21.link/P0969R0

사용자 선언 생성자를 사용한 집계 금지 Prohibit aggregates with user-declared constructors https://wg21.link/P1008R1

비교를 위한 일관성 개선 Consistency improvements for comparisons https://wg21.link/P1120R0

제한된 선언 에 대한 또 다른 접근 방식 Yet another approach for constrained declarations https://wg21.link/P1141R2

내부 노동조합의 활동 회원 변경constexpr Changing the active member of a union inside constexpr https://wg21.link/P1330R0

집계의 괄호 로 묶인 초기화 Parenthesized initialization of aggregates (https://wg21.link/P0960R3)

더욱 강화된 유니코드 요구 사항 https://wg21.link/P1041R4 https://wg21.link/P1139R2

다양한 예외 사양을 사용하여 명시적으로 기본 설정된 함수 https://wg21.link/P1286R2

구조적 바인딩의 람다 캡처 및 스토리지 클래스 지정자 https://wg21.link/P1091R3 https://wg21.link/P1381R1

constexpr컨테이너 운영 https://wg21.link/P0784R7

volatile 일부 deprecate (https://en.cppreference.com/w/cpp/language/cv#Notes)

아래 첨자에서 쉼표 연산자 지원 중단(Deprecate comma operator in subscripts) https://en.cppreference.com/w/cpp/language/operator_other#Built-in_comma_operator

constexpr함수 의 사소한 기본 초기화 https://wg21.link/P1331R2

별칭 템플릿에 대한 클래스 템플릿 인수 추론 https://wg21.link/P1814R0 

집계에 대한 클래스 템플릿 인수 공제 https://wg21.link/P1816R0 https://wg21.link/P2082R1

더 많은 로컬 개체 및 rvalue 참조에 대한 암시적 이동 https://wg21.link/P1825R0

값별 기본값 비교 허용 https://wg21.link/P1946R0

비유형 템플릿 매개변수와의 불일치 https://wg21.link/P1907R1

의사 소멸자는 개체 수명을 종료합니다 https://wg21.link/P0593R6

boolT* 로 변환하는 것은 범위를 좁히는 것을 고려해야 합니다. https://wg21.link/P1957R2

# STL

* 소멸자 호출 생략

    delete(std::destroying_delete) b; // 소멸자 호출 X



포매팅 라이브러리

수학함수





# 런타임 성능 개선

|항목|내용|
|--|--|
|[이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/) (C++11~)|**(C++11~)**<br/>[이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)을 위해 [우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다.|
|[무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/) (C++11~)|**(C++11~)**<br/>[공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 멤버에서 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)/[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 사용 제한이 풀린 [무제한 공용체](https://tango1202.github.io/mordern-cpp/mordern-cpp-unrestricted-union/)가 추가되어 메모리 절약을 위한 코딩 자유도가 높아졌습니다.|
|[임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/) (C++17~)|**(C++17~)**<br/>[임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.|

# 컴파일 타임 프로그래밍

|항목|내용|
|--|--|
|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/) (C++11~)|**(C++11~)**<br/>[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다.<br/><br/>**(C++14~)**<br/>[constexpr 함수의 제약이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)되어 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), 2개 이상의 리턴문, `if()`, `for()`, `while()` 등을 사용할 수 있습니다.<br/><br/>**(C++17~)**<br/>[if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr)을 이용하면 조건에 맞는 부분만 컴파일하고, 그렇지 않으면 컴파일 하지 않습니다.|
|[static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/)|**(C++11~)**<br/>[static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/)가 추가되어 컴파일 타임 진단이 가능해 졌습니다.<br/><br/>**(C++17~)**<br/>[static_assert()의 메시지 생략](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/#c17-static_assert%EC%9D%98-%EB%A9%94%EC%8B%9C%EC%A7%80-%EC%83%9D%EB%9E%B5)을 지원합니다.| 
|개선된 템플릿 (C++11~)|**(C++11~)**<br/>파라메터 팩을 이용한 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)이 추가되어 코딩 자유도가 높아졌습니다.<br/>[sizeof...()](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)에서 [파라메터 팩](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)의 인자수를 구할 수 있습니다.<br/>[extern으로 템플릿 선언](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#extern-%ED%85%9C%ED%94%8C%EB%A6%BF)을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다.<br/>[템플릿 오른쪽 꺽쇠 괄호](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%A5%B8%EC%AA%BD-%EA%BA%BD%EC%87%A0-%EA%B4%84%ED%98%B8) 파싱을 개선하여 템플릿 인스턴스화시 `>`가 중첩되어 `>>`와 같이 되더라도 공백을 추가할 필요가 없습니다.<br/><br/>**(C++14~)**<br/>[변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 변수도 템플릿으로 만들 수 있습니다.<br/><br/>**(C++17~)**<br/>[클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 템플릿 함수처럼 타입을 생략할 수 있습니다.<br/>템플릿이 타입이 아닌 개체를 [템플릿 인자로 사용할때 auto를 사용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-auto-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)할 수 있습니다.<br/>[Fold 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#c17-fold-%ED%91%9C%ED%98%84%EC%8B%9D)을 이용하여 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)에서 [파라메터 팩](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)을 재귀적으로 반복하여 전개할 수 있습니다.|

# 코딩 계약 강화

|항목|내용|
|--|--|
|[타입 형식](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/) (C++11~)|**(C++11~)**<br/>[타입 형식](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/) 체계를 수립하여 컴파일 타임 프로그래밍이나 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)시의 코딩 계약을 강화할 수 있습니다.|
|[noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) (C++11~)|**(C++11~)**<br/>[noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 함수의 예외 방출 여부를 보증합니다.<br/>소멸자는 기본적으로 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 동작합니다.<br/>[noexcept 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 해당 함수가 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)인지 컴파일 타임에 검사할 수 있습니다.<br/><br/>**(C++17~)**<br/>[noexcept가 함수 유형에 포함](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/#c17-noexcept-%ED%95%A8%EC%88%98-%EC%9C%A0%ED%98%95-%ED%8F%AC%ED%95%A8)되어 예외 처리에 대한 코딩 계약을 좀더 단단하게 할 수 있습니다.|
|[명시적 형변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/) (C++11~)|**(C++11~)**<br/>[explicit 형변환 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/)가 추가되어 [명시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 할 수 있습니다.|
|[속성](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/) (C++11~)|**(C++11~)**<br/>[attribute](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/)가 추가되어 컴파일러에게 부가 정보를 전달하는 방식을 표준화 했습니다.<br/><br/>**(C++14~)**<br/>[표준 속성](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-%EC%86%8D%EC%84%B1)에 [[[deprecated]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-%EC%86%8D%EC%84%B1)가 추가되었습니다.<br/><br/>**(C++17~)**<br/>[표준 속성](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-%EC%86%8D%EC%84%B1)에 [[[fallthrough]], [[nodiscard]], [[maybe_unused]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-%EC%86%8D%EC%84%B1)가 추가되었습니다.<br/>`[[msvc::noinline]]` 와 같이 [제조사 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)를 사용할 수 있습니다.|

# 코딩 편의성

|항목|내용|
|--|--|
|[auto<br/>decltype<br/>후행 리턴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/) (C++11~)|**(C++11~)**<br/>값으로부터 타입을 추론하는 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)와 [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)이 추가되어 코딩이 간편해 졌습니다.<br/>[함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 의존하여 리턴 타입을 결정하는 [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)이 추가되어 좀더 동적인 함수 설계가 가능해 졌습니다.<br/><br/>**(C++14~)**<br/>[decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype)의 `()`내 표현식이 복잡할 경우 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)와 같이 좀더 간결하게 작성할 수 있습니다.<br/>[리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)이 가능하여 [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 대신 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)나 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)를 사용할 수 있습니다.<br/><br/>**(C++17~)**<br/>[auto의 중괄호 초기화 특수 추론 규칙 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c17-auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99-%EA%B0%9C%EC%84%A0)이 적용되어, [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 로 추론되는 오류가 개선되었습니다.|
|[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/) (C++11~)|**(C++11~)**<br/>[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)가 추가되어 클래스, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/), 구조체를 일관성 있게 초기화 할 수 있습니다.<br/>[중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)로 함수 인수 전달, 리턴문 작성을 간소화할 수 있습니다.<br>[중괄호 초기화시 인자의 암시적 형변환을 일부 차단](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98-%EC%B0%A8%EB%8B%A8)하여, 코딩 계약이 개선되었습니다.<br/>[initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)가 추가되어 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)등 컨테이너 요소 추가가 간편해 졌습니다.|
|[멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/) (C++11~)|**(C++11~)**<br/>비정적 멤버 변수도 [멤버 선언부에서 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/)를 할 수 있어 초기화 작성이 쉬워졌습니다.<br/><br/>**(C++14~)**<br/>[비정적 멤버 변수의 멤버 선언부 초기화시 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/#c14-%EB%B9%84%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EB%A9%A4%EB%B2%84-%EC%84%A0%EC%96%B8%EB%B6%80-%EC%B4%88%EA%B8%B0%ED%99%94%EC%8B%9C-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 허용합니다.|
|개선된 리터럴 (C++11~)|**(C++11~)**<br/>[nullptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-nullptr/) 리터럴이 추가되어 좀더 타입에 안전한 코딩 계약이 가능해 졌습니다.<br/>`int operator ""_km(long double val);`와 같은 [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 단위계 처리가 쉬워졌습니다.<br/><br/>**(C++14~)**<br/>[이진 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%9D%B4%EC%A7%84-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 `0b`, `0B` 접두어로 이진수 상수를 표현할 수 있습니다.<br/>가독성을 위한 [숫자 구분자](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c14-%EC%88%AB%EC%9E%90-%EA%B5%AC%EB%B6%84%EC%9E%90)가 추가되어 `1'000'000`와 같이 작은 따옴표(`'`)를 숫자 사이에 선택적으로 넣을 수 있습니다.<br/><br/>**(C++17)**<br/>`0xA.9p11`과 같은 [16진수 부동 소수점 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#c17-16%EC%A7%84%EC%88%98-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EB%A6%AC%ED%84%B0%EB%9F%B4)을 제공합니다.|
|[개선된 제어문](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/) (C++11~)|**(C++11~)**<br/>[범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)가 추가되어 컨테이너 요소의 탐색 처리가 쉬워졌습니다.<br/><br/>**(C++17~)**<br/>[초기식을 포함하는 if(), switch()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#c17-%EC%B4%88%EA%B8%B0%EC%8B%9D%EC%9D%84-%ED%8F%AC%ED%95%A8%ED%95%98%EB%8A%94-if-switch)가 추가되어 함수 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 평가하고 소멸하는 코드가 단순해 졌습니다.|
|개선된 변수 및 함수 및 클래스 표현 (C++11~)|**(C++11~)**<br/>[멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 추가되어 멤버 함수에 `&`, `&&` 로 좌측값에서 호출될때와 우측값에서 호출될 때를 구분하여 오버로딩 할 수 있습니다.<br/>[default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#default%EC%99%80-delete)가 추가되어 암시적으로 생성되는 멤버 함수의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.<br/>[override](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#override)가 추가되어 가상 함수 상속의 코딩 규약이 좀더 단단해졌습니다.<br/>[final](https://tango1202.github.io/mordern-cpp/mordern-cpp-function-default-delete-override-final/#final)이 추가되어 가상 함수를 더이상 오버라이딩 못하게 할 수 있고, 강제적으로 상속을 제한할 수 있습니다.<br/>[생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84)이 추가되어 생성자의 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 코드가 좀더 간결해 졌습니다.<br/>[생성자 상속](https://tango1202.github.io/mordern-cpp/mordern-cpp-delegating-inherited-constructor/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%83%81%EC%86%8D)이 추가되어 부모 개체의 생성자도 상속받아 사용할 수 있어 자식 개체의 생성자 재정의 코드가 좀더 간결해 졌습니다.<br/><br/>**(C++17~)**<br/>[인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, [클래스 정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 정의 및 초기화가 쉬워졌습니다.|
|[개선된 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/) (C++11~)|**(C++11~)**<br/>[인라인 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)가 추가되어 API 버전 구성이 편리해 졌습니다.<br/><br/>**(C++14~)**<br/>[중첩 네임스페이스 표현이 단순](https://tango1202.github.io/mordern-cpp/mordern-cpp-namespace/#c17-%EB%8B%A8%EC%88%9C%ED%95%9C-%EC%A4%91%EC%B2%A9-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)해져 `::` 로 표현할 수 있습니다.|
|[범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/) (C++11~)|**(C++11~)**<br/>이름 범위를 한정하는 [범위 있는 열거형](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/)이 추가되어 이름 충돌 회피가 쉬워졌고, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하며, [전방 선언](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)도 지원합니다.<br/><br/>**(C++17~)**<br/>[열거형의 중괄호 직접 초기화를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-scoped-enum/#c17-%EC%97%B4%EA%B1%B0%ED%98%95%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%97%88%EC%9A%A9)하여 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하는 사용자 정의 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 사용이 좀더 쉬워졌습니다.|
|[람다 표현식, 클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) (C++11~)|**(C++11~)**<br/>[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)이 추가되어 1회용 익명 함수를 만들 수 있습니다.<br/><br/>**(C++14~)**<br/>[람다 캡쳐 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B3%90-%EC%B4%88%EA%B8%B0%ED%99%94)로 람다내에서 사용하는 임의 변수를 정의하여 사용할 수 있습니다.<br/>[auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받아 마치 템플릿 함수처럼 동작하는 [일반화된 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98)가 추가되었습니다.<br/><br/>**(C++17~)**<br/>[람다 캡쳐시 *this 를 이용](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EC%BA%A1%EC%B3%90)하여 개체 자체를 복제하여 사용합니다.<br/>[constexpr 람다 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c17-constexpr-%EB%9E%8C%EB%8B%A4-%ED%95%A8%EC%88%98)가 추가되어 람다 함수도 컴파일 타임 함수로 만들 수 있습니다.| 
|[문자, 문자열](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) (C++11~)|**(C++11~)**<br/>[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [char16_t, char32_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#char16_t-%EC%99%80-char32_t)이 추가되었습니다.<br/>[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.<br/>[R"()"리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#raw-string-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 개행이나 [이스케이프 문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%AC%B8%EC%9E%90)를 좀더 편하게 입력할 수 있습니다.<br/><br/>**(C++17~)**<br/>[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.<br/><br/>**(C++20~)**<br/>[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [char8_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#c20-char8_t)이 추가되었습니다.|
|[구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/) (C++17~)|**(C++17~)**<br/>[구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/)을 이용하여 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/), [pair](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-pair/), [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/), 클래스등의 하위 요소나 멤버 변수에 쉽게 접근할 수 있습니다.|

# 타입

|항목|내용|
|--|--|
|[long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/) (C++11~)|**(C++11~)**<br/>최소 8byte 크기를 보장하는 [long long 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)이 추가되었습니다.<br/>[long long](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)용 정수형 상수인 [`ll`, `ull`, `LL`, `ULL` 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-longlong/)이 추가되었습니다.|
|[문자, 문자열 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/) (C++11~)|**(C++11~)**<br/>[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [char16_t, char32_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#char16_t-%EC%99%80-char32_t)이 추가되었습니다.<br/><br/>**(C++20~)**<br/>[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [char8_t 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#c20-char8_t)이 추가되었습니다.|


# 기타

|항목|내용|
|--|--|
|(C++11~)|[using을 이용한 타입 별칭이 추가](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-using-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)되어 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 보다 좀 더 직관적인 표현이 가능해 졌습니다.<br/>[alignas() 와 alignof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-alignas-alignof)를 이용하여 [메모리 정렬 방식](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)을 표준화 했습니다.<br/>파라메터 팩을 이용한 [가변 매크로](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EA%B0%80%EB%B3%80-%EB%A7%A4%ED%81%AC%EB%A1%9C)가 추가되어 C언어와의 호환성이 높아졌습니다.<br/>[멤버의 `sizeof()`](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EB%A9%A4%EB%B2%84-sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)시 동작이 개선되어 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있습니다.|
|(C++17~)|[__has_include](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c17-__has_include)가 추가되어 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하기 전에 파일이 존재하는지 확인할 수 있습니다.|

# deprecate/remove

|항목|내용|
|--|--|
|(~C++11)| [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 예외를 방출하느냐 안하느냐만 관심을 둡니다.<br/>[export 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#export-%ED%85%9C%ED%94%8C%EB%A6%BF) 제대로 구현한 컴파일러는 드물고, 세부사항에 대한 의견이 일치하지 않아 remove 되었습니다.| 
|(~C++17)|[동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 관련해서 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)가 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 이제 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)만 사용해야 합니다.<br/>`&&, &=`등이 특수기호가 없는 인코딩을 사용하는 곳을 위해 제공했던 `trigraph`가 remove되었습니다.([cppreference](https://en.cppreference.com/w/cpp/language/operator_alternative#Trigraphs_.28removed_in_C.2B.2B17.29) 참고)<br/>변수를 CPU 레지스터에 배치하도록 힌트를 주는 `register`가 deprecate 되었습니다.<br/>[bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)의 증감 연산이 deprecate 되었습니다.|





