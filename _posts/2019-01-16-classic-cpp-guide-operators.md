---
layout: single
title: "#16. [고전 C++ 가이드] 연산자들(Operators)과 연산자 오버로딩"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 임시 개체가 생성되지 않도록 이항 산술 연산자(`a = a + b`)보다는 할당 연산자(`a += b`)를 사용하라.
> * 쓸데없는 임시 개체가 생성되고 헷갈리니, 후위형 증감 연산자는 사용하지 마라. 


**할당 연산자**

할당 연산자는 `=` 와 같은 기본 할당 연산자와 산술 연산이나 비트 연산의 결과값을 할당하는 연산자가 있습니다.(`a += b` 는 `a = a + b` 와 결과가 같습니다.) 


|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|할당|`a = b`|O|`T& T::operator =(const T2& b);`|X|
|추가 할당|`a += b`|O|`T& T::operator +=(const T2& b);`|`T& operator +=(T& a, const T2& b);`|
|빼기 할당|`a -= b`|O|`T& T::operator -=(const T2& b);`|`T& operator -=(T& a, const T2& b);`|
|곱셈 할당|`a *= b`|O|`T& T::operator *=(const T2& b);`|`T& operator *=(T& a, const T2& b);`|
|나누기 할당|`a /= b`|O|`T& T::operator /=(const T2& b);`|`T& operator /=(T& a, const T2& b);`|
|나머지 할당|`a %= b`|O|`T& T::operator %=(const T2& b);`|`T& operator %=(T& a, const T2& b);`|
|비트 AND 할당|`a &= b`|O|`T& T::operator &=(const T2& b);`|`T& operator &=(T& a, const T2& b);`|
|비트 OR 할당|`a |= b`|O|`T& T::operator |=(const T2& b);`|`T& operator |=(T& a, const T2& b);`|
|비트 XOR 할당|`a ^= b`|O|`T& T::operator ^=(const T2& b);`|`T& operator ^=(T& a, const T2& b);`|
|비트 Left Shift 할당|`a <<= b`|O|`T& T::operator <<=(const T2& b);`|`T& operator <<=(T& a, const T2& b);`|
|비트 Right Shift 할당|`a >>= b`|O|`T& T::operator >>=(const T2& b);`|`T& operator >>=(T& a, const T2& b);`|


**산술 연산자**

산술 연산의 결과를 리턴합니다. 

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|단항 양수|`+a`|O|`T T::operator +() const;`|`T operator +(const T &a);`|
|단항 음수|`-a`|O|`T T::operator -() const;`|`T operator -(const T &a);`|
|더하기|`a + b`|O|`T T::operator +(const T2 &b) const;`|`T operator +(const T &a, const T2 &b);`|
|빼기|`a - b`|O|`T T::operator -(const T2 &b) const;`|`T operator -(const T &a, const T2 &b);`|
|곱하기|`a * b`|O|`T T::operator *(const T2 &b) const;`|`T operator *(const T &a, const T2 &b);`|
|나누기|`a / b`|`T T::operator /(const T2 &b) const;`|`T operator /(const T &a, const T2 &b);`|
|나머지|`a % b`|`T T::operator %(const T2 &b) const;`|`T operator %(const T &a, const T2 &b);`|
|비트 NOT|`~a`|O|`T T::operator ~() const;`|`T operator ~(const T &a);`|
|비트 AND|`a & b`|O|`T T::operator &(const T2 &b) const;`|`T operator &(const T &a, const T2 &b);`|
|비트 OR|`a | b`|O|`T T::operator |(const T2 &b) const;`|`T operator |(const T &a, const T2 &b);`|
|비트 XOR|`a ^ b`|O|`T T::operator ^(const T2 &b) const;`|`T operator ^(const T &a, const T2 &b);`|
|비트 Left Shift|`a << b`|O|`T T::operator <<(const T2 &b) const;`|`T operator <<(const T &a, const T2 &b);`|
|비트 Right Shift|`a >> b`|O|`T T::operator >>(const T2 &b) const;`|`T operator >>(const T &a, const T2 &b);`|

이항 산술 연산의 경우 연산의 결과값을 리턴해야 하기 때문에 임시 개체를 생성합니다. 따라서, 값 복제가 없도록 할당 연산자를 사용하는게 좋습니다.

