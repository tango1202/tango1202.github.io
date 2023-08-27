---
layout: single
title: "#2. [고전 C++ 가이드] 예외 메카니즘(try-catch, throw와 스택 풀기)"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

C++은 예외 상황이 발생했을때 다음의 메커니즘으로 예외를 발생, 복귀, 전파, 탐지 합니다.

1. `throw()` 문으로 예외를 발생시킵니다. 이때 예외 정보를 담은 개체로 예외 정보를 제공합니다.
2. **스택 풀기**에 의해 예외 발생전의 상태로 복귀합니다.
3. 아무런 작업을 안하거나 `throw`로 예외를 상위 개체로 전파합니다.
4. `try`-`catch()`로 예외를 탐지합니다. 

# 예외 발생과 탐지(try-catch, throw)

그 자리에서 해결할 수 없는 에러 조건을 보고하는 일
어디선가 탐지된 에러를 진짜로 처리하는 일

**발생**
해결할 수 없으면 발생시켜라.

```cpp
char ToChar(int val) {
   return static_cast<char>(val); // 항상성공?
}

char ToChar(int val) {
   if (!(0 <= val && val < 255)) {
        throw out_of_range("범위오류");
   }
   return static_cast<char>(val);
}
```

**탐지**
해결할 수 있는 곳에서 탐지하라.

```cpp
void f(int val) {
   ToChar(val); // 그냥 두면 다운될 수도 있다.(x)
}
void f(int val) {
    if (!(0 <= val && val < 255)) { // ToChar 가정을
       ToChar(val);                       // 미리 확인(O)
    }
}
void f(int val) {
    try { ToChar(val);}
    catch(out_of_range& e) {
       // 뭔가 하던가, 말던가(O)
    }
}
```

**전파**

예외를 발생시키거나 전파시킵니다. 

```cpp
try {
    std::string("abc").substr(10); // (X) 예외 발생. std::length_error
}
catch (const std::exception& e) {
    std::cout << e.what() << '\n';
//  throw e; // (△) 비권장. std::exception 을 복제하여 리턴
    throw;   // (O) std::length_error 를 재전파
}
```


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

# 스택 풀기(예외 복귀)

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






# 예외 지정

지정한 예외이외에는 unexpected 를 방출한다.
쓰지마라.

```cpp
void f() throw(error1, error2) 
```

```cpp
void f() 
{
    try {
    }
    catch (error1&) {
       throw;
    } 
    catch (error2&) {
       throw;
    }
    catch(...) {
       throw unexpected();
    }
}
```



> * 나열된 예외 이외에는 `unexpected`로 강제 변환한다. 사용하지 마라.

**개요**

함수 정의시 `throw`로 함수가 발생하는 예외 목록을 한정할 수 있습니다.

```cpp
void f() throw(error1, error2) {

}
```

하지만 상기 코드는 사실 하기와 같습니다.

```cpp
    void f() {
    try {
    } 
    catch (error1&) {
        throw; 
    }
    catch (error2&) {
        throw; 
    }
    catch(...) {
        throw unexpected(); 
    }
}
```

즉, `error1`과 `error2` 만 발생시키기 위해서, 억지로 다른 예외를 unexpected로 강제 변환합니다. 이러다 보니 불필요하게 `try`-`catch` 만 추가되어 실행속도만 느려집니다. 사용하지 마세요.



