---
layout: single
title: "#19. [레거시 C++ STL] 템플릿 메타 프로그래밍"
categories: "legacy-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * (C++11~) [constexpr](https://tango1202.github.io/cpp/modern-cpp-constexpr/)이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다.
> * (C++11~) [static_assert()](https://tango1202.github.io/cpp/modern-cpp-static-assert/)가 추가되어 컴파일 타임 진단이 가능해 졌습니다.
> * (C++11~) [noexcept 연산자](https://tango1202.github.io/cpp/modern-cpp-noexcept/)가 추가되어 해당 함수가 [noexcept](https://tango1202.github.io/cpp/modern-cpp-noexcept/)인지 컴파일 타임에 검사할 수 있습니다.
> * (C++11~) [type_traits](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/)이 추가되어 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환등의 작업이 간편해 졌습니다.
> * (C++11~) [ratio](https://tango1202.github.io/cpp-stl/modern-cpp-stl-ratio/)가 추가되어 소수점 오차없이 유리 분수를 표현할 수 있습니다. 분자와 분모를 따로 저장하여 유리 분수를 표현하며, 유틸리티들을 이용하여 컴파일 타임 유리수(*정수와 분수*) 연산을 지원합니다.
> * (C++14~) [integer_sequence](https://tango1202.github.io/cpp-stl/modern-cpp-stl-integer_sequence/)가 추가되어 컴파일 타임에 정수 타입의 시퀀스를 만들 수 있습니다.
> * (C++17~) [if constexpr](https://tango1202.github.io/cpp/modern-cpp-constexpr/#c17-if-constexpr)이 추가되어 조건에 맞는 부분만 컴파일하고, 그렇지 않은 부분은 컴파일에서 제외할 수 있습니다.
> * (C++20~) [컨셉(concept)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항(requires)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 추가되어 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 줄 수 있습니다.

# 개요

[템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)들은 [인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 되면서 코드를 생성하다 보니 컴파일 타임에 여러가지 프로그래밍이 가능하다는게 발견되었고, 이를 [템플릿 메타 프로그래밍](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-meta-programming/)이라 합니다. 런타임에 수행되던 내용을 컴파일 타임에 가져옴으로서 실행 속도도 많이 향상됩니다.

|항목|내용|
|--|--|
|조건 분기|[템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)과 [템플릿 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)나 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)으로 구현|
|반복|재귀 호출로 구현|
|변수|[열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)으로 구현|

다음은 컴파일 타임에 팩토리얼(*1~N 까지의 곱*)을 구하는 예입니다.

다음 코드를 보면,

1. `Factorial<5>::Val`에서 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)의 값을 가져오면, 재귀적으로 `Factorial<4>::Val`, `Factorial<3>::Val`, `Factorial<2>::Val`, `Factorial<1>::Val` 의 값을 가져와 곱합니다.
2. 마지막으로 `Factorial<0>::Val`은 `1`을 곱하여, 결과값에 영향을 주지 않고, 재귀적 호출을 중단합니다.

```cpp
// Factorial n -1 을 재귀 호출합니다.
template<unsigned int n>
struct Factorial { 
    enum {
        Val = n * Factorial<n-1>::Val
    };  
};
// 0일때 특수화 버전. 더이상 재귀 호출을 안합니다.
template<>
struct Factorial<0> { 
    enum {
        Val = 1
    }; 
};
// 컴파일 타임에 계산된 120이 val에 대입됩니다.
unsigned int val = Factorial<5>::Val; 
EXPECT_TRUE(val == 1 * 2 * 3 * 4 * 5);
```

> *(C++11~) [constexpr](https://tango1202.github.io/cpp/modern-cpp-constexpr/)이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다. [constexpr 함수](
https://tango1202.github.io/cpp/modern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)에 좀더 간결한 `Factorial_14()` 함수를 예시하였습니다.* 

# CloneTraits 구현

[상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/) 관계에 있는 개체들을 관리할때 부모 개체의 포인터를 이용해서 관리하는데요, 관리중 이를 복제하려면 자식 개체를 복제해야 하므로 `Clone()`함수를 이용합니다.(*[가상 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EA%B0%80%EC%83%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고*)

만약 스마트 포인터를 이용한다면, 부모 클래스의 포인터를 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)로 관리하고, 부모 클래스의 `Clone()`함수를 호출하여 복제해야 합니다.

다음 코드에서는 스마트 포인터인 `my_smart_ptr`에서 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)인 `Shape`을 포인터로 관리하고, `Clone()`을 이용하여 구체화된 `Rectangle` 개체를 생성하고 있습니다.

```cpp
template<typename T>
class my_smart_ptr { 
    T* m_Ptr; 
public:
    my_smart_ptr(T* ptr) : 
        m_Ptr(ptr) {}
    ~my_smart_ptr() {
        delete m_Ptr;
    }

private:
    // 테스트와 상관없어서 복사 생성자와 복사 대입 연산자는 private로 막아 두었습니다.
    my_smart_ptr(my_smart_ptr& other) {}
    my_smart_ptr& operator =(my_smart_ptr& other) {return *this;}

public:
    T* GetPtr() {
        return m_Ptr;
    } 
    // T 개체의 Clone()을 호출합니다.
    T* Clone() const {
        if (m_Ptr == NULL) {
            return(NULL);
        }
        return m_Ptr->Clone();
    }
};

// 추상 클래스 입니다.
class Shape {
protected:
    Shape() {} 
    Shape(const Shape& other) {}
public:
    virtual ~Shape() {}
    // (O) 부모 개체에서는 Shape* 으로 리턴합니다.  
    virtual Shape* Clone() const = 0; 
};

class Rectangle : public Shape {
public:
    // (O) 자식 개체에서는 자식 타입으로 리턴합니다.
    // Rectangle의 복사 생성자를 이용하며 복제본을 리턴합니다.
    virtual Rectangle* Clone() const { 
        return new Rectangle(*this); 
    }
};  

my_smart_ptr<Shape> sp1(new Rectangle);
my_smart_ptr<Shape> sp2(sp1.Clone());     

// sp2는 Rectangle 을 관리합니다.
EXPECT_TRUE(typeid(*sp2.GetPtr()).name() == typeid(Rectangle).name());
```

상기 코드는 잘 작동합니다만, `T`에서 `Clone()` 함수를 제공하지 않으면(*예를 들어 `int`등*), `my_smart_ptr` 사용할 수 없습니다. 스마트 포인터를 좀더 범용적으로 사용하게 하려면, 수정이 필요합니다.

**1. 템플릿 특수화를 이용하는 방법**

`my_smart_ptr`의 일반 버전은 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용하고, `Clone`을 제공하는 개체는 `Clone()`함수를 이용하여 특수화 할 수 있습니다. 하지만 이 방법은 [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/) 코드들이 중복되어 권장하지 않습니다.

```cpp
template<typename T>
class my_smart_ptr { 
    T* m_Ptr; 
public:
    my_smart_ptr(T* ptr) : 
        m_Ptr(ptr) {}
    ~my_smart_ptr() {
        delete m_Ptr;
    }

private:
    my_smart_ptr(my_smart_ptr& other) {}
    my_smart_ptr& operator =(my_smart_ptr& other) {return *this;}

public:
    T* GetPtr() {
        return m_Ptr;
    } 
    // T 개체의 복사 생성자를 호출합니다.
    T* Clone() const {
        if (m_Ptr == NULL) {
            return(NULL);
        }
        return new T(*m_Ptr);
    }
};

// 추상 클래스 입니다.
class Shape {
protected:
    Shape() {} 
    Shape(const Shape& other) {}
public:
    virtual ~Shape() {}
    virtual Shape* Clone() const = 0; 
};

class Rectangle : public Shape {
public:
    virtual Rectangle* Clone() const { 
        return new Rectangle(*this); 
    }
};
// Shape은 Clone()을 사용하므로 특수화 합니다.
template<>
class my_smart_ptr<Shape> { 
    Shape* m_Ptr; 
public:
    my_smart_ptr(Shape* ptr) : 
        m_Ptr(ptr) {}
    ~my_smart_ptr() {
        delete m_Ptr;
    }

private:
    my_smart_ptr(my_smart_ptr& other) {}
    my_smart_ptr& operator =(my_smart_ptr& other) {return *this;}

public:
    Shape* GetPtr() {
        return m_Ptr;
    } 
    // Shape 개체의 Clone()을 호출합니다.
    Shape* Clone() const {
        if (m_Ptr == NULL) {
            return(NULL);
        }
        return m_Ptr->Clone();
    }
};  
my_smart_ptr<Shape> sp1(new Rectangle);
my_smart_ptr<Shape> sp2(sp1.Clone());     

// sp2는 Rectangle 을 관리합니다.
EXPECT_TRUE(typeid(*sp2.GetPtr()).name() == typeid(Rectangle).name());

my_smart_ptr<int> sp3(new int);
my_smart_ptr<int> sp4(sp3.Clone());

// sp4는 int 를 관리합니다.
EXPECT_TRUE(typeid(*sp4.GetPtr()).name() == typeid(int).name());
```

**2. Traits를 이용하는 방법**

[타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-traits/)를 만들면 코드가 다른 부분만 [템플릿 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8A%B9%EC%88%98%ED%99%94)를 할 수 있습니다. 다음 코드에서는 `my_smart_ptr`에서 복제시 타입에 따라 다른 `CloneTraits`를 사용하고 있습니다. [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/) 전체를 특수화할 필요가 없어 훨씬 낫습니다. 

하지만 `Clone()`을 사용하는 개체가 있다면 잊지 않고 `CloneTraits` 특수화를 해야 한다는 점이 좀 부담스럽습니다.

```cpp
template<typename T>
class CloneTraits {
public:
    // 일반적인 복제는 복사 생성자를 사용합니다.
    static T* Clone(const T* ptr) {
        if (ptr == NULL) {
            return  NULL;
        }

        return new T(*ptr);
    }
};

template<typename T>
class my_smart_ptr { 
    T* m_Ptr; 
public:
    my_smart_ptr(T* ptr) : 
        m_Ptr(ptr) {}
    ~my_smart_ptr() {
        delete m_Ptr;
    }

private:
    my_smart_ptr(my_smart_ptr& other) {}
    my_smart_ptr& operator =(my_smart_ptr& other) {return *this;}

public:
    T* GetPtr() {
        return m_Ptr;
    } 
    // CloneTraits를 이용하여 복제합니다.
    T* Clone() const {
        return CloneTraits<T>::Clone(m_Ptr);
    }
};

// 추상 클래스 입니다.
class Shape {
protected:
    Shape() {} 
    Shape(const Shape& other) {}
public:
    virtual ~Shape() {}
    virtual Shape* Clone() const = 0; 
};

class Rectangle : public Shape {
public:
    virtual Rectangle* Clone() const { 
        return new Rectangle(*this); 
    }
};
// Shape을 복제할 땐 Clone 함수를 사용합니다.
template<>
class CloneTraits<Shape> {
public:
    static Shape* Clone(const Shape* ptr) 
    {
        if (ptr == NULL) {
            return NULL;
        }

        return ptr->Clone();
    }
};
my_smart_ptr<Shape> sp1(new Rectangle);
my_smart_ptr<Shape> sp2(sp1.Clone());     

// sp2는 Rectangle 을 관리합니다.
EXPECT_TRUE(typeid(*sp2.GetPtr()).name() == typeid(Rectangle).name());

my_smart_ptr<int> sp3(new int);
my_smart_ptr<int> sp4(sp3.Clone());

// sp4는 int 를 관리합니다.
EXPECT_TRUE(typeid(*sp4.GetPtr()).name() == typeid(int).name()); 
```

**3. 템플릿 메타 프로그래밍을 이용하는 방법**

[템플릿 메타 프로그래밍](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-meta-programming/)을 이용한다면, 컴파일 타임에 `CloneTraits`가 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출할지, `Clone()` 함수를 호출할지 스스로 판단하여 분기하게 할 수 있습니다.

먼저, 다음과 같이 [단위 전략 인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%8B%A8%EC%9C%84-%EC%A0%84%EB%9E%B5-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)인 `ICloneable`을 정의합니다. 추후 `CloneTraits`에서 `ICloneable` 을 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)한 개체는 `Clone()`을 이용하여 복제할 예정입니다.

```cpp
// Clone()을 제공하는 단위 인터페이스
class ICloneable {
private:
    ICloneable(const ICloneable& other) {} // 인터페이스여서 외부에서 사용 못하게 복사 생성자 막음
    ICloneable& operator =(const ICloneable& other) {return *this;} // 인터페이스여서 외부에서 사용 못하게 복사 대입 연산자 막음    
protected:
    ICloneable() {} // 인터페이스여서 상속한 개체에서만 생성할 수 있게함 
    ~ICloneable() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다. 
public:
    virtual ICloneable* Clone() const = 0; // 순가상 함수입니다. 자식 클래스에서 구체화 해야 합니다.
};
```

다음의 `IsDerivedFrom` [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)은 `D`가 `B`를 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)했는지 검사하는 개체입니다.

1. `Yes`와 `No`는 `Test()`함수의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)으로 사용되며, 어떤 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전이 호출되었는지 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)의 `sizeof()`로 확인할 수 있도록 `Yes` 개체는 `No` 개체 보다 크기가 크도록 정의되었습니다.
2. `Test()` 함수는 2개의 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전을 제공합니다. `D*` 가 `B*`로 변환되면 `Yes` 개체를 리턴하고, 그렇지 않으면 `No` 개체를 리턴합니다. 즉, `D`가 `B`를 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)했다면 `Yes`를 리턴합니다.
3. `D`가 `B`를 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)했다면 열거값 `Val`에 `true`를 세팅하고, 그렇지 않으면 `false`를 세팅합니다.