```cpp
T a;
T b;

// (△) 비권장. 임시 개체가 생성됨
T c = a + b; // a + b 인 임시 개체를 생성. c의 복사 생성자 호출하여 임시 개체 대입

// (O) 권장. 임시 개체가 생성되지 않음
T d = a; // d의 복사 생성자를 호출하여 a값 대입
d += b; // d에 b값 증가
```

**증감 연산자**

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|전위 증가|`++a`|O|`T& T::operator ++();`|`T& operator ++(T& a);`|
|전위 감소|`--a`|O|`T& T::operator --();`|`T& operator --(T& a);`|
|후위 증가|`a++`|O|`T T::operator ++(int);`|`T operator ++(T& a, int);`|
|후위 감소|`a--`|O|`T T::operator --(int);`|`T operator --(T& a, int);`|

전위 연산의 경우 값을 먼저 증감시킨뒤 리턴합니다만, 후위 연산의 경우는 현재 값을 리턴한 후 값을 증감 시켜야 합니다. 이에 따라 내부적으로 증감시키기 전의 값을 복제한 임시 개체를 생성합니다. 따라서, 임시 개체가 생성되지 않도록 전위 증감 연산자를 사용하는게 좋습니다.

```cpp
T a;

// (△) 비권장. 임시 개체가 생성됨
a++; // a를 증가시키기 전의 값을 임시 개체를 만들어 리턴함. 하지만 받아서 쓰는 곳도 없음. 임시 개체 생성부하만 있음

// (O) 권장. 임시 개체가 생성되지 않음
a++; // a값을 증가시키고 참조자를 리턴함. 
```

또한, 의도한 코드일 수는 있으나, 분석을 헷갈리게 할 수 있기 때문에, 후위 보다는 전위 증감 연산자를 사용하는게 좋습니다.

```cpp
int n = 10;
int a = ++n;
EXPECT_TRUE(a == 11); // 증가시킨 후 값
EXPECT_TRUE(n == 11);

int b = n++;
EXPECT_TRUE(b == 11); // (△) 비권장. 의도한 것인지 조금 헷갈립니다. 증가시킨 전 값. 
EXPECT_TRUE(n == 12);
```

**논리 연산자**

NOT, AND, OR 논리 조건에 맞춰 `true`, `false`를 계산합니다.


|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|NOT|`!a`|O|`bool T::operator !() const;`|`bool operator !(const T &a);`|
|AND|`a && b`|O|`bool T::operator &&(const T2 &b) const;`|`bool operator &&(const T &a, const T2 &b);`|
|OR|`a || b`|O|`bool T::operator ||(const T2 &b) const;`|`bool operator ||(const T &a, const T2 &b);`|

**비교 연산자**

대소 비교를 합니다.

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|같다|`a == b`|O|`bool T::operator ==(const T2& b) const;`|`bool operator ==(const T& a, const T2& b);`|
|같지 않다.|`a != b`|O|`bool T::operator !=(const T2& b) const;`|`bool operator !=(const T& a, const T2& b);`|
|미만|`a < b`|O|`bool T::operator <(const T2& b) const;`|`bool operator <(const T& a, const T2& b);`|
|큰`a > b`|O|`bool T::operator >(const T2& b) const;`|`bool operator >(const T& a, const T2& b);`|
|이하|`a <= b`|O|`bool T::operator <=(const T2& b) const;`|`bool operator <=(const T& a, const T2& b);`|
|이상|`a >= b`|O|`bool T::operator >=(const T2& b) const;`|`bool operator >=(const T& a, const T2& b);`|

대소 비교는 하기 논리 조건을 만족해야 합니다.

1. `x < x` 는 거짓
2. `x < y` 이고 `y < z` 이면, `x < z` 
3. `!(x < y)` 은 `(y <= x)` 의 의미
4. `x < y` 도 아니고 `y < x` 도 아니면 `x` 는 `y` 와 동등함(equivalence) (동등관계) 
5. `x equivalence y` 이고, `y equivalence z` 이면 `x equivalence z`

상기 논리 조건이 모두 만족한다면, 비교 연산은 하기와 같이 `<` 로 모두 표현할 수 있습니다.

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

