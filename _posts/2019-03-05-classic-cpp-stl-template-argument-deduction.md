---
layout: single
title: "#5. [고전 C++ STL] 템플릿 함수 인수 추론과 오버로딩 결정 규칙"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 오버로딩 시에는 `T`보다는 `T*` 보다는 `const T*`가 선택된다.
> * 연산자 오버로딩 함수는 비멤버 템플릿 함수로 작성하라.
> * 템플릿 함수 인수 추론시 `T&`는 `T`로 추론된다.

# 개요

템플릿 함수의 인스턴스화는 다음 2가지 과정을 거칩니다.

1. 템플릿 함수 인수 추론 : 전달된 타입을 어떤 타입으로 사용할지 추론
2. 템플릿 함수 오버로딩 결정 : 인수 추론 후 일반 함수와 여러개의 템플릿 함수중 적합한 것을 결정

# 템플릿 함수 인수 추론

`<>`내의 인수가 생략됐거나 `<>`가 아예없는 경우 경우 누락된 인수를 추론합니다.

```cpp
template<typename T, typename U>
T f(T, U) {return 2;}

f<int>(0, (double)0); // int f(int, double)
f<double>(0, (int)0); // double f(double, int)
f(0, 0); // int f(int, int)
```

값 타입과 포인터 타입은 타입 그대로, 참조자는 참조성을 제거하고 추론합니다. 단 명시적으로 참조자를 작성하면 참조자로 사용할 수 있습니다.

```cpp
template<typename T, typename U, typename V>
void f(T, U, V) {}

int val = 0;
int* ptr = NULL;
int& ref = val;

f(val, ptr, ref); // f<int, int*, int>(int, int*, int). 참조자가 제거됩니다.

f<int, int*, int&>(val, ptr, ref); // f<int, int*, int&>(int, int*, int&). 명시적으로 지정하면 참조자로 사용됨
```

배열 인수는 포인터로 추론합니다.

```cpp
template<typename T>
void f(T) {}

int arr[3];
// Argument : int[3] -> int*로 조정 
// T : int* 
// Parameter : int*
f(arr); // f<int*>(int*)
```

함수는 함수 포인터로 추론합니다.

```cpp
template<typename T>
void f(T) {}

void Func(int) {}

// Argument : void (*)(int) 
// T : void (*)(int) 
// Parameter : void (*)(int)
f(Func); // // f<void (*)(int)>(void (*)(int))
```

