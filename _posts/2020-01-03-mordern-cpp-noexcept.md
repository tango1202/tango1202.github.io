---
layout: single
title: "#3. [모던 C++] (C++11~) noexcept, noexcept 연산자, (C++17~) noexcept 함수 유형 포함"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 함수의 예외 방출 여부를 보증합니다.
> * (C++11~) 소멸자는 기본적으로 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 동작합니다.
> * (C++11~) [noexcept 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 해당 함수가 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)인지 컴파일 타임에 검사할 수 있습니다.
> * (C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 예외를 방출하느냐 안하느냐만 관심을 둡니다. 
> * (C++17~) [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 함수 유형에 포함되어 예외 처리에 대한 코딩 계약을 좀더 단단하게 할 수 있습니다.
> * (C++17~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 관련해서 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)가 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 이제 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)만 사용해야 합니다. 

# 개요 
기존에는 [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)을 이용하여 함수가 방출하는 예외를 나열했는데요, 사실 명시한 예외 이외에는 `unexpected_handler` 로 분기하므로 사용하지 않는게 낫다고 말씀드렸습니다. 

```cpp
// bad_alloc, range_error 이외에는 unexpected_handler로 분기합니다.
void f() throw(std::bad_alloc, std::range_error) {
}
```

C++11 부터는 예외의 나열이 불필요함을 공감하여, 예외를 방출하냐 안하냐만 제공하도록 수정하였고, [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 이용하여 함수의 예외 방출 여부를 보증합니다. 

|항목|내용|
|--|--|
|nothrow()|기존 방법입니다. 나열된 예외가 없으므로 [unexpected()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 핸들러를 호출하고, 별다른 처리를 하지 않으면, [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/) 될때까지 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)를 합니다.(*[동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 참고*)|
|[noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) (C++11~)|[nothrow 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)을 합니다. 예외를 방출하지 않습니다. 만약 방출하면 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 호출합니다. 컴파일러 최적화에 따라 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)를 하거나 안합니다.|
|`noexcept(true)` (C++11~)|[noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)와 동일합니다.|
|`noexcept(false)` (C++11~)|예외를 방출합니다.|

이에 따라 컴파일러는 어떤 예외를 방출하느냐 보다는 예외 방출 여부만 보고 최적화를 진행합니다. 예를 들면 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)등의 작업이요.

```cpp
// 기존 
// 나열된 예외 이외에는 unexpected() 핸들러로 분기. 
// 나열된 예외가 없으므로 예외 발생시 무조건 unexpected() 핸들러 호출
void f() throw() {
}

// C++11. 예외 방출 안함. 
// 컴파일러는 이 사양을 믿고 스택 풀기등을 안해도 되므로 최적화 수행 
// 혹여나 예외가 발생하면 terminate()를 호출
void f_11() noexcept {
}
```

만약 예외가 방출된다면 컴파일시에 경고가 발생하고, 런타임에서 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 호출하여 프로그램을 종료합니다.

```cpp
void f_11() noexcept {
    throw 'a'; // (X) 컴파일 경고. terminate 된다고 경고하고, 런타임에서 terminate()를 호출하여 종료합니다.
}
```
# 느슨한 noexcept 계약

한가지 아쉬운 점은 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)인 함수 내에서 사용하는 함수가 예외를 발생시키면 컴파일 경고 없이, 런타임에 [terminate()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate)를 호출한다는 점입니다. 

```cpp
void f() {
    throw 'a'; 
}
void f_11() noexcept {
    f(); // f() 함수에서 예외를 발생시킵니다. 컴파일 경고가 없습니다.
}
f_11(); // noexcept 함수 내에서 사용하는 함수 f()가 예외를 발생시켜 terminate()를 호출합니다.
```

[상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)이 ***상수는 상수만 접근한다*** 이듯이, [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)만 접근한다는 계약이 있으면 컴파일 단계에서 감지되어 참 좋을텐데 말이죠. 왜 이런가 봤더니 기존 C스타일 함수는 `noexcept`를 지정할 수 없으니, 호출의 호환성을 유지하기 위해 이렇게 느슨하게 했다는군요.(*아쉽지만, 개발자에게 충분한 자유도를 주는 표준화 위원들의 의견도 존중합니다.*)

아무튼 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)는 컴파일 오류 감지가 안되기에 가볍게 넣거나 뺄 수 있는 코딩 계약이 아닙니다. [상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)처럼 컴파일 오류로 감지할 수 있으면, [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 무조건 들이댄 것처럼 쉽게 적용할 수 있지만, 마구잡이로 적용했다가 예외 발생 상황이 되면, 이를 수습하느라 인생을 낭비하게 될 수 있습니다. 

내부적으로 사용하는 하위 함수와 그 함수가 향후에 어떻게 변화될 것인지 신중하게 고려하여 적용하시고, 하위 함수들도 꼭 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 작성하여 ***[noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) 계약***을 만들어 두시는게 좋습니다.

따라서 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) 함수를 만들때는 다음을 고려하세요.

