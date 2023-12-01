---
layout: single
title: "#3. [고전 C++ STL] 템플릿 특수화, 부분 특수화"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 다형적 동작을 위해 [템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94), [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94), [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94), [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 이용하라.
> * [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)을 정의할때 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)과 특수화 함수의 순서를 지켜라.

# 개요

C++ [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)은 특정 타입에 대한 버전을 재정의 하여 타입에 대한 다형성을 지원합니다. 

인스턴스화시 일반화된 주 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/) 버전과 여러 특수화 버전 중에서 **좀 더 특수화된 버전** 을 선택해서 실행합니다.(**좀 더 특수화된 버전** 은 **좀 더 적은 타입을 허용**한다고 생각하시면 됩니다.)
 
# 템플릿 특수화

[템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)를 위해서는 다음 코드처럼 `template<>`을 이용하여 원하는 타입에 대해 재정의하면 됩니다.

```cpp
template<typename T>
class A {
public:
    int f() {return 1;} // #1    
};

template<>
class A<int> {
public:
    int f() {return 2;} // #2    
};

A<char> a;
A<int> b;

EXPECT_TRUE(a.f() == 1);
EXPECT_TRUE(b.f() == 2); // int에 특수화된 버전 호출
```

# 함수 템플릿 특수화

[함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)의 경우도 `template<>`을 이용하여 정의합니다.

```cpp
template<typename T>
int Func(T val) {return 1;} // #1

template<>
int Func<int>(int val) {return 2;} // #2

// 혹은 추론되는 타입을 아예 빼서 정의할 수 있습니다.
template<>
int Func(int val) {return 2;} // #2


EXPECT_TRUE(Func('a') == 1);
EXPECT_TRUE(Func(10) == 2); // int에 특수화된 버전 호출
```

# 템플릿 멤버 함수 및 템플릿 중첩 클래스 특수화

[템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/) [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)의 경우도 `template<>`을 이용하여 정의합니다. 단, [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/) [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 특수화 하면, 상위 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)도 특수화 하여야 합니다.

```cpp
template<typename T>
class A {
public:
    template<typename U>
    class B {
    public:
        template<typename V>
        int f(V val) {return 1;} // #1
    };
};

template<> // 바깥쪽 주 템플릿도 특수화
template<> // 안쪽 중첩 클래스 특수화
template<> // 템플릿 멤버 함수 특수화
int A<int>::B<int>::f<int>(int val) {return 2;} // #2

// (X) 컴파일 오류. 템플릿 멤버 함수를 특수화 하면, 상위 템플릿도 특수화 해야 합니다.
// template<typename T> 
// template<typename U> 
// template<> 
// int A<T>::B<U>::f<int>(int val) {return 2;} // #2

A<char>::B<int> a;
A<int>::B<int> b;

EXPECT_TRUE(a.f('a') == 1);
EXPECT_TRUE(a.f(10) == 1); 
EXPECT_TRUE(b.f('a') == 1); 
EXPECT_TRUE(b.f(10) == 2); // int에 특수화된 버전 호출
```

# 템플릿 부분 특수화

[템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)는 기존 타입에서 일부분을 특수화한 경우입니다.

다음은 주 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)에서 포인터형만을 특수화한 예제입니다. 

```cpp
template<typename T>
class A { // 주 템플릿
public:
    int f() {return 1;} // #1. 
};

template<typename T>
class A<T*> { // 템플릿 부분 특수화 
public:
    int f() {return 2;} // #2. 
};

A<int> a;
A<int*> b;

EXPECT_TRUE(a.f() == 1);
EXPECT_TRUE(b.f() == 2); // 템플릿 부분 특수화 버전
``` 

# 템플릿 부분 특수화에서 선언과 정의 분리

[템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)가 되어 있는 경우, 해당 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)의 정의부를 분리하면, [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94) 정의 뿐 아니라 전체 특수화 정의도 할 수 있습니다. 

```cpp
// 주 템플릿
template<typename T, typename U> 
class A {
public:
    int f(); 
};
 
// 부분 특수화
template<typename T>
class A<T, int> {
public:
    int f();
};

// 주 템플릿 정의
template<typename T, typename U>
int A<T, U>::f() {return 1;}  

// 부분 특수화 정의
template<typename T>
int A<T, int>::f() {return 2;}
 
// 전체 특수화 정의도 가능
template<>
int A<char, int>::f() {return 3;}

A<int, char> a; // 주 템플릿
A<int, int> b;// U == int 인 부분 특수화
A<char, int> c; // 전체 특수화

EXPECT_TRUE(a.f() == 1);
EXPECT_TRUE(b.f() == 2);
EXPECT_TRUE(c.f() == 3);
```

# 템플릿 특수화 우선 순위

[템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)와 [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)를 혼용할 경우 **좀 더 특수화된 버전** 을 사용합니다.

```cpp
template<typename T>
class A { // 주 템플릿
public:
    int f() {return 1;} // #1. 
};

template<typename T>
class A<T*> { // 템플릿 부분 특수화 
public:
    int f() {return 2;} // #2. 
};

template<>
class A<int*> { // 템플릿 특수화 
public:
    int f() {return 3;} // #3. 
};  
        
A<int> a;
A<int*> b;
A<char*> c;

EXPECT_TRUE(a.f() == 1);
EXPECT_TRUE(b.f() == 3); // 템플릿 특수화 버전
EXPECT_TRUE(c.f() == 2); // 템플릿 부분 특수화 버전
```

