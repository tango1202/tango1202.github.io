---
layout: single
title: "#2. [고전 C++ 개체 지향] 소멸자"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `has-a`관계에서는 `protected` Non-Virtual 소멸자를 사용하라.
> * `is-a`관계에서는 `public` Virtual 소멸자를 사용하라.(`virtual` 소멸자가 아니면 메모리 릭이 발생한다.)
> * 생성자처럼 소멸자에서도 가상 함수를 호출하지 마라.
> * 소멸자에서 예외를 발생하지 마라.(필요하다면 `Release()`함수를 구현하라.)


# 개요

|항목|내용|
|--|--|
|`~T() {}`|소멸자|
|`virtual ~T() {}`|가상 소멸자|
|`virtual ~T() = 0;`|순가상 소멸자|

소멸자는 개체의 수명이 다해 소멸될때 호출되는 특수 함수입니다. 개체가 활동하면서 생성했던 메모리나 리소스를 해제하는 역할을 합니다.

# 소멸자 호출 시점

1. 전역 변수, 정적 변수, 정적 지역 변수인 경우 프로그램 종료시
2. 스택에 생성된 지역 변수의 경우 블록 유효 범위의 끝
3. `new`로 생성된 힙 개체인 경우 `delete` 시
4. 임시 개체인 경우 표현식의 끝
5. 예외 발생에 따른 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)시 

# 소멸자 호출 순서

소멸자가 호출되면, 소멸자 본문이 실행되고, 본문내의 지역 변수를 삭제하고, 멤버 변수를 삭제합니다. 그뒤 부모 클래스의 소멸자를 호출합니다.

1. 소멸자 본문 실행 
2. 본문 내의 지역 변수 삭제(선언의 역순)
3. 멤버 변수 삭제(선언의 역순)
4. 부모 클래스 소멸자 본문 호출
5. 부모 클래스 소멸자 본문내의 지역 변수 삭제(선언의 역순)
6. 부모 클래스 멤버 변수 삭제(선언의 역순)

```cpp
class BaseMemberObj {
public:
    BaseMemberObj() {std::cout<<"1. BaseMemberObj::BaseMemberObj()"<<std::endl;}
    ~BaseMemberObj() {std::cout<<"10. BaseMemberObj::~BaseMemberObj()"<<std::endl;}
};
class BaseLocalObj {
public:
    ~BaseLocalObj() {std::cout<<"9. BaseLocalObj::~BaseLocalObj()"<<std::endl;}
};
class Base {
    BaseMemberObj m_BaseMemberObj;
public:
    Base() {std::cout<<"2. Base::Base()"<<std::endl;}
    ~Base() {
        BaseLocalObj baseLocalObj;
        std::cout<<"8. Base::~Base()"<<std::endl;
    }
};
class DerivedMemberObj {
public:
    DerivedMemberObj() {std::cout<<"3. DerivedMemberObj::DerivedMemberObj()"<<std::endl;}
    ~DerivedMemberObj() {std::cout<<"7. DerivedMemberObj::~DerivedMemberObj()"<<std::endl;}
};
class DerivedLocalObj {
public:
    ~DerivedLocalObj() {std::cout<<"6. DerivedLocalObj::~DerivedLocalObj()"<<std::endl;}
};        
class Derived : public Base {
    DerivedMemberObj m_DerivedMemberObj;
public:
    Derived() {std::cout<<"4. Derived::Derived()"<<std::endl;}
    ~Derived() {
        DerivedLocalObj derivedLocalObj;
        std::cout<<"5. Derived::~Derived()"<<std::endl;
    }
};

Derived d;
```

**실행 결과**

```cpp
1. BaseMemberObj::BaseMemberObj()
2. Base::Base()
3. DerivedMemberObj::DerivedMemberObj()
4. Derived::Derived()
5. Derived::~Derived() // 소멸자 호출
6. DerivedLocalObj::~DerivedLocalObj() // 소멸자 지역 변수 삭제(선언의 역순)
7. DerivedMemberObj::~DerivedMemberObj() // 멤버 변수 삭제(선언의 역순)
8. Base::~Base() // 부모 클래스 소멸자 호출
9. BaseLocalObj::~BaseLocalObj()
10. BaseMemberObj::~BaseMemberObj()
```

# 암시적 소멸자

컴파일러는 소멸자가 정의되지 않으면, 암시적으로 소멸자를 정의합니다. 암시적 소멸자는 아무 작업을 하지 않으며 예외를 발생하지 않습니다.

# `public` Non-Virtual 소멸자

일반적인 개체의 소멸자는 `public` Non-Virtual로 정의합니다.

```cpp
class T {
public:
    ~T() {}
};
class U : public T { // (△) 비권장. has-a 관계가 명확하지 않음. is-a 관계에서는 사용 금지
};
T t;
U u;
```

`public`이기 때문에 `T t;`와 같이 변수 정의(인스턴스화)에도 사용할 수 있고, 부모 클래스로 상속받아 사용할 수도 있습니다.

