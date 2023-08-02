---
layout: single
title: "#5. [고전 C++ STL] 템플릿 특수화, 부분 특수화"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---



**tamplate 특수화**

특정 타입에 대한 버전 재정의(클래스, 함수 모두 가능)

```cpp
template<typename T>
class MyClass
{
};
template<>
class MyClass<int> // int 형에 대해 특수
화
{
};
```

```cpp
template<typename T>
void MyFunc(T arg) {}
template<>
void MyFunc<int>(int arg) {}
혹은, 추론 가능한 int를 아예 빼서
template<>
void MyFunc(int arg) {}
```

# 템플릿 특수화에서 선언과 정의 분리

```cpp
template<typename T>
struct A
{
    struct B {};      // member class 
 
    template<class U> // member class template
    struct C {};
};
 
template<> // specialization
struct A<int> 
{
    void f(int); // member function of a specialization
};
// template<> not used for a member of a specialization
void A<int>::f(int) { /* ... */ }
 
template<> // specialization of a member class
struct A<char>::B
{
    void f();
};
// template<> not used for a member of a specialized member class either
void A<char>::B::f() { /* ... */ }
 
template<> // specialization of a member class template
template<class U>
struct A<char>::C
{
    void f();
};
 
// template<> is used when defining a member of an explicitly
// specialized member class template specialized as a class template
template<>
template<class U>
void A<char>::C<U>::f() { /* ... */ }
```

# 중첩 클래스 특수화

```cpp
template<class T1>
struct A
{
    template<class T2>
    struct B
    {
        template<class T3>
        void mf();
    };
};
 
template<>
struct A<int>;
 
template<>
template<>
struct A<char>::B<double>;
 
template<>
template<>
template<>
void A<char>::B<char>::mf<double>();
```


# 부분 탬플릿 특수화

```cpp
template<typename T>
class MyClass
{
};
template<typename T>
class MyClass<T*> // 모든 포인터에 대해 특수화
{
};
```

```cpp
template<class T1, class T2, int I>
class A {};             // primary template
 
template<class T, int I>
class A<T, T*, I> {};   // #1: partial specialization where T2 is a pointer to T1
 
template<class T, class T2, int I>
class A<T*, T2, I> {};  // #2: partial specialization where T1 is a pointer
 
template<class T>
class A<int, T*, 5> {}; // #3: partial specialization where
                        //     T1 is int, I is 5, and T2 is a pointer
 
template<class X, class T, int I>
class A<X, T*, I> {};   // #4: partial specialization where T2 is a pointer
```

순서 : 더 특수화된 템플릿에 우선 순위가 있다. 
1. template<> class MyClass<int>
2. template<typename T> class MyClass<T*>
3. template<typename T> class MyClass


# 부분 템플릿 특수화의 이름 조회

부분 템플릿 특수화는 이름 조회를 통해 찾을 수 없습니다. 이름 조회를 통해 기본 템플릿을 찾은 경우에만 부분 특수화가 고려됩니다. 특히 기본 템플릿을 표시하는 using 선언은 부분 특수화도 표시합니다.

```cpp
namespace N
{
    template<class T1, class T2> class Z {}; // primary template
}
using N::Z; // refers to the primary template
 
namespace N
{
    template<class T> class Z<T, T*> {};     // partial specialization
}
Z<int, int*> z; // name lookup finds N::Z (the primary template), the
                // partial specialization with T = int is then used

```

# 부분 특수화 호출 순서

```cpp
template<class T1, class T2, int I>
class A {};             // primary template
 
template<class T, int I>
class A<T, T*, I> {};   // #1: partial specialization where T2 is a pointer to T1
 
template<class T, class T2, int I>
class A<T*, T2, I> {};  // #2: partial specialization where T1 is a pointer
 
template<class T>
class A<int, T*, 5> {}; // #3: partial specialization where
                        //     T1 is int, I is 5, and T2 is a pointer
 
template<class X, class T, int I>
class A<X, T*, I> {};   // #4: partial specialization where T2 is a pointer

A<int, int, 1> a1;   // no specializations match, uses primary template
A<int, int*, 1> a2;  // uses partial specialization #1 (T=int, I=1)
A<int, char*, 5> a3; // uses partial specialization #3, (T=char)
A<int, char*, 1> a4; // uses partial specialization #4, (X=int, T=char, I=1)
A<int*, int*, 2> a5; // error: matches #2 (T=int, T2=int*, I=2)
                     //        matches #4 (X=int*, T=int, I=2)
                     // neither one is more specialized than the other
```

