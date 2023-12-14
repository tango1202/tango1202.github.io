---
layout: single
title: "#42. [모던 C++ STL] (C++20~)  범위(Range)와 뷰(View)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

[범위(Range)](??)는 이터레이팅할 수 있는 추상적인 요소들의 집합입니다.

[범위(Range) 컨셉](??)은 다음과 같습니다.

```cpp
template<typename T>
concept range = requires(T& t) {
    ranges::begin(t); 
    ranges::end(t);
};
```

한마디로 [ranges::begin](??)과 [ranges::end](??)를 호출할 수 있는 개체인데요, 

1. [vector](??)와 같은 STL [컨테이너](??)나 
2. [배열](??)이나 
3. 이터레이팅이 가능한 `begin()`함수와 `end()`함수를 멤버로 가진 개체입니다.

[범위(Range)](??)인지 아닌지는 다음과 같이 [ranges::range 컨셉](??)을 확인하면 됩니다.

```cpp
class T {
public:
    int begin() const {return 0;} // int 타입은 이터레이팅 할 수 없습니다.
    int end() const {return 0;} // int 타입은 이터레이팅 할 수 없습니다.
};
class U {
public:
    int* begin() const {return nullptr;} // int* 타입은 이터레이팅 할 수 있습니다.
    int* end() const {return nullptr;} // int* 타입은 이터레이팅 할 수 있습니다.
};         

// static_assert(std::ranges::range<int>); // (X) 컴파일 오류. int 타입은 이터레이팅 할 수 없습니다.
static_assert(std::ranges::range<int[3]>); // (O) 배열
static_assert(std::ranges::range<std::vector<int>>); // (O) STL 컨테이너
// static_assert(std::ranges::range<T>); // (X) 컴파일 오류. begin - end에서 int를 리턴하므로 이터레이팅 할 수 없습니다.
static_assert(std::ranges::range<U>); // (O) 이터레이팅 할 수 있는 개체
```

[범위(Range)](??)이면 [뷰(View)](??)를 사용하여 요소를 탐색하고 수정하는 작업을 손쉽게 처리할 수 있습니다. 이때 [뷰(View)](??)는 [string_view](??)와 마찬가지로 요소를 복제하지 않습니다.

```cpp
class A {
private:
    int m_Val;
public:
    explicit A(int val) : m_Val{val} {}
    A(const A& other) : m_Val(other.m_Val) {
        std::cout << "Range A : Copy Constructor" << std::endl;
    }
    A(A&&) = delete;
    A& operator =(const A&) = delete;
    A& operator =(A&&) = delete;

    int GetVal() const {return m_Val;}
};

std::vector<A> v{A{1}, A{2}, A{3}}; // Copy Constructor 3회 호출합니다.
auto r{std::views::reverse(v)}; // 역순으로 바꿔서 보여주는 view입니다. 이때 요소 복제를 하지 않습니다.

EXPECT_TRUE(v[0].GetVal() == 1 && v[1].GetVal() == 2 && v[2].GetVal() == 3);
EXPECT_TRUE(r[0].GetVal() == 3 && r[1].GetVal() == 2 && r[2].GetVal() == 1); // 역순으로 탐색됩니다.
```

STL에서는 다양한 [미리 정의된 뷰(View)](??)를 제공하며, [뷰(View) 합성](??)을 이용하여 연산을 간소하게 표현할 수 있습니다.

# 포인트 개체(Point Object) 유틸리티

[범위(Range)](??)의 [포인트 개체](??)는 [범위(Range)](??)의 [이터레이터](??)를 구하는 개체입니다.

