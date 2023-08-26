---
layout: single
title: "#6. [고전 C++ 가이드] 예외 보증"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


# 예외 보증과 예외 안정 방법

기본 보증 : 적어도 리소스릭/메모리릭이 없다.
강한 보증 : 예외가 발생해도 내부 자료값이 변하지 않는다.
Nothrow : 절대 예외를 발생하지 않는다.



# 예외 안정 방법

예측할 수 있는 예외상황에 대처하라.

기본보증, 강한보증(할수 있다면)을 준수하라.

처리할 수 있는 곳에서 예외를 처리하라.
가정을 미리 검사하라
try - catch를 사용하라
에러코드 변환후 대처하라


# 예외 안정에 좋은 함수

```cpp
// 함수군 을 Layering 하여 예외가 더이상 방출되지 않는 Layer를 만들고 Error 코드를 리턴하게 한다.
// 이 Layer의 모든 함수군은 기본보증, 강한 보증을 수행해야 한다.
Error f(const T& data) // 필요하다면 error 코드를 리턴, 
                                    // 데이터 추상화된 인자 사용 
{
    
   _ASSERTE(data.IsValid()); // 사전 가정
  
    // 유효하지 않은 인자인 경우 에러 리턴  
    if (!data) {
        // 명백한 코딩 실수라면 이 함수를 사용한 개발자에게 찾아간다. 굳이 if 로 검사하여 release 코드를 작성할 필요는 없다.
       //  현 함수가 할 수 있다면 보정한다. data.Normalize();
       // 혹은 예외를 날린다. throw invalid_argument("이상한 인자");
          return Invalid;
     }

     // 함수 동작 처리
     // 함수 동작 처리
     // 함수 동작 처리

     _ASSERTE(result.IsValid()); // 사후 가정

     return Ok;
}

```

# 예외와 생성자

생성자에서 예외가 발생하면?

생성 후 대입과정에서 예외가 발생하면 난감해지는 상황 예시. 
생성자에서 예외가 발생하더라도 안정적일 수 있도록 멤버를 `Handler`, 스마트 포인터로 작성

```cpp
void f()
{
    T* t = new T; // 예외발생하면 메모리 릭이 발생할까? (보통의 경우 메모리는 자동해제됨)
    delete t;        // 예외발생하면 호출되지 않는다.
}
```

이전에 생성한 개체는 예외발생시 소멸 시켜야 한다.

```cpp
void f()
{
    T* t = new T;
    U* u = new U;

    delete t;  
    delete u;
}
```

```cpp
void f()
{
    T* t = new T;
    try {
        U* u = new U;
        delete u;
    }
    catch (...) {
        delete t;
    }
}
```

스택풀기에 의해 new U 생성시 예외가 발생하면 t의 소멸자가 불린다.

~auto_ptr() {
   delete p; // 관리하는 포인터 삭제
}

```cpp
void f()
{
    auto_ptr<T> t( new T); 
    auto_ptr<U> u( new U);
 }
 ```

# 예외와 소멸자
소멸자에서 예외가 발생하면?

절대 안된다.

예외가 발생하면 스택풀기가 이루어져서 소멸자가 불리는데, 이 소멸자에서 또 예외를 발생하고... 


```cpp

~T()
{
    try {
       // 예외를 발생시키는 함수
    }
    catch (...) { // 절대 예외 발생 금지
       // 근데, 뭘해야 하지? 
   }
}
```
클래스 사용방식을 바꿀 수 밖에 없다.
```cpp


void T::clean() // 소멸전 정리하는 함수
{
    // 에외를 발생시킬수도 있는 정리 코드들

   m_IsClean = true;
}

T::~T() {
     if (m_IsClean) return;
     try {clean();} // 혹시 안했다면 정리
     catch (...) { 
          // 여전히 뭘해야 할지 모르겠다. 
          // 로그파일이나... -_-
     }
}
```

# 예외 안정에 좋은 복사 생성자

생성자 함수 블럭에 대입문을 작성하면,
a, b는 기본생성자 호출후 대입문이 실행된다.

```cpp
class T {
   U a, b;
   T(const T& other) {
       a = other.a;
       b = other.b;
   }
};
```
초기화 목록에서 초기화 하면 복사생성자가 호출된다.

일반적으로 대입문보단 복사생성자가 예외발생 확률이 적다.

```cpp
class T {
   U a, b;
   T(const T& other) :
       a(other.a),
       b(other.b) 
   {}
};
```
# 예외 안정에 좋은 대입 생성자

b 대입중 예외가 발생하면 이미 변경된 a는 어떻게?

```cpp
class T {
   U a, b;
   T& operator =(const T& other) {
      a = other.a;
      b = other.b; // 대입중 예외가 발생하면 a는?
      return *this;
   }
};
```
temp 생성시 예외가 발생하면 temp를 버리면 된다.

```cpp
class T {
   U a, b;
    T& operator =(const T& other) {
      T temp(other);
      Swap(temp); // 절대 예외발생 금지
      return *this;
   }
};
```

# 예외 안정에 좋은 nothrow swap

```cpp
class T {
   U a, b;
   void Swap(T& other) {
      U temp = a;    	// 예외발생
      a = other.a;	// 예외발생
      other.a = temp;	// 예외발생

      temp = b;	// 예외발생
      b = other.b;	// 예외발생
      other.b = temp; 	// 예외발생
   }
};
```

```cpp
class T {
   struct Impl {
        U a, b;
        Impl(const Impl& other) : 
           a(other.a), b(other.b) {}
    };

    Impl* impl; // 구현부 분리. 브릿지. pimpl 이디엄

    T(const T& other) :
       impl(new Impl(other.impl)) {}
    ~T() {delete impl;}

    T& operator =(const T& other) {
      T temp(other);
      Swap(temp); // 절대 예외발생 금지
      return *this;
   }

   void Swap(T& other) {
       Impl* temp = impl; // 포인터끼리 대입. 예외발생 없음
       impl = other.impl;
       other.impl = temp;

       // std::swap(impl, other.impl);  로 한방에 할 수도
   }   
};
```



# 예외 레이어링

모듈의 경계에서 예외가 방출되지 않게 하라.


