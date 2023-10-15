---
layout: single
title: "#14. [고전 C++ 가이드] 초기화"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 생성하면서 초기화 하라.
> * 초기화되지 않은 변수를 사용하지 마라.
> * 자동 제로 초기화를 활용하지 마라. 나중에 낭패 본다.

> **모던 C++**
> * (C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)가 추가되어 클래스, 배열, 구조체를 일관성 있게 초기화 할 수 있습니다.
> * (C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)들이 표준화 되었습니다.

# 개요

초기화에는 다음과 같은 방법들이 있습니다.

|항목|내용|
|--|--|
|기본 초기화|`T obj;`|
|값 초기화|`T obj();`|
|복사 초기화|`T obj = other;` 또는<br/>`T obj(other);`|
|생성 후 대입(불필요한 부하)|`T obj;`<br/>`obj = other;`|
|배열 초기화|`T arr[] = {};`,<br/> `char str[] = "abc";`|
|구조체 초기화|`struct T {`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`int x;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`int y;`<br/>`};`<br/>`T t = {0, 10};`|

생성 후 대입하는 건, 생성과 대입의 2개 과정을 거쳐서 낭비입니다. 또한 **예외 안전** 프로그래밍에도 좋지 않습니다. 생성 후 대입 과정에서 예외가 발생하면 난감해지니까요.([swap을 이용한 예외 안전 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)

또한, 개체 생성시 초기화를 하지 않으면, 초기화되지 않은 개체를 사용하는 실수를 할 수 있습니다. 그러니 항상 생성과 동시에 초기화 하는 것이 좋습니다.

다음은 초기화 테스트용 클래스입니다. [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), `int`를 전달받는 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)(형변환이 되지 않게 `explicit`로 정의. [명시적 변환 생성 지정자(explicit)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit) 참고), [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/)를 정의하였습니다.

```cpp
class T {
public:
    T() {} // 기본 생성자
    explicit T(int t) {} // int를 전달받는 생성자. explicit를 주어 암시적 형변환을 막음
    T(const T& other) {} // 복사 생성자

    void operator =(const T& other) {} // 복사 대입 연산자
};
```

# 기본 초기화

`T obj;` 와 같이 생성하면, [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출되어 기본 초기화를 수행합니다.

```cpp
T obj; // (O) 기본 생성자 호출
```

`T obj1();`와 같이 괄호로 정의하면, 개체 생성이 아니라 T를 리턴하는 `obj1()` 함수 선언으로 인식됩니다. 

```cpp
T obj1(); // (△) 비권장. 초기화 아님. T를 리턴하는 obj1 함수 선언임
```

> *(C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 `T obj{};`와 같이 기본 생성자를 호출할 수 있습니다.*


# 값 초기화

값 초기화는 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)를 이용하여 특정한 값으로 생성할 때 사용합니다. 

```cpp
T obj2(1); // (O) int를 전달받는 생성자 호출
```

또한, `T val(other);` 나 `T val = other;` 를 사용할 수 있는데요, 이 둘은 완전히 동일한 표현입니다.

```cpp
T val(other);
T val = other; // T val(other); 와 동일합니다.
```

따라서, 하기는 `T(2);`로 개체 생성 후 `obj3`에 대입하는 것 같은 모양입니다만, `T obj3(T(2));`와 동일합니다.

즉, `T(2)`로 생성한 임시 개체를 복사 생성하는 코드입니다. 하지만 컴파일러 최적화에 따라 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90) 로 생성한 개체를 그냥 `obj3`로 사용하고, 복사 생성자는 호출하지 않을 수 있습니다. 상황에 따라 최적화가 안되면 비효율적인 코드이니 사용 안하시는게 좋습니다.(그냥 `T obj3(2);`로 사용하세요.)

```cpp
// int를 전달받는 생성자 호출.
// (△) 비권장. T(2) 로 개체 생성후 obj3의 복사 생성자 호출. 컴파일러 최적화로 복사 생성자는 호출되지 않을 수 있음. 컴파일러 최적화가 안될 수도 있으니 T obj3(2); 로 호출하는게 더 좋음.
T obj3 = T(2); // T obj3(T(2)); 와 동일
```

> *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)들이 표준화 되었습니다.*

# 복사 초기화

복사 초기화는 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 인자가 1개인 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출해 줍니다. 인자가 1개인 생성자는 뜻하지 않게 형변환이 될 수 있으므로 `explicit`로 정의하는게 좋습니다.
([명시적 변환 생성 지정자(`explicit`)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit) 참고)

```cpp
T other;

T obj1 = 1; // (X) 컴파일 오류. explicit 가 아니라면 int를 전달받는 값 생성자가 호출됨
T obj1 = other; // (O) 타입이 같다면 복사 생성자가 호출됨
T obj2(other); // (O) 명시적으로 복사 생성자 호출됨
```

# 생성 후 대입 : 하지 마라.

생성 후 대입을 할 수 있습니다만, 서두에 말씀드렸듯이 하지 마십시요.

```cpp
T other;