다만, 상속해서 사용하는 부모클래스라면, `has-a`나 `is-a` 의 명확한 의미 부여를 위해 `protected` Non-Virtual 이나 `public` Virtual 소멸자를 쓰는게 낫습니다.


# `protected` Non-Virtual 소멸자

`has-a` 관계를 맺으려는 부모 클래스는 `protected` Non-Virtual 소멸자로 만듭니다. 

**인터페이스**

다형 소멸을 지원하지 않는 단위 전략 인터페이스용 클래스를 만들때 사용합니다.

`protected` 이기 때문에 개체를 인스턴스화 할 수 없고, 오로지 상속해서만 사용할 수 있습니다. `has-a` 관계에서는 부모 클래스의 포인터나 참조자를 이용하여 다형 소멸을 지원하지 않으므로, `virtual`로 만들 필요도 없습니다. 

```cpp
class IEatable {
protected:
    ~IEatable() {} // 상속받지만, 다형적으로 사용하지 않아 non-virtual 입니다.

public:
    virtual void Eat() = 0;
};
class IWalkable {
protected:
    ~IWalkable() {} // 상속받지만, 다형적으로 사용하지 않아 non-virtual 입니다.

public:
    virtual void Walk() = 0;
};

class Dog :
    public IEatable,
    public IWalkable
{
public:        
    virtual void Eat() {}
    virtual void Walk() {}
};
IEatable eatable; // (X) 컴파일 오류. 소멸자가 protected
IWalkable walkable; // (X) 컴파일 오류. 소멸자가 protected
Dog dog; // (O)
```

**공통 구현**

여러 클래스가 공통 구현을 부모 클래스로 만들어 `has-a` 관계를 맺을때 사용할 수 있습니다.

```cpp
class ResizeableImpl {
private:
    int m_Width;
    int m_Height;    
protected:
    ResizeableImpl(int w, int h) :
        m_Width(w), 
        m_Height(h) {}
    ~ResizeableImpl() {} // has-a 관계로 사용되기 때문에, 단독으로 생성되지 않도록 protected입니다.
public:    
    // Get/Set 함수
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}

    void SetWidth(int val) {m_Width = val;}
    void SetHeight(int val) {m_Height = val;}
};
class Rectangle : public ResizeableImpl {
    int m_Left;
    int m_Top;
public:
    Rectangle(int l, int t, int w, int h) :
        ResizeableImpl(w, h),
        m_Left(l),
        m_Top(t) {}
};
class Ellipse : public ResizeableImpl {
    int m_CenterX;
    int m_CenterY;
public:
    Ellipse(int centerX, int centerY, int w, int h) :
        ResizeableImpl(w, h),
        m_CenterX(centerX),
        m_CenterY(centerY) {}
};
Rectangle r(0, 0, 10, 20);
Ellipse e(5, 10, 10, 20);
```

# public Virtual 소멸자

`is-a` 관계에서는 부모 개체를 이용하여 자식 개체를 제어하고, `delete` 합니다. 즉, 부모 개체의 포인터로 자식 개체를 소멸시켜야 합니다. 이러한 다형 소멸을 지원하려면 꼭 `virtual` 소멸자를 작성해야 합니다.

**다형 소멸**

```cpp
class Base {
public:
    ~Base() {std::cout<<"2. Base::~Base()"<<std::endl;} // (△) 비권장. 그냥 public non-virtual 이어서 다형 소멸을 지원하지 않습니다.
};

class Derived : public Base {
public:
    ~Derived() {std::cout<<"1. Derived::~Derived()"<<std::endl;}
};
```

상기 클래스의 `Derived`를 생성하고 소멸시키면, `Derived`와 `Base` 소멸자가 잘 호출되어 1, 2가 모두 호출됩니다.

```cpp
Derived* d = new Derived;
delete d; // 1, 2 호출됨
```

하지만 `Base`의 포인터를 이용하여 소멸시키면, `Base` 소멸자만 호출됩니다.

```cpp
Derived* d = new Derived;
Base* b = d;
delete b; // (X) 오동작. 2만 호출됨. Derived 소멸자가 호출되지 않음. 메모리 릭.
```

이렇게 부모 클래스 포인터로부터 다형 소멸을 해야 하는 경우에는 꼭 `virtual` 소멸자를 작성해야 합니다.

```cpp
class Base {
public:
    virtual ~Base() {std::cout<<"2. Base::~Base()"<<std::endl;} // (O) 다형 소멸을 지원함
};

class Derived : public Base {
public:
    ~Derived() {std::cout<<"1. Derived::~Derived()"<<std::endl;}
};

Derived* d = new Derived;
Base* b = d;
delete b; // (O) 1, 2 호출됨. 다형 소멸 지원.
```

**가상 함수가 없는 경우의 다형성**
    
보통 다형적 동작을 하는 개체들은 부모 클래스에 가상 함수가 있습니다. 하지만, 단지 타입명만 다르게 하여 사용하고 싶을때도 있습니다. 이럴때 억지로 가상 함수를 추가하고 싶으면 소멸자를 `virtual` 로 만들면 됩니다.

