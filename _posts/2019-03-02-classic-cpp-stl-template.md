---
layout: single
title: "#2. [고전 C++ STL] 템플릿"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 템플릿 클래스

템플릿 클래스는 하기와 같이 정의되고 인스턴스화 됩니다.

```cpp
// 템플릿 클래스 정의부 - 코드가 생성되지 않은 상태
template<typename T> 
class MyClass {}; 

// 템플릿 인스턴스부 - 이때 코드가 생성됨
MyClass<int> myClass;  
```

1. `typename` : `class` 키워드를 사용해도 됩니다. 클래스로 한정하지 않고 타입의 이름이란 의미로 `typename`을 더 선호합니다.
2. `T` : 템플릿 인자(Parameter)입니다. 향후 인스턴스화시 구체 타입으로 대체 됩니다.
3. `int` : 템플릿 인자 `T`가 대체될 인수(Argument)입니다.  

# 템플릿 정의부와 인스턴스부

템플릿은 개체 타입만 변경하여 클래스나 함수를 생성할 수 있는 틀이고, 인스턴스화를 거쳐 클래스나 함수 코드를 생성합니다.

템플릿은 템플릿 정의부와 템플릿 인스턴스화 부분으로 나뉩니다. 

1. 템플릿 정의부 : 아직 타입이 구체화 되지 않은 상태(코드가 생성되지 않은 상태) 
2. 템플릿 인스턴스부 : 지정한 타입과 바인딩되어 구체적인 클래스를 생성(코드가 생성된 상태)

다음 코드는 클래스 `B`에서 클래스 `T`를 상속받고, `T::TType`으로 멤버 변수를 정의합니다. `TType`이 `private`이기 때문에 컴파일 오류가 나와야 하나 아직 인스턴스 부가 없어 컴파일 오류가 없습니다.

```cpp
// 멤버 엑세스 지정
template<typename T> 
class A {
private:
    typedef T TType;
};  

template<typename T> 
class B : public T { // T로 A<int> 를 전달할 예정임. A<int>::TType은 int이나 private여서 접근 불가
    typename T::TType m_Member; // typename은 종속 타입 참고
};
```

다음처럼 인스턴스화 하면 그제야 코드를 생성하면서 컴파일 오류가 발생합니다.

```cpp
B<A<int>> b; // (X) 컴파일 오류. 템플릿 인스턴스화시 private 멤버 접근 오류 발생 
```
# 명시적 인스턴스화

클래스 템플릿 자체는 형식, 개체 또는 다른 엔터티가 아닙니다. 템플릿 정의만 포함된 소스 파일에서 코드가 생성되지 않습니다. 어떤 코드가 나타나기 위해서는, 템플릿이 인스턴스화되어야 합니다: 컴파일러가 실제 클래스(또는 함수 템플릿에서 함수)를 생성할 수 있도록 템플릿 인수가 제공되어야 합니다.

```cpp
namespace N 
{
    template<class T> 
    class Y // template definition
    { 
        void mf() {} 
    }; 
}
 
// template class Y<int>; // error: class template Y not visible in the global namespace
using N::Y;
// template class Y<int>; // error: explicit instantiation outside 
                          // of the namespace of the template
template class N::Y<char*>;       // OK: explicit instantiation
template void N::Y<double>::mf(); // OK: explicit instantiation
```

# 템플릿 인수 명시적 인스턴스화 

불완전한 형식 지정

```cpp
template<typename T>
class X {}; // class template
 
struct A;            // incomplete type
typedef struct {} B; // type alias to an unnamed type
 
int main()
{
    X<A> x1;  // OK: 'A' names a type
    X<A*> x2; // OK: 'A*' names a type
    X<B> x3;  // OK: 'B' names a type
}
```

# 암시적 인스턴스화

코드가 완전히 정의된 형식이 필요한 컨텍스트의 템플릿을 참조하거나 형식의 완전성이 코드에 영향을 주고 이 특정 형식이 명시적으로 인스턴스화되지 않은 경우 암시적 인스턴스화가 발생합니다. 예를 들어, 이 형식의 개체가 생성되지만 이 형식에 대한 포인터가 생성되는 경우는 그렇지 않습니다.

이것은 클래스 템플릿의 멤버에 적용됩니다: 멤버가 프로그램에서 사용되지 않는 한, 멤버는 인스턴스화되지 않으며 정의가 필요하지 않습니다.

