---
layout: single
title: "#5. [고전 C++ 가이드] 예외 보증"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [예외에 안전](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-safe/)하도록 [기본 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)과 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)을 하라.
> * [기본 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 되도록 스마트 포인터나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)를 사용하라.
> * [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 되도록 클래스를 설계하라.(*[예외 안전에 좋은 클래스 설계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84%EC%97%90-%EC%A2%8B%EC%9D%80-%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%84%A4%EA%B3%84) 참고*)
> * 모듈의 경계에서 예외가 방출되지 않도록 예외를 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 한뒤 오류 코드로 변환하라.

# 예외 보증 종류

[예외에 안전](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-safe/)(*[예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)해도 안전하게 [복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)되고 계속 동작해도 무방하게 만드는 코드*)하려면 다음의 [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 이루어져야 합니다.

|항목|내용|
|--|--|
|nothrow 보증|절대 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않습니다.<br/>[상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)이거나, 함수내에서 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 예외를 처리한 함수등에서 한정적으로 가능합니다.|
|[기본 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)|[예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)해도 적어도 메모리 릭이나 리소스 릭이 없습니다.<br/>스마트 포인터나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 개체를 활용하여 스택 개체로 만들면 쉽게 구현할 수 있습니다.(*[포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 참고)*|
|[강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)|[예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)해도 내부 자료값이 변하지 않고 이전 상태로 [복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)합니다.<br/>[생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)와 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 스마트 포인터나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 개체를 활용하여 [기본 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)을 하며(*[복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고*), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서는 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키지 않습니다. 또한, [복사 대입 연산자는 nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94)으로 구현합니다.|

클래스 구현의 구체적인 방법은 [예외 안전에 좋은 클래스 설계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84%EC%97%90-%EC%A2%8B%EC%9D%80-%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%84%A4%EA%B3%84)를 참고하시기 바랍니다.

# 예외 안전에 좋은 함수

1. [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)와 [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)은 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 의해 자동 소멸되도록 합니다.(*[Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 참고*)
2. 가정한 값 외에는 인수가 전달되지 않도록 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 합니다.
3. 함수내에 [사전 가정](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8), [사후 가정](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8)을 작성합니다.(*[공격적 자가진단](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-diagonostics/#%EA%B3%B5%EA%B2%A9%EC%A0%81-%EC%9E%90%EA%B0%80%EC%A7%84%EB%8B%A8) 참고*)

# 예외와 생성자

다음처럼 일부 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)만 초기화 하고, 나중에 별도 함수를 호출하여 초기화를 마무리하면, 사용자가 실수로 빼먹을 수도 있고, [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/)에도 좋지 않습니다. 불완전하게 생성된 개체에 별도 [Setter 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)를 호출하여 완전하게 만드는 중에 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/) 처리를 위해 소멸시켜야 하는데, 혹시나 이미 이 개체를 참조하는 곳이 있다면, 찾기도 힘들고, 찾았더라도 처리를 어찌해야 할지 난감해 지니까요.(*[예외와 생성자](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8%EC%99%80-%EC%83%9D%EC%84%B1%EC%9E%90) 참고*)

다음은 난감해 지는 예입니다. 

1. #1 : `const B* other = t.GetB();`로 `t`개체를 생성해서 사용하고 있는데, 
2. #2 : `t.SetA(new A);`로 `t`개체의 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 마저 설정하려는 순간 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한 경우입니다.

`other`를 사용하는 곳을 추적해서 이전 상태로 [복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)하는 작업을 해야 합니다만, 어떻게 추적해야 할지 난감하죠.

```cpp
class A {
public:
   A() {
      throw "A"; // A 개체를 생성하던 중에 예외가 발생합니다.
   }   
};
class B {
public:
   B() {
      std::cout << "Construct B" << std::endl;
   }   
   ~B() {
      std::cout << "Destruct B" << std::endl;           
   } 
};

class T {
   A* m_A;
   B* m_B;
public:
   explicit T(B* b) : m_B(b) {}
   ~T() {
      delete m_A; // 개체를 소멸합니다.
      delete m_B;
   }
   void SetA(A* a) {m_A = a;} // (△) 비권장. 별도 Setter로 값을 대입합니다.

   const A* GetA() const {return m_A;}
   const B* GetB() const {return m_B;}
};

T t(new B);
const B* other = t.GetB(); // #1. (△) 비권장. 아직 완전하게 생성되지 않은 t개체를 other 포인터로 사용합니다.
.
. // 뭔가 이러저러한 작업을 합니다.
.
t.SetA(new A); // #2 : A개체가 생성되면서 예외를 발생합니다.

// (△) 비권장. t 개체를 소멸해야 합니다.
// (△) 비권장. other 가 사용된 곳을 추적해서 뭔가 예외 처리 작업을 해야 합니다.
```

따라서, 생성할 때 완전하게 생성해야 합니다.(*[완전한 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%99%84%EC%A0%84%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90) 참고*)
생성 도중 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면, 생성하던 개체를 버리면 되니까요.

하지만 이 방법도 인수를 임시 생성해서 전달할때 인수가 여러개라면, 어떤 인수가 먼저 생성되었느냐에 따라 복잡해 집니다. 특히 왼쪽에서 오른쪽으로 인수가 생성되어 전달된다고 보장하지 않습니다.(*[평가 순서](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%8F%89%EA%B0%80-%EC%88%9C%EC%84%9C) 참고*)

다음 코드에서 `T t(new A, new B);`는 `new A` 호출 후 `new B`를 호출한다고 보장하지 않습니다. 어느것이 먼저 호출될지 모릅니다. 컴파일러 마음입니다. 

따라서, 

1. `new B` [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 호출
2. `new A` 에서 예외 방출

하게 되면, 이미 생성된 `B` 개체의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)는 호출되지 않습니다. 따라서, [기본 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)을 위반하게 되죠.

```cpp
class A {
public:
   A() {
      throw "A"; // A 개체를 생성하던 중에 예외가 발생합니다.
   }
};
class B {
public:
   B() {
      std::cout << "Construct B" << std::endl;
   }   
   ~B() {
      std::cout << "Destruct B" << std::endl;           
   } 
};

class T {
   A* m_A;
   B* m_B;
public:
   T(A* a, B* b) : m_A(a), m_B(b) { 
      std::cout << "Construct T" << std::endl;
   } 
   ~T() {
      delete m_A; // 개체를 소멸합니다.
      delete m_B;
   }
   const A* GetA() const {return m_A;}
   const B* GetB() const {return m_B;}
};

try {
   // A 생성에서 예외가 발생하면 T 개체 생성자 호출 자체가 안됩니다.
   // (△) 비권장. B 개체가 이미 생성되었고, A 개체 생성중 예외가 발생했다면, B 개체는 소멸되지 않습니다.
   // 기본 보증 위반입니다.
   T t(new A, new B); 
   const A* other = t.GetA();
   ...
}
catch (...) {
}
```

이런 문제 때문에 인수를 임시 생성하여 전달할때, [예외에 안전](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-safe/)하게 하려면 스마트 포인터나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 개체를 활용해야 합니다. 다음 코드는 스마트 포인터인 [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)을 사용하며, `A`에서 [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시 이미 생성된 `B` 개체도 잘 소멸됩니다.

```cpp
class A {
public:
   A() {
      throw "A"; // A 개체를 생성하던 중에 예외가 발생합니다.
   }
};
class B {
public:
   B() {
      std::cout << "Construct B" << std::endl;
   }   
   ~B() {
      std::cout << "Destruct B" << std::endl;           
   } 
};

class T {
   std::auto_ptr<A> m_A; // 스마트 포인터
   std::auto_ptr<B> m_B;
public:
   T(std::auto_ptr<A> a, std::auto_ptr<B> b) : m_A(a), m_B(b) {  // 소유권 이전
      std::cout << "Construct T" << std::endl;
   } 
   ~T() {
   }
   const A* GetA() const {return m_A.get();}
   const B* GetB() const {return m_B.get();}
};
try {
   // (O) 인자 전달을 위해 인수 생성시 예외가 발생해도 스마트 포인터여서 예외에 안전합니다.
   T t(std::auto_ptr<A>(new A), std::auto_ptr<B>(new B)); 
   const A* other = t.GetA();
   ...
}
catch (...) {
}
```

# 예외와 소멸자

[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 예외를 방출하면 안됩니다. [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)가 이루어져서 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 불리는데, 이 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)에서 또 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하면 정상적인 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)를 방해합니다.(*[소멸자에서 예외 방출 금지](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90%EC%97%90%EC%84%9C-%EC%98%88%EC%99%B8-%EB%B0%A9%EC%B6%9C-%EA%B8%88%EC%A7%80) 참고*)

예외 방출을 막기 위해 단순히 [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)로 예외를 무시하기 보다는

```cpp
~T()
{
    try {
       // 예외를 발생시키는 함수
    }
    catch (...) { // 절대 예외 발생 금지
       // 근데, 뭘해야 할지 모릅니다.
   }
}
```

다음처럼 정리하는 `Release()` 함수를 별도로 만들어 두고, 만약 사용자가 정리하지 않은 경우 정리 시도를 하는게 좋습니다.

```cpp
void T::Release() { // 소멸전 정리하는 함수
   // 에외를 발생시킬수도 있는 정리 코드들

   m_IsReleased = true;
}

T::~T() {
    if (m_IsReleased) return;
    try {
        Release(); // 혹시 안했다면 정리
    } 
    catch (...) { 
        // 여전히 뭘해야 할지 모르겠습니다.
        // 로그 파일 작성 정도를 추천합니다.
    }
}
```

# 예외 안전에 좋은 복사 생성자

[복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)에 언급한 것처럼, 스마트 포인터나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/) 개체를 활용하여 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 구현하면, [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)에 의해 [기본 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)과 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)을 하게 됩니다.

# 예외 안전에 좋은 복사 대입 연산자

[swap을 이용한 예외 보증 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에 언급한 것처럼, `Swap()`을 이용하면 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한 개체를 버림으로서 [기본 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)과 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)을 하게 됩니다. 단, 이때 `Swap()`은 예외를 방출하지 말아야 합니다.

```cpp
class T {
T& operator =(const T& other) {

   // other를 복제한 임시 개체를 만듭니다.
   T temp(other); // (O) 생성시 예외가 발생하더라도 this는 그대로 입니다. 예외 발생시 temp를 버리면 됩니다.

   // this의 내용과 임시 개체의 내용을 바꿔치기 합니다.
   // this는 이제 other를 복제한 값을 가집니다.
   // 여기서 예외를 발생하면 this가 일부 바꿔치기 되어 강한 보증이 안됩니다.
   // 그러니 꼭 nothrow swap으로 구현하세요.
   Swap(temp);

   return *this;

   } // temp는 지역 변수여서 자동으로 소멸됩니다.
};
```

# 예외 안전에 좋은 nothrow swap

[예외 안전에 좋은 복사 대입 연산자](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84%EC%97%90-%EC%A2%8B%EC%9D%80-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 언급처럼 `Swap()`은 예외를 방출하지 말아야 합니다. 만약 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한다면, 바꿔치기 하다가 일부만 변경되고 중단된 것이어서 어떻게 [복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)해야 할지 난감해 집니다. 예외 발생 전의 상태로 완벽하게 [복원](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%8A%A4%ED%83%9D-%ED%92%80%EA%B8%B0%EC%98%88%EC%99%B8-%EB%B3%B5%EC%9B%90)하려면 `Swap()`은 절대 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시켜서는 안됩니다. 

일반적으로 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이나 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)은 새로운 메모리를 할당하기 때문에 [예외가 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)한다고 가정하시는게 좋습니다.

```cpp
class T {
   U a, b;
   void Swap(T& other) {
      U temp = a; // 예외가 발생할 수 있습니다.
      a = other.a; // 예외가 발생할 수 있습니다.
      other.a = temp; // 예외가 발생할 수 있습니다.

      temp = b; // 예외가 발생할 수 있습니다.
      b = other.b; // 예외가 발생할 수 있습니다.
      other.b = temp; // 예외가 발생할 수 있습니다.
   }
};
```

`Swap()`에서 [예외를 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)시키지 않으려면, 포인터형 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 1개 만들고, [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)과 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 포인터 연산으로 바꿔서 구현하면 됩니다. 포인터끼리의 대입은 8byte끼리의 복사이므로 복사 부하도 없고, [예외 발생](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)도 없다고 가정하셔도 무방하니까요.(*[nothrow swap - 포인터 멤버 변수를 이용한 swap 최적화](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94) 와 [스마트 포인터를 이용한 PImpl 이디엄 구현](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-pimpl/#%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-pimpl-%EC%9D%B4%EB%94%94%EC%97%84-%EA%B5%AC%ED%98%84)을 참고하세요.*)

```cpp
class T {
   struct Impl {
        U a, b;
        Impl(const Impl& other) : 
           a(other.a), b(other.b) {}
    };

    Impl* m_Impl; // 포인터형 멤버 변수 1개입니다.

    T(const T& other) :
       m_Impl(new Impl(other.m_Impl)) {}
    ~T() {delete impl;}

    T& operator =(const T& other) {
      T temp(other);
      Swap(temp); // 절대 예외발생 금지
      return *this;
   }

   void Swap(T& other) {
       Impl* temp = m_Impl; // 포인터끼리 대입입니다. 복사 부하도 없고, 예외 발생도 없다고 가정해도 무방합니다.
       m_Impl = other.m_Impl;
       other.m_Impl = temp;

       // std::swap(m_Impl, other.m_Impl); 로 한방에 할 수도 있음
   }   
};
```

# 예외 레이어링

예외를 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않으면 [전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)되어 최종적으로 프로그램이 종료됩니다. 따라서, 대형 프로그램을 개발하거나 라이브러리를 개발하는 경우에, 각 역할별로 모듈을 레이어링 하여 예외가 사용자 모듈로 [전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)되지 않도록 하는게 좋습니다.

1. 라이브러리 모듈에서는 [예외를 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)합니다. 
2. 모듈의 경계에서 상위 예외 처리 모듈을 둡니다. 해당 모듈에서는 예외를 [catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 한뒤 오류 코드로 변환합니다. 따라서 사용자 모듈로 더이상 [예외가 전파](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EC%A0%84%ED%8C%8C)되지 않습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/ef2063c7-1cfe-4d2d-9738-0e6ff7885682)
