---
layout: single
title: "#3. [고전 C++ 개체 지향] 소멸자"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 획득된 자원은 꼭 소멸시켜라.
> * 암시적 소멸자가 정상 작동하도록 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 정의시 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 사용하라.
> * 다형 소멸이 필요하면 부모 개체에 `virtual` 소멸자를 사용하라.(`virtual` 소멸자가 아니면 메모리 릭이 발생한다.)
> * `public` Non-Virtual 소멸자인 개체는 상속하지 마라.
> * `is-a`관계에서는 `public` Virtual 소멸자를 사용하라.(`virtual` 소멸자가 아니면 메모리 릭이 발생한다.)
> * `has-a`관계에서는 `protected` Non-Virtual 소멸자를 사용하라.
> * 생성자처럼 소멸자에서도 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 마라.
> * 소멸자에서 예외를 발생하지 마라.(필요하다면 `Release()`함수를 구현하라.)


# 개요

|항목|내용|
|--|--|
|`~T() {}`|소멸자|
|`virtual ~T() {}`|가상 소멸자|
|`virtual ~T() = 0;`|순가상 소멸자|

소멸자는 개체의 수명이 다해 소멸될때 호출되는 특수 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)입니다. 개체가 활동하면서 생성했던 메모리나 리소스를 해제하는 역할을 합니다.

`new` 로 생성한 [포인터 멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 사용후 `delete`로 소멸시키지 않으면, 메모리에 그대로 남아 있어(메모리 릭), 메모리 부족으로 프로그램이 중단됩니다. 

따라서 획득된 자원은 꼭 소멸([RAII(Resource Acquisition Is Initialization)](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/))시켜야 하는데요, 소멸자는 개체가 소멸될때 호출되므로, 사용했던 자원을 소멸시키는 시점으로 딱 좋습니다.

```cpp
class T {
    int* m_Ptr;
public:
    T() : m_Ptr(new int(10)) {} // new로 생성한 것은
    ~T() {delete m_Ptr;} // (O) delete로 소멸
};
```

# 소멸자 호출 시점

소멸자는 하기 상황에서 자동으로 호출됩니다.

1. [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), 정적 변수([정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98), [함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98))인 경우 프로그램 종료시
2. [스택](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%8A%A4%ED%83%9D)에 생성한 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)의 경우 블록 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)의 끝
3. `new`로 생성한 [힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99) 개체인 경우 `delete` 시
4. [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)인 경우 표현식의 끝
5. 예외 발생에 따른 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)시 

# 개체 소멸 순서

개체가 소멸되면, 소멸자 본문이 실행되고, 본문내의 지역 변수를 소멸시키고, [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)를 소멸시킵니다. 그뒤 부모 클래스의 소멸자를 호출합니다. 모든 소멸자 호출이 끝나면, 메모리를 해제합니다.