```cpp
class Error {
private:
    std::string m_Message;
public:
    virtual ~Error() {} // (O) 가상 함수가 없어 억지로 만듬
    // 모든 자식 개체들에서 사용합니다. 가상 함수는 없습니다.
    void SetMessage(const char* msg) {m_Message = msg;}
    const std::string& GetMessage() const {return m_Message;}
};

// 모든 자식 개체들이 타입명만 다릅니다.
class FileError : public Error {};
class NetworkError : public Error {};
class PrintError : public Error {};

std::vector<Error*> errors;
errors.push_back(new FileError());
errors.push_back(new NetworkError());
errors.push_back(new PrintError());

// 다형 소멸
for(std::vector<Error*>::iterator itr = errors.begin(); itr != errors.end(); ++itr) {
    delete *itr;
}
errors.clear();
```

# 순가상 소멸자

C++에서는 순가상 소멸자를 허용합니다. 다만 실제 구현 정의가 반드시 있어야 합니다.(없으면 컴파일 오류가 납니다.)

```cpp
class T {
public:
    virtual ~T() = 0;
};
T::~T() {} // 실제 구현 정의가 있어야 함
```

순가상 소멸자는 다음의 경우에 사용할 수 있습니다만, 다른 방식을 사용하는게 더 낫습니다.

1. 상속 전용 기반 클래스
   
    `protected` 생성자나 `protected` Non-Virtual 소멸자처럼 개체 인스턴스화를 못하게 할 때 사용합니다.
    
    이런 경우라면 `protected` Non-Virtual을 을 사용하는게 좋습니다. 생성자는 여러개 만들 수 있어서 `protected`인지 신경 쓰기 번거롭고, 순가상 소멸자는 정의를 따로 만들어야 하기 때문에 번거롭습니다.(특히 함수 내부의 로컬 클래스로 정의할때는 소멸자 정의를 할 방법이 없습니다.)

    ```cpp
    class T {
    public:
        virtual ~T() = 0;
    };
    T::~T() {} // 실제 구현 정의가 있어야 함
    class U : public T {};

    T t; // (X) 컴파일 오류. 순가상 소멸자가 있어 개체 정의(인스턴스화) 안됨
    U u; // 상속하면 개체 정의(인스턴스화) 가능
    ```

2. 다형적 동작을 안하는 인터페이스
   
    `protected` Non-Virtual 소멸자처럼 인터페이스 구현시 사용합니다. 

    이 경우에서도 `protected` Non-Virtual을 사용하는게 좋습니다. 순가상 소멸자는 정의를 따로 만들어야 하기 때문에 번거롭습니다.

# 소멸자에서 가상 함수 호출 금지

부모 개체의 소멸자에서 가상 함수를 호출하면, 자식 개체가 이미 소멸되었기 때문에, 자식 개체의 가상 함수가 호출되는게 아니라 부모 클래스의 가상 함수가 호출됩니다. 

일반적인 가상 함수 호출시에는 자식 개체의 함수가 호출되는데, 소멸자에서는 다르죠. 이는, 코드 분석에 어려움을 주고 잠재적인 코드 결함을 유발할 수 있으니, 소멸자에서는 아예 가상 함수를 호출하지 않는게 좋습니다.

```cpp
class BaseMemberObj {
public:
    ~BaseMemberObj() {std::cout<<"4. BaseMemberObj::~BaseMemberObj()"<<std::endl;}
};
class Base {
    BaseMemberObj m_BaseMemberObj;
public:
    virtual ~Base() {
        std::cout<<"3. Base::~Base()"<<std::endl;

        f(); // (△) 비권장. 소멸자에서 가상 함수 호출. Derived는 이미 소멸되었고, Base::f()가 호출됨
    }
    virtual void f() {std::cout<<"*** Base::f() ***"<<std::endl;}
};
class DerivedMemberObj {
public:
    ~DerivedMemberObj() {std::cout<<"2. DerivedMemberObj::~DerivedMemberObj()"<<std::endl;}
};

class Derived : public Base {
    DerivedMemberObj m_DerivedMemberObj;
public:
    ~Derived() { std::cout<<"1. Derived::~Derived()"<<std::endl;}
    virtual void f() {std::cout<<"*** Derived::f() ***"<<std::endl;}
};

Derived d; // (△) 비권장. 소멸자에서 가상 함수 호출
```

# 소멸자에서 예외 발생 금지

예외 발생에 따른 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)시에도 개체가 소멸되면서 소멸자가 호출됩니다. 이러한 상황에서 또다시 예외가 발생하면, 정상적인 스택 풀기를 방해하므로 소멸자에서는 예외가 발생하지 않도록 해야 합니다. 
소멸자에서 예외가 발생할 것 같으면 `Release()`와 같은 별도의 정리 함수를 만드는게 좋습니다.

```cpp
class T {
public:
    void Release() {} // 예외 발생할 수 있는 경우, 메모리나 리소스 해제
};     

T t;
t.Release(); // 예외 발생할 수 있는 경우, 메모리나 리소스 해제
```


