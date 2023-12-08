---
layout: single
title: "#21. [모던 C++] (C++11~) 특성(attribute)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [특성(attirbute)](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/)이 추가되어 컴파일러에게 부가 정보를 전달하는 방식을 표준화 했습니다.
> * (C++14~) [[[deprecated]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c14-deprecated-deprecated%EC%9D%B4%EC%9C%A0)가 추가되어 소멸 예정인 것을 컴파일 경고로 알려줍니다.
> * (C++17~) [[[fallthrough]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-fallthrough)가 추가되어 `switch()`에서 의도적으로 `break`를 생략하여 다음 `case`로 제어를 이동시킬때 발생하는 컴파일 경고를 차단할 수 있습니다.
> * (C++17~) [[[nodiscard]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard)가 추가되어 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이나 타입을 무시하면 컴파일 경고로 알려줍니다.
> * (C++17~) [[[maybe_unused]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-maybe_unused)가 추가되어 사용되지 않은 개체의 컴파일 경고를 차단할 수 있습니다.
> * (C++17~) [특성 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-%ED%8A%B9%EC%84%B1-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)가 추가되어 `[[msvc::noinline]]` 와 같이 사용할 수 있습니다.
> * (C++20~) [[[nodiscard]]의 생성자 지원](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-nodiscard%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A7%80%EC%9B%90-nodiscard%EC%9D%B4%EC%9C%A0), [[[nodiscard("이유")]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-nodiscard%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A7%80%EC%9B%90-nodiscard%EC%9D%B4%EC%9C%A0)가 추가되었습니다.
> * (C++20~) [[[likely]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-likely-unlikely), [[[unlikely]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-likely-unlikely)가 추가되어 컴파일러에게 최적화 힌트를 줄 수 있습니다.
> * (C++20~) [[[no_unique_address]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-no_unique_address)가 추가되어 아무 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 없는 개체의 크기를 최적화합니다.

# 개요

컴파일러에게 최적화 힌트나 경고 처리 힌트등 문법적으로 전달하기 어려운 정보를 컴파일러에 전달할때 기존에는 컴파일러 마다 자체적인 방식을 사용했습니다.(*`__attribute__()`, `__declspec()` 등*) 

C++11 부터는 [특성(attirbute)](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/)이 추가되어 부가 정보 전달을 표준화 했습니다.

`[[`특성명`]]`와 같이 `[[]]`사이에 사용할 [특성](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/)을 기재하면 됩니다.

# 표준 특성

C++버전에 따라 다음의 [표준 특성](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#%ED%91%9C%EC%A4%80-%ED%8A%B9%EC%84%B1)이 제공됩니다. 

|항목|내용|
|--|--|
|[[[noreturn]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#noreturn) (C++11~)|함수가 항상 예외를 [throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하거나 프로그램을 종료합니다.|
|[[[carries_dependency]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#carries_dependency) (C++11~)|(작성중)|
|[[[optimize_for_synchronized]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#optimize_for_synchronized) (C++11~)|(작성중)|
|[[[deprecated]], [[deprecated("이유")]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c14-deprecated-deprecated%EC%9D%B4%EC%9C%A0) (C++14~)|소멸 예정인 것을 컴파일 경고로 알려줍니다.|
|[[[fallthrough]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-fallthrough) (C++17~)|`case A: break;`<br/>`case B:`<br/>`case C: break;`<br/>와 같이 `case B:`가 의도적으로 `case C:`를 실행함을 알립니다.|
|[[[nodiscard]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard) (C++17~)<br/>[[[nodiscard("이유")]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-nodiscard%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A7%80%EC%9B%90-nodiscard%EC%9D%B4%EC%9C%A0) (C++20~)|개체나 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 무시되면 안됩니다.(*에러 코드 리턴하는 함수에 사용하면 좋습니다.*)|
|[[[maybe_unused]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-maybe_unused) (C++17~)|사용되지 않은 개체의 컴파일 경고를 막습니다.|
|[[[likely]], [[unlikely]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-likely-unlikely) (C++20~)|`if()`나 `switch()`에서 자주 사용하는 코드 조각을 알려주어 최적화 힌트를 제공합니다.|
|[[[no_unique_address]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-no_unique_address) (C++20~)|아무 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 없는 개체의 크기를 최적화합니다.|
|`assume("표현식")` (C++23~)|특정 상황에 표현식이 `true`가 되도록 가정합니다. 컴파일러는 이 가정을 신뢰하고 이에 따른 최적화를 합니다.(*가정이 거짓일때 동작은 정의되지 않았습니다.*)|

# [[noreturn]]

컴파일러에게 리턴하지 않는다는 최적화 힌트를 줍니다.

"리턴하지 않는다"가 좀 오해가 있는 표현인데요, 

`void f() {}`는 `void`를 리턴하므로, 리턴하는 함수입니다.

리턴하지 않는 함수는 무작정 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키거나 [abort()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%80%EC%9B%90) 하여 프로그램을 종료하는 함수를 말합니다. 

```cpp
// 함수가 항상 예외를 throw하거나 종료합니다. 
// 컴파일러는 호출후 제어 연결을 할 필요가 없으므로, 이에 따른 최적화가 가능합니다.
[[noreturn]] void f_11() {throw "error";}

// 실제로는 리턴하므로 컴파일러는 경고를 발생합니다.
[[noreturn]] int g_11() {return 0;} // (X) 컴파일 경고. function declared 'noreturn' has a 'return' statement
[[noreturn]] void g_11() {} // (X) 컴파일 경고. 'noreturn' function does return
```

# [[carries_dependency]]

(작성중)

# [[optimize_for_synchronized]]

(작성중)

# (C++14~) [[deprecated]], [[deprecated("이유")]]

소멸 예정인 것을 컴파일 경고로 알려줍니다. `deprecated("이유")`를 이용하여 컴파일 경고시 소멸 이유를 표시할 수 있습니다.

```cpp
namespace [[deprecated]] MyLib {} // 네임스페이스
class [[deprecated]] MyClass { // 클래스, 구조체, 공용체
    [[deprecated]] int m_Val; // 멤버 변수
    [[deprecated]] void f() {} // 멤버 함수
}; 
template<typename T>
class [[deprecated]] A {}; // 템플릿
using MyType [[deprecated]] = int; // using
[[deprecated]] typedef int YourType; // typedef
[[deprecated]] int val; // 변수  
#if 201703L <= __cplusplus // C++17~
    [[deprecated]] auto [a, b] = std::make_pair(1, 3.14); // 구조화된 바인딩 
#endif  
[[deprecated]] void f() {} // 함수 
enum [[deprecated]] MyEnum {MyVal [[deprecated]]}; // 열거형, 열거자
enum class [[deprecated]] YourEnum {YourVal [[deprecated]]}; // 범위 있는 열거형, 열거자
```
# (C++17~) [[fallthrough]]

`switch()`에서 `break`를 생략하면 다음 `case`로 제어가 이동하는데, 이를 `fall through`라고 합니다. 보통 컴파일러가 경고를 표시하는데(*GCC의 경우 `-Wimplicit-fallthrough`옵션이 필요합니다.*), 이를 무시하기 위해 사용합니다.

```cpp
int val{0};
switch (val) {
case 0: 
    val = 0; 
    break;
case 1: 
    val = 1; 
    [[fallthrough]]; // 의도한 fall through이니 컴파일러에게 경고하지 말라고 알려줍니다.
case 2: 
    val = 2; 
    break;
}
```

# (C++17~) [[nodiscard]]

예외 상황이 발생했을때 오류 코드를 리턴하면, 호출한 곳에서 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 확인하고 예외 처리를 해야 하는데, 강제성이 없어 오류 코드 검사를 빼먹을 수 있었는데요(*[예외 메카니즘(try-catch, throw와 스택 풀기, terminate)](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/) 참고*),

C++17 부터는 [[[nodiscard]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard)가 추가되어 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 무시하면 컴파일 경고로 알려줍니다.

```cpp
enum class Error_11 {Ok, Fail};

// 리턴값을 무시하면 안됩니다.
[[nodiscard]] Error_11 GetLastError_17() {return Error_11::Ok;} 

// GetLastError_17(); // (X) 컴파일 경고. 리턴값을 무시하면 안됩니다.
Error_11 error = GetLastError_17(); // (O)
```

또한 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)외에 특정 타입에 [[[nodiscard]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard)를 주어 해당 타입을 무시하면 경고로 알려줍니다.

```cpp
// Error_17 타입이 리턴되면 무시하면 안됩니다.
enum class [[nodiscard]] Error_17 {Ok, Fail};

Error_17 GetForcedError_17() {return Error_17::Ok;}

// GetForcedError_17(); // (X) 컴파일 경고. Error_17 타입이 리턴되면 무시하면 안됩니다.
Error_17 error = GetForcedError_17(); // (O)
```
> *(C++20~) [[[nodiscard]]의 생성자 지원, [[nodiscard("이유")]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard)가 추가되었습니다.*

# (C++17~) [[maybe_unused]]

기존에는 [이름이 없는 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 사용할 경우 주석을 이용하여 컴파일 경고를 우회했는데요(*[인자(매개변수, Parameter)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 참고*),

C++17 부터는 [[[maybe_unused]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-maybe_unused)가 추가되어 사용되지 않은 개체의 컴파일 경고를 차단할 수 있습니다.

특히 디버그 모드에서만 사용하는 개체라면, 릴리즈 모드에서는 사용하지 않으므로 컴파일러가 경고로 알려주는데, 이 경우에 유용하게 사용할 수 있습니다.

[[[deprecated]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c14-deprecated-deprecated%EC%9D%B4%EC%9C%A0)와 동일하게 사용하며, [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)는 지원하지 않습니다.

```cpp
// namespace [[maybe_unused]] MyLib {} // (X) 컴파일 경고. 네임스페이스는 지원하지 않습니다.
class [[maybe_unused]] MyClass { // 클래스, 구조체, 공용체
    [[deprecated]] int m_Val; // 멤버 변수
    [[deprecated]] void f() {} // 멤버 함수
}; 
template<typename T>
class [[maybe_unused]] A {}; // 템플릿
using MyType [[maybe_unused]] = int; // using
[[maybe_unused]] typedef int YourType; // typedef
[[maybe_unused]] int val; // 변수    
[[maybe_unused]] auto [a, b] = std::make_pair(1, 3.14); // 구조화된 바인딩     
[[maybe_unused]] void f() {} // 함수 
void g([[maybe_unused]] int a, [[maybe_unused]] int b) {} // 함수 인자 
enum [[maybe_unused]] MyEnum {MyVal [[maybe_unused]]}; // 열거형, 열거자
enum class [[maybe_unused]] YourEnum {YourVal [[maybe_unused]]}; // 범위 있는 열거형, 열거자
```

# (C++17~) 특성 네임스페이스

C++17 부터는 [특성 네임스페이스](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-%ED%8A%B9%EC%84%B1-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4)가 추가되어 `[[msvc::noinline]]` 와 같이 사용할 수 있습니다.

# (C++20~) [[nodiscard]]의 생성자 지원, [[nodiscard("이유")]]

기존에는 [[[nodiscard]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c17-nodiscard)가 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 지원하지 않았지만, C++20 부터는 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 지원합니다. 생성된 개체가 사용되지 않으면 컴파일 경고로 알려줍니다.

```cpp
class T_20 {
public:
    [[nodiscard]] T_20(int, char) {}

    static void f(T_20) {}
};

// T{10, 'a'}; // (X) 컴파일 경고. 생성되어 만들어진 개체를 무시하면 안됩니다.
T_20 a{10, 'a'}; // a 변수에서 사용
T_20 b = T_20{10, 'a'}; // b 변수에서 사용
a = T_20{10, 'a'}; // a 변수에서 사용
T_20::f(T_20{10, 'a'}); // f 함수에 인자로 전달해서 사용
```

또한 [[[nodiscard("이유")]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-nodiscard%EC%9D%98-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A7%80%EC%9B%90-nodiscard%EC%9D%B4%EC%9C%A0)를 이용하여 설명을 추가할 수 있습니다.

# (C++20~) [[likely]], [[unlikely]]

`if()`나 `switch()`에서 자주 사용하는 코드 조각을 알려주어 컴파일러에게 최적화 힌트를 제공합니다.

```cpp
int val{0};
if (val < 0) [[likely]] {} // if가 참일때가 자주 실행되니 최적화를 해주세요.
else {}

switch(val) {
case 0: break;
[[likely]] case 1: break; // case 1인 경우가 자주 실행되니 최적화를 해주세요.
[[unlikely]]case 2: break; // case 2인 경우는 드물게 실행되니 최적화를 해주세요.   
}
```

# (C++20~) [[no_unique_address]]

기존에는 빈 클래스는 1byte이고, 다른 개체에 포함될 경우 해당 크기가 유지된다고 말씀드렸는데요(*[빈 클래스와 자식 개체의 크기](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EB%B9%88-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80-%EC%9E%90%EC%8B%9D-%EA%B0%9C%EC%B2%B4%EC%9D%98-%ED%81%AC%EA%B8%B0) 참고*),

C++20 부터는 [[[no_unique_address]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-no_unique_address)가 추가되어 아무 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 없는 개체의 크기를 최적화합니다.

[[[no_unique_address]]](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/#c20-no_unique_address)를 사용하면, 빈 클래스인 경우 메모리를 차지하지 않도록 해줍니다.

```cpp
class Empty {}; // 빈 클래스는 강제로 1byte
EXPECT_TRUE(sizeof(Empty) == 1);

class Composite {
    int m_X;
    Empty m_Empty; // 1byte 이지만 3byte 패딩됨
};
EXPECT_TRUE(sizeof(Composite) == sizeof(int) + sizeof(int));

class Composite_20 {
    int m_X;
    [[no_unique_address]] Empty m_Empty; // 0byte
};
EXPECT_TRUE(sizeof(Composite_20) == sizeof(int)); 
```