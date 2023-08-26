---
layout: single
title: "#2. [고전 C++ 가이드] 스택 풀기"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

예외가 발생하기 전의 상태로 복귀해야 한다.


https://en.cppreference.com/w/cpp/language/throw


# 예외 메카니즘

예외상황이 발생하면 스택을 하나하나 풀면서 해제한다.
자동 변수(지역 변수)를 활용하면 예외발생시 스택풀기에 의해 소멸자가 호출된다.

**try-catch**

예외가 무시되지 않는다.

```cpp
void f(int val) {throw out_of_range();}

void g(int val) 
{
   f(val); // 예외 발생.  
             // k는 호출되지 않고, 스택을 푼다.
   k();
}
void start()
{
    try { g(10);}
    catch(out_of_range&) { } // 예외를 처리
}


```

# 스택 풀기

https://en.cppreference.com/w/cpp/language/throw 참고

**에러코드**
예외가 무시된다.
```cpp
error f(int val) {}

void g(int val) {
    f(val); // f가 리턴한 에러코드는 무시된다.
    k();     // f에서 에러가 있어도 k를 실행
}
```

스택풀기
https://en.cppreference.com/w/cpp/language/throw 참고