T obj; // 기본 생성자
obj = other; // (△) 비권장. 생성하고 대입하지 말고, 완전하게 생성하세요. T obj(other); 가 낫습니다.
```

# 배열 초기화

배열 정의시 배열의 크기가 유추될 수 있어야 하며, [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 초기화 할 수 있습니다.([배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 참고) 

```cpp
int arr1[3]; // (△) 비권장. int 형 3개 정의. 초기화 되지 않아 비권장 
// int arr1[]; // (X) 컴파일 오류. 갯수가 지정되지 않음. 오류
// int arr1[] = {}; // (X) 컴파일 오류. 갯수가 지정되지 않음. 오류
int arr2[] = {0, 1, 2}; // (O) 갯수만큼 초기화
int arr3[3] = {}; // (O) 3개 모두 0으로 초기화
int arr4[3] = {0, 1, }; // (O) 갯수가 적거나 같아야 함. 모자라면 0

EXPECT_TRUE(arr2[2] == 2);
EXPECT_TRUE(arr3[0] == 0 && arr3[1] == 0 && arr3[2] == 0);
EXPECT_TRUE(arr4[2] == 0);

char str1[] = "abc"; // (O) {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str1[0] == 'a');
EXPECT_TRUE(str1[1] == 'b');
EXPECT_TRUE(str1[2] == 'c');
EXPECT_TRUE(str1[3] == '\0'); // 널문자가 추가됨

wchar_t str2[] = L"abc"; // (O) {L'a', L`b`, L'c', L'\0'};
EXPECT_TRUE(str2[0] == L'a');
EXPECT_TRUE(str2[1] == L'b');
EXPECT_TRUE(str2[2] == L'c');
EXPECT_TRUE(str2[3] == L'\0'); // 널문자가 추가됨
```

# 구조체 초기화

구조체는 별도로 값 초기화를 위한 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)를 구현하지 않더라도 중괄호로 초기화를 할 수 있습니다.

```cpp
struct T {int x; int y;}; 
T t = {10, 20}; // (O) 중괄호로 초기화

EXPECT_TRUE(t.x == 10 && t.y == 20);
```

# 자동 제로 초기화

대부분의 변수들은 생성시 초기화하지 않으면 기존 메모리에 있는 쓰레기 값을 갖게 되지만, 하기의 경우는 메모리 영역을 제로(`0`)로 만들어 초기화 합니다.

1. [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98), [정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)
   
2. 배열의 요소 갯수보다 초기화 값을 적게 제공한 경우 나머지 요소

3. 클래스, 구조체, 공용체의 멤버 변수

    * `T t;`나 `T* ptr = new T;`와 같이 괄호 없이 기본 생성자를 호출하면 자동 제로 초기화를 하지 않습니다.
    * `T* ptr = new T();` 와 같이 괄호를 사용하여 기본 생성자를 호출하면 자동 제로 초기화를 합니다. 
 
```cpp
T t;
T* ptr1 = new T; // 괄호 없이 생성합니다. 자동 초기화가 안됩니다.
T* ptr2 = new T(); // 괄호로 생성합니다. 자동 초기화가 됩니다.

EXPECT_TRUE(g_Val == 0); // 전역 변수는 0으로 자동 초기화
EXPECT_TRUE(s_Val == 0); // 정적 전역 변수는 0으로 자동 초기화
EXPECT_TRUE(T::s_m_Val == 0); // 정적 멤버 변수는 0으로 자동 초기화
EXPECT_TRUE(T::s_c_m_Val == 0); // 정적 상수 멤버 변수는 명시적 초기화
EXPECT_TRUE(ptr1->m_Val == 0 || ptr1->m_Val != 0); // new T; 로 생성하면 자동 초기화 되지 않습니다.
EXPECT_TRUE(ptr2->m_Val == 0); // new T(); 로 생성하면 자동 초기화됩니다.
EXPECT_TRUE(t.f1() == 0); // 정적 지역 변수는 0으로 자동 초기화
// EXPECT_TRUE(t.f2() != 0); // 지역 변수는 쓰레기값이 될 수도 있음

int arr[3] = {1, }; 
EXPECT_TRUE(arr[0] == 1 && arr[1] == 0 && arr[2] == 0); // 배열 갯수 보다 초기화 갯수가 적을때 나머지 요소는 0으로 자동 초기화

delete ptr1;
delete ptr2;
```

복잡하죠? 복잡하니, 모든 변수를 초기화 한다는 대원칙을 가지고 작성하세요. 개발 초기에서 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 없어서 자동 제로 초기화를 활용해서 개발했다가, 누군가 나중에 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 넣어버리면 낭패이고, 디버그 모드와 릴리즈 모드가 결과가 달라도 낭패이니까요.

|항목|자동 제로 초기화 지원|선언에서 명시적 초기화 지원|권장 초기화 방법|
|--|:--:|:--:|--|
|전역 변수|O|O|선언시 초기화|
|정적 전역 변수|O|O|선언시 초기화|
|정적 멤버 변수|O|X|사용 비권장. 정적 지역 변수 사용|
|정적 상수 멤버 변수|X|O|선언시 초기화|
|개체의 멤버 변수|△|X|[생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/). (GCC 디버그 모드에서는 `0`이 아니어서 △로 표기했습니다. 신뢰할 수 없으니 명시적으로 초기화 하세요.)|
|정적 지역 변수|O|O|선언시 초기화|
|지역 변수|X|O|선언시 초기화|