```cpp
// D가 B를 상속하였는지 검사하는 템플릿
template<typename D, typename B> 
class IsDerivedFrom {
    class No {};
    class Yes {No no[2];}; // No 클래스보다 크기가 큰 클래스

    // B*로 변환되는 타입이라면 Yes를 리턴하고, 그렇지 않은 모든 것을은 No를 리턴합니다.
    static Yes Test(B*); // 컴파일 타임에만 사용해서 선언만 하고 정의는 불필요합니다.
    static No Test(...); // 컴파일 타임에만 사용해서 선언만 하고 정의는 불필요합니다.
public:
    // Test() 함수에 D*를 전달했을때 B*로 변환되었다면 상속받았기 때문에 true, 그렇지 않으면 false를 저장합니다.
    enum {Val = sizeof(Test(static_cast<D*>(0))) == sizeof(Yes)};
};
```
> *(C++11~) [type_traits](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/)이 추가되어 컴파일 타임 프로그래밍시 각 타입의 조건들을 검사하거나 타입 변환등의 작업이 간편해 졌습니다. [is_base_of](https://tango1202.github.io/cpp-stl/modern-cpp-stl-type_traits/#%ED%83%80%EC%9E%85-%EA%B4%80%EA%B3%84)를 참고하세요.*

이제 `CloneTraits`에서 `IsDerivedFrom<>::Val`에 따라 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 `Clone()`함수를 호출해 주면 됩니다. 

이때 일반적인 프로그래밍 방식대로 `if()`문을 이용하면, 코드내에 언급한 것처럼 `Shape`의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)는 `protected` 여서 사용할 수 없으므로 컴파일 오류가 납니다. 

