---
layout: single
title: "#8. [모던 C++ STL] (C++11~) function, bad_function_call"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `function`은 `()`로 호출 가능한 개체를 저장합니다.
> * [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)가 deprecate 되었습니다.

# 개요

기존에는 함수처럼 호출하는 개체를 만들기 위해 함수자를 이용했는데요([함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 참고),

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

`unary_function`등의 [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 사용하는 방식이 좀 까다로웠습니다.

C++11 부터는 좀 더 간단한 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)을 사용할 수 있는데요,

```cpp
std::vector<int> v{7, 4, 3};
std::find_if(
    v.begin(), 
    v.end(), 
    [](int val) -> int {return val < 7 ? true : false;}
);
```

이에 따라 C++11 부터는 기존의 까다로웠던 [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 deprecate 하고,  `function` 개체를 추가하여 `()`로 호출 가능한 개체들을 다룰 수 있게 했습니다.

`function` 개체는 다음 개체들을 저장할 수 있습니다.

1. [함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/)
2. [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)
3. [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)
4. `bind()` 표현식

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

`function`에서 `()`을 호출할 대상이 없을 때 발생하는 예외입니다.

```cpp
std::function<void()> func{nullptr};

try {
    func();
}
catch (std::bad_function_call&) {
}
```

# mem_fn()

기존의 [아답터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)인 `mem_fun()`이나 `mem_fun_ref()`는 `f(x)`의 호출을 `x->f()`나 `x.f()`로 호출해 줬는데요, 사실 함수의 인수를 전달할 방법이 없어 사용상의 제약이 있었습니다.

`mem_fun()`은 인자가 있는 멤버 함수도 사용할 수 있는 함수자를 만들어 줍니다.

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

`reference_warapper` 는 복사나 대입이 안되는 참조자를 래핑합니다. 이를 활용하면, 참조자를 컨테이너 요소로 사용할 수 있습니다.

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

`reference_wrapper` 개체를 생성합니다. 

|항목|내용|
|--|--|
|`ref(T)`|`std::reference_wrapper<T>` 생성|
|`cref(T)`|`std::reference_wrapper<const T>` 생성|

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

기존에는 이항 함수를 단항 함수로 만들기 위해 `bind1st(op, x)`나 `bind2nd(op, y)`와 같은 [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)를 이용했는데요,

C++11 부터는 `bind()`함수를 추가하여, 다수의 인자(GCC의 경우 `_1`~`_29` 범위에서 지원됨)를 가진 함수도 지원합니다.

`bind()`는 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 `function` 개체를 리턴합니다.

다음 코드에서,

1. `func1()`은 `Sum()`의 모든 값을 지정했습니다.
2. `func2()`는 `Sum()`의 `b`인자만 자리 표시자를 사용합니다.
3. `func3()`은 `Sum()`의 `b`인자, `c`인자에 자리 표시자를 사용합니다.
4. 
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

`auto`를 사용하면 좀더 간결하게 표현할 수 있습니다.

```cpp
auto func3{
    std::bind(Sum, 1, std::placeholders::_1, std::placeholders::_2)
};
EXPECT_TRUE(func3(4, 5) == 1 + 4 + 5);
```

`bind()`는 인자를 복사하기 때문에, 만약 참조를 사용하는 함수를 사용한다면,  오동작하게 됩니다.

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
    std::bind(Add, a, b, c) // 복사 대입합니다.
};
add();
// (X) 런타임 오류. 복사 대입하기 때문에 값이 변하지 않았습니다.
EXPECT_TRUE(a == 1 && b == 2 && c == 3);
```

이런 경우 참조자로 잘 전달되도록 `reference_wrapper`를 전달하여야 하며, `ref()`나 `cref()`를 이용합니다.
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

# is_placeholder

기존에는 함수처럼 호출하는 개체를 만들기 위해 함수자를 이용했는데요([함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 참고),


bind
is_bind_expression  
is_placeholder
_1, _2, _3 _4
