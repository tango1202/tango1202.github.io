---
layout: single
title: "#13. [고전 C++ 가이드] 메모리 세그먼트(Memory Segment)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 힙에 동적 메모리 할당한 것은 명시적으로 소멸시켜라.
> * 스택에 생성된 변수는 유효 범위가 종료되면 자동 소멸 된다.

# 개요

프로그램을 실행하면 각 코드 요소는 하기 구조에 맞춰 메모리에 로딩됩니다. 

|항목|읽기/쓰기|내용|
|--|--|--|
|코드(텍스트) 세그먼트|읽기|프로그램 코드, 제어문, 상수, 함수등|
|데이터 세그먼트(BSS, Block Started by Single)|읽기/쓰기|초기화 안된(혹은 0으로 초기화된) 전역 변수 또는 정적 변수|
|데이터 세그먼트(rodata, read only data)|읽기|문자열 상수|
|데이터 세그먼트(data)|읽기/쓰기|0 이외의 값으로 초기화된 전역 변수 또는 정적 변수|
|힙(Heap) 세그먼트|읽기/쓰기|`new` 등으로 생성한 동적 메모리. 명시적으로 소멸시켜야 함|
|스택(Stack) 세그먼트|읽기/쓰기|함수의 지역 변수, 인자, 리턴값등. 자동 소멸됨| 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5905dc13-603e-4ee3-9d19-e51b60e03cef)

# 코드 세그먼트

코드 세그먼트 부분은 프로그램 코드가 저장됩니다.

# 데이터 세그먼트 

데이터 세그먼트 부분은 3개(BSS, rodata, data)로 나뉘어 있고, 전역 변수나 정적 변수는 BSS 영역과 data 영역에 저장됩니다.

* BSS 영역은 0으로 초기화해야 할 것들이 모여 있습니다. 이렇게 한곳에 모여 있기에 일괄로 0으로 초기화 하여 시스템 성능을 향상 시킬 수 있습니다.
   
* data 영역은 개별적으로 초기화할 항목이 모여 있습니다.

* rodata 영역은 문자열 상수만 특별히 모아둔 영역입니다. 일반적으로 상수(`const int a = 10;`의 표현에서 `10`)는 코드 세그먼트 영역에 있지만, 문자열은 크기 때문에 별도로 rodata 영역에 두고 이를 포인터 등으로 참조하여 사용합니다. 또한, 중복된 문자열을 코드 여기저기에서 사용하더라도, 컴파일러가 알아서 1개로 취합해 줍니다. 

# 힙

힙은 `new`로 할당한 동적 메모리 영역입니다. `delete`로 명시적으로 소멸시키지 않으면, 메모리 릭이 발생합니다.
  
```cpp
int* p = new int;
...
delete p; // new로 생성한 것은 반드시 delete
```
  
# 스택

스택은 함수내의 지역 변수, 인자, 리턴값이 호출될때마다 차곡 차곡 쌓이는 공간입니다.(함수내에서 `new`로 동적 메모리를 할당했다면, 해당 메모리는 힙에 할당됩니다.) 함수 호출이 종료되거나 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)가 종료되었다면 자동으로 소멸됩니다. 이러한 자동 소멸은 향후 [스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-smart-pointer/) 개념으로 많이 활용되니 잘 알아 두시기 바랍니다.
  
하기 호출 과정을 개념적으로 메모리에 도식화하면 다음과 같습니다.

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
