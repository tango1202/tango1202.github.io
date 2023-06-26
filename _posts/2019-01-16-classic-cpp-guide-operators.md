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

의도한 코드일 수는 있으나, 분석을 헷갈리게 할 수 있기 때문에, 후위 보다는 전위 증감 연산자를 사용하는게 좋습니다.

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

형변환의 자세한 내용은 [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/) 을 참고하세요.

**접근 연산자**

|항목|내용|오버로딩| 개체 멤버 정의| 개체 비멤버 정의|
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






함수 호출
괄호

생성/소멸 연산자
new() type : 메모리 지정

throw

sizeof 연산자

typeid 연산자

콤마 연산자

,(쉼표) : f((++i, v[i]))-> i를 증가시키고
v의 i 항목을 f에 전달


연산자 우선순위

연산자 오버로딩

오버로딩시 `T&`와 같이 자기자신의 참조를 리턴하는데요, 이는 `a = b = c;` 와 같이 사용할 때 필요합니다. `b = c;`의 결과 `b`의 참조자를 리턴하고, 이게 `a = b`를 통해 `a`에 대입됩니다.



할당연산자는 레퍼런스, 산술연산자는 복제본. 할당 연산자가 메모리 절약

비멤버 버전 오버로딩

후위 증감의 더미 int, 복제본


