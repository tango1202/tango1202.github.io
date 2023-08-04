---
layout: single
title: "#3. [고전 C++ STL] 템플릿 특수화, 부분 특수화"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 템플릿 함수는 특수화를 하지 마라. 템플릿 부분 특수화와 우선순위가 혼동된다.

# 개요

C++ 템플릿은 특정 타입에 대한 버전을 재정의 하여 타입에 대한 다형성을 지원합니다. 인스턴스화 하여 호출시 좀 더 특수화된 버전을 선택해서 실행합니다.

**좀 더 특수화된 버전** 은 **좀 더 적은 타입을 허용**한다고 생각하시면 됩니다. 
 
# 탬플릿 특수화

템플릿 특수화를 위해서는 다음 코드처럼 `template<>`을 이용하여 원하는 타입에 대해 재정의하면 됩니다.

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
``````

**템플릿 함수 특수화**

템플릿 함수의 경우도 `template<>`을 이용하여 정의합니다.

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

**템플릿 멤버 함수 및 템플릿 중첩 클래스 특수화**

템플릿 멤버 함수의 경우도 `template<>`을 이용하여 정의합니다. 단, 템플릿 멤버 함수를 특수화 하면, 상위 템플릿도 특수화 하여야 합니다.

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

# 탬플릿 부분 특수화

템플릿 부분 특수화는 기존 타입에서 일부분을 특수화한 경우입니다.

다음은 주 템플릿에서 포인터형만을 특수화한 예제입니다. 

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

템플릿 부분 특수화가 되어 잇는 경우, 해당 멤버 함수의 정의부를 분리하면, 부분 특수화 정의 뿐 아니라 전체 특수화 정의도 할 수 있습니다. 

```cpp
// 주 탬플릿
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

# 탬플릿 특수화 우선 순위

템플릿 특수화와 템플릿 부분 특수화를 혼용할 경우 **좀 더 특수화된 버전** 을 사용합니다.

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

# 탬플릿 함수에서 특수화와 부분 특수화 우선 순위 오류

템플릿 클래스의 경우는 잘 동작하지만, 템플릿 함수에서는 템플릿 특수화와 템플릿 부분 특수화간에 어느것이 더 특수화 되었는지 명확히 모를 때가 있습니다.

다음 코드는 기대하는 대로, #3이 호출됩니다.

```cpp
template<typename T> 
int f(T) {return 1;} // #1

template<typename T> 
int f(T*) {return 2;} // #2. 템플릿 부분 특수화

template<> 
int f(int*) {return 3;} //#3. 템플릿 특수화 

int a;
EXPECT_TRUE(f(a) == 1);

// (△) 비권장. T* 보다는 int*가 더 특수화 되었으니, 
// 템플릿 특수화 버전이 실행됩니다.
// 하지만 호출되는 이유는 다른데에 있습니다.
EXPECT_TRUE(f(&a) == 3); 
```

하지만 #2와 #3의 순서를 변경하면 가장 마지막에 정의된 #2가 호출되고, 명시적으로 호출하면 #3이 호출됩니다.

```cpp
template<typename T> 
int f(T) {return 1;} // #1

template<> 
int f(int*) {return 3;} //#3. 템플릿 특수화 

template<typename T> 
int f(T*) {return 2;} // #2. 템플릿 부분 특수화

int a;
EXPECT_TRUE(f(a) == 1);

// (△) 비권장. 정의 순서를 바꿨더니 #2가 호출됩니다.
EXPECT_TRUE(f(&a) == 2); 
EXPECT_TRUE(f<>(&a) == 2);

// (△) 비권장. 명시적으로 호출하면 #3이 호출됩니다.
EXPECT_TRUE(f<int*>(&a) == 3);
```

GCC의 버그일까요? 정의 순서에 따라 결과가 다르다니요. 템플릿 함수 특수화는 사용하지 않고 다음처럼 일반 함수로 사용하여 오버로딩 하면, 언뜻 정상이 되는 듯 합니다.

하지만, 이경우도 어이없는 결과가 나옵니다. `f<int*>(&a)`와 같이 명시적으로 템플릿 부분 특수화 버전을 호출하려고 하면, 아예 주 템플릿인 `f(T)`가 호출되어 버립니다. 향후 유지보수시 누군가가 템플릿 부분 특수화를 사용할 수 있으므로, 템플릿 함수는 부분 특수화만 허용하고, 템플릿 특수화를 아예 하지 않도록 규정하는게 좋지 않을까 싶습니다.

```cpp
template<typename T> 
int f(T) {return 1;} // #1

int f(int*) {return 3;} //#3. 일반 함수입니다.

template<typename T> 
int f(T*) {return 2;} // #2. 템플릿 부분 특수화

int a;
char ch;
EXPECT_TRUE(f(a) == 1);

// (O) 일반 함수를 호출합니다.
EXPECT_TRUE(f(&a) == 3); 

// (O) 템플릿 부분 특수화 버전을 호출합니다.
EXPECT_TRUE(f(&ch) == 2); 

// (△) 비권장. 억지로 템플릿 부분 특수화 버전을 호출을 시도 하면 주 템플릿 버전이 실행됩니다.
EXPECT_TRUE(f<int*>(&a) == 1);
```

# 템플릿에서 연산자 오버로딩 멤버 버전 전역 버전의 우선 순위 오류


공식적으로, 두 함수 템플릿 중 어느 것이 더 특수화되었는지 결정하기 위해 부분 순서 지정 프로세스는 먼저 다음과 같이 두 템플릿 중 하나를 변환합니다.

매개 변수 팩을 포함한 각 형식, 비 형식 및 템플릿 매개 변수 (C ++ 11 이후)에 대해 고유 한 가상 형식, 값 또는 템플릿이 생성되어 템플릿의 함수 형식으로 대체됩니다.

비교되는 두 함수 템플릿 중 하나만 멤버 함수이고 해당 함수 템플릿이 일부 클래스의 비정적 멤버인 경우 새 첫 번째 매개 변수가 매개 변수 목록에 삽입됩니다. 

함수 템플릿의 cv 한정자로 cv 를 사용하고 함수 템플릿의 ref 한정자로 ref 를 사용할 때 (C ++ 11 이후) ref 가 && 또는 ref 가 존재하지 않는 한 새 매개 변수 유형은 cv 입니다. 

다른 템플릿의 첫 번째 매개 변수에는 rvalue 참조 유형이 있습니다.이 경우 유형은 cv A & & (C ++ 11 이후)입니다.

이는 연산자의 순서를 지정하는 데 도움이 되며, 연산자는 멤버 함수와 비멤버 함수 모두로 조회됩니다. AA&

```cpp
struct A {};
 
template<class T>
struct B
{
    template<class R>
    int operator*(R&); // #1
};
 
template<class T, class R>
int operator*(T&, R&); // #2
 
int main()
{
    A a;
    B<A> b;
    b * a; // template argument deduction for int B<A>::operator*(R&) gives R=A 
           //                             for int operator*(T&, R&), T=B<A>, R=A
 
    // For the purpose of partial ordering, the member template B<A>::operator*
    // is transformed into template<class R> int operator*(B<A>&, R&);
 
    // partial ordering between 
    //     int operator*(   T&, R&)  T=B<A>, R=A
    // and int operator*(B<A>&, R&)  R=A 
    // selects int operator*(B<A>&, A&) as more specialized
}

```