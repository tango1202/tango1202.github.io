---
layout: single
title: "#25. [모던 C++] (C++20~) 삼중 비교 연산자, 비트 쉬프트 연산자의 기본 비트 표준화"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++20~) [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [비교 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90) 구현이 간소화 되었습니다.
> * (C++20~) [삼중 비교 연산자를 default로 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90-default-%EC%A0%95%EC%9D%98)할 수 있습니다.
> * (C++20~) [비트 쉬프트 연산자의 기본 비트가 표준화](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EB%B9%84%ED%8A%B8-%EC%89%AC%ED%94%84%ED%8A%B8-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EA%B8%B0%EB%B3%B8-%EB%B9%84%ED%8A%B8-%ED%91%9C%EC%A4%80%ED%99%94)되어 `<< 1`는 곱하기 2의 효과가 있는 비트(*즉, `0`*)로 채워지고, `>> 1`은 나누기 2의 효과가 있는 비트(*즉, 양수면 `0`, 음수면 `1`*)로 채워집니다.

# (C++20~) 삼중 비교 연산자

기존에는 [비교 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)를 구현하기 위해서 `==`, `!=`, `<`, `>`, `<=`, `>=` 6개의 [비교 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)를 각각 구현해야 했는데요(*[연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고*),

```cpp
class T {
    int m_Val;
public:
    explicit T(int val) : m_Val{val} {}
    bool operator ==(const T& other) const {return m_Val == other.m_Val;} // < 로부터 구현하면, !(*this < other || other < *this)로 할 수 있음. 단 < 을 2회 하므로 비효율적임.
    bool operator !=(const T& other) const {return !(*this == other);} // == 로부터 구현
    bool operator <(const T& other) const {return m_Val < other.m_Val;}
    bool operator >(const T& other) const {return other < *this;} // < 로부터 구현
    bool operator <=(const T& other) const {return !(other < *this);} // < 로부터 구현
    bool operator >=(const T& other) const {return !(*this < other);} // < 로부터 구현
};  

EXPECT_TRUE(T{10} == T{10});  
EXPECT_TRUE(T{10} != T{20}); 
EXPECT_TRUE(T{10} < T{20}); 
EXPECT_TRUE(T{20} > T{10}); 
EXPECT_TRUE(T{10} <= T{20} && T{10} <= T{10}); 
EXPECT_TRUE(T{20} >= T{10} && T{10} >= T{10}); 
```

C++20 부터는 [삼중 비교 연산자(<=>)](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 `==`과 [삼중 비교 연산자(<=>)](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)만 정의하면 6개의 [비교 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)가 컴파일러에 의해 자동으로 정의됩니다.(*`strong_ordering`은 [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)를 참고하세요.*)

```cpp
class T_20 {
    int m_Val;
public:
    explicit T_20(int val) : m_Val{val} {}
    
    std::strong_ordering operator <=>(const T_20& other) const {return m_Val <=> other.m_Val;}
    bool operator ==(const T_20& other) const {return m_Val == other.m_Val;}
};  

EXPECT_TRUE(T_20{10} == T_20{10});  
EXPECT_TRUE(T_20{10} != T_20{20}); 
EXPECT_TRUE(T_20{10} < T_20{20}); 
EXPECT_TRUE(T_20{20} > T_20{10}); 
EXPECT_TRUE(T_20{10} <= T_20{20} && T_20{10} <= T_20{10}); 
EXPECT_TRUE(T_20{20} >= T_20{10} && T_20{10} >= T_20{10}); 
```
# 삼중 비교 연산자 비교

`<=>` 연산자를 정의하면, 기존 `==`, `!=`, `<`, `>`, `<=`, `>=` 로 비교할 수 있을 뿐만 아니라, `<=>`로 직접 비교할 수 있습니다. 

이때 `strcmp()`와 유사하게 다음과 같이 비교합니다.

* 작음 : `(left <=> right) < 0`
* 같음 : `(left <=> right) == 0`
* 큼 : `(left <=> right) > 0`

```cpp
class T_20 {
    int m_Val;
public:
    explicit T_20(int val) : m_Val{val} {}
    
    std::strong_ordering operator <=>(const T_20& other) const {return m_Val <=> other.m_Val;}
    bool operator ==(const T_20& other) const {return m_Val == other.m_Val;}
};  

EXPECT_TRUE((T_20{10} <=> T_20{10}) == 0);  // left == right
EXPECT_TRUE((T_20{10} <=> T_20{20}) != 0); // left != right
EXPECT_TRUE((T_20{10} <=> T_20{20}) < 0); // left < right
EXPECT_TRUE((T_20{20} <=> T_20{10}) > 0);  // left > right
EXPECT_TRUE((T_20{10} <=> T_20{20}) <= 0 && (T_20{10} <=> T_20{10}) <= 0); // left <= right
EXPECT_TRUE((T_20{20} <=> T_20{10}) >= 0  && (T_20{10} <=> T_20{10}) >= 0); // left >= right
```

또한 이종 타입과의 비교를 지원하는데요, 예를 들어 다음 `T`는 `int`와 비교하기 위해 무려 18개의 함수를 구현해야 합니다. 더군다나 `int < T`와 같은 형태는 멤버 함수로 만들수도 없어 비멤버 버전으로 만들어야 합니다.(*[연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고*)

```cpp
class T {
    int m_Val;
public:
    explicit T(int val) : m_Val{val} {} 

    // T op T 의 형태로 비교
    bool operator ==(const T& other) const {return m_Val == other.m_Val;} // < 로부터 구현하면, !(*this < other || other < *this)로 할 수 있음. 단 < 을 2회 하므로 비효율적임.
    bool operator !=(const T& other) const {return !(*this == other);} // == 로부터 구현
    bool operator <(const T& other) const {return m_Val < other.m_Val;}
    bool operator >(const T& other) const {return other < *this;} // < 로부터 구현
    bool operator <=(const T& other) const {return !(other < *this);} // < 로부터 구현
    bool operator >=(const T& other) const {return !(*this < other);} // < 로부터 구현

    // T op int 의 형태로 비교
    bool operator ==(int other) const {return m_Val == other;} 
    bool operator !=(int other) const {return !(m_Val == other);} 
    bool operator <(int other) const {return m_Val < other;}
    bool operator >(int other) const {return other < m_Val;} 
    bool operator <=(int other) const {return !(other < m_Val);} 
    bool operator >=(int other) const {return !(m_Val < other);} 
}; 
// int op T의 형태로 비교
bool operator ==(int left, const T& right) {return T{left} == right;}
bool operator !=(int left, const T& right) {return T{left} != right;}
bool operator <(int left, const T& right) {return T{left} < right;}
bool operator >(int left, const T& right) {return T{left} > right;}
bool operator <=(int left, const T& right) {return T{left} <= right;}
bool operator >=(int left, const T& right) {return T{left} >= right;}

EXPECT_TRUE(T{10} < T{20}); // T op T
EXPECT_TRUE(T{10} < 20); // T op int  
EXPECT_TRUE(10 < T{20}); // int op T
```

[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)를 이용하면, `(left <=> right) < 0` 이 유효하지 않다면 비교 연산자의 대칭성(*`a < b`는 `b > a` 입니다.*)을 고려하여 `(right <=> left) > 0` 로 변환하여 비교합니다. 이 특징을 이용하면, `int`를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받는 [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)와 `==` 멤버 버전만 추가하면 `T_20`과 `int`를 손쉽게 비교할 수 있습니다.

* `(T_20{10} <=> 10) < 0`와 같이 호출하면, `operator <=>(int other)` 버전이 호출됩니다.
* `(10 <=> T_20{20}) < 0`와 같이 호출하면, 유효하지 않으므로, `(T_20{20} <==> 10) > 0`으로 변환하여 `operator <=>(int other)` 버전이 호출됩니다.

```cpp
class T_20 {
    int m_Val;
public:
    explicit T_20(int val) : m_Val{val} {}
    
    // T_20 op T_20
    std::strong_ordering operator <=>(const T_20& other) const {return m_Val <=> other.m_Val;}
    bool operator ==(const T_20& other) const {return m_Val == other.m_Val;}

    // T_20 op int 또는 int op T_20
    std::strong_ordering operator <=>(int other) const {return m_Val <=> other;}
    bool operator ==(int other) const {return m_Val == other;}
};  

EXPECT_TRUE((T_20{10} <=> T_20{20}) < 0); // T_20 op T_20
EXPECT_TRUE((T_20{10} <=> 20) < 0); // T_20 op int  
EXPECT_TRUE((10 <=> T_20{20}) < 0); // int op T_20. (T_20 <=> 10) > 0으로 변경합니다.  

EXPECT_TRUE(T_20{10} < T_20{20}); // T_20 op T_20
EXPECT_TRUE(T_20{10} < 20); // T_20 op int  
EXPECT_TRUE(10 < T_20{20}); // int op T_20. T_20 > 10으로 변경합니다.  
```

만약 `T_20`이 [explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)를 사용하지 않아 [암시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 된다면, `operator <=>(const T_20& other)` 버전만 구현해도 됩니다. 하지만 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)은 나쁜 것이니 피해야 하겠죠.

```cpp
class T_20 {
    int m_Val;
public:
    T_20(int val) : m_Val{val} {} // (△) 비권장. explicit 가 없습니다. 암시적 형변환 됩니다.
    
    // T_20 op T_20
    std::strong_ordering operator <=>(const T_20& other) const {return m_Val <=> other.m_Val;}
    bool operator ==(const T_20& other) const {return m_Val == other.m_Val;}
};  

EXPECT_TRUE((T_20{10} <=> T_20{20}) < 0); // T_20 op T_20
EXPECT_TRUE((T_20{10} <=> 20) < 0); // int를 암시적으로 T_20으로 형변환  
EXPECT_TRUE((10 <=> T_20{20}) < 0); // int op T_20. (T_20 <=> 10) > 0으로 변경후 int를 암시적으로 T_20으로 형변환

EXPECT_TRUE(T_20{10} < T_20{20}); // T_20 op T_20
EXPECT_TRUE(T_20{10} < 20); // int를 암시적으로 형변환.
EXPECT_TRUE(10 < T_20{20}); // int op T_20. T_20 > 10으로 변경후 int를 암시적으로 T_20으로 형변환
```

# 삼중 비교 연산자 default 정의

[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)를 [default](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#default%EC%99%80-delete)로 정의할 수 있습니다. 이때에는 `==`도 컴파일러가 같이 정의해 줍니다. 

컴파일러는 각 멤버 변수의 선언 순서대로 비교합니다. 이때 컴파일러에 따라 비교 속도 최적화를 위해 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)와 같은 [컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/)는 각 요소의 대소 비교 전에 크기에 대한 비교를 선행할 수 있습니다.  

리턴 타입은 3개의 [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)(*`strong_ordering`, `weak_ordering`, `partial_ordering`*) 중 하나입니다. 컴파일러 판단을 그대로 따르기 위해 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용하기도 합니다.(*(C++14~) [리턴 타입 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0)이 가능하여 [후행 리턴](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#%ED%9B%84%ED%96%89-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 참고*)

```cpp
class T_20 {
    int m_Val;
public:
    explicit T_20(int val) : m_Val{val} {}
    
    auto operator <=>(const T_20& other) const = default; 
    // bool operator ==(const T_20& other) const {return m_Val == other.m_Val;} // default 로 정의하면, ==를 정의할 필요가 없습니다.
};  

EXPECT_TRUE(T_20{10} == T_20{10});  
EXPECT_TRUE(T_20{10} != T_20{20}); 
EXPECT_TRUE(T_20{10} < T_20{20}); 
EXPECT_TRUE(T_20{20} > T_20{10}); 
EXPECT_TRUE(T_20{10} <= T_20{20} && T_20{10} <= T_20{10}); 
EXPECT_TRUE(T_20{20} >= T_20{10} && T_20{10} >= T_20{10});
```

만약 `==`, `!=`, `<`, `>`, `<=`, `>=`사용자 정의 버전과 `<=>` 버전이 혼용된다면, `<=>`으로 비교하면 `<=>` 버전을 사용하고, `==`, `!=`, `<`, `>`, `<=`, `>=` 으로 사용하면, 사용자 정의 버전을 우선적으로 사용합니다. 단, `!=` 경우 사용자 정의 버전이 없더라도 `==` 가 정의되어 있다면, `==`을 사용합니다.

```cpp
class T_20 {
    int m_Val;
public:
    explicit T_20(int val) : m_Val{val} {}
    
    auto operator <=>(const T_20& other) const = default; 
    bool operator <(const T_20& other) const { // 사용자 정의 버전입니다.
        std::cout << "T_20::operator <" << std::endl;
        return m_Val < other.m_Val;
    } 
    bool operator ==(const T_20& other) const { // 사용자 정의 버전입니다.
        std::cout << "T_20::operator ==" << std::endl;
        return m_Val == other.m_Val;
    }             
}; 

EXPECT_TRUE(T_20{10} < T_20{20}); // < 을 사용합니다. 
EXPECT_TRUE(T_20{20} > T_20{10}); // > 이 없으므로 <=> 을 사용합니다.
EXPECT_TRUE((T_20{10} <=> T_20{20}) < 0); //<=> 을 사용합니다.

EXPECT_TRUE(T_20{10} == T_20{10}); // == 을 사용합니다.
EXPECT_TRUE(T_20{10} != T_20{20}); // !=이 없으나 사용자 정의한 ==이 있으므로 == 을 사용합니다. 
EXPECT_TRUE((T_20{10} <=> T_20{10}) == 0); //<=> 을 사용합니다.
```

# 상등 비교와 동등 비교

[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)는 기존의 [비교 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)처럼 `bool`를 리턴하는 것이 아니라 [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)(*`strong_ordering`, `weak_ordering`, `partial_ordering`*) 중 하나를 리턴합니다. [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)를 알아보기 전에 먼저 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)와 [동등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)의 개념을 알아 두어야 합니다.

[대소 비교의 논리 조건](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%8C%80%EC%86%8C-%EB%B9%84%EA%B5%90%EC%9D%98-%EB%85%BC%EB%A6%AC-%EC%A1%B0%EA%B1%B4)에서 `x < y` 도 아니고 `y < x` 도 아니면, `x` 는 `y` 와 동등하다는 논리를 말씀드렸는데요, 두개의 개체가 같은지를 비교하는 건 세부적으로 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)와 [동등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)로 구분할 수 있습니다.

* [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90) : 두 개체의 데이터가 동일합니다.
* [동등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90) : 두 개체의 데이터가 개념적으로 동일합니다.

예를 들어 면적을 다루는 `Area` 개체를 생각해 봅시다. 가로 X 세로가 `2 X 3`과 `3 X 2` 인 개체는 가로와 세로값이 달라 [상등](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)하지 않지만, 개념적으로 면적값은 `6`이므로 [동등](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)하다고 할 수 있습니다. 

```cpp
class Area {
    int m_Width;
    int m_Height;
public:
    Area(int width, int height) : m_Width{width}, m_Height{height} {}
    int GetData() const {return m_Width * m_Height;}

    // 상등 비교. 가로와 세로가 같은지 검사합니다.
    bool IsEqual(const Area& other) const {
        if (m_Width != other.m_Width) return false;
        return m_Height == other.m_Height;
    }

    // 동등 비교 : 개념적으로 같은지, 즉 면적이 같은지 검사합니다.
    bool IsEquivalence(const Area& other) const {
        return GetData() == other.GetData();
    }
};
Area a{2, 3};
Area b{3, 2};
EXPECT_TRUE(a.IsEqual(b) == false); // 2 X 3 과 3 X 2 는 상등하지 않습니다. 
EXPECT_TRUE(a.IsEquivalence(b) == true); // 2 X 3 과 3 X 2 는 동등합니다. 
```

`==` 구현시 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 할 것인지, [동등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 할 것인지는 전적으로 개발자의 판단에 따릅니다.

# 비교 카테고리와 삼중 비교 연산자의 리턴 타입

[삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)는 3개의 [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85) 중 하나를 리턴합니다. 따라서, 개체가 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 지원하는지, [동등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 지원하는지, `==` 비교를 신뢰할 수 있는지 좀더 쉽게 파악할 수 있습니다.

|항목|내용|
|--|--|
|`strong_ordering`|`==`, `!=`, `<`, `>`, `<=`, `>=`의 비교 연산을 제공합니다. 여기서 `==`은 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)입니다. 즉 데이터들이 완전히 동일함을 의미합니다.|
|`weak_ordering`|`==`, `!=`, `<`, `>`, `<=`, `>=`의 비교 연산을 제공합니다. 여기서 `==`은 [동등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 합니다. 즉, 개념적으로 동일함을 의미합니다. 예를들어 대소문자 구분없이 비교 할때 `A`와 `a`는 [아스키 코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%95%84%EC%8A%A4%ED%82%A4-%EC%BD%94%EB%93%9C)값이 다르므로 [상등](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)하지 않지만, [동등](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)(*개념적으로 동일*) 합니다.|
|`partial_ordering`|`==`, `!=`, `<`, `>`, `<=`, `>=`의 비교 연산을 제공합니다. 실수 타입과 같이 대소 비교는 가능한데, `==`는 소수점 오차등으로 [상등 비교](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EC%83%81%EB%93%B1-%EB%B9%84%EA%B5%90%EC%99%80-%EB%8F%99%EB%93%B1-%EB%B9%84%EA%B5%90)를 신뢰하기 애매한 경우입니다.|

[비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)의 포함 관계는 다음과 같습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5e78afe1-0ac2-4b6e-a20e-5589b12f733c)

만약 클래스의 멤버 변수가 [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)들을 혼합해서 사용한다면, [삼중 비교 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EC%82%BC%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)는 상기 포함 관계 따라 리턴되는 [비교 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-3way-comparison/#%EB%B9%84%EA%B5%90-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EC%99%80-3%EC%A4%91-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EB%A6%AC%ED%84%B4-%ED%83%80%EC%9E%85)를 결정합니다.

```cpp
class Strong_20 {
    int m_Val;
public:
    explicit Strong_20(int val) : m_Val{val} {}
    std::partial_ordering operator <=>(const Strong_20& other) const = default; 
};
class Weak_20 {
    int m_Val;
public:
    explicit Weak_20(int val) : m_Val{val} {}
    std::partial_ordering operator <=>(const Weak_20& other) const = default; 
};
class Partial_20 {
    int m_Val;
public:
    explicit Partial_20(int val) : m_Val{val} {}
    std::partial_ordering operator <=>(const Partial_20& other) const = default; 
};

class Mix_20 {
    Strong_20 m_Strong;
    Weak_20 m_Weak;
    Partial_20 m_Partial;

public:
    explicit Mix_20(int strong, int weak, int partial) : m_Strong{strong}, m_Weak(weak), m_Partial(partial) {}
    auto operator <=>(const Mix_20& other) const = default; // partial ordering
};

std::partial_ordering result{Mix_20{0, 0, 0} <=> Mix_20{1, 1, 1}}; // partial_ordering을 리턴합니다.
```

# (C++20~) 비트 쉬프트 연산자의 기본 비트 표준화  

기존에 [비트 쉬프트 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%B9%84%ED%8A%B8-%EC%89%AC%ED%94%84%ED%8A%B8-%EC%97%B0%EC%82%B0%EC%9E%90)는 비록 표준에 정의되지는 않았으나, 양수던 음수이던, `<< 1`은 곱하기 2의 효과가 있고, `>> 1`은 나누기 2의 효과가 있었는데요(*[비트 쉬프트 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%B9%84%ED%8A%B8-%EC%89%AC%ED%94%84%ED%8A%B8-%EC%97%B0%EC%82%B0%EC%9E%90)* 참고), 

C++20부터는 [비트 쉬프트 연산자의 기본 비트가 표준화](https://tango1202.github.io/mordern-cpp/mordern-cpp-operators/#c20-%EB%B9%84%ED%8A%B8-%EC%89%AC%ED%94%84%ED%8A%B8-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-%EA%B8%B0%EB%B3%B8-%EB%B9%84%ED%8A%B8-%ED%91%9C%EC%A4%80%ED%99%94)되어 `<< 1`는 곱하기 2의 효과가 있는 비트(*즉, `0`*)로 채워지고, `>> 1`은 나누기 2의 효과가 있는 비트(*즉, 양수면 `0`, 음수면 `1`*)로 채워집니다.