최상위 `const`는 무시됩니다.([오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고)

```cpp
using namespace Deduction_4;

const int a = 0;
// Argument : const int -> int로 조정 
// T : int 
// Parameter : int
f(a); // f<int>(int)
```

참조자의 참조자는 참조자가 될 수 있도록 변환합니다.

```cpp
template<typename T>
void f(const T&) {}

int a = 0;
int& b = a;

// Argument : int 
// T : int 
// Parameter : const int&
f(a); // f<int>(const int&)

// Argument : int& 
// T : int 
// Parameter : const int&
f(b); // f<int>(const int&)
```

포인터의 포인터가 중첩되어 만들어지지 않도록 변환합니다.

```cpp
template<typename T>
void f(const T*) {}

int a = 0;
int* b = &a;

// Argument : int* 
// T : int 
// Parameter : const int*
f(&a); // f<int>(const int*)
f(b); // f<int>(const int*)
```

자식 개체는 부모 개체로 조정될 수 있습니다.

```cpp
template<typename T>
class Base {};

template<typename T>
class Derived : public Base<T> {};

template<typename T>
void f(Base<T>*) {}

Derived<int> d;
// Argument : Derived<int>* -> Base<int>* 로 조정 
// T : int 
// Parameter : Base<int>*
f(&d); // f<int>(Base<int>*)
```

다른 인수의 추론으로 부터 추론될 수 있습니다.

```cpp
template<typename T>
class Other {};

template<typename T>
class Another {
public:
    typedef T Type;
};

template<typename T>
void f(Other<T>, typename Another<T>::Type) {} // typename : 템플릿 정의 내에서 종속된 타입임

Other<int> other;
// Argument1 : Other<int> 
// T : int 
// Parameter1 : Other<int>
// Argument2 : int -> Argument1에서 T가 int로 추론되어 Another<int>::Type 확인 
// Parameter2 : int
f(other, 10); // f<int>(Other<int>, int)
```

템플릿의 인자 타입이 정확히 일치하지 않으면 추론할 수 없습니다. 하기에서 `A<10>`에서 `10`은 `int` 타입이기 때문에 `short`를 사용하는 `f()` 함수로 전달할 수 없습니다. 

다만, `f<10>(a)`와 같이 명시적으로 호출하여 `short` 에 `10`을 전달하고, 같은 `A<10>` 타입으로 만들어 사용할 수 있습니다.  

```cpp
template<int i> 
class A {};

template<short s>
void f(A<s> a) {}

A<10> a;
// Argument :A<10>에서 10은 int 타입임
// Parameter : A<short>이어서 A<10>을 전달받을 수 없음.
f(a); // (X) 컴파일 오류. A<int>와 A<short>는 서로 다른 타입이기에 암시적 변환할 수 없음

// Argument :A<10> 
// Parameter : A<10>
f<10>(a); // 명시적으로 10을 전달하여 A<10> 타입이 됨. 같은 타입이어서 함수 호출됨   
```

# 템플릿 함수 오버로딩 결정 규칙

서로 인자가 다른 함수들은 오버로딩 후보군에서 가장 적합한 것으로 결정됩니다.([오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고)


하지만 템플릿 함수의 경우는 정의시에는 인자가 다르지만, 인스턴스화 과정에서 인자가 같아질 수 있습니다. 이런 경우 **Partial Ordering**을 통해 좀더 특수화된 오버로딩 버전(**좀 더 특수화된 버전** 은 **좀 더 적은 타입을 허용**한다고 생각하시면 됩니다.)을 선택하게 됩니다.


예를 들면,

```cpp
template<typename T> 
int f(T) {return 1;} // #1.

template<typename T> 
int f(T*) {return 2;} // #2. 템플릿 함수 오버로딩. 

int a;
// #1 T == int* 이면 int f(int*)
// #2 T == int 이면 int f(int*)
EXPECT_TRUE(f(&a) == 2); 
```

상기 코드는 다음 단계에 따라 특수화된 버전을 선택합니다.

1. 템플릿 함수 후보 생성  
   
    ```cpp
    int f(int*) // #1. 로부터 T == int* 인 경우
    int f(int*) // #2. 로 부터 T == int 인 경우
    ```

2. 더 특수화된 버전 선택 - **Partial Ordering**
    ```cpp
    template<typename T> 
    int f(T) {return 1;} // #1.

    template<typename T> 
    int f(T*) {return 2;} // #2.
    ```
    
    에서 포인터만 처리하는(좀 더 적은 타입을 허용하는) #2 선택

3. 템플릿이 아닌 일반 함수와 오버로딩 규칙 적용. 이때 일반 함수가 우선 순위가 높음

# 오버로딩 사례

**템플릿 함수보다는 일반 함수**

템플릿 함수 보다는 일반 함수를 선호합니다.

```cpp
int f(int) {return 1;} // #1.

template<typename T>
T f(T) {return 2;}  // #2.

// #1 : f(int)
// #2 : T == int 이면 f(int)
// 일반 버전을 선택함
EXPECT_TRUE(f(1) == 1); 
```

**`T` 보다는 `T*` 보다는 `const T*`**

`T` 보다는 `T*` 가 특수화된 버전이므로, 선택됩니다.

```cpp
template<typename T>
int f(T a) {return 1;} // #1.

template<typename T>
int f(T* a) {return 2;} // #2.

int* p;
// #1 : T == int* 이면 f(int*) 
// #2 : T == int 이면 f(int*)
// 특수화된 버전으로 선택함.
EXPECT_TRUE(f(p) == 2);
```

`T*` 보다는 `const T*` 가 특수화된 버전이므로, 선택됩니다.

```cpp
template<typename T>
int f(T a) {return 1;} // #1.

template<typename T>
int f(T* a) {return 2;} // #2.

template<typename T>
int f(const T* a) {return 3;} // #3.

const int* p;
// #1 : T == const int* 이면 f(const int*) 
// #2 : T == const int 이면 f(const int*) 
// #3 : T == int 이면 f(const int*) 
// 더 특수화된 버전으로 선택함.    
EXPECT_TRUE(f(p) == 3);
```

특수화된 버전 선택시 기본 인자는 무시하고 오버로딩 버전을 결정합니다.

```cpp
template<typename T>
int f(T) {return 1;} // #1.

template<typename T>
int f(T*, int = 1) {return 2;} // #2.

int* p;
// #1 : T == int* 이면 f(int*) 인자 1개 - 인자가 1개라고 무조건 선택되지 않음. 인자 2개인 #2에서 기본값 인자를 뺀 버전과 경합
// #2 : T == int 이면 f(int*, int = 1) 인자 2개
// 더 특수화된 버전으로 선택함.
EXPECT_TRUE(f(p) == 2);
```

**특수화된 인자**

인자가 템플릿 타입을 이용하여 좀 더 적합하게 특수화(좀 더 적은 타입을 허용) 되었다면, 해당 특수화된 버전을 선택합니다.

```cpp
template<typename T>
class A {};

template<typename T>
int f(T&) {return 1;} // #1.

template<typename T>
int f(A<T>&) {return 2;} // #2.   

A<int> a;
// #1 : T == A<int> 이면 f(A<int>&) 
// #2 : T == int 이면 f(A<int>&)
// 더 특수화된 버전으로 선택함.
EXPECT_TRUE(f(a) == 2);
```
다음 경우에는 `A<T, U>` 보다는 `A<T, T>` 가 더 특수화 되어, 선택됩니다.

```cpp
template<typename T, typename U>
class A {};

template<typename T, typename U>
int f(T, A<T, U>* p = 0) {return 1;} // #1.

template<typename T>
int f(T, A<T, T>* p = 0) {return 2;} // #2. 

// #1 : T == int, U == char 이면 f(int, A<int, char>*) 
// #2 : T == int 이면 f(int, A<int, int>*)
// 인자가 더 잘 맞는 버전으로 선택함.
EXPECT_TRUE(f(0, (A<int, char>*)0) == 1); 

// #1 : T == int, U == int 이면 f(int, A<int, int>*) 
// #2 : T == int 이면 f(int, A<int, int>*) 
// 더 특수화된 버전으로 선택함.
EXPECT_TRUE(f(0, (A<int, int>*)0) == 2); 
EXPECT_TRUE(f(0) == 2);      
```     

**연산자 오버로딩 템플릿 함수**

일반적으로 연산자 오버로딩 시에는 멤버 버전과 비멤버 버전중 멤버 버전이 선택됩니다.

```cpp
template<typename T>
class A {
public:
int operator +(int) const {return 1;} // #1.
};

template<typename T>
int operator +(const T&, int) {return 2;} // #2.    

using namespace Overloading_5;
A<int> a;
// #1 : T == A<int> 이면 A<int>&.operator +(int)
// #2 : T == A<int> 이면 operator +(A<int>&, int) 
// 멤버 함수 버전 호출
EXPECT_TRUE( a + 10 == 1);
```

하지만 멤버 버전이 템플릿 멤버 함수라면, 비멤버 버전으로 임시 생성하고 오버로딩을 평가하기 때문에, 모호성 문제가 발생하며, 그냥 대충 비멤버 버전을 호출합니다.(경고가 나오긴 합니다.) 

```cpp
template<typename T>
class A {
public:
    template<typename U>
    int operator +(U) const {return 1;} // #1. 멤버 버전
};

template<typename T, typename U>
int operator +(const T&, U) {return 2;} // #2.   

A<int> a;

// #1 : T == A<int>, U == int 이면 A<int>&.operator +(int)
//      비멤버 버전 변환        operator +(A<int>&, int)             
// #2 : T == A<int>, U == int 이면 operator +(A<int>&, int) 
// (X) 컴파일 경고. #1 과 #2 가 모호하고, 대충 비멤버 버전 호출함
EXPECT_TRUE( a + 10 == 2);
```

따라서 연산자 오버로딩을 템플릿으로 구현해야 한다면, 아예 비멤버 버전으로 작성하는게 좋습니다.

```cpp
template<typename T>
class A {
};

template<typename T, typename U>
int operator +(const A<T>&, U) {return 1;} // #1. 비멤버 버전

template<typename T, typename U>
int operator +(const T&, U) {return 2;} // #2.   

A<int> a;

// #1 : T == int, U == int 이면 operator +(A<int>&, int)
// #2 : T == A<int>, U == int 이면 operator +(A<int>&, int) 
// (O) 더 특수화된 버전으로 선택함.
EXPECT_TRUE( a + 10 == 1);
```

# 오버로딩 실패 사례

**동등한 템플릿 함수**

동등한 템플릿이면 모호하여 컴파일 오류가 발생합니다. 다음의 경우는 `T`와 `const T`로 다릅니다만, 최상위 `const`는 제거되어 결국 동등해집니다.

```cpp
template<typename T>
int f(T a) {return 1;} // #1.

template<typename T>
int f(const T a) {return 2;} // #2. (X) 컴파일 오류. 재정의됨. 최상위 const는 제거되어 f(T a)와 동일

// #1 : T == int 이면 f(int) 
// #2 : T == int 이면 f(const int)인데, 오버로딩시 최상위 const는 제거되므로 f(int)
// (X) 컴파일 오류. 최상위 const는 제거됨. 오버로딩 함수가 중복됨
EXPECT_TRUE(f(a) == 1); 
EXPECT_TRUE(f(b) == 2);
```

**참조자로 인한 모호**

포인터의 경우 `T` 보다는 `T*` 가 특수화된 버전이므로, 선택됩니다만, 참조자는 붙이거나 뗄 수 있습니다. 다음 코드에서는 #1, #2 모두 `f(int&)` 버전을 제공할 수 있기 때문에 모호성 오류가 발생합니다.

```cpp
template<typename T>
int f(T) {return 1;}  // #1.

template<typename T>
int f(T&) {return 2;} // #2.

int a = 0;
int& b = a;

// #1 : T == int 이면 f(int), 
//      T == int& 이면 f(int&) 
// #2 : T == int 이면 f(int&)
// (X) 컴파일 오류. 어느것을 호출할지 모호함.
EXPECT_TRUE(f(a) == 1);
EXPECT_TRUE(f(b) == 2);
```

**특수화 판단 모호**

어느것이 더 특수화 되었는지 판단하기 어려울때 컴파일 오류가 발생합니다. 다음 코드에서는 #1, #2 모두 `f(int, int*)` 버전을 제공할 수 있기 때문에 모호성 오류가 발생합니다.

```cpp
template<typename T>
int f(T, T*) {return 1;}   // #1.

template<typename T>
int f(T, int*) {return 2;} // #2.

char ch;
int i;
int* p;

// #1 : T == char 이면 f(char, char*)
// #2 : T == char 이면 f(char, int*) 
// 인자 타입이 일치하는 #2를 선택함.
EXPECT_TRUE(f(ch, p) == 2);

// #1 : T == int 이면 f(int, int*)
// #2 : T == int 이면 f(int, int*) 
// (X) 컴파일 오류. 어느것을 호출할지 모호함.
EXPECT_TRUE(f(i, p) == 2);
```