```cpp
template<typename T>
class CloneTraits
{
public:

    // if 문을 사용했기 때문에 런타임에 복사 생성자를 사용할지 Clone()을 사용할 지 결정합니다.
    static T* Clone(const T* ptr) {
    	if (ptr == NULL) {
    		return  NULL;
    	}

        // ICloneable을 상속하지 않았다면 복사 생성
        // (X) 컴파일 오류. Shape의 복사 생성자는 protected 여서 사용할 수 없습니다.
        if (!IsDerivedFrom<T, ICloneable>::Val) {
            return new T(*ptr);
        }
        // ICloneable을 상속했다면 Clone() 호출
        else {
            return ptr->Clone();
        }
    } 
};
Shape* shape = CloneTraits<Shape>::Clone(new Rectangle);
EXPECT_TRUE(typeid(*shape).name() == typeid(Rectangle).name());
delete shape;
```

`사실 Shape`은 `else`문에 있는 `ptr->Clone()`만 호출하면 되는데, 사소한 것 때문에 컴파일 오류가 나왔네요. 그냥 `public`로 바꿔버리면 될까요?

컴파일 오류를 해결하겠다고 `Shape`의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 `public`으로 바꿔선 안됩니다. 부모 개체인 `Shape`은 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)로서 인스턴스화 되면 안되기에 외부에서 사용 못하도록 성심 성의껏 `protected`로 만든 것이니까요. (*또한, `CloneTraits<int>::Clone(new int)` 와 같이 기본 타입인 `int`를 사용한다면, 이번엔 `ptr->Clone()`이 없다며 컴파일 오류가 발생할 것입니다.*) 

