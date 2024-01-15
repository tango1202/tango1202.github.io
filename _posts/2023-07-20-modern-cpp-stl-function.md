---
layout: single
title: "#20. [모던 C++ STL] function, mem_fn, reference_wrapper, bindinvoke (C++11, C++17, C++20)" 
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#34] [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) 보다는 람다[람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)를 선호하라.(*[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) 참고*)
>   * [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)이 가독성이 좋다.

> * (C++11~) [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function)이 추가되어 `()`로 호출 가능한 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 저장할 수 있습니다.
> * (C++11~) [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function)에서 `()`을 호출할 대상이 없을 때 [bad_function_call](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bad_function_call) 예외를 방출합니다.
> * (C++11~) [mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn)이 추가되었습니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 있는 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)도 호출하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 좀 더 간편하게 만들어 줍니다.
> * (C++11~) [reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper)가 추가되어 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이나 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 안되는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 래핑할 수 있습니다.
> * (C++11~) [ref(), cref()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#ref-cref)가 추가되어 [reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper) 개체를 좀더 간편하게 생성할 수 있습니다.
> * (C++11~) [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)가 추가되어 `placeholders::_1`(*GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨*)와 같은 자리 표시자와 조합하여 특정 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)만을 사용하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 생성할 수 있습니다.
> > * (C++11~) [is_bind_expression](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_bind_expression)는 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)로 생성한 함수인지 검사합니다.
> > * (C++11~) [is_placeholder](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_placeholder)가 추가되어 자리 표시자를 사용했는지 검사할 수 있습니다.
> * (C++11~) [함수자 타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)([unary_function, binary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)등), [바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)([bind1st(), bind2nd()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)등), [어뎁터와 부정자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)([mem_fun(), mem_fun_ref(), ptr_fun(), not1(), not2(), unary_negate(), binary_negate(), pointer_to_unary_function(), pointer_to_binary_function()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)등)가 [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D), [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function), [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind), [mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn)등으로 대체되어 [deprecate](https://tango1202.github.io/cpp-stl/modern-cpp-stl-preview/#deprecateremove) 되었습니다. 
> * (C++17~) [invoke()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c17-invoke)가 추가되어 일반 함수와 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 동일한 방식으로 호출할 수 있습니다.
> * (C++20~) [bind_front()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c20-bind_front)가 추가되었습니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들을 순서대로 배치하므로, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 순서 변경이 없다면 간편하게 사용할 수 있습니다.

# 개요

기존에는 함수처럼 호출하는 개체를 만들기 위해 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 이용했는데요,

```cpp
// 7보다 작은지 검사하는 함수
class Less_7 : std::unary_function<int, bool> {
public:
    result_type operator ()(argument_type val) {
        return val < 7 ? true : false;
    }       
};

std::vector<int> v{7, 4, 3};
auto itr = std::find_if(v.begin(), v.end(), Less_7{});
EXPECT_TRUE(*itr == 4);  
```

[unary_function 이나 binary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)등의 [함수자 타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 사용하는 방식이 좀 까다로웠습니다.

C++11 부터는 좀 더 간단한 [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)을 사용할 수 있는데요,

```cpp
std::vector<int> v{7, 4, 3};
std::find_if(
    v.begin(), 
    v.end(), 
    [](int val) -> int {return val < 7 ? true : false;}
);
```

이에 따라 C++11 부터 STL 에서는 기존의 까다로웠던 [함수자 타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 [deprecate](https://tango1202.github.io/cpp-stl/modern-cpp-stl-preview/#deprecateremove) 하고, [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function) 개체를 추가하여 `()`로 호출 가능한 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)들을 다룰 수 있게 했습니다.

항목|(~C++11)|(C++11~)|내용|
|--|--|--|--|
|[함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/) 정의|[함수자 타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)(*[unary_function, binary_function](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)*)|[람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)등 활용.<br/>[function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function) 개체로 저장|기존은 불필요한 제약이었습니다.|
|[바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)|[bind1st(), bind2nd()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)|[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)|다수의 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 지원합니다.|
|[어뎁터와 부정자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)|[mem_fun(), mem_fun_ref(), ptr_fun(), not1(), not2()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)|[mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn)|[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 있는 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 지원합니다.|
|참조 타입 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 지원|X|[reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper)<br/>[ref(), cref()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#ref-cref)|기존에 지원하지 못했던 참조 타입 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 지원합니다.

# function

[function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function) 개체는 다음 개체들을 저장할 수 있습니다.

1. [함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/)
2. [함수 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)
3. [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)
4. [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) 표현식

```cpp
std::function<int(int)> func{[](int val) -> int {return val < 7 ? true : false;}};

std::vector<int> v{7, 4, 3};
auto itr{std::find_if(
    v.begin(), 
    v.end(), 
    func) 
};
EXPECT_TRUE(*itr == 4); 
```

# bad_function_call

[function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function)에서 `()`을 호출할 대상이 없을 때 [bad_function_call](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bad_function_call) 예외를 방출합니다.

```cpp
std::function<void()> func{nullptr};

try {
    func();
}
catch (std::bad_function_call&) {
}
```

# mem_fn()

기존의 [어뎁터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)인 [mem_fun() 이나 mem_fun_ref()](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EC%96%B4%EB%8E%81%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)는 `f(x)`의 호출을 `x->f()`나 `x.f()`로 호출해 줬는데요, 사실 함수의 인수를 전달할 방법이 없어 사용상의 제약이 있었습니다.

C++11 부터는 [mem_fn()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#mem_fn)이 추가되었습니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 있는 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)도 호출하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 좀 더 간편하게 만들어 줍니다.

```cpp
class T {
public:
    int Add(int a, int b) const {return a + b;}
};

T obj;

auto add{std::mem_fn(&T::Add)};

EXPECT_TRUE(add(obj, 1, 2) == 3); // obj.Add(1, 2);를 호출합니다.
```

# reference_wrapper

[reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper)는 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이나 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 안되는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 래핑하여 복사/대입이 가능하게 하고, [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 유지시켜 줍니다. 

`get()`으로 관리하는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 구할 수 있으며, 다른 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 생성시 `operator T&() const` 을 이용한 [암시적 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 통해 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 대입합니다.([암시적 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)이 필요하여 `{}`는 안됩니다.) 

```cpp
int a{0};
std::reference_wrapper<int> rw(a);

rw.get() = 10; // reference_wrapper가 관리하는 참조자에 값 대입
EXPECT_TRUE(a == 10);

int& b = rw; // 참조자 생성시 암시적 형변환을 통해 reference_wrapper가 관리하는 참조자 대입
b = 20;
EXPECT_TRUE(a == 20);
```

특히, [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 [컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-container/) 요소로 사용할 때 유용합니다.

```cpp
int a{1};
int b{2};
int& ref1{a}; // 참조자 타입으로 만드려고 억지로 정의했습니다. ref()나 cref()가 더 낫습니다.
int& ref2{b};

// std::vector<int&> v; // (X) 컴파일 오류.
std::vector<std::reference_wrapper<int>> v;
v.push_back(ref1); // 참조자를 벡터에 추가할 수 있습니다.
v.push_back(ref2);

EXPECT_TRUE(v[0] == 1 && v[1] == 2);
a = 10;
b = 20;
EXPECT_TRUE(v[0] == 10 && v[1] == 20); // 참조자를 저장했으므로 vector의 요소로 확인해도 값이 바껴있습니다.
```

# ref(), cref()

[ref(), cref()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#ref-cref)가 추가되어 [reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper) 개체를 좀더 간편하게 생성할 수 있습니다. 

|항목|내용|
|--|--|
|`ref(T)` (C++11~)|`reference_wrapper<T>` 생성|
|`cref(T)` (C++11~)|`reference_wrapper<const T>` 생성|

```cpp
int a{1};
int b{2};

std::vector<std::reference_wrapper<int>> v;
v.push_back(std::ref(a)); // std::reference_wrapper를 생성합니다.
v.push_back(std::ref(b));

EXPECT_TRUE(v[0] == 1 && v[1] == 2);
a = 10;
b = 20;
EXPECT_TRUE(v[0] == 10 && v[1] == 20); 
```

# bind()

기존에는 이항 함수를 단항 함수로 만들기 위해 `bind1st(op, x)`나 `bind2nd(op, y)`와 같은 [바인더](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)를 이용했는데요,

C++11 부터 STL 에서는 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)함수를 추가하여, 다수의 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)(*GCC의 경우 `_1`~`_29` 범위에서 지원됨*)를 가진 함수도 지원합니다.

[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)가 추가되어 `placeholders::_1`(*GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨*)와 같은 자리 표시자와 조합하여 특정 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)만을 사용하는 [함수자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-functor/)를 생성할 수 있습니다.

다음 코드에서,

1. `func1()`은 `Sum()`의 모든 값을 지정했습니다.
2. `func2()`는 `Sum()`의 `b`[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)만 자리 표시자를 사용합니다.
3. `func3()`은 `Sum()`의 `b`[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter), `c`[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 자리 표시자를 사용합니다.
 
```cpp

int Sum(int a, int b, int c) {return a + b + c;}

// Sum 함수의 인자를 1, 2, 3으로 고정합니다.
std::function<int()> func1{
    std::bind(Sum, 1, 2, 3)
};
EXPECT_TRUE(func1() == 1 + 2 + 3);

// Sum 함수의 2번째 인자(b)를 함수 호출시의 1번째 인자로 사용합니다.
std::function<int(int)> func2{
    std::bind(Sum, 1, std::placeholders::_1, 3)
};
EXPECT_TRUE(func2(4) == 1 + 4 + 3);

// Sum 함수의 2번째 인자(b)를 함수 호출시의 1번째 인자로 사용하고,
// 3번째 인자(c)를 함수 호출시의 2번째 인자로 사용합니다.
std::function<int(int, int)> func3{
    std::bind(Sum, 1, std::placeholders::_1, std::placeholders::_2)
};
EXPECT_TRUE(func3(4, 5) == 1 + 4 + 5);

```

[auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)를 사용하면 좀더 간결하게 표현할 수 있습니다.

```cpp
auto func3{
    std::bind(Sum, 1, std::placeholders::_1, std::placeholders::_2)
};
EXPECT_TRUE(func3(4, 5) == 1 + 4 + 5);
```

사실 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) 보다는 [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)이 훨씬 더 직관적이니, [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)보다는 [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)을 사용하시는게 좋습니다.(*더 직관적인 [람다 표현식](https://tango1202.github.io/cpp/modern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)이 있는데, 도대체 왜 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)를 표준에 넣었을까 찾아보니, 2005년 표준의 TR1에 이미 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)가 포함되었다고 하네요. 기존 코드 호환 용도 정도로만 사용하라고 제공하는 듯 합니다.*)

```cpp
auto func3{
    [](int b, int c) {return Sum(1, b, c);}
};
EXPECT_TRUE(func3(4, 5) == 1 + 4 + 5); 
```

> *(C++20~) [bind_front()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c20-bind_front)가 추가되었습니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들을 순서대로 배치하므로, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 순서 변경이 없다면 간편하게 사용할 수 있습니다.*

# bind() 와 인수의 참조성 유지

[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)는 인수를 복사하기 때문에, 만약 참조자를 사용하는 함수라면 오동작하게 됩니다.

```cpp
// 인자들의 값을 1씩 증가시킵니다.
void Add(int& a, int& b, int& c) {
    ++a;
    ++b;
    ++c;
}

int a{1};
int b{2};
int c{3};

std::function<void()> add{
    std::bind(Add, a, b, c) // a, b, c를 복제합니다.
};
add();
// (X) 오동작. bind()에서 인수를 복제했기 때문에 참조성이 깨졌습니다.
EXPECT_TRUE(a == 1 && b == 2 && c == 3);
```

이런 경우 복제되더라도 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 잃지 않도록 [reference_wrapper](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#reference_wrapper)를 전달하여야 하며, [ref()나 cref()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#ref-cref)를 이용합니다.

```cpp
int a{1};
int b{2};
int c{3};

std::function<void()> add{
    std::bind(Add, std::ref(a), std::ref(b), std::ref(c)) // 참조자 래퍼를 대입합니다.
};
add();
// (O) 참조자 래퍼를 대입하여 값이 수정되었습니다.
EXPECT_TRUE(a == 2 && b == 3 && c == 4);    
```

# is_bind_expression

[is_bind_expression](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_bind_expression)는 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)로 생성한 함수인지 검사합니다. 단, [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function) 개체에 저장한 것을 사용하면, [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) 관련 속성을 잃어버리기 때문에 오동작 합니다.

```cpp
int Sum(int a, int b, int c) {return a + b + c;}
template<typename T>
bool IsBind(const T&) {
    return std::is_bind_expression<T>::value;
}

EXPECT_TRUE(IsBind(std::bind(Sum, 1, 2, 3)) == true);  

auto func1{std::bind(Sum, 1, 2, 3)}; 
EXPECT_TRUE(IsBind(func1) == true); 

std::function<int()> func2{std::bind(Sum, 1, 2, 3)}; // (X) 오동작. function 개체에 저장하면, bind에 대한 관련 속성을 잃어버립니다.
EXPECT_TRUE(IsBind(func2) == false);   
```

# is_placeholder

[is_placeholder](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#is_placeholder)가 추가되어 자리 표시자를 사용했는지 검사할 수 있습니다. 단, [function](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#function) 개체에 저장한 것을 사용하면, [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) 관련 속성을 잃어버리기 때문에 오동작 합니다.

```cpp
int Sum(int a, int b, int c) {return a + b + c;}
template<typename T>
bool IsBind(const T&) {
    return std::is_bind_expression<T>::value;
}

EXPECT_TRUE(IsPlaceholder(std::bind(Sum, 1, std::placeholders::_1, 3)) == true);  

auto func1{std::bind(Sum, 1, std::placeholders::_1, 3)}; 
EXPECT_TRUE(IsPlaceholder(func1) == true); 

std::function<int(int)> func2{std::bind(Sum, 1, std::placeholders::_1, 3)}; // (X) 오동작. function 개체에 저장하면, bind에 대한 관련 속성을 잃어버립니다.
EXPECT_TRUE(IsPlaceholder(func2) == false);   
```

# (C++17~) invoke()

C++17 부터는 [invoke()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c17-invoke)가 추가되어 일반 함수와 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 동일한 방식으로 호출할 수 있습니다. 일반 함수인 경우 `Func(params...)`를 호출하고, [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)인 경우 `params[1].Func(params[2]...)`을 호출합니다.

```cpp
class T {
public:
    int Sum(int a, int b, int c) {return a + b + c;} // 멤버 함수
};

int Sum(int a, int b) {return a + b;} // 일반 함수

T t;
EXPECT_TRUE(std::invoke(T::Sum, t, 1, 2, 3) == 1 + 2 + 3); // 멤버 함수를 호출합니다.

EXPECT_TRUE(std::invoke(Sum, 1, 2) == 1 + 2); // 일반 함수를 호출합니다.
```

# (C++20~) bind_front()

C++11 부터 [bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind)가 추가되어 특정 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들을 재구성할 수 있었는데요(*[bind()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#bind) 참고*), 

C++20 부터는 좀더 간편한 [bind_front()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-function/#c20-bind_front)가 추가되었습니다. [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들을 순서대로 배치하므로, [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 순서 변경이 없다면 간편하게 사용할 수 있습니다.

```cpp
int Sum(int a, int b, int c) {return a + b + c;}

auto func1{
    std::bind(Sum, 1, std::placeholders::_1, std::placeholders::_2) // placeholders를 지정해야 합니다.
};
EXPECT_TRUE(func1(2, 3) == 1 + 2 + 3);

auto func2{
    std::bind_front(Sum, 1) // 앞쪽 인자부터 순서대로 적용하기 때문에 placeholders를 지정할 필요가 없습니다.
};
EXPECT_TRUE(func2(2, 3) == 1 + 2 + 3);
```