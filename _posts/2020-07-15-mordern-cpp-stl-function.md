---
layout: single
title: "#15. [모던 C++ STL] (C++11~) 함수자(function, bad_function_call, mem_fn(), 참조자 래핑, bind())"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `function`은 `()`로 호출 가능한 함수자를 저장합니다.
> * `function`에서 `()`을 호출할 대상이 없을 때 `bad_function_call` 예외를 방출합니다.
> * `mem_fn()`은 인자가 있는 멤버 함수도 호출하는 함수자를 만들어 줍니다.
> * `reference_warapper` 는 복사나 대입이 안되는 참조자를 래핑합니다.
> * `ref()`, `cref()`는 `reference_wrapper` 개체를 생성합니다.
> * `bind()`는 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.
> > * `is_bind_expression`는 `bind()`로 생성한 함수인지 검사합니다.
> > * `is_placeholder`는 자리 표시자를 사용했는지 검사합니다.
> * [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)(`unary_function`, `binery_function` 등), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)(`bind1st()`, `bind2nd()` 등), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)(`mem_fun()`, `mem_fun_ref()`, `ptr_fun()`, `not1()`, `not2()` 등)가 람다 표현식, `function`, `bind()`, `mem_fn()`등으로 대체되어 deprecate 되었습니다. 

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