1. 사용되는 하위 함수들도 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)인지 면밀히 검토하여야 합니다. 
2. 또한, 향후 하위 함수들이 수정되어 혹여나 예외를 발생시키면, 사용되는 모든 곳의 예외 처리를 재검토 해서 수정하겠다는 ***모든 조직원의 공감대***가 형성되어야 합니다.

그렇지 않으면 오히려 안쓰는게 낫습니다. 그나마 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80)의 기회조차 컴파일러 최적화에 의해 차단되니까요.

# 소멸자 예외

기존에는 예외 안정을 위해 소멸자에서는 예외를 방출하지 않아야 한다고 말씀 드렸는데요(*[스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EA%B7%80) 와 [소멸자에서 예외 방출 금지](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90%EC%97%90%EC%84%9C-%EC%98%88%EC%99%B8-%EB%B0%A9%EC%B6%9C-%EA%B8%88%EC%A7%80) 참고*), 

C++11 부터는 소멸자는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) 가 생략되었더라도 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 동작하여 코딩 계약이 좀더 단단해 졌습니다.

다음처럼 `noexcept(false)`을 사용하여 억지로 예외를 방출할 수는 있지만, 하지 마세요. 소멸자에서는 예외를 방출하지 않아야 합니다.

```cpp
class T_11{
public:
    ~T_11() noexcept(false) {} // 예외를 방출할 수 있습니다.
};
```

# 이동 연산 변환의 안전성

C++11의 컴파일러는 기존 복사 연산을 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)으로 대체하여 성능을 향상시킵니다. 다만, 기존 코드와의 호환성이 보장될때만 대체합니다. 

한마디로, ***가능하면 이동하되, 필요하면 복사합니다.***

[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 `push_back()`을 보면 새로운 요소를 추가할때 [용량](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#size%EC%99%80-capacity)이 부족하다면, 새로운 메모리 영역을 할당하고, 기존 요소를 새로운 메모리 영역에 복사하고, 새로운 요소를 추가합니다. 이때 요소의 복사 과정에서 예외가 발생하더라도, 원본 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)는 그대로 이므로 `push_back()`은 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 된다고 할 수 있습니다.

하지만 이를 이동 연산으로 하면 어떨까요?

기존 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 요소들을 이동하는 중에 예외가 발생한다면, 원본 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)는 이미 수정된 상태이기에 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 된다고 할 수 없습니다. 따라서, 컴파일러가 마음 놓고 복사 연산을 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)으로 바꿀 수는 없습니다. 이전 코드의 예외 안전성을 해치니까요.

그래서, 컴파일러는 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)이 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 선언되어 예외를 방출하지 않는 경우에만 복사 연산을  [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)으로 대체한다고 합니다.

그러니  [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)을 위한 이동 생성자와 이동 대입 연산자는 최대한 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 만드는게 좋습니다. 그리고, [nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94) 도요.(*궁극적으로 모든 함수가 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)면 어떨까... 상상만 해봅니다.*)

# noexcept() 연산자

컴파일 타임에 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/) 인지 검사합니다.

```cpp
void f() {
    throw 'a'; 
}
void f_11() noexcept {
}
EXPECT_TRUE(noexcept(f()) == false);   
EXPECT_TRUE(noexcept(g()) == true); 
```

# (C++17~) noexcept 함수 유형 포함 

기존에는 함수 유형에 noexcept가 포함되지 않았습니다.

따라서, 함수 포인터로 유형 정의를 할때 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)를 포함할 수 없었습니다.

```cpp
// C++14에서 noexcept는 함수 유형의 일부가 아닙니다. 
typedef void (*MyFunc)(void); // 함수 포인터 typedef
typedef void (*MyFunc_17)(void) noexcept; // (X) 컴파일 오류. noexcept는 함수 유형의 일부가 아닙니다. 

void FuncTrue_11() noexcept(true) {}
void FuncFalse_11() noexcept(false) {}

// ~C++17 이전에는 noexcept 여부와 상관없이 대입할 수 있습니다.
MyFunc f1{FuncTrue_11}; 
MyFunc f2{FuncFalse_11};
```

C++17 부터는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 함수 유형에 포함되었으므로, 다음 예제처럼 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)인 함수만 전달받게 할 수 있습니다. 따라서 예외 처리에 대한 코딩 계약을 좀더 단단하게 할 수 있습니다.

```cpp
typedef void (*MyFunc_17)(void) noexcept; 

void FuncTrue_11() noexcept(true) {}
void FuncFalse_11() noexcept(false) {}

// C++17~ 이후에는 noexcept를 고려하여 대입할 수 있습니다.
MyFunc_17 f3_17{FuncTrue_11}; 
MyFunc_17 f4_17{FuncFalse_11}; // (X) 컴파일 오류. noexcept가 다릅니다.
```

