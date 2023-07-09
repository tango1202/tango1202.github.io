---
layout: single
title: "#4. [고전 C++ 개체 지향] 멤버 함수, 상수 멤버 함수, 논리적 상수성, Getter, Setter"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 멤버 변수를 수정하지 않으면, 상수 멤버 함수로 작성하라.

**개요**

|항목|내용|
|--|--|
|`T() {}`|생성자<br/>([생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 참고)|
|`~T() {}`|소멸자<br/>([소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 참고)|
|`T& operator =(const T& other) {}`|대입 연산자<br/>([대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/) 참고)|
|`operator U() const {}`|형변환 연산자<br/>([형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고)|
|`U f() {}`|멤버 함수|
|`U f() const {}`|상수 멤버 함수|
|`virtual U f() {}`|가상 함수|
|`virtual U f() = 0;`|순가상 함수|
|`static U f() {}`|정적 멤버 함수|

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

따라서 상수성을 잘 지키면, 예외에 안정적이며, 상수성 계약에 의해 안정적으로 코딩할 수 있습니다. 함수가 객체를 변경시키는지
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
    int GetInnerVal() const {m_T.GetVal();}; // (X) 컴파일 오류. m_T.GetVal()은 비 상수 멤버 함수여서 상수 멤버 함수인 GetInnerVal() 이 호출할 수 없습니다.
};
```

# 논리적 상수성


상수 멤버 함수는 내부 구현에서 상수 멤버 함수만을 호출합니다.(컴파일 오류)
상수 멤버 함수는 예외를 발생하지 않습니다.(노력해서 구현해야 함)
mutable로 논리적 상수성을 구현한 경우 예외를 발생하지 않아야 합니다.(노력해서 구현해야 함)

# 가상 함수

`virtual`을 붙이면 가상 함수가 되며, 부모 개체로 자식 개체에서 구현한 함수를 호출할 수 있습니다.

```cpp
class Base {
public:
    int f() {return 10;}
    virtual v() {return 10;}
};

class Derived : public Base {
public:
    int f() {return 20;}
    virtual v() {return 20;} // 가상 함수 재구현
};

Derived d;
Base* b = &d;

EXPECT_TRUE(b->f() == 10 && b->v() == 20);
EXPECT_TRUE(d.f() == 20 && d.v() == 20);
```


# 순가상 함수
# Getter 함수

# Setter 함수

특성으로

getter, setter

class Date {
int d, m, y; public:
int day() const {return d;}
int month() const {return m;}
int year() const {return y;} void day(int val) {d = val;} void day(int val) const {d = val;} // 컴파일 오류
};
Date date1, date2; date1.day(10); date2.day(20); const Date& r = date1; r.day(); // (O) r.day(20); // (X) 컴파일 오류 const 정확도를 지켜라







Date를 문자열로 리턴하는 함수를 만들고 싶다. 문자열 변환에 비용이 많이 들어 이 값을 멤버 변수에 저장(cache)해 두어야 할 것 같다. 물론, Date의 값이 바뀌면 멤버변수는 초기화 되야 할 것이다


class Date {
int d, m, y; string text; // text에 저장해 둘 것이다. public: Date(int dd, int mm, int yy) : d(dd), m(mm), y(yy) {
}void day(int dd) {
text.clear(); d = dd; }string getString() const {
if(text.empty()) {
text = toString(d, m, y); // 멤버변수를 바꾼다. }return text; } private: static string toString(d, m, y); };

이전에 저장된 정보를 초기화

논리적으론 get 해오므로 상수

물리적 상수성을 해치므로 컴파일 오류(지연 생성에서 흔히 발생) mutable string text; 로 정의하여 해결