# 함수 템플릿 오버로딩

[함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)은 [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)를 지원하지 않으며, 대신 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 지원합니다. 모양은 마치 [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)와 비슷합니다.

다음 코드에서 `f(&a)` 호출시 #1과 #2은 각각 다음처럼 인스턴스화 후보로 만들어 집니다.

1. #1 : `T == int*` 라면, `f<int*>(int*)` 로 호출 가능합니다.
2. #2 : `T == int` 라면, `f<int>(int*)` 로 호출 가능합니다.

상기 인스턴스화 후보 목록에서 좀더 [특수화된 함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)은 2번이므로(포인터만 호출 가능), `int f(T*)` 가 호출됩니다.

```cpp
template<typename T> 
int f(T) {return 1;} // #1

template<typename T> 
int f(T*) {return 2;} // #2. 함수 템플릿 오버로딩. 부분 특수화와 모양이 비슷합니다.

int a;
EXPECT_TRUE(f(a) == 1); // 주 함수 템플릿 버전이 실행됩니다. T == int, f<int>(int)
EXPECT_TRUE(f(&a) == 2); // 템플릿 오버로딩 버전이 실행됩니다. T == int, f<int>(int*)
```

# 함수 템플릿 오버로딩과 함수 템플릿 특수화의 혼용

[함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)의 모양이 [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)와 유사하나 굳이 구별하여 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)이라고 하는 건, 함수 선택 과정에서 차이가 있기 때문입니다.

1. [템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)와 [템플릿 부분 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94) : 특수화된 목록에서 좀 더 특수화된 항목 선택
2. [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) : [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 목록에서 좀더 특수화된 항목 선택후, 선택된 [함수 템플릿의 좀더 특수화된 항목](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 선택

상기의 방식 차이 때문에 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)과 [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)가 혼용되면, 순서에 주의해야 합니다. 

다음 코드에서 [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전인 #3을 #1과 #2 사이에 기재 했습니다. #3 입장에서는 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 #1 밖에 없으므로, #3은 #1의 특수화 버전입니다.

`f(&a)` 호출시 #1, #3, #2은 각각 다음처럼 인스턴스화 후보로 만들어 집니다.

1. #1 : `T == int*` 라면, `f<int*>(int*)` 로 호출 가능합니다.
2. #3 : #1의 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 채택되었을때, `T == int*` 라면 호출됩니다.
3. #2 : `T == int` 라면, `f<int>(int*)` 로 호출 가능합니다.

`f(&a)` 호출시 가장 적합한 것은 특수화 버전인 #3인 것 같지만, #3은 #1의 [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전이고, #1과 #2중 좀더 특수화된 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)은 #2이므로, `int f(T*)` 가 호출됩니다.

```cpp
template<typename T> 
int f(T) {return 1;} // #1

template<> 
int f(int*) {return 3;} //#3. #1의 템플릿 특수화 버전. T== int*, int f<int*>(int*)

template<typename T> 
int f(T*) {return 2;} // #2. 함수 템플릿 오버로딩. 

int a;
EXPECT_TRUE(f(&a) == 2); // 템플릿 오버로딩 버전이 실행됩니다. T == int, f<int>(int*)
```

타입을 명시적으로 작성하면 하기와 같이 호출됩니다.

```cpp
int a;
EXPECT_TRUE(f<int*>(&a) == 3); // #1 함수 템플릿이 선택되고, 이중 특수화 버전인 #3 이 호출됩니다.
EXPECT_TRUE(f<int>(&a) == 2); // #2 함수 템플릿이 호출됩니다.
```

이번에는 [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전인 #3 의 순서를 바꿔, #2 다음에 정의하면 결과가 바뀝니다. #3은 #1과 #2중 좀더 적합한 #2의 특수화 버전이 되며, 다음처럼 인스턴스화 후보가 만들어 집니다.

1. #1 : `T == int*` 라면, `f<int*>(int*)` 로 호출 가능합니다.
2. #2 : `T == int` 라면, `f<int>(int*)` 로 호출 가능합니다.
3. #3 : #2의 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 채택되었을때, `T == int` 라면 호출됩니다.

`f(&a)` 호출시 #1과 #2중 좀더 [특수화된 함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)은 #2이고, 이의 int 특수화 버전인 `int f(int*)` 가 호출됩니다.

```cpp
template<typename T> 
int f(T) {return 1;} // #1

template<typename T> 
int f(T*) {return 2;} // #2. 함수 템플릿 오버로딩. 

template<> 
int f(int*) {return 3;} //#3. #2의 템플릿 특수화 버전. T== int, int f<int>(int*)

int a;
EXPECT_TRUE(f(&a) == 3); // 템플릿 특수화 버전이 실행됩니다. T == int, f<int>(int*)
```

타입을 명시적으로 작성하면 하기와 같이 호출됩니다.

```cpp
int a;
EXPECT_TRUE(f<int*>(&a) == 1); // #1 함수 템플릿이 선택됩니다.
EXPECT_TRUE(f<int>(&a) == 3); // #2 함수 템플릿이 선택되고, 이중 특수화 버전인 #3 이 호출됩니다.
```

[함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전은 이처럼 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전의 충분한 정보가 없다면, 엉뚱한 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)을 특수화 할 수 있습니다. 따라서, 다음의 순서로 [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전과 [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전을 작성해야 합니다.


1. 주 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF) 정의
2. [함수 템플릿 오버로딩](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전 정의
3. [함수 템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94) 버전 정의