**형변환 연산자**

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|C언어 스타일|`(T)a`|O|`operator T() const;`|X|
|상수성만 변환|`const_cast`|X|X|X|
|타입 유사성을 지키며 변환|`static_cast`|X|X|X|
|타입 유사성을 지키며 변환. (RTTI(Runtime Type Info) 지원)|`dynamic_cast`|X|X|X|
|상속관계를 무시하고 변환|`reinterpret_cast`|X|X|X|

형변환 연산자의 자세한 내용은 [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/) 을 참고하세요.

**접근 연산자**

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|배열 항목|`a[b]`|O|`R& T::operator [](S b);`|X|
|포인터 실제값|`*a`|O|`R& T::operator *();`|`R& operator*(T a);`|
|개체 주소|`&a`|O|`R* T::operator &();`|`R* operator&(T a);`|
|개체의 멤버|`a.b`|X|X|X|
|포인터 개체의 멤버|`a->b`|O|`R* T::operator ->();`|X|
|개체 멤버에 대한 포인터|`a.*b`|X|X|X|
|포인터인 개체 멤버에 대한 포인터|`a->*b`|O|`R& T::operator ->*(S b);`|`R& operator ->*(T a, S b);`|
|네임스페이스 멤버|`namespaceName::member`|X|X|X|
|전역 멤버|`::member`|X|X|X|
|개체의 static 멤버|`className::member`|X|X|X| 
|개체 하위 정의 요소|`className::member`|X|X|X| 

**함수 호출 연산자**

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|함수 호출 연산자|`a(a1, a2)`|O|`R T::operator ()(Arg1 &a1, Arg2 &a2, ...);`|X|

괄호 표현식으로 사용할 수 있는 특수한 표현식입니다. 표준 템플릿 라이브러리(Standard Template Library, STL)의 함수자 처럼 사용할 수 있습니다.([함수자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-functor/) 참고)

```cpp
class T {
public:
    int operator ()(int a, int b) const { return a + b; }
};

T t;
EXPECT_TRUE(t(10, 20) == 30); // operator() 호출
EXPECT_TRUE(t.operator ()(10, 20) == 30); // operator()를 명시적으로 호출        
```

**콤마 연산자**

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|콤마 연산자|`a, b`|O|`T2& T::operator ,(T2 &b);`|`T2& operator ,(const T &a, T2 &b);`|

`a`표현식을 평가하고, `b`표현식을 평가합니다. (`a, b, c, d`와 같이 나열할 수 있습니다.)

```cpp
// 1를 증가시키고, v의 i 항목을 f 에 전달합니다.
// (△) 비권장. 분석하기 복잡합니다.
f((++i, v[i])); 
```

**조건 연산자**

|항목|내용|오버로딩|개체 멤버 정의|개체 비멤버 정의|
|--|--|:--:|:--:|:--:|
|조건 연산자|`a ? b : c`|X|X|X|

`a`가 참인 경우 `b`, 거짓인 경우 `c`를 평가합니다.

```cpp
int result = true ? 10 : 20;
EXPECT_TRUE(result == 10);
```

**생성/소멸 연산자**

개체 생성시 사용하는 `new`는 하기 단계를 수행합니다.

1. 전역 `operator new`를 이용하여 메모리 공간 할당
2. 구조체이거나 클래스이면 생성자 호출
3. 메모리 주소를 해당 타입으로 캐스팅하여 리턴

즉 `new`와 `operator new`의 역할은 다릅니다. `operator new`는 C언어의 `malloc`과 유사하다고 할 수 있습니다.


|항목|내용|사용예|오버로딩|개체 멤버 정의|전역 정의|
|--|--|--|:--:|:--:|:--:|
|`operator new`|개체 생성|`T* p = new T;`|O|`void* operator new(std::size_t sz);`|`void* operator new(std::size_t sz);`|
|`operator delete`|개체 소멸|`delete p;`|O|`void operator delete(void* ptr, std::size_t size)`|`void operator delete(void* ptr, std::size_t size)`|
|`operator new[]`|배열 생성시 사용|`T* arr = new T[10];`|O|`void* operator new[](std::size_t sz);`|`void* operator new[](std::size_t sz);`|
|`operator delete[]`|배열 소멸시 사용|`delete[] arr;`|O|`void operator delete[](void* ptr);`|`void operator delete[](void* ptr);`|
|`new()`|위치 지정 생성(특정 메모리 위치에 개체 생성자 호출)|`T* p = new(buf) T;`|X|X|X|