|항목|내용|
|--|--|
|[ranges::begin, ranges::end](??) (C++20~)|순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 구합니다.|
|`ranges::rbegin, ranges::rend` (C++20~)|[역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 구합니다.|  
|`ranges::cbegin, ranges::cend` (C++20~)|순방향 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)를 구합니다. 이때 요소를 수정할 수 없습니다.|
|`ranges::crbegin, ranges::crend` (C++20~)|[역방향 이터레이터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/#%EC%97%AD%EB%B0%A9%ED%96%A5-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)를 구합니다. 이때 요소를 수정할 수 없습니다.|  
|`ranges::size` (C++20~)|[범위(Range)](??)의 크기를 상수 시간에 구합니다. 즉, 임의 접근이 가능한 [이터레이터](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/)만 사용할 수 있습니다.| 
|`ranges::ssize` (C++20~)|[범위(Range)](??)의 크기를 부호 있는 `signed` 타입으로 구합니다.| 
|`ranges::empty` (C++20~)|[범위(Range)](??)가 비었는지 검사합니다.| 
|`ranges::data` (C++20~)|[범위(Range)](??)가 관리하는 메모리 블록을 구합니다.| 
|`ranges::cdata` (C++20~)|(작성중)| 

[포인트 개체 유틸리티](??)의 모양들이 C++11 부터 추가된 [이터레이터 유틸리티](??) 함수들인 [begin(), end()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-iterator/#c11-%EB%B2%94%EC%9C%84-%EC%A0%91%EA%B7%BC)들과 유사한데요, 차이점은 다음과 같습니다.

1. [포인트 개체](??)는 실제로 이터레이팅 할 수 있는 개체만 사용할 수 있습니다.

    다음 예에서 `std::begin(t)`는 실제로 이터레이팅 할 수 없는 `T` 개체를 사용할 수 있습니다. 하지만 `std::ranges::begin(t)`는 컴파일 오류가 발생합니다.
    ```cpp
    class T {
    public:
        int begin() const {return 0;} // int 타입은 이터레이팅 할 수 없습니다.
    };

    T t;

    EXPECT_TRUE(std::begin(t) == 0); // (△) 비권장. 전달받은 개체의 begin() 함수를 호출합니다.
    // EXPECT_TRUE(std::ranges::begin(t) == 0); // (X) 컴파일 오류. 전달받은 개체의 이터레이팅 시작점을 리턴합니다. 이터레이팅 할 수 없으므로 오류를 리턴합니다.

    class U {
    public:
        int* begin() const {return nullptr;} // int* 타입은 이터레이팅 할 수 있습니다.
    }; 
    U u;

    EXPECT_TRUE(std::begin(u) == nullptr); //  전달받은 개체의 begin() 함수를 호출합니다.
    EXPECT_TRUE(std::ranges::begin(u) == nullptr); // 전달받은 개체의 begin() 함수를 호출합니다.
    ```

2. [포인트 개체](??)는 [함수자](??)로 사용할 수 있습니다.

    ```cpp
    // auto f{std::begin}; // (X) 컴파일 오류. 함수자로 사용할 수 없습니다.
    auto g{std::ranges::begin}; // 함수자로 사용할 수 있습니다.
    ```

# 범위(Range) 타입

|항목|내용|
|--|--|
|`ranges::iterator_t` (C++20~)|[ranges::begin](??)으로 구해지는 [이터레이터](??) 타입입니다.|
|`ranges::sentinel_t` (C++20~)|[ranges::end](??)로 구해지는 [이터레이터](??) 타입입니다.|
|`ranges::range_difference_t` (C++20~)|[이터레이터](??)를 `-`했을때의 타입입니다.|
|`ranges::range_size_t` (C++20~)|[이터레이터](??)의 크기 타입입니다.|
|`ranges::range_value_t` (C++20~)|[이터레이터](??)의 값 타입입니다.|
|`ranges::range_reference_t` (C++20~)|[이터레이터](??) 값의 [좌측값 참조](??) 타입입니다.|
|`ranges::range_rvalue_reference_t` (C++20~)|[이터레이터](??) 값의 [우측값 참조](??) 타입입니다.|
|`ranges::range_common_reference_t` (C++20~)|[이터레이터](??)의 값 타입과 [좌측값 참조](??) 타입의 공통 참조 타입입니다.|
|`ranges::const_iterator_t` (C++23~)|(작성중)|
|`ranges::const_sentinel_t` (C++23~)|(작성중)|
|`ranges::range_const_reference_t` (C++23~)|(작성중)|

# Dangling 이터레이터

|항목|내용|
|--|--|
|`ranges::dangling` (C++20~)|(작성중)|
|`ranges::borrowed_iterator_t, ranges::borrowed_subrange_t` (C++20~)|(작성중)|


# 범위(Range) 알고리즘

기존에는 [알고리즘](??)을 사용하기 위해서 [이터레이터](??)를 전달했는데요,

```cpp
std::vector<int> v{3, 2, 1};
std::sort(v.begin(), v.end()); // 기존 방식은 시작과 끝 이터레이터를 전달해야 합니다.
EXPECT_TRUE(v[0] == 1 && v[1] == 2 && v[2] == 3);
```

C++20 부터는 [범위(Range) 알고리즘](??)이 추가되어 대부분의 알고리즘에서 [범위(Range)](??)를 지원합니다.(*`std::ranges` [네임스페이스](??)를 사용합니다.*) 

다음과 같이 [이터레이터](??)를 전달할 필요없이 [범위(Range)](??)를 바로 전달하면 됩니다.

```cpp
std::vector<int> v{3, 2, 1};
std::ranges::sort(v); // 범위를 전달하면 됩니다.
EXPECT_TRUE(v[0] == 1 && v[1] == 2 && v[2] == 3);  
```

# 범위(Range) 알고리즘의 Projection

[범위(Range) 알고리즘](??)에는 [Projection](??)이 추가되어 [범위(Range)](??) 요소 대신 사용할 개체를 지정할 수 있습니다.

에를 들어 다음과 같이 `std::pair<std::string, int>`로 이름과 점수를 관리하는 [컨테이너](??)를 [sort()](??)를 사용하여 정렬하면, `std::pair<std::string, int>` 타입을 비교하여 정렬하므로, `pair::first`가 먼저 비교되어 이름 순서로 정렬됩니다.

```cpp
std::vector<std::pair<std::string, int>> v{
    {"Kim", 30}, 
    {"Park", 10},
    {"Lee", 20} 
};

std::sort(v.begin(), v.end());
EXPECT_TRUE(v[0].first == "Kim" && v[1].first == "Lee" && v[2].first == "Park"); // pair 값으로 정렬되어 Kim, Lee, Park 순서입니다.
```

이를 성적순으로 정렬하려면, `Compare`를 수정하여 `pair::second`가 비교 되도록 수정해야 합니다.

```cpp
std::vector<std::pair<std::string, int>> v{
    {"Kim", 30}, 
    {"Park", 10},
    {"Lee", 20} 
};

std::sort(
    v.begin(),
    v.end(), 
    // [](std::pair<std::string, int> left, std::pair<std::string, int> right) { // (C++14부터 람다 표현식에 auto를 사용할 수 있습니다.)
    [](auto left, auto right) { // Compare를 pair::second를 사용하도록 사용자 정의합니다.
        return left.second < right.second;
    }
);
EXPECT_TRUE(v[0].first == "Park" && v[1].first == "Lee" && v[2].first == "Kim"); // pair.second 값으로 정렬되어 Park, Lee, Kim 순서입니다.
```

[범위(Range) 알고리즘](??)의 [Projection](??)을 이용하면, `Compare`를 수정할 필요없이 [범위(Range)](??) 요소 대신 `pair::second`를 사용하도록 지정할 수 있습니다.

```cpp
std::vector<std::pair<std::string, int>> v{
    {"Kim", 30}, 
    {"Park", 10},
    {"Lee", 20} 
};
std::ranges::sort(
    v, 
    {}, // 기본 Compare를 사용합니다.
    &std::pair<std::string, int>::second // 요소 타입인 std::pair<std::string, int> 대신 std::pair<std::string, int>::second를 비교하는데 사용합니다.
);
EXPECT_TRUE(v[0].first == "Park" && v[1].first == "Lee" && v[2].first == "Kim"); // pair.second 값으로 정렬되어 Park, Lee, Kim 순서입니다.
```

# 뷰(View)


[뷰(View) 컨셉](??)은 다음과 같습니다.

```cpp
template<typename T>
concept view = 
    ranges::range<T> && // range 이고
    std::movable<T> && // 이동 가능하고
    ranges::enable_view<T>; // view_base 또는 view_interface 로부터 파생되어야 합니다.
```

[뷰(View)](??)는 [범위(Range)](??) 요소들을 재배치/수정/필터링한 일종의 [범위(Range)](??)입니다. 이때 [string_view](??)와 마찬가지로 요소를 복제하지 않습니다.

|항목|내용|
|--|--|
|`ranges::view_base` (C++20~)|사용자 정의 [뷰(View)](??)를 생성시 사용하는 부모 개체 입니다. 아무 함수도 없이 정의된 껍데기 입니다.(*`struct view_base { };`*)|
|[ranges::view_interface](??) (C++20~)|사용자 정의 [뷰(View)](??)를 생성시 사용하는 부모 개체 입니다.|
|[ranges::subrange](??) (C++20~)|[뷰(View)](??)의 하위 [범위(Range)](??) 입니다.|
|`ranges::subrange_kind` (C++20~)|[ranges::subrange](??)가 [ranges::sized_range](??)인지 아닌지 구별합니다.|


[ranges::view_interface](??)의 멤버 함수는 다음과 같습니다.

|항목|내용|
|--|--|
|`empty()` (C++20~)|[뷰(View)](??)가 비었는지 확인합니다.|
|`operator bool()` (C++20~)|[bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)로 형변환시 [뷰(View)](??)가 비었는지 확인합니다.|
|`operator []()` (C++20~)|[뷰(View)](??)가 [random_access_range](??)인 경우 요소를 리턴합니다.|
|`data()` (C++20~)|관리하는 메모리 블록을 리턴합니다.|
|`size()` (C++20~)|의 요소 갯수를 리턴합니다. 이때 [sized_sentinel_for](??)이어야 합니다.|
|`front()` (C++20~)|첫번째 요소의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. [ranges::forward_range](??)이어야 합니다.|
|`back()` (C++20~)|마지막 요소의 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴합니다. [ranges::bidirectional_range](??)이어야 합니다.|
|`cbegin()` (C++23~)|(작성중)|
|`cend()` (C++23~)|(작성중)|


다음은 [범위(Range)](??)를 전달받아 그대로 이터레이팅하는 기본적인 [뷰(View)](??)입니다. [ranges::view_interface](??)를 상속하여 구현하며, [생성자](??)에서 복사되지 않도록 [참조자](??)를 사용합니다.

```cpp
template<std::ranges::range R> // R은 range이어야 합니다.
class MyView : public std::ranges::view_interface<MyView<R>> {
private:
    std::ranges::iterator_t<R> m_Begin;
    std::ranges::iterator_t<R> m_End;
public:
    // 컴파일 타임에도 동작할 수 있도록 constexpr 생성자로 만듭니다.
    // 복사 되지 않게 참조 타입으로 전달받습니다.
    constexpr explicit MyView(R& range) : 
        m_Begin{std::ranges::begin(range)},
        m_End{std::ranges::end(range)} {}
    constexpr auto begin() {return m_Begin;}
    constexpr auto end() {return m_End;}
};

std::vector<int> v{1, 2, 3};

MyView view{v};

EXPECT_TRUE(view.size() == 3); 
EXPECT_TRUE(view[0] == 1 && view[1] == 2 && view[2] == 3);
```
# 미리 정의된 뷰(View)

STL에서는 다양한 [뷰(View)](??)를 미리 정의해 두었으며, `views` [네임스페이스](??)에 각 [뷰(View)](??)를 생성하는 유틸리티 함수들을 제공합니다.

예를들어 `std::views::empty()`는 `ranges::empty_view`를 생성합니다.

|항목|내용|
|--|--|
|항목|내용|
|--|--|
|`ranges::views::all_t, ranges::views::all()` (C++20~)|[범위(Range)](??)의 모든 요소로 구성됩니다.|
|`ranges::ref_view` (C++20~)|다른 [범위(Range)](??)를 참조로 래핑합니다.|
|`ranges::owning_view` (C++20~)|[범위(Range)](??)의 고유한 소유권을 갖습니다. 다른 [뷰(view)](??)에 전달할때 [move()](??)를 사용합니다.|
|`ranges::empty_view, views::empty()` (C++20~)|요소가 없는 빈 [뷰(View)](??)입니다.|
|`ranges::single_view, views::single(val)` (C++20~)|단일 요소인 `val`만 가진 [뷰(View)](??)입니다.|
|`ranges::iota_view, views::iota(init)` (C++20~)|`init` 부터 `1`씩 증가한 요소를 가진 [뷰(View)](??)입니다. 무한한 요소를 표현하며, 특별히 `Bound`를 주어 유한하게 만들 수 있습니다.(*`std::views::iota(1,5)`는 1, 2, 3, 4를 나타냅니다.*)|
|`ranges::basic_istream_view, views::istream()` (C++20~)|(작성중)|
|`ranges::filter_view, views::filter(range, Predicate)` (C++20~)|`range`중 [조건자](??)가 `true`인 요소들로 구성된 [뷰(View)](??)입니다. 상수 시간에 크기를 구할 수 없습니다.(*즉, range::size()를 구할 수 없습니다.*)|
|`ranges::transform_view, views::transform(range, Function)` (C++20~)|`range`에 `Function`을 적용한 [뷰(View)](??)입니다.|
|`ranges::take_view, views::take(range, N)` (C++20~)|`range`에서 처음 `N`개를 사용하는 [뷰(View)](??)입니다.|
|`ranges::take_while_view, views::take_while()` (C++20~)|(작성중)|
|`ranges::drop_view, views::drop(range, N)` (C++20~)|`range`에서 처음 `N`개를 뺀 나머지 요소들을 사용하는 [뷰(View)](??)입니다.|
|`ranges::drop_while_view, views::drop_while()` (C++20~)|(작성중)|
|`ranges::join_view, views::join(range)` (C++20~)|`range`의 요소들이 하위 [범위(Range)](??)로 구성된 경우 이를 평면화한 [뷰(View)](??)입니다.|
|`ranges::split_view, views::split(range, pattern)` (C++20~)|`range`를 `pattern`으로 나누어 여러개의 [std::subrange](??)로 분할합니다.|
|`ranges::lazy_split_view, views::lazy_split()` (C++20~)|(작성중)|
|`views::counted` (C++20~)|(작성중)|
|`ranges::common_view, views::common(range)` (C++20~)|`range`를 [ranges::common_range](??)로 변환합니다.|
|`ranges::reverse_view, views::reverse(range)` (C++20~)|`range`를 역순으로 변경한 [뷰(View)](??)입니다.|
|`ranges::elements_view, views::elements<N>(range)` (C++20~)|[tuple](??)구성된 `range`에서 [tuple](??)의 `N`번째 요소로 구성된 [뷰(View)](??)입니다.|
|`ranges::keys_view, views::keys(range)` (C++20~)|[tuple](??)이나 [pair](??)로 구성된 `range`에서 `0`번째 요소로 구성된 [뷰(View)](??)입니다. `map`인 경우 **Key** 로 구성됩니다.|
|`ranges::values_view, views::values()` (C++20~)|[tuple](??)이나 [pair](??)로 구성된 `range`에서 `1`번째 요소로 구성된 [뷰(View)](??)입니다. `map`인 경우 **Value** 로 구성됩니다.|
|`ranges::enumerate_view, views::enumerate()` (C++23~)|(작성중)|
|`ranges::zip_view, views::zip()` (C++20~)|(작성중)|
|`ranges::zip_transform_view, views::zip_transform()` (C++23~)|(작성중)|
|`ranges::repeat_view, views::repeat()` (C++23~)|(작성중)|
|`ranges::adjacent_view, views::adjacent()` (C++23~)|(작성중)|
|`ranges::adjacent_transform_view, views::adjacent_transform()` (C++23~)|(작성중)|
|`ranges::join_with_view, views::join_with()` (C++23~)|(작성중)|
|`ranges::slide_view, views::slide()` (C++23~)|(작성중)|
|`ranges::chunk_view, views::chunk()` (C++23~)|(작성중)|
|`ranges::chunk_by_view, views::chunk_by()` (C++23~)|(작성중)|
|`ranges::as_const_view, views::as_const()` (C++23~)|(작성중)|
|`ranges::as_rvalue_view, views::as_rvalue()` (C++23~)|(작성중)|
|`ranges::as_stride_view, views::as_stride()` (C++23~)|(작성중)|
|`ranges::artesian_product_view, views::artesian_product()` (C++23~)|(작성중)|

다음은 [범위(View)](??)에 각 [뷰(View)](??)를 사용한 예입니다.

```cpp
{ // empty_view
    std::ranges::empty_view<int> r;
    EXPECT_TRUE(r.size() == 0);
    EXPECT_TRUE(r.begin() == nullptr); // 이터레이터가 nullptr입니다.
    EXPECT_TRUE(r.end() == nullptr);
}
{ // single_view
    int i = 3;
    auto r{std::views::single(3)};
    EXPECT_TRUE(r.size() == 1 && r[0] == 3); // 이터레이팅 할 수 있는 요소가 1개입니다.
} 
{ // iota_view
    auto r{std::views::iota(10, 13)};
    EXPECT_TRUE(
        r.size() == 3 && 
        r[0] == 10 && r[1] == 11 && r[2] == 12 // 10에서 13보다 작을때까지 만듭니다.
    );
}
{ // filter_view
    int arr[]{1, 2, 3, 4, 5};
    auto r{
        std::views::filter(
            arr, 
            [](int val) {return 3 < val ? true : false;} // 3보다 큰 것만 true입니다. 상수 시간에 크기를 알지 못하므로 길이를 알지 못하므로 ranges::size()를 구할 수 없습니다.
        )
    };   
    EXPECT_TRUE(
        *r.begin() == 4 &&  // 랜덤 접근을 지원하지 않습니다.
        *(++r.begin()) == 5
    );
}  
{ // transform_view
    int arr[]{1, 2, 3};
    auto r{
        std::views::transform(
            arr, 
            [](int val) {return val * 10;} // 요소에 10을 곱합니다. 
        )
    };   
    EXPECT_TRUE(
        r.size() == 3 && 
        r[0] == 10 && r[1] == 20 && r[2] == 30
    );
    EXPECT_TRUE(arr[0] == 1 && arr[1] == 2 && arr[2] == 3); // 원본이 수정되는건 아닙니다.

}    
{ // take_view
    int arr[]{1, 2, 3, 4, 5};
    auto r{std::views::take(arr, 2)}; // 앞의 2개로 구성됩니다.
    EXPECT_TRUE(
        r.size() == 2 && 
        r[0] == 1 && r[1] == 2
    );
}
{ // drop_view
    int arr[]{1, 2, 3, 4, 5};
    auto r{std::views::drop(arr, 2)}; // 앞의 2개를 뺀 나머지로 구성됩니다. 
    EXPECT_TRUE(
        r.size() == 3 && 
        r[0] == 3 && r[1] == 4 && r[2] == 5
    );
}   
{ // join_view
    std::vector<std::string> v{std::string{"ab"}, std::string{"cd"}};
    auto r{std::views::join(v)}; // v의 요소들이 range인 경우 이를 평면화합니다. 상수 시간에 크기를 알지 못하므로 ranges::size()를 구할 수 없습니다.
    EXPECT_TRUE(
        *r.begin() == 'a' && // 랜덤 접근을 지원하지 않습니다. 
        *(++r.begin()) == 'b' && 
        *(++++r.begin()) == 'c' && 
        *(++++++r.begin())  == 'd'
    );
}
{ // split_view
    std::string v{"ab^^cd"};
    auto r{std::views::split(v, std::string{"^^"})}; // v를 ^^으로 분할합니다. 상수 시간에 크기를 알지 못하므로 ranges::size()를 구할 수 없습니다.
    auto sub1 = *r.begin();
    auto sub2 = *(++r.begin());
    EXPECT_TRUE(
        sub1.size() == 2 &&
        sub1[0] == 'a' && sub1[1] == 'b' &&

        sub2.size() == 2 &&
        sub2[0] == 'c' && sub2[1] == 'd'            
    );
} 
{ // reverse_view
    int arr[]{1, 2, 3};    
    auto r{std::views::reverse(arr)}; // v를 역순으로 구성합니다.
    EXPECT_TRUE(
        r.size() == 3 &&
        r[0] == 3 && r[1] == 2 && r[2] == 1            
    );
}
{ // elements_view
    std::vector<std::tuple<int, std::string>> v{
        {0, "Kim"}, 
        {1, "Lee"}
    };
    auto r{std::views::elements<0>(v)}; // tuple의 0번째 요소로 구성합니다.
        EXPECT_TRUE(
        r.size() == 2 && 
        r[0] == 0 && r[1] == 1 
    );
}  
{ // keys_view, values_view
    std::map<int, std::string> m{
        {0, "Kim"}, 
        {1, "Lee"}
    };
    auto keys{std::views::keys(m)}; // map의 Key로 요소로 구성합니다.
    EXPECT_TRUE(
        keys.size() == 2 && 
        *keys.begin() == 0 && // 랜덤 접근을 지원하지 않습니다. 
        *(++keys.begin()) == 1 
    );
    auto values{std::views::values(m)}; // map의 value로 요소로 구성합니다.
    EXPECT_TRUE(
        values.size() == 2 && 
        *values.begin() == "Kim" && // 랜덤 접근을 지원하지 않습니다.
        *(++values.begin()) == "Lee"
    );
}
```

# 뷰(View) 합성

[뷰(View)](??)를 [범위(Range)](??)에 여러번 적용해서 사용할 수 있습니다.

다음은 [pair](??)를 요소로 사용하는 [vector](??)에 대하여,

1. [views::elements()](??)로 점수만 추출한 후,
2. [views::filter()](??)로 `20`이상인 것만 구성하고,
3. [view::transform()](??)으로 `10`을 곱한 예입니다.

```cpp
std::vector<std::pair<std::string, int>> v{
    {"Kim", 30}, 
    {"Park", 10},
    {"Lee", 20} 
};
auto r1{std::views::elements<1>(v)}; // 점수만 추출합니다.
auto r2{ // 20 이상인 것으로 구성합니다.
    std::views::filter(
        r1,
        [](int val) {return 20 <= val;} 
    )
}; 
auto r3{
    std::views::transform( // 20이상인 것에 10을 곱합니다.
        r2,
        [](int val) {return val * 10;}
    )
};
EXPECT_TRUE(*r3.begin() == 300 && *(++r3.begin()) == 200); // filter에서 랜덤 접근을 지원하지 않습니다.
```

상기 코드는 다음처럼 중첩해서 사용할 수도 있습니다.

```cpp
std::vector<std::pair<std::string, int>> v{
    {"Kim", 30}, 
    {"Park", 10},
    {"Lee", 20} 
};
auto r{
    std::views::transform( // 20이상인 것에 10을 곱합니다.
        std::views::filter( // 20 이상인 것으로 구성합니다.
            std::views::elements<1>(v), // 점수만 추출합니다.
            [](int val) {return 20 <= val;}
        ),
        [](int val) {return val * 10;} 
    )
};

EXPECT_TRUE(*r.begin() == 300 && *(++r.begin()) == 200); // filter에서 랜덤 접근을 지원하지 않습니다.   
```

[뷰(View)](??)에서는 `|`을 이용하여 좀더 간소화한 표현을 제공합니다. 왼쪽 [뷰(View)](??)의 결과 [범위(Range)](??)가 오른쪽 [뷰(View)](??)의 입력 [범위(Range)](??)로 전달됩니다.

```cpp
std::vector<std::pair<std::string, int>> v{
    {"Kim", 30}, 
    {"Park", 10},
    {"Lee", 20} 
};
auto r{
    std::views::elements<1>(v) | // 점수만 추출합니다.
    std::views::filter([](int val) {return 20 <= val;}) | // 20 이상인 것으로 구성합니다.
    std::views::transform([](int val) {return val * 10;}) // 20이상인 것에 10을 곱합니다.
};

EXPECT_TRUE(*r.begin() == 300 && *(++r.begin()) == 200); // filter에서 랜덤 접근을 지원하지 않습니다. 
```

# 뷰(View)의 성질 전파

대부분의 [뷰(View)](??)는 상수 시간에 크기를 구할 수 있는 [ranges::sized_range](??)이고 랜덤 접근이 가능하지만, 입력값으로 사용된 [범위(Range)](??)에 따라 달라집니다. 


예를 들어 [transform_view](??)는 `size()`를 구할 수 있고, 랜덤 접근도 가능한데요,


```cpp
std::vector<int> v{1, 2, 3};
auto r{std::views::transform(v, [](int val) {return val * 10;})};

// transform은 size()와 랜덤접근이 가능합니다.
EXPECT_TRUE(r.size() == 3 && r[0] == 10 && r[1] == 20 && r[2] == 30);
```

`size()`를 사용할 수 없고, 랜덤 접근도 안되는 [filter_view](??)와 [뷰(View) 합성](??)을 하면, `size()`와 `[]`을 사용할 수 없습니다.

```cpp
std::vector<int> v{1, 2, 3};
auto r1{std::views::filter(v, [](int val) {return 20 <= val;})};
auto r2{std::views::transform(r1, [](int val) {return val * 10;})};

// (X) 컴파일 오류. filter()의 결과 Range()를 사용하기 때문에 size()와 랜덤 접근을 사용할 수 없습니다.
// EXPECT_TRUE(r1.size() == 3 && r1[0] == 10 && r1[1] == 20 && r1[2] == 30);
```

# 뷰(View)의 지연 생성

[views::iota()](??)의 경우 초기값에서 `1`씩 증가하는 무한한 시퀀스를 만듭니다. 이때 시퀀스를 미리 생성하지 않고, 실제로 사용할 때 유한한 크기로 생성합니다. 

```cpp
auto r{
    std::views::iota(10) | // 10부터 1씩 증가하는 무한한 시퀀스입니다.
    std::views::take(3) // 앞의 3개로만 구성합니다. 즉, 10, 11, 12입니다.
};

int sum{0};
for(auto val : r) {
    sum += val;
}
EXPECT_TRUE(sum == 10 + 11 + 12);
```

# (C++23~) 범위(Range) 변환

|항목|내용|
|--|--|
|`ranges::to()` (C++23~)|(작성중)|

# (C++23~) 범위(Range) 생성

|항목|내용|
|--|--|
|`generator` (C++23~)|(작성중)|

# (C++23~) 범위(Range) 어뎁터 유틸리티

|항목|내용|
|--|--|
|`ranges::range_adaptor_closure` (C++23~)|(작성중)|

# (C++23~) 범위(Range) 유틸리티

|항목|내용|
|--|--|
|`from_range_t, from_range` (C++23~)|(작성중)|