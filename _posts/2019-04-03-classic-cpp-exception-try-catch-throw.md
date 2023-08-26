---
layout: single
title: "#3. [고전 C++ 가이드] 예외 발생과 탐지(try, catch, throw)"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 예외 발생과 탐지(try-catch)

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

# throw

예외를 전파시킵니다. 

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