`operator new`와 `operator delete`와 위치 지정 생성(Placement New)에 대해서는 [생성과 소멸](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-new-delete/) 참고하세요.

**sizeof 연산자**

|항목|내용|
|--|--|
|`sizeof(개체명)`|개체의 용량 리턴|
|`sizeof(타입명)`|자료형이나 클래스명, 구조체명, 공용체명의 용량 리턴|

개체의 용량을 리턴합니다. 단 참조자의 경우 참조하는 개체와 동일 크기를 리턴하도록 스펙에 정의되어 있습니다.(`sizeof(T&) == sizeof(T)`, [기본 자료형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 언급)

```cpp
short i;
int arr[10];
class T {
    int i;
};
struct S {
    int x;
    int y;
};
S s;
S& ref = s; // ref는 s의 참조자

EXPECT_TRUE(sizeof(i) == 2); // short는 2byte
EXPECT_TRUE(sizeof(arr) == sizeof(int) * 10); // 배열은 요소의 전체 크기
EXPECT_TRUE(sizeof(T) = sizeof(int) * 1); // 클래스와 요소는 멤버 변수의 합 
EXPECT_TRUE(sizeof(S) = sizeof(int) * 2); 
EXPECT_TRUE(sizeof(s) == sizeof(ref)); // 참조자의 크기는 참조하는 개체의 크기와 동일
```

**typeid 연산자**

개체의 타입 비교를 위해 사용합니다.(`<typeinfo>`를 `include`해야 합니다.)

|항목|내용|
|--|--|
|`typeid(개체명)`|개체 타입 정보 리턴|
|`typeid(타입명)`|자료형이나 클래스명, 구조체명, 공용체명의 타입 정보 리턴|

하기 예에서 처럼 RTTI(Runtime Type Info)가 있는 개체(가상 함수가 있는 개체)는 참조하는 개체의 원래 타입 정보를 리턴합니다.

```cpp
#include <typeinfo> 
// 가상 함수 없음
class Base {};
class Derived : public Base {};

// 가상 함수 있음
class Base2 { 
public:
    virtual void f() {}
};
class Derived2 : public Base2 {};

Base b;
const std::type_info& b1 = typeid(Base);
const std::type_info& b2 = typeid(b);
EXPECT_TRUE(b1 == b2);
EXPECT_TRUE(b1.hash_code() == b2.hash_code());

Derived d;
Base& bRef = d; // 가상 함수 없음

// bRef = d로 bRef는 Base 타입이 됨
EXPECT_TRUE(typeid(bRef).hash_code() == typeid(b).hash_code());

Derived2 d2;
Base2& b2Ref = d2; // 가상 함수 있음

// b2Ref = d2로 b2Ref는 다형적 동작하며, 여전히 d2 타입임.(원래 개체의 타입 정보)
EXPECT_TRUE(typeid(b2Ref).hash_code() == typeid(d2).hash_code());  
```

**연산자 우선순위**

기본적으로 평가되는 연산자 우선 순위는 하기와 같으며, `()`로 감싸서 우선순위를 높일 수 있습니다.

|순위|연산자|
|--|--|
|1|::|
|2|`a++`, `a--`, `()`(함수 호출 연산자), `a[]`, `.`, `->`|
|3|`++a`, `--a`, `+a`, `-a`, `!`, `~`, `(int)`(C언어 스타일 형변환), `a`, `&a`, `sizeof`, `new`, `new[]`, `delete`, `delete[]`|
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

**연산자 오버로딩**

오버로딩시 `T&`와 같이 자기자신의 참조를 리턴하는데요, 이는 `a = b = c;` 와 같이 사용할 때 필요합니다. `b = c;`의 결과 `b`의 참조자를 리턴하고, 이게 `a = b`를 통해 `a`에 대입됩니다.



할당연산자는 레퍼런스, 산술연산자는 복제본. 할당 연산자가 메모리 절약

비멤버 버전 오버로딩

후위 증감의 더미 int, 복제본


