---
layout: single
title: "#7. [고전 C++ 가이드] 상수(Const)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `const` 정확성을 지켜라.
> * 무조건 `const`를 들이대라.
> * `const` 변수와 `const` 함수는 메모리를 수정하지 않기 때문에 예외가 발생하지 않는다.

**개요**

|항목|내용|
|--|--|
|`const` 개체|개체를 변경할 수 없습니다. 생성시 초기화 해야 합니다.|
|`const` 멤버 함수|멤버 함수의 뒤에 `const`를 붙여 상수 멤버 함수를 만들 수 있습니다. 상수 멤버 함수는 개체의 멤버 변수를 변경할 수 없습니다. 단, `mutable`로 정의된 개체는 수정 가능합니다.([논리적 상수성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-mutable/) 참고)|


`const`인 개체나 함수를 사용하면, 메모리의 수정이 없으므로 예외에 안정적입니다. 안정적인 프로그램을 위해, 할 수 있는 한 최대한 많이 `const`로 작성하는 것이 좋습니다.

다만, 하기 **상수성 계약** 을 준수하세요. 예외 발생 부분만 빼고는 대부분 컴파일 오류가 발생하니, 계약을 지키기 까다롭지도 않습니다.

**상수성 계약**

1. 상수 개체는 생성과 함께 초기화 되어야 합니다.(컴파일 오류)
2. 상수 개체는 변경할 수 없습니다.(컴파일 오류)
3. 상수 함수는 멤버 변수를 수정하지 않습니다.(컴파일 오류, 단 `mutable`로 변경 가능.([논리적 상수성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-mutable/) 참고))
4. 상수 함수는 멤버 변수를 수정할 수 있는 포인터나 참조자를 리턴하지 않습니다.(컴파일 오류. 단, `const_cast`로 억지로 구현 가능)
5. 상수 함수는 상수 함수만을 호출합니다.(컴파일 오류)
6. 상수 함수는 예외를 발생하지 않습니다.(노력해서 구현해야 함)
7. `mutable`로 [논리적 상수성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-mutable/)을 구현한 경우 예외를 발생하지 않아야 합니다.(노력해서 구현해야 함)

**상수 변수**

상수 변수는 하기와 같이 정의합니다. 반드시 초기값을 설정해야 합니다. 

```cpp
// const int x; // (X). 초기값 없음
const int x = 10; // (O) x의 값은 변경될 수 없음
```

포인터(혹은 참조자)형 변수의 경우 `const*`와 같이 `const`의 오른쪽에 `*`이 있으면 실제 데이터가 `const` 이고, 왼쪽에 `*`이 있으면 포인터형 변수가 `const`입니다.

```cpp
const int* p1; // (O) *p를 수정할 수 없음. *p = 30; 불가
int const* p2; // (O) const int* 와 동일(자주 사용하지 않는 형식)
int* const p3 = &x; // (O) p의 값을 수정할 수 없음. *p = 30;은 가능
const int* const p4 = &x; // (O) p, *p를 수정할 수 없음.
```

**리턴값의 상수성**

리턴값에 무의미하게 `const`를 붙일 필요는 없습니다. 의미에 맞게 붙이거나 떼야 합니다.

하기와 같이 리턴값이 기본 자료형이라면 어짜피 복제되어 리턴되므로, `const를 붙일 필요가 없습니다.(단 operator ++() 오버로딩시에 유의미할 수 있음. [연산자 오버로딩](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-operator-overloading/) 참고)

```cpp
int GetX1() const { return m_X; } // (O) 멤버 변수의 값을 리턴하는 const 함수
const int GetX2() const { return m_X; } // (X) 멤버 변수의 값을 쓸데없이 const로 리턴하는 const 함수. int k = t.GetX2(); 로 실행하므로 const int 리턴은 무의미함. operator ++() 에서는 유효할 수도 있다. 연산자 오버로딩 참고
 
```

멤버 변수의 포인터를 리턴하는 경우, `const` 함수인지 아닌지에 따라 포인터 상수성을 맞춰서 리턴해야 합니다. `GetX4()`처럼 맞지 않게 리턴하면, **상수성 계약** 위반입니다.(억지로 `const_cast`를 하지 마세요.)

```cpp
const int* GetX3() const { // (O) 멤버 변수의 값을 수정하지 않는 const 함수 
	return &m_X;
}               
int* GetX4() const { // (X) 멤버 변수의 값을 수정하는 const 함수
	return const_cast<int*>(&m_X); 
}   
int* GetX5() { // (O) 맴버 변수의 값을 수정하는 none-const 함수      
	return &m_X; 
} 	                        
```

**인자의 상수성**

인자에 	`const`를 사용하여 전달된 인자를 함수가 수정하는지, 수정하지 않는지 **코딩 계약**을 만들어 주는 게 좋습니다. 전달된 인자를 함수에서 수정하지 않는다면, 꼭 `const`로 만들어 주길 바랍니다.(단, 인자가 인수에 복사된다면, `const`는 무의미 하므로 사용할 필요가 없습니다.)

```cpp
void f(int x); // (O) 인자 x를 복사해서 사용함.
void f(const int x); // (X) 인자 x를 복사해서 쓰되 f에서 수정하지 않음. 호출하는 쪽에선 무의미

void f(int* x); void f(int& x); // (O) 인자 x를 f에서 수정함.
void f(const int* x); void f(const int& x); // (O) 인자 x를 f에서 수정하지 않음.  
```

