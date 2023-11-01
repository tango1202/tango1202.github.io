---
layout: single
title: "#14. [고전 C++ 가이드] 초기화"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 생성하면서 [초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/) 하라.
> * [초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/)되지 않은 변수를 사용하지 마라.
> * [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)에 의존하지 마라. 낭패볼 수 있다.

> **모던 C++**
> * (C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)가 추가되어 클래스, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/), 구조체를 일관성 있게 초기화 할 수 있습니다.
> * (C++11~) 비정적 멤버 변수도 [멤버 선언부에서 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/)를 할 수 있어 초기화 작성이 쉬워졌습니다.
> * (C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.
> * (C++17~) [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, [클래스 정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 정의 및 초기화가 쉬워졌습니다.

# 개요

[생성 후 대입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1-%ED%9B%84-%EB%8C%80%EC%9E%85--%ED%95%98%EC%A7%80-%EB%A7%88%EB%9D%BC)하는 건, 생성과 대입의 2개 과정을 거쳐서 낭비입니다. 또한 [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/)에도 좋지 않습니다. [생성 후 대입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1-%ED%9B%84-%EB%8C%80%EC%9E%85--%ED%95%98%EC%A7%80-%EB%A7%88%EB%9D%BC) 과정에서 예외가 발생하면 난감해지니까요.(*[swap을 이용한 예외 보증 복사 대입 연산자](??) 참고*)

또한, 개체 생성시 초기화를 하지 않으면, 초기화되지 않은 개체를 사용하는 실수를 할 수 있습니다. 그러니 ***항상 생성과 동시에 초기화*** 하는 것이 좋습니다.

하지만 초기화 방법은 다른 언어에 비해 난해합니다. 

`T obj = val;`과 같은 직관적이고 단순한 표현이, 조금 파보면 숨은 의미들이 생겨납니다.(*사실 이런게 C++의 재미죠.*) 

* 위치가 전역인지, 정적인지, 멤버인지, 지역인지에 따라 불법이 될 수도 있고 아닐 수도 있습니다.(*[정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98), 개체의 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)는 선언과 동시에 초기화하는게 불법입니다.*) 

    > *(C++11~) 비정적 멤버 변수도 [멤버 선언부에서 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/)를 할 수 있어 초기화 작성이 쉬워졌습니다.*<br/>
    > *(C++17~) [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, [클래스 정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 정의 및 초기화가 쉬워졌습니다.*
    
* 또한 클래스나 구조체 세계로 가면, [형변환으로 해석](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94)될 수도 있고, [함수 선언으로 해석](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98)될 수도 있고, 비효율적일 수도 있고, [비효율적이라 생각했는데 컴파일러가 알아서 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94) 해줄 수도 있습니다. 

* 구조체 정의를 어떻게 했으냐에 따라 [중괄호 집합 초기화를 사용할 수도, 사용하지 못할 수도](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B5%AC%EC%A1%B0%EC%B2%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 있습니다.

* 위치가 전역인지, 정적인지, 멤버인지, 지역인지와 생성자를 어떻게 호출했는지에 따라 [자동 제로 초기화가 될수도, 안될 수도](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94) 있습니다.

초기화에는 다음과 같은 방법들이 있습니다.

|항목|내용|
|--|--|
|[기본 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B8%B0%EB%B3%B8-%EC%B4%88%EA%B8%B0%ED%99%94)|`T obj;`|
|[값 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94)|`T obj();`|
|[복사 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94)|`T obj = other;` 또는<br/>`T obj(other);`|
|[생성 후 대입(*불필요한 부하*)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1-%ED%9B%84-%EB%8C%80%EC%9E%85--%ED%95%98%EC%A7%80-%EB%A7%88%EB%9D%BC)|`T obj;`<br/>`obj = other;`|
|[배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94)|`T arr[] = {};`,<br/> `char str[] = "abc";`|
|[구조체 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B5%AC%EC%A1%B0%EC%B2%B4-%EC%B4%88%EA%B8%B0%ED%99%94)|`struct T {`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`int x;`<br/>&nbsp; &nbsp; &nbsp; &nbsp;`int y;`<br/>`};`<br/>`T t = {0, 10};`|

다음은 초기화 테스트용 클래스입니다. [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), `int`를 전달받는 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)(*형변환이 되지 않게 [explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)로 정의했습니다. [명시적 변환 생성 지정자(explicit)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit) 참고하세요.*), [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 정의하였습니다.

```cpp
class T {
public:
    T() {} // 기본 생성자
    explicit T(int t) {} // int를 전달받는 생성자. explicit를 주어 암시적 형변환을 막음
    T(int x, int y) {} // 값 2개를 전달받는 생성자
    T(const T& other) {} // 복사 생성자

    void operator =(const T& other) {} // 복사 대입 연산자
};
```

# 기본 초기화

`T obj;` 와 같이 생성하면, [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출되어 [기본 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B8%B0%EB%B3%B8-%EC%B4%88%EA%B8%B0%ED%99%94)를 수행합니다.

```cpp
T obj; // (O) 기본 생성자 호출
```

`T obj();`와 같이 괄호로 정의하면, 개체 생성이 아니라 `T`를 리턴하는 `obj()` 함수 선언으로 인식되니 주의하세요. (*[초기화 파싱 오류](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98) 참고*)

```cpp
T obj(); // (△) 비권장. 초기화 아님. T를 리턴하는 obj 함수 선언임
```

> *(C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 `T obj{};`와 같이 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출할 수 있습니다.*

# 값 초기화

값 초기화는 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)를 이용하여 특정한 값으로 생성할 때 사용합니다. 

```cpp
T obj(1); // (O) int를 전달받는 생성자 호출
T obj(1, 2); // (O) 값 2개를 전달받는 생성자 호출
```

특별히 값이 1개이면, [형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)라고 하는데요, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)(`=`) 형태로 표현할 수 있습니다. 하지만 이게 프로그래머의 의도인지, 실수인지 헷갈릴때가 있습니다.

```cpp
T obj = 1; // (△) 비권장. T t(int x); 를 호출을 위한 것인지, 정수 1을 T에 잘못 대입한 것인지 헷갈립니다.
```

그래서 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)(`=`) 형태로 사용하는 것은 형변환이 되지 않도록 [explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)를 사용하여 막아주는게 좋습니다. 컴파일 오류로 알려 주거든요.

대신 `T obj = T(1);`와 같이 명시적으로 생성한 개체를 대입하는 형태로 표현할 수도 있는데요, 이 방식은 `T(1)`로 생성한 개체를 `T obj` 의 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성하는 구문이어서 생성자를 2회 호출합니다. 개체 1개를 생성하기 위해 생성자를 2회 호출하니 비효율적이죠.(*사실 컴파일러 최적화에 의해 생성자를 1회 호출하기는 합니다. [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94) 참고*)

```cpp
T obj = T(1); // (△) 비권장. T(1)로 생성한 개체를 T obj 의 복사 생성자를 호출하여 생성합니다.
```

그리고, `T obj(T(1));`와 같이 명시적으로 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)와 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하는 표현이 있을 수 있습니다. 이 또한 `T obj = T(1);` 와 동일하게 생성자가 2회 호출하므로 비효율적입니다.(*사실 컴파일러 최적화에 의해 생성자를 1회 호출하기는 합니다. [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94) 참고*)

```cpp
T obj(T(1)); // (△) 비권장. T(1)로 생성한 개체를 T obj 의 복사 생성자를 호출하여 생성합니다.
```

상기와 여러가지 가능한 표현 방식이 있습니다만, 다음과 같이 생성자를 직접 호출하는게 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)에 안전하고 생성자를 1회만 호출하므로 효율적입니다.(*자꾸 쓰다보면 가독성도 좋아지더라구요.*)

```cpp
T obj(1); // (O) int를 전달받는 생성자 호출
T obj(1, 2); // (O) 값 2개를 전달받는 생성자 호출
```

> *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.*

# 복사 초기화

[값 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94)에서 특별히 같은 타입을 전달한다면 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출해 주며, [복사 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94) 라고 합니다. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)도 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)(`=`) 형태로 사용하는 것을 [explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)를 사용하여 막을 수 있습니다만, 형변환이 되는게 아니므로, 굳이 막을 필요는 없습니다.