본 문제 발생의 원인은 `new T(*ptr)`와 `ptr->Clone()`을 같은 함수안에서 처리한다는 점입니다. 타입에 따라 복제하는 방식이 다르니 호출하는 함수도 다른 함수로 만들어 줘야 합니다. 

이 문제는 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 통해 해결할 수 있습니다. 하나의 함수에서 `if()`를 통해 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 `Clone()`함수를 호출하는게 아니라, [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용하는 함수와 `Clone()`을 사용하는 함수를 각각 구현하고, 개체에 따라 해당 함수를 호출하게 하면 됩니다.

1. `Clone()`함수를 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 하기 위해 `CloneTag<true>`와 `CloneTag<false>`타입을 만듭니다.
2. `IsDerivedFrom<>::Val`에 따라 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)된 `Clone()`함수를 호출합니다.

```cpp
// CloneTag<true>와 CloneTag<false> 타입을 만듭니다.
template<bool val>
class CloneTag {
public:
    enum {Val = val};
};

// IsDerivedFrom을 이용하여 ICloneable 상속 여부를 컴파일 타임에 판단하여, 컴파일 타임에 복사 생성할지, Clone을 호출할지 결정합니다.
template<typename T>
class CloneTraits
{
    static T* Clone(const T* ptr, CloneTag<false>) {
        return new T(*ptr); 
    }
    static T* Clone(const T* ptr, CloneTag<true>) {
        return ptr->Clone();
    }
public:
    // 오버로딩을 통해 컴파일 타임에 복사 생성자를 사용할지 Clone()을 사용할 지 결정합니다.
    static T* Clone(const T* ptr) {
        if (ptr == NULL) {
            return  NULL;
        }

        return Clone(
            ptr, 
            CloneTag<IsDerivedFrom<T, ICloneable>::Val>()
        );
    }    
};
```

