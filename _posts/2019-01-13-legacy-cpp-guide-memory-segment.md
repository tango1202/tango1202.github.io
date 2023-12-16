---
layout: single
title: "#13. [레거시 C++ 가이드] 메모리 세그먼트(Memory Segment)"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)에 동적으로 메모리에 할당한 것은 명시적으로 소멸시켜라.
> * [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 생성된 변수는 [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)가 종료되면 자동 소멸 된다.

# 개요

메모리 구조를 알아두면, [초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/)나 [예외 처리](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/)등 C++ 동작 원리를 이해할 수 있어 많은 도움이 됩니다.

프로그램을 실행하면 각 코드 요소는 하기 구조에 맞춰 메모리에 로딩됩니다. 

|항목|읽기/쓰기|내용|
|--|--|--|
|[코드(*텍스트*) 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%BD%94%EB%93%9C-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)|읽기|프로그램 코드, [제어문](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-statements/), [리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/), [함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/)등|
|[데이터 세그먼트(*BSS, Block Started by Single*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)|읽기/쓰기|초기화 안된(혹은 0으로 초기화된) [전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98) 또는 정적 변수|
|[데이터 세그먼트(*rodata, read only data*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)|읽기|[문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)|
|[데이터 세그먼트(*data*)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)|읽기/쓰기|0 이외의 값으로 초기화된 [전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98) 또는 정적 변수|
|[힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)(*Heap*) 세그먼트|읽기/쓰기|[new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 등으로 생성한 동적 메모리. 명시적으로 소멸시켜야 함|
|[스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)(*Stack*) 세그먼트|읽기/쓰기|함수의 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter), [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)등. 자동 소멸됨| 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5905dc13-603e-4ee3-9d19-e51b60e03cef)

# 코드 세그먼트

[코드 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%BD%94%EB%93%9C-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)는 프로그램 코드, [제어문](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-statements/), [리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/), [함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/)등이 저장되며 읽기 전용입니다.

# 데이터 세그먼트 

[데이터 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)는 3개(*BSS, rodata, data*)로 나뉘어 있습니다. [전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98), [함수내 정적 지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)는 BSS 영역과 data 영역에 저장됩니다.

* BSS 영역은 0으로 초기화해야 할 것들이 모여 있습니다. 이렇게 한곳에 모여 있기에 일괄로 0으로 초기화 하여 시스템 성능을 향상 시킬 수 있습니다.
   
* data 영역은 개별적으로 초기화할 항목이 모여 있습니다.

* rodata 영역은 [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)만 특별히 모아둔 영역입니다. 일반적으로 상수(*`const int a = 10;`의 표현에서 `10`*)들은 [코드 세그먼트](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%BD%94%EB%93%9C-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 영역에 있지만, 문자열은 용량이 좀 되기 때문에 별도로 rodata 영역에 두고 이를 [포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/) 등으로 참조하여 사용합니다. 또한, 중복된 문자열을 코드 여기저기에서 정의하더라도, 컴파일러가 용량 최적화를 하면서 알아서 1개로 취합해 줍니다. 

# 힙

[힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)은 [new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 할당한 동적 메모리 영역입니다. [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 명시적으로 소멸시키지 않으면, 계속 메모리를 차지하고 있는데요,이런 메모리 릭이 조금씩 쌓이다 보면 결국 프로그램 메모리가 부족해서 종료될 수 있습니다. 그러니 꼭 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 하셔야 합니다. 
  
```cpp
int* p = new int;
...
delete p; // new로 생성한 것은 반드시 delete
```

[힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99)은 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D) 보다 메모리 할당 속도가 훨씬 느립니다.

# 스택

[스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)은 [함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/)내의 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter), [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 호출될때마다 차곡 차곡 쌓이는 공간입니다.

[함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/) 호출이 종료되거나 [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)가 종료되었다면 ***자동으로 소멸***됩니다. 이러한 자동 소멸은 향후 스마트 포인터(*[auto_ptr](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)의 개념으로 많이 활용되니 잘 알아 두시기 바랍니다.
  
하기 호출 과정에서의 [스택](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)의 상황을 개념적으로 도식화하면 다음과 같습니다.

```cpp
int f1(int a) {
    int b = 10;
    return f2(a, b);
}
int f2(int a, int b) {
    int c = 20;
    return a + b + c;
}
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/d1d0de8f-f0dd-4f60-aa29-0f3769b53041)