이에 따라 C++11 부터 STL 에서는 기존의 까다로웠던 [함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), [바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94), [어뎁터와 부정자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 deprecate 하고, [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function) 개체를 추가하여 `()`로 호출 가능한 함수자들을 다룰 수 있게 했습니다.

항목|(~C++11)|(C++11~)|내용|
|--|--|--|--|
|함수자 정의|[함수자 타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)(`unary_function`, `binery_function`)|[람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/)등 활용.<br/>[function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function) 개체로 저장|기존은 불필요한 제약이었습니다.|
|[바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)|`bind1st()`, `bind2nd()`|[bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)|다수의 인자를 지원합니다.|
|[바인더](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)|`bind1st()`, `bind2nd()`|[bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)|다수의 인자를 지원합니다.|



# function

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

`function`에서 `()`을 호출할 대상이 없을 때 `bad_function_call` 예외를 방출합니다.

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

`mem_fn()`은 인자가 있는 멤버 함수도 호출하는 함수자를 만들어 줍니다.

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

`reference_warapper` 는 복사나 대입이 안되는 참조자를 래핑하여 복사/대입이 가능하게 하고, 참조성을 유지시켜 줍니다. 

`get()`으로 관리하는 참조자를 구할 수 있으며, 다른 참조자 생성시 `operator T&() const` 을 이용한 암시적 형변환을 통해 참조자를 대입합니다.(암시적 형변환이 필요하여 `{}`는 안됩니다.) 

```cpp
int a{0};
std::reference_wrapper<int> rw(a);

rw.get() = 10; // reference_wrapper가 관리하는 참조자에 값 대입
EXPECT_TRUE(a == 10);

int& b = rw; // 참조자 생성시 암시적 형변환을 통해 reference_wrapper가 관리하는 참조자 대입
b = 20;
EXPECT_TRUE(a == 20);
```

**템플릿 함수 인수 추론시의 참조성**

템플릿 함수 인수 추론시에는 참조자의 참조성을 제거하고 추론합니다.([템플릿 함수 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 참고)

다음 `Assign()` 함수는 당연히 인수로 전달한 `a`의 값을 수정합니다. 인자가 `int&` 타입 이니까요.

```cpp
void Assign(int& obj, int val) {
    obj = 10;
}

int a{0};
Assign(a, 10); // 참조성이 유지되어 a값이 수정됩니다.
EXPECT_TRUE(a, 10);
```

이걸 템플릿으로 바꿔보면, `T == int`여서 `Assign()`은 `a`의 복제본인 `obj`를 사용합니다. `a`가 `int` 타입이니 당연한 결과입니다.

```cpp
template<typename T>
void Assign(T obj, int val) {
    obj = 10;
}

int a{0};
Assign(a, 10); // (X) 오동작. T == int 여서 a의 복제본이 사용됩니다. 참조성이 깨졌습니다.
EXPECT_TRUE(a == 0);   
```

하지만, 강제로 참조자로 바꿔도, 인수 `int&`는 `int`로 추론되기 때문에 `T == int` 가 되어 `obj`는 `a`의 복제본입니다. 즉, 참조자를 인식하지 못합니다.

```cpp
int a{0};
int& ref{a}; // 강제로 참조자로 만들었습니다.
Assign(ref, 10); // (X) 오동작. 여전히 T == int 여서 ref의 복제본이 사용됩니다. 참조성이 깨졌습니다.
EXPECT_TRUE(a == 0);  
```

그래서 다음처럼 `T&`로 작성했습니다.

```cpp
template<typename T>
void Assign(T& obj, int val) { // 참조자입니다.
    obj = 10;
}

int a{0};
Assign(a, 10); // (O) 
EXPECT_TRUE(a == 10);  
```

**인수의 참조성 유지**

템플릿 인자에 `T&`를 사용해서 억지로 참조성을 유지할 수는 있지만, 일반적이지는 못합니다.

다음처럼 호출하려는 함수와 함수의 인수들을 전달받아 함수를 호출하는 `Forwarding()` 함수를 만들었다고 합시다. 

```cpp
// func(params...) 를 호출합니다.
template<typename Func, typename... Params>
void Forwarding(Func func, Params... params) {
    func(params...);
}  
```

`void f(int, int*);` 와 같은 함수는 잘 호출됩니다만, 참조자를 사용하면 문제가 생깁니다. 

인수가 `T`던, `T&` 던 `Forwarding()` 함수에서는 인자를 `T`로 취급해 복제본을 사용해 버리니까요. `func()`호출시 복제된 인자를 사용하기 때문에, 참조성은 깨져버립니다.

이럴때 `reference_wrapper`를 사용하면, `Forwarding()` 함수가 `reference_wrapper`를 복제해 버리더라도, 내부적으로는 개체의 참조자를 관리하므로, 참조성을 유지할 수 있습니다. 

```cpp
void Assign(int& obj, int val) {
    obj = 10;
} 

// func(params...) 를 호출합니다.
template<typename Func, typename... Params>
void Forwarding(Func func, Params... params) {
    func(params...);
} 

int a{0};

// (X) 오동작. 
// int 타입을 전달했기 때문에 Forwarding()의 파라메터 팩에서 int 타입으로 간주하여 복제본을 만들어 Assign()에 전달합니다.
Forwarding(Assign, a, 10); 
EXPECT_TRUE(a == 0);  

int& ref{a};
// (X) 오동작. 
// int& 타입을 전달했기 때문에 Forwarding()의 파라메터 팩에서 int 타입으로 간주하여 복제본을 만들어 Assign()에 전달합니다.
Forwarding(Assign, ref, 10); 
EXPECT_TRUE(a == 0);        

// (O) 참조성이 유지됩니다.
std::reference_wrapper<int> rw{a};
Forwarding(Assign, rw, 10); 
EXPECT_TRUE(a == 20);  
```

**컨테이너 요소 활용**

또한, 참조자를 컨테이너 요소로 사용할 수 있습니다.

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

`ref()`, `cref()`는 `reference_wrapper` 개체를 생성합니다. 

|항목|내용|
|--|--|
|`ref(T)` (C++11~)|`std::reference_wrapper<T>` 생성|
|`cref(T)` (C++11~)|`std::reference_wrapper<const T>` 생성|

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

`bind()`는 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.

다음 코드에서,

1. `func1()`은 `Sum()`의 모든 값을 지정했습니다.
2. `func2()`는 `Sum()`의 `b`인자만 자리 표시자를 사용합니다.
3. `func3()`은 `Sum()`의 `b`인자, `c`인자에 자리 표시자를 사용합니다.
 
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
# bind() 와 인수의 참조성 유지

`bind()`는 인수를 복사하기 때문에, 만약 참조자를 사용하는 함수라면 오동작하게 됩니다.

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

이런 경우 복제되더라도 참조성을 잃지 않도록 `reference_wrapper`를 전달하여야 하며, `ref()`나 `cref()`를 이용합니다.

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

`is_bind_expression`는 `bind()`로 생성한 함수인지 검사합니다. 단, `function` 개체에 저장한 것을 사용하면, `bind()` 관련 속성을 잃어버리기 때문에 오동작 합니다.

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

`is_placeholder`는 자리 표시자를 사용했는지 검사합니다. 단, `function` 개체에 저장한 것을 사용하면, `bind()` 관련 속성을 잃어버리기 때문에 오동작 합니다.

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