```cpp
T other;

T obj1 = other; // (O) 타입이 같다면 복사 생성자가 호출됨
T obj2(other); // (O) 명시적으로 복사 생성자 호출됨
```

# 초기화 파싱 오류

안타깝게도 함수 선언의 문법과 생성자 호출의 문법이 유사하여 컴파일러가 이 둘을 구분하지 못합니다. 따라서, C언어와의 호환성을 위해 그냥 함수 선언으로 해석합니다.

```cpp
T f(); // T 타입을 리턴하는 함수 f()를 선언합니다.
T obj(); // T 타입의 obj 개체를 기본 생성자로 생성하고 싶지만, 사실은 T 타입을 리턴하는 함수 obj()를 선언합니다.
```

|항목|의도|컴파일러 해석|
|--|--|--|
|`T obj();`|`T` 타입의 `obj` 개체를 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성하고 싶습니다.|`T` 타입을 리턴하는 함수 `obj()`를 선언합니다.|
|`T obj(T());`|`T` 타입의 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성한 것을 `obj`에 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하고 싶습니다.|`T` 타입을 리턴하고, `T(*)()` [함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달받은 함수 `obj()`를 선언합니다.|

따라서 상기 의도에 따른 표현은 다음과 같이 해야 합니다.

|항목|바른 표현|내용|
|--|--|--|
|`T obj();`|`T obj;`|단, [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)가 안됩니다.|
|`T obj();` 또는<br/>`T obj(T());`|`T obj = T();`|[자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)가 됩니다. 다만 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성한 것을 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)로 한번더 생성하는 것이므로, 생성자를 2회 호출하는 표현인데요,  다행스럽게도 컴파일러에 따라 1회로 최적화 해줍니다.(*[생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94) 참고*)|


