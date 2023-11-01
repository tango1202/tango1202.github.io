---
layout: single
title: "#18. [고전 C++ 가이드] 연산자들(Operators)과 연산자 오버로딩"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 생성되지 않도록 이항 산술 연산자(*`a = a + b`*)보다는 산술형 대입 연산자(*`a += b`*)를 사용하라.
> * [후위형 증감 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A6%9D%EA%B0%90-%EC%97%B0%EC%82%B0%EC%9E%90)는 헷갈리고, 쓸데없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 생성되니 사용하지 마라. 
> * 비교 연산 오버로딩은 [`<`을 활용](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%8C%80%EC%86%8C-%EB%B9%84%EA%B5%90%EC%9D%98-%EB%85%BC%EB%A6%AC-%EC%A1%B0%EA%B1%B4)해서 구현하라.

> **모던 C++**
> * (C++11~) `int operator ""_km(long double val);`와 같은 [사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-literals/#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되어 단위계 처리가 쉬워졌습니다.
> * (C++11~) [sizeof...()](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)에서  [파라메터 팩](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)의 인자수를 구할 수 있습니다.
> * (C++11~) [멤버의 `sizeof()`](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EB%A9%A4%EB%B2%84-sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)시 동작이 개선되어 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있습니다.
> * (C++11~) [type_index](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_index/)는 [type_info](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)의 래퍼로서 [type_info](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)를 컨테이너에서 관리할 수 있게 합니다.
> * (C++11~) [addressof()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-memory/#addressof)는 `operator &()`가 오버로딩 되었어도 실제 주소를 리턴합니다.

# 복사 대입 연산자

[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 `=` 와 같은 기본 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)와 산술 연산이나 비트 연산의 결과값을 대입하는 산술형 대입 연산자가 있습니다.(*`a += b` 는 `a = a + b` 와 결과가 같습니다.*) 


|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|[복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|`a = b`|O|`T& T::operator =(const T2& b);`|X|
|추가 대입|`a += b`|O|`T& T::operator +=(const T2& b);`|`T& operator +=(T& a, const T2& b);`|
|빼기 대입|`a -= b`|O|`T& T::operator -=(const T2& b);`|`T& operator -=(T& a, const T2& b);`|
|곱셈 대입|`a *= b`|O|`T& T::operator *=(const T2& b);`|`T& operator *=(T& a, const T2& b);`|
|나누기 대입|`a /= b`|O|`T& T::operator /=(const T2& b);`|`T& operator /=(T& a, const T2& b);`|
|나머지 대입|`a %= b`|O|`T& T::operator %=(const T2& b);`|`T& operator %=(T& a, const T2& b);`|
|비트 AND 대입|`a &= b`|O|`T& T::operator &=(const T2& b);`|`T& operator &=(T& a, const T2& b);`|
|비트 OR 대입|`a |= b`|O|`T& T::operator |=(const T2& b);`|`T& operator |=(T& a, const T2& b);`|
|비트 XOR 대입|`a ^= b`|O|`T& T::operator ^=(const T2& b);`|`T& operator ^=(T& a, const T2& b);`|
|비트 Left Shift 대입|`a <<= b`|O|`T& T::operator <<=(const T2& b);`|`T& operator <<=(T& a, const T2& b);`|
|비트 Right Shift 대입|`a >>= b`|O|`T& T::operator >>=(const T2& b);`|`T& operator >>=(T& a, const T2& b);`|

# 산술 연산자

산술 연산의 결과를 계산합니다. 

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|단항 양수|`+a`|O|`T T::operator +() const;`|`T operator +(const T &a);`|
|단항 음수|`-a`|O|`T T::operator -() const;`|`T operator -(const T &a);`|
|더하기|`a + b`|O|`T T::operator +(const T2 &b) const;`|`T operator +(const T &a, const T2 &b);`|
|빼기|`a - b`|O|`T T::operator -(const T2 &b) const;`|`T operator -(const T &a, const T2 &b);`|
|곱하기|`a * b`|O|`T T::operator *(const T2 &b) const;`|`T operator *(const T &a, const T2 &b);`|
|나누기|`a / b`|O|`T T::operator /(const T2 &b) const;`|`T operator /(const T &a, const T2 &b);`|
|나머지|`a % b`|O|`T T::operator %(const T2 &b) const;`|`T operator %(const T &a, const T2 &b);`|
|비트 NOT|`~a`|O|`T T::operator ~() const;`|`T operator ~(const T &a);`|
|비트 AND|`a & b`|O|`T T::operator &(const T2 &b) const;`|`T operator &(const T &a, const T2 &b);`|
|비트 OR|`a | b`|O|`T T::operator |(const T2 &b) const;`|`T operator |(const T &a, const T2 &b);`|
|비트 XOR|`a ^ b`|O|`T T::operator ^(const T2 &b) const;`|`T operator ^(const T &a, const T2 &b);`|
|비트 Left Shift|`a << b`|O|`T T::operator <<(const T2 &b) const;`|`T operator <<(const T &a, const T2 &b);`|
|비트 Right Shift|`a >> b`|O|`T T::operator >>(const T2 &b) const;`|`T operator >>(const T &a, const T2 &b);`|

# 증감 연산자

전위형 연산의 경우 값을 먼저 증감시킨뒤 증감한 값을 리턴합니다만, 후위형 연산의 경우는 값을 증감 시키기 전의 값을 리턴합니다.

후위형의 이러한 특징은 불필요한 복사 부하가 생길 뿐만아니라, 분석을 헷갈리게 할 수 있기 때문에(*의도인지, 실수인지*), 후위형 보다는 전위형 증감 연산자를 사용하는게 좋습니다.

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|전위 증가|`++a`|O|`T& T::operator ++();`|`T& operator ++(T& a);`|
|전위 감소|`--a`|O|`T& T::operator --();`|`T& operator --(T& a);`|
|후위 증가|`a++`|O|`const T T::operator ++(int);`|`const T operator ++(T& a, int);`|
|후위 감소|`a--`|O|`const T T::operator --(int);`|`const T operator --(T& a, int);`|

```cpp
// 전위형 - 증감시킨 값을 리턴
{
    int n = 10;
    int val = ++n;
    EXPECT_TRUE(val == 11); // 증가시킨 후 값
    EXPECT_TRUE(n == 11);
}
// 후위형 - 증감시키기 전의 값을 리턴
{
    int n = 10;
    int val = n++; 
    EXPECT_TRUE(val == 10); // (△) 비권장. 의도한 것인지 조금 헷갈립니다. 증가시킨 전 값. 
    EXPECT_TRUE(n == 11);
}
```

# 논리 연산자

NOT, AND, OR 논리 조건에 맞춰 `true`, `false`를 평가합니다.

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|NOT|`!a`|O|`bool T::operator !() const;`|`bool operator !(const T &a);`|
|AND|`a && b`|O|`bool T::operator &&(const T2 &b) const;`|`bool operator &&(const T &a, const T2 &b);`|
|OR|`a || b`|O|`bool T::operator ||(const T2 &b) const;`|`bool operator ||(const T &a, const T2 &b);`|

# 비교 연산자

대소 비교를 합니다. 실수 비교 연산의 경우는 오차 범위를 고려해야 합니다.(*[실수 비교](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%EC%8B%A4%EC%88%98-%EB%B9%84%EA%B5%90) 참고*) 

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|같다|`a == b`|O|`bool T::operator ==(const T2& b) const;`|`bool operator ==(const T& a, const T2& b);`|
|같지 않다.|`a != b`|O|`bool T::operator !=(const T2& b) const;`|`bool operator !=(const T& a, const T2& b);`|
|미만|`a < b`|O|`bool T::operator <(const T2& b) const;`|`bool operator <(const T& a, const T2& b);`|
|큰|`a > b`|O|`bool T::operator >(const T2& b) const;`|`bool operator >(const T& a, const T2& b);`|
|이하|`a <= b`|O|`bool T::operator <=(const T2& b) const;`|`bool operator <=(const T& a, const T2& b);`|
|이상|`a >= b`|O|`bool T::operator >=(const T2& b) const;`|`bool operator >=(const T& a, const T2& b);`|

# 대소 비교의 논리 조건

대소 비교는 하기 논리 조건을 만족해야 합니다.

1. `x < x` 는 거짓
2. `x < y` 이고 `y < z` 이면, `x < z` 
3. `!(x < y)` 이면, `(y <= x)` 
4. `x < y` 도 아니고 `y < x` 도 아니면, `x` 는 `y` 와 동등함(*equivalence. 동등관계*) 
5. `x equivalence y` 이고 `y equivalence z` 이면, `x equivalence z`

상기 논리 조건이 모두 만족한다면(*만족해야만 합니다.*), 비교 연산들은 모두 `<` 로 표현할 수 있습니다.

```cpp
int x = 10;
int y = 10;
EXPECT_TRUE(!(x < y || y < x)); // x == y

x = 10;
y = 20;   
EXPECT_TRUE((x < y || y < x)); // x != y

x = 10;
y = 20;
EXPECT_TRUE((x < y)); // x < y

x = 20;
y = 10;
EXPECT_TRUE((y < x)); // x > y      

x = 10;
y = 20;
EXPECT_TRUE(!(y < x)); // x <= y

x = 10;
y = 10;
EXPECT_TRUE(!(y < x)); // x <= y   
x = 20;
y = 10;
EXPECT_TRUE(!(x < y)); // x >= y

x = 10;
y = 10;
EXPECT_TRUE(!(x < y)); // x >= y 
```

# 형변환 연산자

형변환은 최대한 안하는 것이 좋습니다. 형변환의 자세한 내용은 [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/) 을 참고하세요.

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|C언어 스타일|`(T)a`|O|`operator T() const;`|X|
|[상수성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)만 변환|[const_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|X|X|X|
|타입 유사성을 지키며 변환|[static_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|X|X|X|
|타입 유사성을 지키며 변환. ([Runtime Type Info(RTTI)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#runtime-type-infortti%EC%99%80-%ED%98%95%EB%B3%80%ED%99%98) 지원)|[dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|X|X|X|
|상속관계를 무시하고 변환|[reinterpret_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|X|X|X|

# 접근 연산자

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 요소|`a[b]`|O|`R& T::operator [](S b);`<br/>`const R& T::operator [](S b) const;`|X|
|포인터 실제값|`*a`|O|`R& T::operator *();`<br/>`const R& T::operator *() const;`|`R& operator *(T a);`|
|개체 주소|`&a`|O|`R* T::operator &();`<br/>`const R* T::operator &() const;`|`R* operator &(T a);`|
|개체의 멤버|`a.b`|X|X|X|
|포인터 개체의 멤버|`a->b`|O|`R* T::operator ->();`<br/>`const R* T::operator ->() const;`|X|
|개체 멤버에 대한 포인터|`a.*b`|X|X|X|
|포인터인 개체 멤버에 대한 포인터|`a->*b`|O|`R& T::operator ->*(S b);`<br/>`const R& T::operator ->*(S b) const;`|`R& operator ->*(T a, S b);`|
|범위 확인 연산자(*[네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/) 멤버*)|`namespaceName::member`|X|X|X|
|범위 확인 연산자(*전역 멤버*)|`::member`|X|X|X|
|범위 확인 연산자(*개체의 정적 멤버, [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)*)|`className::member`|X|X|X| 
|범위 확인 연산자(*개체 멤버*)|`className::member`|X|X|X| 

# 함수 호출 연산자

괄호 표현식으로 사용할 수 있는 특수한 표현식입니다. 표준 템플릿 라이브러리(*Standard Template Library, STL*)의 [함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 처럼 사용할 수 있습니다.

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|함수 호출 연산자|`a(a1, a2)`|O|`R T::operator ()(Param1 &a1, Param2 &a2, ...);`|X|

```cpp
class T {
public:
    int operator ()(int a, int b) const {return a + b;}
};

T t;
EXPECT_TRUE(t(10, 20) == 30); // operator() 호출
EXPECT_TRUE(t.operator ()(10, 20) == 30); // t(10, 20) 호출과 동일. operator()를 명시적으로 호출        
```

# 콤마 연산자

`a`표현식을 평가하고, `b`표현식을 평가합니다. (*`a, b, c, d`와 같이 나열할 수 있습니다.*) 코드 분석이 어려워 권장하지 않습니다.

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|콤마 연산자|`a, b`|O|`T2& T::operator ,(T2 &b);`|`T2& operator ,(const T &a, T2 &b);`|

```cpp
class T {
public:
    static int f(int val) {return val;}
};

int arr[] = {1, 2, 3};
int i = 0;

// 1를 증가시키고, v의 i 항목을 f 에 전달합니다.
// (△) 비권장. 분석하기 복잡합니다.
EXPECT_TRUE(T::f((++i, arr[i])) == 2);  
```

# 조건 연산자

`a`가 참인 경우 `b`, 거짓인 경우 `c`를 평가합니다.

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|조건 연산자|`a ? b : c`|X|X|X|

```cpp
int result = true ? 10 : 20;
EXPECT_TRUE(result == 10);
```

# 생성/소멸 연산자

개체 생성시 사용하는 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는 하기 단계를 수행합니다.

1. 전역 [operator new(std::size_t)]((https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98))로 메모리 공간 할당
2. 구조체이거나 클래스이면 [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1) 인 `operator new(void*)`를 실행하여 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 호출
3. 메모리 주소를 해당 타입으로 형변환하여 리턴

즉 [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)는  

* [operator new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98) : C언어의 [malloc()](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)과 유사
* [위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1) : 생성자 호출

로 구성되어 있습니다. 좀더 자세한 내용은 [개체 생성과 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/)을 참고하세요.


|항목|내용|사용예|오버로딩|개체 멤버 정의|전역 정의|
|--|--|--|:--:|:--:|:--:|
|[operator new(std::size_t)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|개체 생성|`T* p = new T;`|O|`void* operator new(std::size_t sz);`|`void* operator new(std::size_t sz);`|
|[operator delete(void*)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98)|개체 소멸|`delete p;`|O|`void operator delete(void* ptr, std::size_t sz)`|`void operator delete(void* ptr, std::size_t sz)`|
|[operator new[](std::size_t)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98-1)|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 생성시 사용|`T* arr = new T[10];`|O|`void* operator new[](std::size_t sz);`|`void* operator new[](std::size_t sz);`|
|[operator delete[](void*)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-new%EC%99%80-operator-delete-%EC%9E%AC%EC%A0%95%EC%9D%98-1)|[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 소멸시 사용|`delete[] arr;`|O|`void operator delete[](void* ptr);`|`void operator delete[](void* ptr);`|
|[operator new(void*)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1)|[위치 지정 생성(Placement New)](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#operator-newptr--placement-new%EC%9C%84%EC%B9%98-%EC%A7%80%EC%A0%95-%EC%83%9D%EC%84%B1). 특정 메모리 위치에 개체 생성자 호출|`T* p = new(buf) T;`|X|X|X|

# sizeof 연산자

개체의 용량을 리턴합니다. 단 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 경우 참조하는 개체와 동일 크기를 리턴하도록 스펙에 정의되어 있습니다.(*`sizeof(T&) == sizeof(T)`, [기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 언급*)

|항목|내용|
|--|--|
|`sizeof(개체명)`|개체의 용량 리턴|
|`sizeof(배열명)`|배열의 전체 용량 리턴|
|`sizeof(타입명)`|타입이나 클래스명, 구조체명, 공용체명의 용량 리턴|
|`sizeof(참조자)`|참조하는 개체와 동일|

```cpp
short i;
int arr[10];
class T {
    int m_Val;
};
struct S {
    int x;
    int y;
};
S s;
S& ref = s; // ref는 s의 참조자

EXPECT_TRUE(sizeof(i) == 2); // short는 2byte
EXPECT_TRUE(sizeof(arr) == sizeof(int) * 10); // 배열은 요소의 전체 크기
EXPECT_TRUE(sizeof(T) = sizeof(int) * 1); // 클래스와 구조체는 멤버 변수들의 합 
EXPECT_TRUE(sizeof(S) = sizeof(int) * 2); 
EXPECT_TRUE(sizeof(s) == sizeof(ref)); // 참조자의 크기는 참조하는 개체의 크기와 동일
```
> *(C++11~) [sizeof...()](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)에서  [파라메터 팩](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/#%ED%8C%8C%EB%9D%BC%EB%A9%94%ED%84%B0-%ED%8C%A9-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%ED%99%95%EC%9E%A5)의 인자수를 구할 수 있습니다.*<br/>
> *(C++11~) [멤버의 `sizeof()`](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EB%A9%A4%EB%B2%84-sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)시 동작이 개선되어 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있습니다.*

# typeid 연산자

개체의 타입 비교를 위해 사용합니다.(*`<typeinfo>`를 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include)해야 합니다.*)

|항목|내용|
|--|--|
|`typeid(개체명)`|개체 `type_info` 리턴|
|`typeid(타입명)`|타입이나 클래스명, 구조체명, 공용체명의 `type_info` 리턴|

`type_info`의 `name()`은 타입의 이름입니다만, 모든 타입에 대해 유일하다고 보장하지는 않습니다.(*컴파일러 제조사 마음입니다.*) 따라서, 개발이나 디버깅시의 힌트를 참고하는 정도로만 사용하시기 바랍니다. 

[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있는 개체인지 아닌지에 따라 동작이 다릅니다.

|항목|내용|
|--|--|
|[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 없는 경우|정의한 개체 타입을 리턴합니다.|
|[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있는 경우|참조하는 개체 타입을 리턴합니다.|



```cpp
// 가상 함수 없음
class Base1 {};
class Derived1 : public Base1 {};

// 가상 함수 있음
class Base2 { 
public:
    virtual void f() {}
};
class Derived2 : public Base2 {};

// Base1과 개체 b1은 이름이 동일합니다.
{
    Base1 b1;
    const std::type_info& ti1 = typeid(Base1);
    const std::type_info& ti2 = typeid(b1);
    EXPECT_TRUE(ti1 == ti2);
    EXPECT_TRUE(ti1.name() == ti2.name());
}
// 가상 함수가 없는 경우의 참조 - 참조 대상이 Derived1이지만, 정의한 Base1 타입으로 변경됨
{
    Derived1 d1;
    Base1& b1Ref = d1; // 가상 함수 없음

    // b1Ref = d1으로 bRef는 Base1 타입이 됨
    EXPECT_TRUE(typeid(b1Ref).name() == typeid(Base1).name());
}
// 가상 함수가 있는 경우의 참조 - 참조 대상인 Derived2 타입으로 유지됨
{
    Derived2 d2;
    Base2& b2Ref = d2; // 가상 함수 있음

    // b2Ref = d2로 b2Ref는 다형적 동작하며, 여전히 Derived2 타입임.(원래 개체의 타입 정보)
    EXPECT_TRUE(typeid(b2Ref).name() == typeid(Derived2).name());   
}
```
# 스트림 연산자

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|출력|`cout << a;`|O|X|`ostream& operator <<(ostream& os, const T& a)`|
|입력|`cin >> a;`|O|X|`istream& operator >>(istream& is, T& a)`|

# 연산자 우선 순위

기본적으로 평가되는 연산자 우선 순위는 하기와 같으며, `()`로 감싸서 우선 순위를 높일 수 있습니다.

|순위|연산자|
|--|--|
|1|`::`(*범위 확인 연산자*)|
|2|`a++`, `a--`, `()`(*함수 호출 연산자*), `a[]`, `.`, `->`|
|3|`++a`, `--a`, `+a`, `-a`, `!`, `~`, `(int)`(*C언어 스타일 형변환*), `a`, `&a`, `sizeof`, [new](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8), [new[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8), [delete](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8), [delete[]](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/#%EB%B0%B0%EC%97%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)|
|4|`.*`, `->*`|
|5|`a * b`, `a / b`, `a %b`|
|6|`a + b`, `a - b`|
|7|`<<`, `>>`|
|8|`<`, `<==`, `>`, `>==`|
|9|`==`, `!=`|
|10|`a & b`|
|11|`a ^ b`|
|12|`a | b`|
|13|`a && b`|
|14|`a || b`|
|15|`a ? b : c`, `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `<<=`, `>>=`, `&=`, `^=`, `|=`|
|16|`,`|

# 연산자 오버로딩

클래스나 구조체에서 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 위해 연산자를 오버로딩할 수 있습니다. 

**복사 대입 연산자**

하기는 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)(`=`) 연산자를 오버로딩한 예입니다. `T&` 와 같이 자기 자신의 참조를 리턴하는데요, 이는 `t1 = t2 = t2;`과 같이 연달아 대입하는 경우를 지원하고, 복사 부하를 줄이기 위함입니다.(*예외에 안전한 방법은 [swap을 이용한 예외 보증 복사 대입 연산자](??)를 참고하세요.*)

```cpp
class T {
private:
    int m_X;
    int m_Y;
public:
    T& operator =(const T& other) {
        this->m_X = other.m_X;
        this->m_Y = other.m_Y;
        return *this; // 자기 자신을 리턴합니다.
    }
}; 
T t1, t2, t3;
t1 = t2 = t3; // t2 = t3의 결과 t2의 참조자를 리턴하고, t1에 대입합니다.
```

**`+=` 연산자**

하기는 `+=` 연산자를 오버로딩한 예입니다. `int`형도 지원하기 위해 `T& operator +=(int val)` 도 구현 하였습니다.

```cpp
class T {
private:
    int m_Val;
public:
    explicit T(int val) : m_Val(val) {}
    int GetVal() const {return m_Val;}

    // 같은 T타입인 경우
    T& operator +=(const T& other) {
        this->m_Val += other.m_Val;
        return *this; // 자기 자신을 리턴합니다.
    }

    // int 형도 지원
    T& operator +=(int val) {
        this->m_Val += val;
        return *this; // 자기 자신을 리턴합니다.        
    }
};

T t1(10), t2(20);
t1 += t2; // operator +=(const T& other) 호출
EXPECT_TRUE(t1.GetVal() == 30); 

t1 += 10;// operator +=(int val) 호출
EXPECT_TRUE(t1.GetVal() == 40); 
```

**`+` 연산자**

하기는 이항 연산자인 `+`연산자를 오버로딩한 예입니다. 

```cpp
class T {
private:
    int m_Val;
public:
    explicit T(int val) : m_Val(val) {}
    int GetVal() const {return m_Val;}

    // 같은 T타입인 경우
    T operator +(const T& other) {
        return T(this->m_Val + other.m_Val); // 새로운 개체를 리턴합니다.
    }

    // int 형도 지원
    T operator +(int val) {
        return T(this->m_Val + val); // 새로운 개체를 리턴합니다.       
    }
};

T t1(10), t2(20);
T t3(0);
t3 = t1 + t2; // operator +(const T& other) 호출
EXPECT_TRUE(t1.GetVal() == 10); 
EXPECT_TRUE(t3.GetVal() == 30); 

t3 = t1 + 10;// operator +(int val) 호출
EXPECT_TRUE(t1.GetVal() == 10); 
EXPECT_TRUE(t3.GetVal() == 20); 
```

**`+` 보다는 `+=`이 좋은 이유**

산술형 대입 연산자가 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 형식(`T&`)를 리턴하는데 반해, 이항 산술 연산의 경우 개체 형식(`T`, 값 타입)을 리턴하는데요, 이는 연산의 결과값을 저장할 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 있어야 하기 때문입니다. 

따라서, [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 생성 부하가 없도록 산술형 대입 연산자를 사용하는 코딩 습관을 가지시는게 좋습니다.

```cpp
T t1(10);
T t2(20);

// (△) 비권장. 임시 개체가 생성됨
T t3 = t1 + t2; // t1 + t2 인 임시 개체를 생성. t3의 복사 생성자 호출하여 임시 개체 대입

// (O) 권장. 임시 개체가 생성되지 않음
T t3 = t1; // t3의 복사 생성자를 호출하여 t1값 대입
t3 += t2; // t3에 t2값 증가
```
**`+` 연산자 비멤버 버전**

`+`연산시 `int`형을 먼저 작성하면 컴파일 되지 않습니다. `t3 = 10 + t1;` 은 사실은 `10.operator +(T other)` 호출하는 것과 같기 때문입니다. `int`형인 `10`에는 해당 함수가 없으므로 컴파일 오류가 납니다.

```cpp
T t1(10);
T t2(0);
T t3(0);

t2 = t1 + 10;// t1.operator +(int val) 호출
t2 = t1.operator +(10); // t2 = t1 + 10과 동일

t3 = 10 + t1;// (X) 컴파일 오류. 10.operator +(T other) 호출합니다. int 형인 10에는 해당 operator 가 없습니다.
EXPECT_TRUE(t2.GetVal() == 20 && t3.GetVal() == 20);
```

이를 허용하려면 비멤버 버전을 만들면 됩니다.

```cpp
// T t = 10 + other; 지원
inline T operator +(int left, const T& right) {
    T result(left + right.GetVal());
    return result;
}
```

**증감 연산자**

증감 연산자는 오버로딩시 전위형과 후위형을 구분하기 위해, 후위형의 경우 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 `int`를 dummy로 넣습니다. 

또한 후위형은 

1. 증감 시키기 전의 값을 리턴하기 위해 현재값을 복제하며,(*후위형은 분석을 헷갈리게 할 뿐 아니라, 복사 부하 까지 있습니다. 이러한 특성 때문에 후위형 보다는 전위형을 쓰시는게 좋습니다.*)
2. `t++++;`처럼 사용할 수 없도록 `const T`를 리턴합니다.

```cpp
class T {
private:
    int m_Val;
public:
    explicit T(int val) : m_Val(val) {}
    int GetVal() const {return m_Val;}

    // 전위형
    T& operator ++() {
        ++m_Val;
        return *this; // 자기 자신을 리턴합니다.
    }
    // 후위형. 인자 int는 전위형과 구분하기 위한 dummy입니다.
    const T operator ++(int) { // t++++가 안되도록 const T를 리턴합니다.
        T result = *this; // 복제합니다.
        ++m_Val; // this의 값을 증가시킵니다.
        return result; // 증가시키기 전에 복제한 값을 리턴합니다.
    }
};

T t(10);
T t1 = ++t;
EXPECT_TRUE(t1.GetVal() == 11); // 증가시킨 후 값
EXPECT_TRUE(t.GetVal() == 11);

T t2 = t++; 
EXPECT_TRUE(t2.GetVal() == 11); // (△) 비권장. 의도한 것인지 조금 헷갈립니다. 증가시킨 전 값. 
EXPECT_TRUE(t.GetVal() == 12);
```

**비교 연산자**

비교 연산자는 일관된 논리 결과값을 구하기 위해 `<` 만 구현하고, `==`, `!=`, `<`, `>`, `<=`, `>=`는 `<`로 부터 구현하는게 좋습니다.(*[대소 비교의 논리 조건](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EB%8C%80%EC%86%8C-%EB%B9%84%EA%B5%90%EC%9D%98-%EB%85%BC%EB%A6%AC-%EC%A1%B0%EA%B1%B4) 참고*)

각각 구현하다보면, `a < b` 와 `b < c` 인데, `c < a` 가 되버리는 논리적 결함이 생길 수도 있거든요.

```cpp
class T {
private:
    int m_Val;
public:
    explicit T(int val) : m_Val(val) {}
    int GetVal() const {return m_Val;}

    bool operator <(const T& other) const {
        return this->m_Val < other.m_Val;
    } 

    bool operator ==(const T& other) const {
        return !(this->m_Val < other.m_Val || other.m_Val < this->m_Val);
    }
    bool operator !=(const T& other) const {
        return !(*this == other);
    }
    bool operator >(const T& other) const {
        return other < *this;
    }
    bool operator <=(const T& other) const {
        return !(other < *this);
    }
    bool operator >=(const T& other) const {
        return !(*this < other);
    }
};

T t1(10);
T t2(10);
EXPECT_TRUE(t1 == t2);

T t1(10);
T t2(20);   
EXPECT_TRUE(t1 != t2);

T t1(10);
T t2(20);  
EXPECT_TRUE(t1 < t2); 

T t1(20);
T t2(10);  
EXPECT_TRUE(t1 > t2);  

T t1(10);
T t2(20); 
EXPECT_TRUE(t1 <= t2);

T t1(10);
T t2(10);
EXPECT_TRUE(t1 <= t2);

T t1(20);
T t2(10); 
EXPECT_TRUE(t1 >= t2);

T t1(10);
T t2(10); 
EXPECT_TRUE(t1 >= t2);   
```

**열거형 연산자 오버로딩**

[열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)도 연산자 오버로딩을 할 수 있습니다.

```cpp

class Week {
public:
    enum Val {
        Sunday, Monday, Tuesday, Wednesday, 
        Thursday, Friday, Saturday
    };
};
// 전위 증가. 
Week::Val& operator ++(Week::Val& d) { 
    return d = (Week::Saturday == d) ? Week::Sunday : static_cast<Week::Val>(d + 1);
}  

Week::Val val = Week::Saturday;

EXPECT_TRUE(++val == Week::Sunday); // 토요일 에서 1 증가하면, 제일 처음 값인 일요일로 순회됨
EXPECT_TRUE(++val == Week::Monday); // 일요일 에서 1 증가하여 월요일
```
