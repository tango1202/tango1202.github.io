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
> * 람다 표현식이 추가되어 1회용 익명 함수를 만들 수 있습니다.([람다 표현식, 클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda) 참고) 
> * `function`은 `()`로 호출 가능한 함수자를 저장합니다.([function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/) 참고)
> * `mem_fn()`은 인자가 있는 멤버 함수도 호출하는 함수자를 만들어 줍니다.([mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn) 참고)
> * [reference_wrapper](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#reference_wrapper), [ref(), cref()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#ref-cref) 는 복사나 대입이 안되는 참조자를 래핑합니다.
> * `bind()`는 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.([bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind) 참고)
> * 함수자 타입 특성 클래스(`unary_function`, `binery_function` 등), 바인더(`bind1st()`, `bind2nd()` 등), 어뎁터와 부정자(`mem_fun()`, `mem_fun_ref()`, `ptr_fun()`, `not1()`, `not2()` 등)가 람다 표현식, `function`, `bind()`, `mem_fn()`등으로 대체되어 deprecate 되었습니다.([function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function) 참고)
> * `hash()`는 각 타입별로 데이터의 해시값(Digest)을 리턴하는 함수자 입니다.([hash()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-hash/) 참고)


# 함수자와 조건자

함수자는 함수처럼 동작하는 개체입니다. 함수는 다음처럼 함수명 + `()`를 이용해서 호출하는데요, 함수자는 `operator ()`을 재구현한 개체입니다.(특별히 조건 검사를 위해 `bool` 을 리턴하면 조건자(Predicate) 라고도 합니다.)

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

> *C++11부터는 람다 표현식이 추가되어 1회용 익명 함수를 만들 수 있습니다.([람다 표현식, 클로저](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda) 참고)*

# 함수자에서 상태 활용

함수자는 개체이므로 상태를 멤버 변수로 저장하여 활용할 수 있습니다.

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

함수자 정의시 표준적인 사용을 위해 다음의 타입 특성 클래스를 사용하는게 좋습니다.

|항목|내용|
|--|--|
|`unary_function`|단항 함수자|
|`binary_function`|이항 함수자|

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

`Adder` 함수자는 다음처럼 `unary_function`을 상속하여 구현할 수 있습니다.

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
> *C++11 부터 함수자 타입 특성 클래스(`unary_function`, `binery_function` 등)은 deprecate 되었습니다.([function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function) 참고)*

> *C++11 부터는 `function`을 이용하여 `()`로 호출 가능한 함수자를 사용합니다.([function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/) 참고)*

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

`vector`에서 7보다 작은 첫 요소를 찾으려고 합니다. `find_if()` 를 사용하려고 봤더니 단항 함수자인 조건자(Predicate)를 사용합니다.

```cpp
template<typename InputIterator, typename Predicate>
InputIterator my_find_if( // std 버전도 대략 이렇게 구현되었습니다.
    InputIterator first, 
    InputIterator last,
    Predicate pred) {
    
    // 조건자(Predicate) 가 참이면 해당 위치의 이터레이터를 리턴합니다.
    while (first != last && !pred(*first)) {
        ++first;
    }
    return first;
}
```

따라서 하기와 같이 단항 함수자를 만들어야 합니다.

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

하지만, `bind2nd(op, y)` 를 이용하면, 표준의 이항 함수자인 `less<int>()` 를 단항 함수자로 만들어 사용할 수 있습니다.

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

> *C++11 부터 바인더(`bind1st()`, `bind2nd()` 등)은 deprecate 되었습니다.([function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function) 참고)*

> *C++11 부터는 `bind()`와 `placeholders::_1`(GCC의 경우 `_1`, `_2`, `_3`, ... `_29`가 정의됨)와 같은 자리 표시자와 조합하여 특정 인자만을 사용하는 함수자를 생성합니다.([bind()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#bind) 참고)*

# 아답터와 부정자

알고리즘에서는 함수 호출시 `f(컨테이너 요소)`의 형태로 구현되어 있습니다. 따라서 요소의 멤버 함수를 호출 할 수 없는데요, 하기와 같은 아답터를 이용하면 됩니다.

|항목|내용|
|--|--|
|`mem_fun()`|알고리즘에서 `f(x)`의 호출을 `x->f()` 처럼 호출되게 합니다.|
|`mem_fun_ref()`|알고리즘에서 `f(x)`의 호출을 `x.f()` 처럼 호출되게 합니다.|
|`ptr_fun()`|바인더, 아답터, 부정자와 일반 함수가 호환될 수 있도록 일반 함수를 `unary_function`이나 `binary_function` 개체로 만듭니다.|
|`not1()`|단항 조건자의 반환값을 부정합니다.|
|`not2()`|이항 조건자의 반환값을 부정합니다.|

보통 컨테이너 요소들의 특정 함수를 호출해야 하는 경우 다음과 같이 이터레이터를 통해 멤버 함수를 호출하지만,

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

다음처럼 `for_each()`와 `mem_fun_ref()`를 이용하면, 좀 더 간결히 만들 수 있습니다.

```cpp
class A {
public:
    void Func() {}
};

std::vector<A> v(2);
std::for_each(v.begin(), v.end(), std::mem_fun_ref(&A::Func));
```
> *C++11 부터 어뎁터와 부정자(`mem_fun()`, `mem_fun_ref()`, `ptr_fun()`, `not1()`, `not2()` 등)은 deprecate 되었습니다.([function](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function) 참고)*

> *C++11 부터는 `mem_fn()`을 이용하여 멤버 함수를 호출합니다.([mem_fn()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-function/#mem_fn) 참고)*