```cpp
template<class T> 
struct Z // template definition
{
    void f() {}
    void g(); // never defined
}; 
 
template struct Z<double>; // explicit instantiation of Z<double>
Z<int> a;                  // implicit instantiation of Z<int>
Z<char>* p;                // nothing is instantiated here
 
p->f(); // implicit instantiation of Z<char> and Z<char>::f() occurs here.
        // Z<char>::g() is never needed and never instantiated:
        // it does not have to be defined
```

# 템플릿 클래스 별칭

템플릿 클래스는 다음과 같이 별칭을 사용할 수 있습니다.

```cpp
template<typename T>
class A {};

template<typename T>
using Alias = A<T>; // A의 별칭

Alias<int> alias;
```

# 템플릿 함수

템플릿 함수는 다음처럼 정의하고 인스턴스화 합니다.

```cpp
template <typename T>
T Plus(T left, T right) {
    return left + right;
}

EXPECT_TRUE(Plus<int>(10, 10) == 20);
EXPECT_TRUE(Plus<char>('a', 1) == 'b');
```

명시적인 타입을 생략하고 인수의 타입에 따라 추론이 가능합니다. 

```cpp
EXPECT_TRUE(Plus(10, 10) == 20); // (O) 인수로부터 int가 추론됨
EXPECT_TRUE(Plus<>(10, 10) == 20); // (O) <>를 기재하여 템플릿 함수를 명시하고, 인수로부터 추론

EXPECT_TRUE(Plus('a', 1) == 'b'); // (X) 인수가 int, char로 각각 다르므로 추론이 어려움 
EXPECT_TRUE(Plus('a', static_cast<char>(1)) == 'b'); // (O) 인수로부터 char 가 추론됨
```

템플릿 인수 추론의 좀더 상세한 내용은 [템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)을 참고하시기 바랍니다.


멤버 함수, 중첩 클래스로 템플릿 사용 가능

(가상 함수는 지원하지 않습니다.)

```cpp

#include <algorithm>
#include <iostream>
#include <string>
#include <vector>
 
struct Printer
{
    // generic functor
    std::ostream& os;
    Printer(std::ostream& os) : os(os) {}
    template<typename T>
    void operator()(const T& obj) { os << obj << ' '; } // member template
};
 
int main()
{
    std::vector<int> v{1,2,3};
    std::for_each(v.begin(), v.end(), Printer(std::cout));
    std::string s{"abc"};
    std::ranges::for_each(s, Printer(std::cout));
}

```

# 특수화 지원

```cpp
struct A
{
    template<class T> struct B;        // primary member template
    template<class T> struct B<T*> {}; // OK: partial specialization
//  template<> struct B<int*> {};      // OK via CWG 727: full specialization
};
template<> struct A::B<int*> {};       // OK
template<class T> struct A::B<T&> {};  // OK
```

# 선언과 정의 분리

바깥쪽 클래스 선언이 클래스 템플릿인 경우 멤버 템플릿이 클래스 본문 외부에 정의되면 두 개의 템플릿 매개 변수 집합이 필요한데, 하나는 바깥쪽 클래스에 대한 것이고 다른 하나는 바깥쪽 클래스에 대한 것입니다.

```cpp
template<typename T1>
struct string
{
    // member template function
    template<typename T2>
    int compare(const T2&);
    // constructors can be templates too
    template<typename T2>
    string(const std::basic_string<T2>& s) { /*...*/ }
};
// out of class definition of string<T1>::compare<T2> 
template<typename T1> // for the enclosing class template
template<typename T2> // for the member template
int string<T1>::compare(const T2& s) { /* ... */ }
```

# 소멸자, 복사생성자는 템플릿이 안됨

# 템플릿은 virtual 일 수 없음

# 동일 함수 오버로딩

```cpp
template<typename T>
struct A
{
    void f(int); // non-template member
 
    template<typename T2>
    void f(T2); // member template
};
 
//template member definition
template<typename T>
template<typename T2>
void A<T>::f(T2)
{
    // some code
}
 
int main()
{
    A<char> ac;
    ac.f('c'); // calls template function A<char>::f<char>(char)
    ac.f(1);   // calls non-template function A<char>::f(int)
    ac.f<>(1); // calls template function A<char>::f<int>(int)
}
```