이제 다음과 같이 **2. Traits를 이용하는 방법** 의 `my_smart_ptr`을 그대로 사용하고, `Shape`은 `ICloneable`을 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)해 주면 됩니다. 

이때 `Shape::Clone()`은 `ICloneable*`이 아니라 `Shape*`를 리턴해야 합니다.

그렇지 않으면, 다음 코드는 

```cpp
static T* Clone(const T* ptr, CloneTag<true>) {
    return ptr->Clone();
}
```

`CloneTraits<Shape>`으로 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 되어 다음처럼 되는데요,

```cpp
static Shape* Clone(const Shape* ptr, CloneTag<true>) {
    return ptr->Clone(); // (X) 컴파일 오류. Shape::Clone()이 ICloneable*을 리턴하면 ICloneable*이 Shape* 으로 변환될 수 없으므로 컴파일 오류가 발생합니다.
}
```

`ICloneable*`이 `Shape*` 으로 변환될 수 없으니 컴파일 오류가 발생합니다. 

따라서, `Shape`이 비록 [추상 클래스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)이고, `Shape`개체 자체를 복제하여 인스턴스화 할 수 없다고 하더라도, 다음처럼 `Clone()`함수는 `Shape*`를 리턴해야 해야 합니다.

```cpp
// Shape::Clone()을 하면 ICloneable*이 아니라 Shape* 리턴되도록 조정합니다.
virtual Shape* Clone() const = 0;
```

다음은 전체 테스트 코드 입니다.