> *(C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 `T obj{};`와 같이 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출할 수 있습니다.*<br/>
> *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94?), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.*

# 생성자 호출 및 함수 인수 전달 최적화

생성자 호출이나 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 전달시 다음 초기화 사례들은 컴파일러에 따라 1회 생성자 호출로 최적화 해줍니다.(*상황에 따라 최적화가 안되면 비효율적인 코드가 될 수 있습니다.*)

|항목|의도|컴파일러 최적화|
|--|--|--|
|`T obj = T();`|[기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)로 생성한 것을 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하여 전달합니다.|`obj`의 메모리 위치에 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.|
|`T obj = T(arg);`|`T(arg)`로 생성한 것을 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하여 전달합니다.|`obj`의 메모리 위치에 `T(arg)` 생성자를 호출합니다.|
|`T obj(T(arg));`|`T(arg)`로 생성한 것을 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하여 전달합니다.|`T obj = T(arg);`과 동일합니다.|

> *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.*

# 생성 후 대입 : 하지 마라.

생성 후 대입을 할 수 있습니다만, 서두에 말씀드렸듯이 하지 마십시요.

```cpp
T other;

T obj; // 기본 생성자
obj = other; // (△) 비권장. 생성하고 대입하지 말고, 완전하게 생성하세요. T obj(other); 가 낫습니다.
```

# 배열 초기화

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 정의시 [배열 요소의 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)가 유추될 수 있도록 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 사용할 수 있으며, [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 초기화 할 수 있습니다.(*더 자세한 내용은 [배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94)를 참고하세요.*) 

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

구조체는 별도로 [값 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94)를 위한 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)를 구현하지 않더라도 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 사용하여 초기화 할 수 있습니다.

단,  모든 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)의 [접근 지정자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A0%91%EA%B7%BC-%EC%A7%80%EC%A0%95%EC%9E%90)가 `public`이어야 합니다.(*더 세부적인 제약 조건은 [집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)을 참고 하세요.*)
```cpp
struct T {int x; int y;}; // 멤버 변수가 public 입니다.
T t = {10, 20}; // (O) 중괄호로 초기화

EXPECT_TRUE(t.x == 10 && t.y == 20);
```

# 자동 제로 초기화

기본적으로 변수는 생성시 초기화하지 않으면, 기존 메모리에 있는 쓰레기 값을 갖게 되지만, 하기의 경우는 메모리 영역을 제로(`0`)로 만들어 초기화 합니다.

1. [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98), [함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)
   
2. [배열 요소의 갯수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/#%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C%EC%9D%98-%EA%B0%AF%EC%88%98)보다 초기화 값을 적게 제공한 경우 나머지 요소

3. 클래스, 구조체, [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4)의 멤버 변수

    * `T t;`나 `T* ptr = new T;`와 같이 괄호 없이 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하면 [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 하지 않습니다.
    * `T* ptr = new T();` 와 같이 괄호를 사용하여 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하면 [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 합니다. 
 
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

뭐는 자동으로 0이 되고, 어떻게 호출하느냐에 따라 안될 수도 있고, 복잡하죠? 

복잡하니, ***모든 변수를 초기화 한다는 대원칙***을 가지고 작성하세요. 생성자 없이 [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)에 의존해서 개발했는데, 다른 누군가가 `T* ptr1 = new T;`와 같이 사용한다면 낭패니까요.

|항목|[자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94) 지원|선언에서 명시적 초기화 지원|권장 초기화 방법|
|--|:--:|:--:|--|
|[전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)|O|O|선언시 초기화|
|[정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)|O|O|선언시 초기화|
|[정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)|O|X|사용 비권장. [함수내의 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 사용|
|[정적 상수 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)|X|O|선언시 초기화|
|개체의 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|△|X|[기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)의 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8). (*`new T;`와 `new T();`가 달라서 △로 표기했습니다. 실수할 소지가 많으니 명시적으로 초기화 하세요.*)|
|[함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)|O|O|선언시 초기화|
|[지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)|X|O|선언시 초기화|

> *(C++11~) 비정적 멤버 변수도 [멤버 선언부에서 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/)를 할 수 있어 초기화 작성이 쉬워졌습니다.*<br/>
> *(C++17~) [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, [클래스 정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 정의 및 초기화가 쉬워졌습니다.*

