---
layout: single
title: "#16. [고전 C++ STL] 함수자(Functor), 바인더, 어뎁터, 부정자"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * (C++11~) [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)이 추가되어 1회용 익명 함수를 만들 수 있습니다.
> * (C++11~) [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function)은 `()`로 호출 가능한 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 저장합니다. 
> * (C++11~) [mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn)은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 있는 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)도 호출하는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 만들어 줍니다.
> * (C++11~) [reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper), [ref(), cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref)는 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이나 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 안되는 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 래핑합니다.
> * (C++11~) [bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)는 `placeholders::_1`(*GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨*)와 같은 자리 표시자와 조합하여 특정 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)만을 사용하는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 생성합니다.
> * (C++11~) [함수자 타입 특성 클래스(unary_function, binary_function)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits), 바인더([bind1st(), bind2nd()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)등), 어뎁터와 부정자([mem_fun(), mem_fun_ref(), ptr_fun(), not1(), not2()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)등)가 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D), [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#function), [bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind), [mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn)등으로 대체되어 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.
> * (C++11~) [hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-hash/)는 각 타입별로 데이터의 [해시값(Digest)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 리턴하는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 입니다.
> * (C++17~) [not_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%EB%B6%80%EC%A0%95%EC%9E%90)이 추가되어 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)(*단항, 이항 제한이 없습니다.*)로 전달한 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 부정하는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 만듭니다.


# 함수자와 조건자

[함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)는 함수처럼 동작하는 개체입니다. 함수는 다음처럼 함수명 + `()`를 이용해서 호출하는데요, [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)는 `operator ()`을 재구현한 개체입니다.(*특별히 조건 검사를 위해  [bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)을 리턴하면 [조건자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)(*Predicate*) 라고도 합니다.*)

```cpp
template<typename InputIterator, typename Function>
Function my_for_each(InputIterator first, InputIterator last, Function f) {
    for (; first != last; ++first) { 
        f(*first); // 함수명 + () 로 호출됩니다.
    }
    return f; 
}
// 10을 세팅하는 함수입니다.
void Func(int& val) {
    val = 10;
}

// 20을 세팅하는 함수자입니다.
class Functor {
public:
    void operator ()(int& val) {
        val = 20;
    } 
};

// 0으로 초기화 되어 생성
std::vector<int> v(3);

// 함수 호출
my_for_each(v.begin(), v.end(), Func);
EXPECT_TRUE(v[0] == 10 && v[1] == 10 && v[2] == 10); 

// 함수자 호출
my_for_each(v.begin(), v.end(), Functor());
EXPECT_TRUE(v[0] == 20 && v[1] == 20 && v[2] == 20); 
```

> *(C++11~) [](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)이 추가되어 1회용 익명 함수를 만들 수 있습니다.*

# 함수자에서 상태 활용

[함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)는 개체이므로 상태를 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)로 저장하여 활용할 수 있습니다.

```cpp
// 각 요소에 지정한 값을 추가합니다.
class Adder {
    int m_Val;
public:
    explicit Adder(int val) : m_Val(val) {}
    void operator ()(int& val) {
        val += m_Val;
    }  
};

std::vector<int> v;
v.push_back(1);
v.push_back(2);
v.push_back(3);

// v 의 각 요소에 10을 더합니다.
Adder adder(10);
std::for_each(v.begin(), v.end(), adder);
EXPECT_TRUE(v[0] == 11 && v[1] == 12 && v[2] == 13);
```

# 함수자 타입 특성 클래스(Traits)

[함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 정의시 표준적인 사용을 위해 다음의 [타입 특성 클래스](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-traits/)를 사용하는게 좋습니다.

|항목|내용|
|--|--|
|`unary_function`|단항 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)|
|`binary_function`|이항 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)|

```cpp
template<typename Arg, typename Res>
struct unary_function {
    typedef Arg argument_type; 
    typedef Res result_type; 
};
template<typename Arg, typename Arg2, typename Res>
struct binary_function {
    typedef Arg first_argument_type; 
    typedef Arg2 second_argument_type; 
    typedef Res result_type; 
};
```

`Adder` [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)는 다음처럼 [unary_function](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)을 상속하여 구현할 수 있습니다.

```cpp
// 인자 1개를 사용하는 함수자 정의
class Adder : std::unary_function<int&, void> {
    argument_type m_Val;
public:
    explicit Adder(argument_type val) : m_Val(val) {}
    result_type operator ()(argument_type val) {
        val += m_Val;
    } 
};

std::vector<int> v;
v.push_back(1);
v.push_back(2);
v.push_back(3);

// v 의 각 요소에 10을 더합니다.
int init = 10; // int& 타입이어서 변수로 값을 전달합니다.
Adder adder(init);
std::for_each(v.begin(), v.end(), adder);
EXPECT_TRUE(v[0] == 11 && v[1] == 12 && v[2] == 13);
```
> *(C++11~) [함수자 타입 특성 클래스(unary_function, binary_function)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits)는 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다. [function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/)을 사용합니다.*<br/>

# STL 기본 함수자

|항목|내용|
|--|--|
|`equal_to`|arg1 == arg2|
|`not_equal_to`|arg1 != arg2|
|`greater`|arg1 > arg2|
|`less`|arg1 < arg2|
|`greater_equal`|arg1 >= arg2|
|`less_equal`|arg1 <= arg2|
|`logical_and`|arg1 == arg2|
|`logical_or`|arg1 \|\| arg2|
|`logical_not`|!arg|
|`plus`|arg1 + arg2|
|`minus`|arg1 - arg2|
|`multiplies`|arg1 * arg2|
|`divides`|arg1 / arg2|
|`modulus`|arg1 % arg2|
|`negate`|-arg|