# 부분 템플릿 특수화에서 선언과 정의 분리

```cpp
template<class T, int I> // primary template
struct A
{
    void f(); // member declaration
};
 
template<class T, int I>
void A<T, I>::f() {}      // primary template member definition
 
// partial specialization
template<class T>
struct A<T, 2>
{
    void f();
    void g();
    void h();
};
 
// member of partial specialization
template<class T>
void A<T, 2>::g() {}
 
// explicit (full) specialization
// of a member of partial specialization
template<>
void A<char, 2>::h() {}
 
int main()
{
    A<char, 0> a0;
    A<char, 2> a2;
    a0.f(); // OK, uses primary template’s member definition
    a2.g(); // OK, uses partial specialization's member definition
    a2.h(); // OK, uses fully-specialized definition of
            // the member of a partial specialization
    a2.f(); // error: no definition of f() in the partial
            // specialization A<T,2> (the primary template is not used)
}
```

# 특수화

비공식적으로 "A는 B보다 더 전문화되어 있습니다"는 "A가 B보다 적은 유형을 허용합니다"를 의미합니다.

공식적으로, 두 함수 템플릿 중 어느 것이 더 특수화되었는지 결정하기 위해 부분 순서 지정 프로세스는 먼저 다음과 같이 두 템플릿 중 하나를 변환합니다.

매개 변수 팩을 포함한 각 형식, 비 형식 및 템플릿 매개 변수 (C ++ 11 이후)에 대해 고유 한 가상 형식, 값 또는 템플릿이 생성되어 템플릿의 함수 형식으로 대체됩니다
비교되는 두 함수 템플릿 중 하나만 멤버 함수이고 해당 함수 템플릿이 일부 클래스의 비정적 멤버인 경우 새 첫 번째 매개 변수가 매개 변수 목록에 삽입됩니다. 함수 템플릿의 cv 한정자로 cv 를 사용하고 함수 템플릿의 ref 한정자로 ref 를 사용할 때 (C ++ 11 이후) ref 가 && 또는 ref 가 존재하지 않는 한 새 매개 변수 유형은 cv 입니다. 다른 템플릿의 첫 번째 매개 변수에는 rvalue 참조 유형이 있습니다.이 경우 유형은 cv A & & (C ++ 11 이후)입니다. 이는 연산자의 순서를 지정하는 데 도움이 되며, 연산자는 멤버 함수와 비멤버 함수 모두로 조회됩니다. AA&

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

```cpp
template<class T>
void f(T);        // template #1
template<class T>
void f(T*);       // template #2
template<class T>
void f(const T*); // template #3
 
void m()
{
    const int* p;
    f(p); // overload resolution picks: #1: void f(T ) [T = const int *]
          //                            #2: void f(T*) [T = const int]
          //                            #3: void f(const T *) [T = int]
 
    // partial ordering:
 
    // #1 from transformed #2: void(T) from void(U1*): P=T A=U1*: deduction ok: T=U1*
    // #2 from transformed #1: void(T*) from void(U1): P=T* A=U1: deduction fails
    // #2 is more specialized than #1 with regards to T
 
    // #1 from transformed #3: void(T) from void(const U1*): P=T, A=const U1*: ok
    // #3 from transformed #1: void(const T*) from void(U1): P=const T*, A=U1: fails
    // #3 is more specialized than #1 with regards to T
 
    // #2 from transformed #3: void(T*) from void(const U1*): P=T* A=const U1*: ok
    // #3 from transformed #2: void(const T*) from void(U1*): P=const T* A=U1*: fails
    // #3 is more specialized than #2 with regards to T
 
    // result: #3 is selected
    // in other words, f(const T*) is more specialized than f(T) or f(T*)
}
```

```cpp
template<class T>
void f(T, T*);   // #1
template<class T>
void f(T, int*); // #2
 
void m(int* p)
{
    f(0, p); // deduction for #1: void f(T, T*) [T = int]
             // deduction for #2: void f(T, int*) [T = int]
 
    // partial ordering:
 
    // #1 from #2: void(T,T*) from void(U1,int*): P1=T, A1=U1: T=U1
    //                                            P2=T*, A2=int*: T=int: fails
 
    // #2 from #1: void(T,int*) from void(U1,U2*): P1=T A1=U1: T=U1
    //                                             P2=int* A2=U2*: fails
 
    // neither is more specialized w.r.t T, the call is ambiguous
}
```