1. 소멸자 본문 실행 
2. 본문 내의 지역 변수 소멸(선언의 역순)
3. [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 소멸(선언의 역순)
4. 부모 클래스 소멸자 본문 호출
5. 부모 클래스 소멸자 본문내의 지역 변수 소멸(선언의 역순)
6. 부모 클래스 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 소멸(선언의 역순)
7. 메모리 해제

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
6. DerivedLocalObj::~DerivedLocalObj() // 소멸자 지역 변수 소멸(선언의 역순)
7. DerivedMemberObj::~DerivedMemberObj() // 멤버 변수 소멸(선언의 역순)
8. Base::~Base() // 부모 클래스 소멸자 호출
9. BaseLocalObj::~BaseLocalObj()
10. BaseMemberObj::~BaseMemberObj()
```

# 암시적 소멸자

복사 생성자, 대입 연산자와 마찬가지로, 소멸자를 정의하지 않으면, 컴파일러는 암시적으로 소멸자를 정의합니다. 암시적 소멸자는 아무 작업을 하지 않으며 예외를 발생하지 않습니다.

소멸자를 명시적으로 구현해서 사용한 리소스를 직접 소멸시키기 보다는 암시적 소멸자를 그대로 사용할 수 있도록 [스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 이용하는게 실수를 줄일 수 있고 예외 안정에 더 좋습니다.([Holder의 필요성](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/#holder%EC%9D%98-%ED%95%84%EC%9A%94%EC%84%B1) 참고)

[스마트 포인터](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-smart-pointer/)를 이용해서 암시적 소멸자와 호환하는 방법은 [복사 생성자만 지원하는 스마트 포인터](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A7%8C-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0) 에서 언급한 것처럼, 

1.스마트 포인터를 멤버 변수로 정의하고,
2. 스마트 포인터 소멸자에서 `new`한 개체를 `delete`를 해주시면 됩니다.

# 다형 소멸

개체 지향 프로그래밍을 하다보면, 부모 개체 포인터로 자식 개체를 사용하고 `delete`하는 작업을 빈번히 하게 됩니다.

```cpp
class Base {};
class Derived1 : public Base {};
class Derived2 : public Base {};

Base* ptr1 = new Derived1();
Base* ptr2 = new Derived2();
... // ptr1, ptr2를 사용하고,

delete ptr1; // Derived1을 소멸시킵니다.
delete ptr2; // Derived2를 소멸시킵니다.
```

다음 클래스의 `Derived`를 생성하고 소멸시키면, `Derived`와 `Base` 소멸자가 잘 호출되어 1, 2가 모두 호출됩니다.

```cpp
class Base {
public:
    ~Base() {std::cout<<"2. Base::~Base()"<<std::endl;} // (△) 비권장. 그냥 public non-virtual 이어서 다형 소멸을 지원하지 않습니다.
};

class Derived : public Base {
public:
    ~Derived() {std::cout<<"1. Derived::~Derived()"<<std::endl;}
};
Derived* d = new Derived;
delete d; // 1, 2 호출됨
```

하지만 `Base`의 포인터로 소멸시키면, 

```cpp
Derived* d = new Derived;
Base* b = d;
delete b; // (X) 오동작. 2만 호출됨. Derived 소멸자가 호출되지 않음. 메모리 릭.
```

`Base` 소멸자만 호출됩니다. 따라서, `Derived` 소멸자에서 사용한 리소스 정리 작업이 호출되지 않아 메모리 릭이 발생할 수 있습니다. 

이렇게 부모 개체 포인터로부터 다형 소멸을 해야 하는 경우에는 자식 개체의 소멸자가 호출되도록 꼭 `virtual` 소멸자로 작성하여야 합니다.

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

# public Non-Virtual 소멸자

부모 개체로 사용하지 않을 것이라면, 소멸자는 `public` Non-Virtual로 정의합니다.

1. 외부에서 생성/소멸해야 하니 `public`이어야 하겠고,
2. 상속받지 않으니 다형 소멸이 필요없어 Non-Virtual로 정의합니다.

다만, 아무런 제약이 없기 때문에 `U` 와 같이 상속한다면, 다형 소멸이 안되는 우려가 있습니다. 개체가 `public` Non-Virtual 소멸자라면, 상속하지 말라는 뜻이니, 절대 상속하지 마세요. 혹시나 상속이 필요하다면, 다음에 설명할 `public` Virtual 소멸자나, `protected` Non-Virtual 소멸자로 리팩토링 하시기 바랍니다.(상속을 강제로 제한하는 방법은 [상속 제한](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C) 참고)

```cpp
class T {
public:
    ~T() {}
};
// (△) 비권장. 다형 소멸이 되지 않아, 기능 개선을 하다 보면 나중에 메모리 릭이 발생할 수 있습니다.
class U : public T { 
};
T t;
U u;
```

# public Virtual 소멸자

`is-a` 관계에서는 부모 개체 포인터로 자식 개체를 제어하고, `delete` 합니다. ([is-a 관계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#is-a-%EA%B4%80%EA%B3%84) 참고)
 
1. 외부에서 생성/소멸해야 하니 `public`이어야 하겠고,
2. 다형 소멸을 해야 하니 `virtual`로 정의합니다.

# protected Non-Virtual 소멸자

`has-a` 는 다형 소멸을 하지 않는 상속 관계입니다. 자식 개체가 부모 개체의 멤버 변수나 멤버 함수의 기능을 포함하고 제공하는 관계라고 할 수 있습니다.([has-a 관계](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#has-a-%EA%B4%80%EA%B3%84) 참고)

1. 외부에서 부모 클래스로 직접 생성/소멸하지 않고, 자식 개체를 통해 생성/소멸하므로 `protected`이어야 하겠고,
2. 다형 소멸을 하지 않으니 Non-Virtual로 정의합니다.

`protected` 이기 때문에 부모 개체를 직접 인스턴스화 할 수 없고, 오로지 상속해서만 사용할 수 있습니다. 또한 실수로 부모 개체 포인터로 `delete`하려고 하면, `protected`여서 컴파일 오류가 발생합니다. 단단한 **코딩 계약**입니다.

```cpp
class Base {
protected:
    ~Base() {} // 인터페이스여서 protected non-virtual(상속해서 사용하고, 다형 소멸 안함) 입니다.
public:
    virtual void Func() = 0;
};
class Derived :
    public Base {
public:        
    virtual void Func() {}
};
Base base; // (X) 컴파일 오류. 소멸자가 protected
Derived derived; // (O)
Base* p = &derived;
delete p; // (X) 컴파일 오류. Base의 소멸자가 protected
```

# 가상 함수가 없는 경우의 다형성
    
보통 다형적 동작을 하는 개체들은 부모 클래스에 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있습니다. 하지만, 가상 함수가 없을 때도 있습니다. 이럴때 억지로 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 추가하고 싶으면 소멸자를 `virtual` 로 만들면 됩니다.

다음은 부모 개체를 상속받아 자식 개체에서 타입명만 다르게 한 예입니다. 

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

순가상 소멸자는 다음의 경우에 사용할 수 있습니다.

1. 상속 전용 기반 클래스
   
    `protected` 생성자나 `protected` Non-Virtual 소멸자처럼 개체 인스턴스화를 못하게 하고, 상속해서만 사용할 수 있게 만듭니다.  이런 경우라면 `protected` 생성자를 사용하는게 좋습니다.([상속 전용 기반 클래스 - protected 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%83%81%EC%86%8D-%EC%A0%84%EC%9A%A9-%EA%B8%B0%EB%B0%98-%ED%81%B4%EB%9E%98%EC%8A%A4---protected-%EC%83%9D%EC%84%B1%EC%9E%90) 참고, [상속 강제](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C) 참고)

2. 인터페이스
   
    `protected` Non-Virtual 소멸자처럼 인터페이스 구현시 사용합니다. 

    이 경우는 `protected` Non-Virtual 소멸자를 사용하는게 좋습니다. 순가상 소멸자는 정의를 따로 만들어야 하기 때문에 번거롭습니다.

3. 마땅한 순가상 함수가 없는 추상 클래스
   
   이경우에는 순가상 소멸자를 사용해야 합니다. [추상 클래스](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/#%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)를 참고하세요. 

# 소멸자에서 가상 함수 호출 금지

부모 개체의 소멸자에서 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하면, 자식 개체의 소멸자가 이미 호출되었기 때문에, 자식 개체의 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 호출되는게 아니라 부모 클래스의 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 호출됩니다. 

일반적인 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 호출시에는 자식 개체의 함수가 호출되는데, 소멸자에서는 다르죠. 이는 코드 분석에 어려움을 주고 잠재적인 코드 결함을 유발할 수 있으니, 소멸자에서는 아예 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 않는게 좋습니다.

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

        f(); // (X) 오동작. 가상 함수를 소멸자에서 호출합니다. Derived는 이미 소멸되었고, Base::f()가 호출됩니다.
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

Derived d; // (X) 오동작. 소멸자에서 가상 함수 호출
```

**실행 결과**

```cpp
1. Derived::~Derived() // 자식 개체의 소멸자가 먼저 호출됩니다. 
2. DerivedMemberObj::~DerivedMemberObj()
3. Base::~Base()
*** Base::f() *** // (X) 오동작. Base의 가상 함수가 호출됩니다.
4. BaseMemberObj::~BaseMemberObj()
```

# 소멸자에서 예외 발생 금지

예외 발생에 따른 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)시에도 개체가 소멸되면서 소멸자가 호출됩니다. 이러한 상황에서 또다시 예외가 발생하면, 정상적인 [스택 풀기](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-stack-unwinding)를 방해하므로 소멸자에서는 예외가 발생하지 않도록 해야 합니다. 
소멸자에서 예외가 발생할 것 같으면 `Release()`와 같은 별도의 정리 함수를 만들어 소멸자 호출전에 명시적으로 호출하는게 좋습니다.

```cpp
class T {
public:
    void Release() {} // 예외 발생할 수 있는 경우, 메모리나 리소스 해제
};     

T t;
t.Release(); // 소멸자 호출전에 명시적으로 메모리나 리소스 해제
```


