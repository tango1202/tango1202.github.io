---
layout: single
title: "#13. [모던 C++ STL] 진단"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 이 deprecate 되면서 [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)도 함께 deprecate 되었습니다.
> * (C++11~) `future_error`, `regex_error`, `system_error`, `ios_base::failure`, [bad_weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#bad_weak_ptr), [bad_function_call](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bad_function_call), `bad_array_new_length`가 추가되었습니다.
> * (C++11~) `get_terminate()`가 추가되었습니다.
> * (C++11~) [예외 캡쳐 및 저장](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#c11-%EC%98%88%EC%99%B8-%EC%BA%A1%EC%B3%90-%EB%B0%8F-%EC%A0%80%EC%9E%A5)이 추가되었습니다.
> * (C++11~) [시스템 오류](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98)가 추가되었습니다.
> * (C++17~) [filesystem_error](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-filesystem/#%EC%98%88%EC%99%B8), [bad_any_cast](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/), [bad_optional_access](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/), [bad_variant_access](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/)가 추가되었습니다.

# 진단

|항목|내용|
|--|--|
|[assert()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/)|설계한 기능이 올바르게 사용되는지 디버그 모드에서 확인합니다.|

# exception

[throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)를 이용한 예외 보고시 일관된 인터페이스를 제공합니다. STL에서의 모든 예외는 `exception`을 상속합니다.

|항목|내용|
|--|--|
|`=`|[복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)합니다.|
|`what()`|설명 문자열을 리턴합니다.|

# 표준 예외

표준 예외는 다음을 준수해야 합니다.

1. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 구현
2. [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현

|항목|내용|
|--|--|
|`logic_error`|프로그램 내의 논리가 잘못되었습니다.|
|[invalid_argument](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8)| 잘못된 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달했습니다.|
|`domain_error`|해결하고자 하는 문제 영역(도메인) 에 오류가 있습니다.|
|`length_error`|길이 제한을 초과했습니다.|
|[out_of_range](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#-%EA%B3%BC-at)|범위를 벗어난 요소에 접근했습니다.|
|`future_error` (C++11~)|[future](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#future)나 [promise](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-future/#promise)의 오류를 보고합니다.|
|`runtime_error`|논리적 문제는 아니나 실행중에 오류가 발생했습니다.|
|[range_error](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8)|유효한 범위를 벗어났습니다.|
|`overflow_error`|계산 결과가 허용 범위 초과입니다.|
|`underflow_error`|계산 결과가 허용 범위 미만입니다.|
|`regex_error` (C++11~)|(작성중)|
|`system_error` (C++11~)|(작성중)|
|`ios_base::failure` (C++11~)|(작성중)|
|`tx_exception`|(작성중)|
|`nonexistent_local_time` (C++20~)|(작성중)|
|`ambiguous_local_time` (C++20~)|(작성중)|
|`format_error` (C++20~)|(작성중)|
|`bad_typeid`|널포인터를 역참조하여 typeid를 구할때 발생합니다.<br/>`T* p = NULL;`<br/>`typeid(*p).name();`|
|[bad_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|[dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)의 실패시 발생합니다.|
|[filesystem_error](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-filesystem/#%EC%98%88%EC%99%B8) (C++17~)|파일 시스템 처리에서 오류가 발생했습니다.|
|[bad_any_cast](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/) (C++17~)|[any](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-any/) 사용시 타입이 일치하지 않을때 예외를 발생합니다.|
|[bad_optional_access](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/) (C++17~)|[optional](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-optional/)에서 `value()` 사용시 값이 없을때 예외를 발생합니다.|
|[bad_variant_access](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/) (C++17~)|[variant](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-variant/)에서 주어진 타입이 없을때 예외를 발생합니다.|
|`bad_expected_access` (C++23~)|(작성중)|
|[bad_weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#bad_weak_ptr) (C++11~)|[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/)에서 잘못된 [weak_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/#weak_ptr)을 사용할때 발생하는 예외입니다.|
|[bad_function_call](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bad_function_call) (C++11~)|[function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)에서 `()`로 호출할 대상이 없을 때 [bad_function_call](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bad_function_call) 예외를 방출합니다.|
|[bad_alloc](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|메모리 할당에 실패했습니다.|
|`bad_array_new_length` (C++11~)|(작성중)|
|[bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)|[동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)에 [bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)이 포함되면, `unexpected_handler` 가 호출되고, 여기서 [throw;](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시 [bad_exception](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)으로 변환되어 전파됩니다.|

# 예외 처리 실패

예외를 `catch()`하지 않으면 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 함수를 호출합니다. 자세한 내용은 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 참고 하세요.

|항목|내용|
|--|--|
|[terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)|예외를 `catch()`하지 않으면 호출됩니다. 기본적으로 `abort()`가 호출됩니다.|
|`terminate_handler`|[terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)호출시 실행되는 함수입니다.|
|`get_terminate()` (C++11~)|(작성중)|
|`set_terminate()`|`terminate_handler`를 설정합니다.|

# 동적 예외 사양 실패

[동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)에 없는 예외가 발생할 경우 [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 함수가 호출됩니다. 자세한 내용은 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)을 참고하세요.

|항목|내용|
|--|--|
|[unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) (~C++11)|동적 예외 사양에서 명시한 예외 이외의 예외가 발생하면 호출됩니다.|
|`unexpected_handler` (~C++11)|`bad_exception`을 방출합니다.|
|`get_expected()` (C++11~C++17)|(작성중)|
|`set_expected()` (~C++17)|`unexpected_handler`를 설정합니다.|

# 오류 번호

|항목|내용|
|--|--|
|`errorno`|오류 번호 입니다. [strerror()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#strerror)를 이용하여 오류 메시지를 확인할 수 있습니다. POSIX와 호환되며 `<cerrono>` 에 [매크로 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)로 정의되어 있습니다.<br/>자세한 내용은 [cppreference.com](https://en.cppreference.com/w/cpp/error/errno_macros)을 참고하기 바랍니다.|

# (C++11~) 예외 캡쳐 및 저장

|항목|내용|
|--|--|
|`uncaught_exception()` (C++11~C++17)<br/>`uncaught_exceptions()` (C++17~)|예외가 발생되어 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80) 중인지를 감지합니다.|
|`exception_ptr` (C++11~)|(작성중)|
|`make_exception_ptr()` (C++11~)|(작성중)|
|`current_exception()` (C++11~)|(작성중)|
|`rethrow_exception()` (C++11~)|(작성중)|
|`nested_exception` (C++11~)|(작성중)|  
|`throw_with_nested()` (C++11~)|(작성중)|  
|`rethrow_if_nested()` (C++11~)|(작성중)|  

# (C++11~) 시스템 오류

|항목|내용|
|--|--|
|`error_category` (C++11~)|(작성중)|
|`generic_category()` (C++11~)|(작성중)|
|`system_category()` (C++11~)|(작성중)|
|`error_condition` (C++11~)|(작성중)|
|`errc` (C++11~)|POSIX(운영체제 호환 표준)와 호환되는 에러코드 정의입니다.[cppreference](https://en.cppreference.com/w/cpp/error/errc)를 참고하세요.|
|`error_code` (C++11~)|(작성중)| 
|`system_error` (C++11~)|(작성중)|

# (C++23~) Stacktrace

|항목|내용|
|--|--|
|`stacktrace_entry` (C++23~)|(작성중)|
|`basic_stacktrace` (C++23~)|(작성중)|