```cpp
template<class T>
void g(T);  // template #1
template<class T>
void g(T&); // template #2
 
void m()
{
    float x;
    g(x); // deduction from #1: void g(T ) [T = float]
          // deduction from #2: void g(T&) [T = float]
 
    // partial ordering:
 
    // #1 from #2: void(T) from void(U1&): P=T, A=U1 (after adjustment), ok
 
    // #2 from #1: void(T&) from void(U1): P=T (after adjustment), A=U1: ok
 
    // neither is more specialized w.r.t T, the call is ambiguous
}
```

```cpp
template<class T>
struct A { A(); };
 
template<class T>
void h(const T&); // #1
template<class T>
void h(A<T>&);    // #2
 
void m()
{
    A<int> z;
    h(z); // deduction from #1: void h(const T &) [T = A<int>]
          // deduction from #2: void h(A<T> &) [T = int]
 
    // partial ordering:
 
    // #1 from #2: void(const T&) from void(A<U1>&): P=T A=A<U1>: ok T=A<U1>
 
    // #2 from #1: void(A<T>&) from void(const U1&): P=A<T> A=const U1: fails
 
    // #2 is more specialized than #1 w.r.t T
 
    const A<int> z2;
    h(z2); // deduction from #1: void h(const T&) [T = A<int>]
           // deduction from #2: void h(A<T>&) [T = int], but substitution fails
 
    // only one overload to choose from, partial ordering not tried, #1 is called
}
```

```cpp
template<class T>
void f(T);         // #1
template<class T>
void f(T*, int = 1); // #2
 
void m(int* ip)
{
    int* ip;
    f(ip); // calls #2 (T* is more specialized than T)
}
```

```cpp
template<class T>
void g(T);       // #1
template<class T>
void g(T*, ...); // #2
 
void m(int* ip)
{
    g(ip); // calls #2 (T* is more specialized than T)
}
```

```cpp
template<class T, class U>
struct A {};
 
template<class T, class U>
void f(U, A<U, T>* p = 0); // #1
template<class U>
void f(U, A<U, U>* p = 0); // #2
 
void h()
{
    f<int>(42, (A<int, int>*)0); // calls #2
    f<int>(42);                  // error: ambiguous
}
```

```cpp
template<class T>
void g(T, T = T()); // #1
template<class T, class... U>
void g(T, U...);    // #2
 
void h()
{
    g(42); // error: ambiguous
}
```

```cpp
template<class T, class... U>
void f(T, U...); // #1
template<class T>
void f(T);       // #2
 
void h(int i)
{
    f(&i); // calls #2 due to the tie-breaker between parameter pack and no parameter
           // (note: was ambiguous between DR692 and DR1395)
}
```

```cpp
template<class T, class... U>
void g(T*, U...); // #1
template<class T>
void g(T);        // #2
 
void h(int i)
{
    g(&i); // OK: calls #1 (T* is more specialized than T)
}
```

```cpp
template<class... T>
int f(T*...);    // #1
template<class T>
int f(const T&); // #2
 
f((int*)0); // OK: selects #2; non-variadic template is more specialized than
            // variadic template (was ambiguous before DR1395 because deduction
            // failed in both directions)
```

```cpp
template<class... Args>
void f(Args... args);        // #1
template<class T1, class... Args>
void f(T1 a1, Args... args); // #2
template<class T1, class T2>
void f(T1 a1, T2 a2);        // #3
 
f();        // calls #1
f(1, 2, 3); // calls #2
f(1, 2);    // calls #3; non-variadic template #3 is more
            // specialized than the variadic templates #1 and #2
```

```cpp
template<class T>
T f(int); // #1
template<class T, class U>
T f(U);   // #2
 
void g()
{
    f<int>(1); // specialization of #1 is explicit: T f(int) [T = int]
               // specialization of #2 is deduced:  T f(U) [T = int, U = int]
 
    // partial ordering (only considering the argument type):
 
    // #1 from #2: T(int) from U1(U2): fails
    // #2 from #1: T(U) from U1(int): ok: U=int, T unused
 
    // calls #1
}
```

```cpp
template<class T>
void f(T);      // #1: template overload
template<class T>
void f(T*);     // #2: template overload
 
void f(double); // #3: non-template overload
template<>
void f(int);    // #4: specialization of #1
 
f('a');        // calls #1
f(new int(1)); // calls #2
f(1.0);        // calls #3
f(1);          // calls #4
``````