# 바인더

이항 연산이 필요한 함수를 단항 함수처럼 바꿔주는 개체입니다.

|항목|내용|
|--|--|
|`bind1st(op, x)`|알고리즘엔 단항 함수로 전달되며, `op(x, 요소)`로 이항 함수를 호출합니다.|
|`bind2nd(op, y)`|알고리즘엔 단항 함수로 전달되며, `op(요소, y)`로 이항 함수를 호출합니다.|

[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)에서 7보다 작은 첫 요소를 찾으려고 합니다. `find_if()` 를 사용하려고 봤더니 단항 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)인 [조건자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)(*Predicate*)를 사용합니다.

```cpp
template<typename InputIterator, typename Predicate>
InputIterator my_find_if( // std 버전도 대략 이렇게 구현되었습니다.
    InputIterator first, 
    InputIterator last,
    Predicate pred) {
    
    // 조건자(Predicate)가 참이면 해당 위치의 이터레이터를 리턴합니다.
    while (first != last && !pred(*first)) {
        ++first;
    }
    return first;
}
```

따라서 하기와 같이 단항 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 만들어야 합니다.

```cpp
// 7보다 작은지 검사하는 함수
class Less_7 : std::unary_function<int, bool> {
public:
    result_type operator ()(argument_type val) {
        return val < 7 ? true : false;
    }       
};

std::vector<int> v;
v.push_back(7);
v.push_back(4);
v.push_back(3);   

std::vector<int>::iterator itr = my_find_if( 
    v.begin(),
    v.end(),
    Less_7()
);  
EXPECT_TRUE(*itr == 4);  
```

하지만, `bind2nd(op, y)` 를 이용하면, 표준의 이항 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)인 `less<int>()` 를 단항 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)로 만들어 사용할 수 있습니다.

```cpp
std::vector<int> v;
v.push_back(7);
v.push_back(4);
v.push_back(3);   

// less(x, y)는 x < y 를 통해 true/false를 리턴하는 이항 함수자
// bind2nd()를 통해 알고리즘에서 less(벡터 요소, 7) 로 호출됨
// 7보다 작은 첫 요소를 리턴함
std::vector<int>::iterator itr = std::find_if( 
    v.begin(),
    v.end(),
    std::bind2nd(std::less<int>(), 7)
);  

EXPECT_TRUE(*itr == 4);  
```

> *(C++11~) 바인더([bind1st(), bind2nd()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EB%B0%94%EC%9D%B8%EB%8D%94)등)은 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.*<br/>
> *(C++11~) [bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind)는 `placeholders::_1`(*GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨*)와 같은 자리 표시자와 조합하여 특정 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)만을 사용하는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 생성합니다.*

# 어뎁터와 부정자

알고리즘에서는 함수 호출시 `f(컨테이너 요소)`의 형태로 구현되어 있습니다. 따라서 요소의 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 호출 할 수 없는데요, 하기와 같은 어뎁터를 이용하면 됩니다.

|항목|내용|
|--|--|
|`mem_fun()`|알고리즘에서 `f(x)`의 호출을 `x->f()` 처럼 호출되게 합니다.|
|`mem_fun_ref()`|알고리즘에서 `f(x)`의 호출을 `x.f()` 처럼 호출되게 합니다.|
|`ptr_fun()`|바인더, 어뎁터, 부정자와 일반 함수가 호환될 수 있도록 일반 함수를 [unary_function 이나 binary_function](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90-%ED%83%80%EC%9E%85-%ED%8A%B9%EC%84%B1-%ED%81%B4%EB%9E%98%EC%8A%A4traits) 개체로 만듭니다.|
|`not1()`|단항 [조건자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)의 반환값을 부정합니다.|
|`not2()`|이항 [조건자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%ED%95%A8%EC%88%98%EC%9E%90%EC%99%80-%EC%A1%B0%EA%B1%B4%EC%9E%90)의 반환값을 부정합니다.|

보통 [컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/) 요소들의 특정 함수를 호출해야 하는 경우 다음과 같이 [이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/)를 통해 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 호출하지만,

```cpp
class A {
public:
    void Func() {}
};

std::vector<A> v(2);
std::vector<A>::iterator itr = v.begin();
std::vector<A>::iterator endItr = v.begin();
for (; itr != endItr; ++itr) {
    (*itr).Func();
}
```

다음처럼 [for_each()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-algorithm/#for_each)와 [mem_fun_ref()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)를 이용하면, 좀 더 간결히 만들 수 있습니다.

```cpp
class A {
public:
    void Func() {}
};

std::vector<A> v(2);
std::for_each(v.begin(), v.end(), std::mem_fun_ref(&A::Func));
```
> *(C++11~) 어뎁터와 부정자([mem_fun(), mem_fun_ref(), ptr_fun(), not1(), not2()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/#%EC%95%84%EB%8B%B5%ED%84%B0%EC%99%80-%EB%B6%80%EC%A0%95%EC%9E%90)등)은 [deprecate](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-preview/#deprecateremove) 되었습니다.*<br/>
> *(C++11~) [mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn)을 이용하여 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 호출합니다.*<br/>
> *(C++17~) [not_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-functor/#%EB%B6%80%EC%A0%95%EC%9E%90)이 추가되어 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)(단항, 이항 제한이 없습니다.)로 전달한 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 부정하는 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/)를 만듭니다.*