```cpp
// Clone()을 제공하는 단위 인터페이스
class ICloneable {
private:
    ICloneable(const ICloneable& other) {} // 인터페이스여서 외부에서 사용 못하게 복사 생성자 막음
    ICloneable& operator =(const ICloneable& other) {return *this;} // 인터페이스여서 외부에서 사용 못하게 복사 대입 연산자 막음    
protected:
    ICloneable() {} // 인터페이스여서 상속한 개체에서만 생성할 수 있게함 
    ~ICloneable() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다. 
public:
    virtual ICloneable* Clone() const = 0; // 순가상 함수입니다. 자식 클래스에서 구체화 해야 합니다.
};

// D가 B를 상속하였는지 검사하는 템플릿
template<typename D, typename B> 
class IsDerivedFrom {
    class No {};
    class Yes {No no[2];}; // No 클래스보다 크기가 큰 클래스

    // B*로 변환되는 타입이라면 Yes를 리턴하고, 그렇지 않은 모든 것을은 No를 리턴합니다.
    static Yes Test(B*); // 컴파일 타임에만 사용해서 선언만 하고 정의는 불필요합니다.
    static No Test(...); // 컴파일 타임에만 사용해서 선언만 하고 정의는 불필요합니다.
public:
    // Test() 함수에 D*를 전달했을때 B*로 변환되었다면 상속받았기 때문에 true, 그렇지 않으면 false를 저장합니다.
    enum {Val = sizeof(Test(static_cast<D*>(0))) == sizeof(Yes)};
};

// CloneTag<true>와 CloneTag<false> 타입을 만듭니다.
template<bool val>
class CloneTag {
public:
    enum {Val = val};
};

// IsDerivedFrom을 이용하여 ICloneable 상속 여부를 컴파일 타임에 판단하여, 컴파일 타임에 복사 생성할지, Clone을 호출할지 결정합니다.
template<typename T>
class CloneTraits {
    static T* Clone(const T* ptr, CloneTag<false>) {
        return new T(*ptr); 
    }
    static T* Clone(const T* ptr, CloneTag<true>) {
        return ptr->Clone();
    }
public:
    // 오버로딩을 통해 컴파일 타임에 복사 생성자를 사용할지 Clone()을 사용할 지 결정합니다.
    static T* Clone(const T* ptr) {
        if (ptr == NULL) {
            return  NULL;
        }

        return Clone(
            ptr, 
            CloneTag<IsDerivedFrom<T, ICloneable>::Val>()
        );
    }    
};

template<typename T>
class my_smart_ptr { 
    T* m_Ptr; 
public:
    my_smart_ptr(T* ptr) : 
        m_Ptr(ptr) {}
    ~my_smart_ptr() {
        delete m_Ptr;
    }

private:
    // 테스트와 상관없어서 복사 생성자와 복사 대입 연산자는 private로 막아 두었습니다.
    my_smart_ptr(my_smart_ptr& other) {}
    my_smart_ptr& operator =(my_smart_ptr& other) {return *this;}

public:
    T* GetPtr() {
        return m_Ptr;
    } 
    // CloneTraits를 이용하여 복제합니다.
    T* Clone() const {
        return CloneTraits<T>::Clone(m_Ptr);
    }
};  

// 추상 클래스 입니다.
// ICloneable을 상속합니다.
class Shape : public ICloneable {
protected:
    Shape() {} 
    Shape(const Shape& other) {}
public:
    virtual ~Shape() {}

    // Shape::Clone()을 하면 ICloneable*이 아니라 Shape* 리턴되도록 조정합니다.
    virtual Shape* Clone() const = 0;
};

class Rectangle : public Shape {
public:
    // (O) 자식 개체에서는 자식 타입으로 리턴합니다.
    // Rectangle의 복사 생성자를 이용하며 복제본을 리턴합니다.
    virtual Rectangle* Clone() const { 
        return new Rectangle(*this); 
    }
}; 

my_smart_ptr<Shape> sp1(new Rectangle);
my_smart_ptr<Shape> sp2(sp1.Clone());     

// sp2는 Rectangle 을 관리합니다.
EXPECT_TRUE(typeid(*sp2.GetPtr()).name() == typeid(Rectangle).name());

my_smart_ptr<int> sp3(new int);
my_smart_ptr<int> sp4(sp3.Clone());

// sp4는 int 를 관리합니다.
EXPECT_TRUE(typeid(*sp4.GetPtr()).name() == typeid(int).name());  
```

> *(C++17~) [if constexpr](https://tango1202.github.io/cpp/modern-cpp-constexpr/#c17-if-constexpr)이 추가되어 조건에 맞는 부분만 컴파일하고, 그렇지 않은 부분은 컴파일에서 제외할 수 있습니다.*<br/>
> *(C++20~) [컨셉(concept)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)과 [요구사항(requires)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%ADrequires)이 추가되어 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)나 [auto](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto)에 [제약 조건(constraint)](https://tango1202.github.io/cpp/modern-cpp-concept/#%EC%BB%A8%EC%85%89concept%EA%B3%BC-%EC%A0%9C%EC%95%BD-%EC%A1%B0%EA%B1%B4)을 줄 수 있습니다.*