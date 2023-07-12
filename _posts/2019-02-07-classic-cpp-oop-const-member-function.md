---
layout: single
title: "#7. [고전 C++ 개체 지향] 멤버 함수, 상수 멤버 함수, Getter, Setter"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 멤버 변수를 수정하지 않으면, 상수 멤버 함수로 작성하라.
> * 자식 개체에서 부모 개체의 비 가상 함수를 재정의 하지 마라.
> * Getter 함수의 리턴값은 기본 자료형인 경우 값 복사로, 클래스 타입인 경우 참조자로 작성하라.
> * Setter 함수의 인자는 기본 자료형인 경우 값 복사로, 클래스 타입인 경우 참조자로 작성하라.

**개요**

|항목|내용|
|--|--|
|`T() {}`|생성자<br/>([생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 참고)|
|`~T() {}`|소멸자<br/>([소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 참고)|
|`T& operator =(const T& other) {}`|대입 연산자 와 연산자 오버로딩<br/>([대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/) 와 [연산자 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고)|
|`operator U() const {}`|형변환 연산자<br/>([형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고)|
|`U f() {}`|멤버 함수|
|`U f() const {}`|상수 멤버 함수|
|`virtual U f() {}`|가상 함수|
|`virtual U f() = 0;`|순가상 함수|
|`static U f() {}`|정적 멤버 함수<br/>([정적 멤버 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 참고)|

# 멤버 함수

클래스는 데이터(멤버 변수)와 이를 처리하는 함수(멤버 함수)를 응집하여, [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 가능하게 합니다.

```cpp
class Date {
    int m_Year;
    int m_Month;
    int m_Day;
public: 
    Date(int year, int month, int day) :
        m_Year(year),
        m_Month(month), 
        m_Day(day) {}

    // Getter/Setter
    int GetYear() const {return m_Year;} // 상수 멤버 함수
    int GetMonth() const {return m_Month;}
    int GetDay() const {return m_Day;}

    void SetYear(int val) {m_Year = val;} // 멤버 함수
    void SetMonth(int val) {m_Month = val;}
    void SetDay(int val) {m_Day = val;}

    // 내부적으로 전체 개월수를 계산하기 위해,
    // 데이터와 처리하는 함수를 응집하였습니다. 
    int CalcTotalMonth() const { // 상수 멤버 함수
        return m_Year * 12 + m_Month; 
    }
};
Date date(20, 2, 10); // 20년 2월 10일
EXPECT_TRUE(date.CalcTotalMonth() == 20 * 12 + 2); 
```

# 상수 멤버 함수

멤버 함수의 뒤에 `const`를 붙여 상수 멤버 함수를 만들 수 있습니다. 상수 멤버 함수는 [상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD) 에 따라 다음을 준수합니다.

1. 멤버 변수를 수정하지 않습니다.
    
    ```cpp
    class T {
        int m_Val;
    public:
        void Func() const {m_Val = 10;} // (X) 컴파일 오류. 멤버 변수 수정
    };
    ```

2. 멤버 변수를 몰래 수정할 수 있는 포인터나 참조자를 리턴하지 않습니다.
   
    ```cpp
    class T {
        int m_Val;
    public:
        int* Func() const {return &m_Val;} // (X) 컴파일 오류. int* 리턴. const int*를 리턴해야 함.
    };
    ```

3. 내부 구현시 상수 멤버 함수만을 호출합니다.
 
    ```cpp
    class T {
        int m_Val;
    public:
        void Func() const { NonConstFunc();} // (X) 컴파일 오류. 비 상수 멤버 함수 호출.
        void NonConstFunc() {}
    };
    ```   

4. 메모리를 수정하지 않기 때문에 예외를 발생하지 않습니다.

따라서 상수성을 잘 지키면, 예외에 안정적이며, 상수성 계약에 의해 안정적으로 코딩할 수 있습니다. 함수가 개체를 변경시키는지
아닌지 항상 분명하게 인지하고, 최대한 상수 멤버 함수로 작성하세요.

# 비 상수 멤버 함수의 비 상수성 전파

상수 멤버 함수가 될 수 있음에도 비 상수 멤버 함수로 작성한다면, 이를 사용하는 모든 함수나 개체들이 비 상수로 만들어져야 합니다. 비 상수성은 전파되니 상수 멤버 함수가 될 수 있다면 꼭 상수 멤버 함수로 만드세요.

```cpp
class T {
    int m_Val;
public:
    int GetVal() {return m_Val;} // (△) 비권장. 상수 멤버 함수인데 비 상수로 정의했습니다.
};

class U {
    T m_T;
public:
    int GetInnerVal() const {m_T.GetVal();} // (X) 컴파일 오류. m_T.GetVal()은 비 상수 멤버 함수여서 상수 멤버 함수인 GetInnerVal() 이 호출할 수 없습니다.
};
```

# 가상 함수

`virtual`을 붙이면 가상 함수가 되며, 부모 개체를 이용하여 자식 개체에서 재구현한 함수에 접근할 수 있습니다.

다음 코드에서 일반 함수인 `f()`와 가상 함수인 `v()`를 `Derived`에서 재구현 했을때, 어떻게 동작하는지 나타내었습니다.

부모 개체인 `Base`에서 일반 함수인 `f()`를 호출하면, `Base::f()`가 호출되고, 자식 개체에서는 `Derived::f()` 가 호출됩니다. 일관성이 없으므로 사용하지 말아야 하고, 자식 개체에서 부모 개체의 비 가상 함수를 가려서는 안됩니다.

부모 개체인 `Base`에서 가상 함수인 `v()`를 호출하면, `Derived::v()`가 정상적으로 호출됩니다.

```cpp
class Base {
public:
    int f() {return 10;}
    virtual int v() {return 10;} // 가상 함수
};

class Derived : public Base {
public:
    int f() {return 20;} // (△) 비권장. Base의 동일한 이름의 비 가상 함수를 가림
    virtual int v() {return 20;} // (O) Base의 가상 함수 재구현
};

Derived d;
Base* b = &d;

EXPECT_TRUE(b->f() == 10); // (△) 비권장. Base 개체를 이용하면 Base::f()가 호출됨 
EXPECT_TRUE(d.f() == 20); // (△) 비권장. Derived 개체를 이용하면 Derived::f()가 호출됨        
EXPECT_TRUE(static_cast<Base&>(d).f() == 10); // (△) 비권장. 가려진 Base::f() 함수를 호출

EXPECT_TRUE(b->v() == 20); // (O) 가상 함수여서 Derived::v() 가 호출됨
EXPECT_TRUE(d.v() == 20); // (O) 가상 함수여서 Derived::v() 가 호출됨
```

# 순가상 함수

순가상 함수는 실제 구현없이 함수 규약만 정의할때 사용합니다. 순가상 함수가 있는 클래스는 인스턴스화 할 수 없으며, 반드시 상속해서 자식 개체에서 구현해야 합니다.

```cpp
class IEatable {
public:
    virtual void Eat() = 0; // 순가상 함수
};

class Dog :
    public IEatable {
public:        
    virtual void Eat() {} // 순가상 함수는 자식 개체에서 실제 구현을 해야 합니다.
};
IEatable eatable; // (X) 컴파일 오류. 순가상 함수
Dog dog; // (O)
```

# Getter 함수

개체의 멤버 변수를 리턴하는 함수를 특별히 Getter 함수라고 합니다. 다음 규칙에 맞춰 리턴문을 작성하는게 좋습니다.

1. `int` 등 기본 자료형의 경우는 복사 부하가 참조 부하보다 적기 때문에 값을 리턴하는게 좋습니다. 
2. 클래스 등 복사 부하가 참조 부하보다 큰 개체는 참조자를 리턴하는게 좋습니다.
3. 멤버 변수는 널이 되는 경우가 없기 때문에 포인터보다는 참조자로 리턴하는게 좋습니다.
4. 멤버 변수를 수정하지 않는다면 상수 멤버 함수로 작성합니다.
5. 값 타입을 리턴하는 경우는 어짜피 리턴값이 복제되므로, 리턴값에 `const`를 굳이 붙일 필요가 없습니다. 

```cpp
class T {};
class U {
    int m_Val1;
    T m_Val2;
public:
    int GetVal1() const {return m_Val1;} // (O) 멤버 변수의 값을 리턴
    int& GetVal1() {return m_Val1;} // (O) 멤버 변수의 값을 수정하는 참조자를 리턴    

    const T& GetVal2() const {return m_Val2;} // (O) 멤버 변수의 참조자 리턴
    T& GetVal2() {return m_Val2;} // (O) 멤버 변수의 값을 수정하는 참조자를 리턴  

    // const int GetVal1() const {return m_Val1;} // (△) 비권장. 리턴값이 기본 타입이라면 어짜피 리턴값이 복제되므로, `const` 부적절
    // int GetVal1() {return m_Val1;} // (△) 비권장. 멤버 변수를 수정하지 않으므로 상수 함수가 적절
    // const int GetVal1() {return m_Val1;} // (△) 비권장. 멤버 변수를 수정하지 않으므로 상수 함수가 적절

    // (△) 비권장. 포인터 보다는 참조자가 적절
    // const int* GetVal1() const {return &m_Val1;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // int* GetVal1() {return &m_Val1;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // int* GetVal1() const {return &m_Val1;} // (X) 컴파일 오류. 상수성 계약 위반
    // const int* GetVal1() {return &m_Val1;} // (△) 비권장. 상수 함수가 적절 

    // const int& GetVal1() const {return m_Val1;} // (△) 비권장. int는 기본 자료형이어서 참조 보다는 값 전달이 적절
    // int& GetVal1() const {return m_Val1;} // (X) 컴파일 오류. 상수성 계약 위반
    // const int& GetVal1() {return m_Val1;} // (△) 비권장. 상수 함수가 적절

    // (△) 비권장. 복사 부하가 참조 부하보다 큰 개체여서 값 복사 보다는 참조가 적절
    // const T GetVal2() const {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절. 어짜피 리턴값이 복제되므로, `const` 부적절
    // T GetVal2() const {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절
    // T GetVal2() {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절. 멤버 변수를 수정하지 않으므로 상수 함수가 적절
    // const T GetVal2() {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절. 어짜피 리턴값이 복제되므로, `const` 부적절, 멤버 변수를 수정하지 않으므로 상수 함수가 적절

    // (△) 비권장. 포인터 보다는 참조자가 적절
    // const T* GetVal2() const {return &m_Val2;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // T* GetVal2() {return &m_Val2;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // T* GetVal2() const {return &m_Val2;} // (X) 컴파일 오류. 상수성 계약 위반
    // const T* GetVal2() {return &m_Val2;} // (△) 비권장. 상수 함수가 적절

    // T& GetVal2() const {return m_Val2;} // (X) 컴파일 오류. 상수성 계약 위반
    // const T& GetVal2() {return m_Val2;} // (△) 비권장. 상수 함수가 적절
};
```

# Setter 함수

개체의 멤버 변수를 설정하는 함수를 특별히 Setter 함수라고 합니다. 다음 규칙에 맞춰 인자를 작성하는게 좋습니다.

1. int 등 기본 자료형의 경우는 복사 부하가 참조 부하보다 적기 때문에 값을 전달하는게 좋습니다. 
2. 클래스 등 복사 부하가 참조 부하보다 큰 개체는 참조자를 전달하는게 좋습니다.
3. 멤버 변수를 수정하는 것이지 인자를 수정하는게 아니기 때문에 인자는 상수여야 합니다.
4. 널검사가 최소화 될 수 있도록, 널이 되지 않는 경우라면 포인터보다는 참조자를 전달하는게 좋습니다.
5. 값 타입을 전달하는 경우는 어짜피 인자에 복제되므로, 굳이 인자에 `const`를 붙일 필요가 없습니다. 

```cpp
class T {};
class U {
    int m_Val1;
    T m_Val2;
public:
    void SetVal1(int val) {m_Val1 = val;} // (O) 멤버 변수에 값 대입
    void SetVal2(const T& val) {m_Val2 = val;} // (O) 참조자를 통해 전달받은 인자를 현 멤버 변수에 복사

    // void SetVal1(const int val) {m_Val1 = val1;} // (△) 비권장. 인자에 복사되므로 int와 const int는 동일 취급됨
    // void SetVal1(int* val) {m_Val = *val;} // (△) 비권장. 인자는 상수 타입이어야 함
    // void SetVal1(const int* val) {m_Val = *val;} // (△) 비권장. 포인터보다는 참조자가 좋음
    // void SetVal1(int& val) {m_Val = *val;} // (△) 비권장. 인자는 상수 타입이어야 함
    // void SetVal1(const int& val) {m_Val = *val;} // (△) 비권장. 기본 자료형의 경우 값 복사가 좋음

    // void SetVal2(T val) {m_Val2 = val;} // (△) 비권장. 값 복사 보다는 참조가 적절
    // void SetVal2(const T val) {m_Val2 = val;} // (△) 비권장. 인자에 복사되므로 T와 const T는 동일 취급됨

    // void SetVal2(T* val) {m_Val2 = *val;} // (△) 비권장. 인자는 상수 타입이어야 함
    // void SetVal2(const T* val) {m_Val2 = *val;} // (△) 비권장. 포인터보다는 참조자가 좋음
    // void SetVal2(T& val) {m_Val2 = val;} // (△) 비권장. 인자는 상수 타입이어야 함
};
```
