---
layout: single
title: "#12. [C++ 코딩 패턴] 논리적 상수성(지연 생성, 캐쉬)"
categories: "cpp-coding-pattern"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

논리적으로는 데이터를 얻어오는 [상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)이나, 실질적으로는 멤버 변수를 수정하는 함수가 있습니다. 주로 지연 생성이나 캐쉬를 구현하는 경우에 필요합니다.

`Date`를 문자열로 리턴하는 함수를 만든다고 해봅시다. 다음 가정을 해봅시다.

1. 문자열 변환에 비용이 많이 듭니다.
2. 프로그램내에서 광범위하게 `Date`개체가 많이 사용됩니다.
3. 이중에 일부만 어쩌다 한번씩 문자열로 변환해서 사용합니다.


# 필요할 때만 데이터를 생성하는 방법

실제 문자열이 필요할때 `MakeText()` 함수를 호출하여 문자열을 생성하여 사용할 수 있습니다.

```cpp
class Date {
    int m_Year;
    int m_Month;
    int m_Day;
public: 
    Date(int year, int month, int day) :
        m_Year(year),
        m_Month(month), 
        m_Day(day) {
    }

    // Getter/Setter
    void SetYear(int val) {m_Year = val;} 
    void SetMonth(int val) {m_Month = val;}
    void SetDay(int val) {m_Day = val;}
public:
    // (△) 비권장. 호출할 때마다 문자열을 새롭게 만듭니다.
    // 데이터를 yyyy-mm-dd 형식으로 만듬
    std::string MakeText() const { // 멤버 변수를 수정하지 않으므로 const
        char buffer[] = "yyyy-mm-dd";
        snprintf(buffer, 11, "%04d-%02d-%02d", m_Year, m_Month, m_Day); // 널문자 포함 11
        return buffer;
    }
};
Date date(20, 2, 10); // 20년 2월 10일
EXPECT_TRUE(date.MakeText().compare("0020-02-10") == 0);  
```

하지만 이 방식은 `MakeText()`함수를 호출할때 마다 문자열을 생성한다는 문제가 있습니다. 이미 동일한 문자열을 만든적이 있음에도 불구하고요. 만약 문자열 생성에 속도 비용이 많이 든다면 불필요한 속도 지연이 있게 됩니다. 

# 매번 데이터를 생성하는 방법

이미 만들어진 문자열을 다시 만드는게 부담스러워, `Date` 값이 수정될 때에만 문자열을 만들 수 있습니다.

```cpp
class Date {
    int m_Year;
    int m_Month;
    int m_Day;
    std::string m_Text; // yyyy-mm-dd
public: 
    Date(int year, int month, int day) :
        m_Year(year),
        m_Month(month), 
        m_Day(day),
        m_Text() {
        MakeText(); // (△) 비권장. 값이 설정되었으니 문자열을 만듬
    }

    // Getter/Setter
    void SetYear(int val) {
        m_Year = val; 
        MakeText(); // (△) 비권장. 값이 수정되었으니 문자열을 만듬
    } 
    void SetMonth(int val) {
        m_Month = val;
        MakeText(); // (△) 비권장. 값이 수정되었으니 문자열을 만듬
    }
    void SetDay(int val) {
        m_Day = val;
        MakeText(); // (△) 비권장. 값이 수정되었으니 문자열을 만듬
    }

    const std::string& GetText() const {return m_Text;}

private:
    // 데이터를 yyyy-mm-dd 형식으로 만듬
    void MakeText() {
        char buffer[] = "yyyy-mm-dd";
        snprintf(buffer, 11, "%04d-%02d-%02d", m_Year, m_Month, m_Day); // 널문자 포함 11
        m_Text = buffer;
    }
};
Date date(20, 2, 10); // 20년 2월 10일
EXPECT_TRUE(date.GetText().compare("0020-02-10") == 0); 
```

`Date`개체에서 값 수정시마다 `MakeText()`함수를 호출하고, 쓸데없이 미리 문자열도 만들어 저장합니다. 특히나 모든 `Date`개체에서 미리 문자열을 만들어 두기 때문에 메모리 관점에서도 낭비입니다. 

# 캐쉬를 사용하는 방법

데이터 생성에 비용이 많이 드는 경우 계산된 값을 캐쉬해 두고, 값이 변경된 경우에만 새롭게 문자열을 생성할 수 있도록 캐쉬를 초기화하여 불필요한 문자열 생성을 최소화할 수 있습니다.

```cpp
class Date {
    int m_Year;
    int m_Month;
    int m_Day;
    std::string m_Cached; // 문자열을 캐쉬해 둡니다.
public: 
    Date(int year, int month, int day) :
        m_Year(year),
        m_Month(month), 
        m_Day(day),
        m_Cached() {
    }

    // Getter/Setter
    void SetYear(int val) {
        m_Year = val;
        m_Cached.clear(); // 캐쉬를 초기화
    } 
    void SetMonth(int val) {
        m_Month = val;
        m_Cached.clear(); // 캐쉬를 초기화
    }
    void SetDay(int val) {
        m_Day = val;
        m_Cached.clear(); // 캐쉬를 초기화
    }
public:
    // (O) 한번 호출되면 데이터를 캐쉬해 둡니다.
    // (O) 멤버 변수를 리턴하므로 const std::string& 리턴
    // (△) 비권장. 멤버 변수의 값을 리턴하는데 상수 멤버 함수가 아닙니다.
    // 데이터를 yyyy-mm-dd 형식으로 만듬
    const std::string& MakeText() { 

        // 캐쉬된게 없다면 생성
        if (m_Cached.empty()) {
            char buffer[] = "yyyy-mm-dd";
            snprintf(buffer, 11, "%04d-%02d-%02d", m_Year, m_Month, m_Day); // 널문자 포함 11
            m_Cached = buffer; // 캐쉬에 저장
        }

        return m_Cached;
    }
};
Date date(20, 2, 10); // 20년 2월 10일
EXPECT_TRUE(date.MakeText().compare("0020-02-10") == 0); 
EXPECT_TRUE(date.MakeText().compare("0020-02-10") == 0); // 기존 캐쉬를 사용

date.SetYear(21); // 년도를 21년으로 변경. 캐쉬가 초기화됨
EXPECT_TRUE(date.MakeText().compare("0021-02-10") == 0); // 문자열 생성
```

여기서 아쉬운 점은 `MakeText()`함수가 멤버 변수의 값을 리턴하는 Getter이지만 비 상수 멤버 함수라는 점입니다. 어찌되었건 멤버 변수인 `m_Cached`를 수정하니까요. 

# mutable로 상수 멤버 함수를 만드는 방법

이럴때 `m_Cached`를 `mutable`로 정의하면, [상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)이더라도 멤버 변수를 수정할 수 있습니다. Getter의 의미처럼 보이도록 함수명도 `MakeText()`에서 `GetText()` 로 변경하였습니다.

```cpp
const std::string& GetText() const  
```

```cpp
class Date {
    int m_Year;
    int m_Month;
    int m_Day;
    mutable std::string m_Cached; // (O) 상수 멤버 함수에서 수정 가능합니다. 문자열을 캐쉬해 둡니다.
public: 
    Date(int year, int month, int day) :
        m_Year(year),
        m_Month(month), 
        m_Day(day),
        m_Cached() {
    }

    // Getter/Setter
    void SetYear(int val) {
        m_Year = val;
        m_Cached.clear(); // 캐쉬를 초기화
    } 
    void SetMonth(int val) {
        m_Month = val;
        m_Cached.clear(); // 캐쉬를 초기화
    }
    void SetDay(int val) {
        m_Day = val;
        m_Cached.clear(); // 캐쉬를 초기화
    }

    // (O) 한번 호출되면 데이터를 캐쉬해 둡니다.
    // (O) 멤버 변수를 리턴하므로 const std::string& 리턴
    // (O) mutable 개체를 수정합니다.
    // 데이터를 yyyy-mm-dd 형식으로 만듬
    const std::string& GetText() const { 

        // 캐쉬된게 없다면 생성
        if (m_Cached.empty()) {
            char buffer[] = "yyyy-mm-dd";
            snprintf(buffer, 11, "%04d-%02d-%02d", m_Year, m_Month, m_Day); // 널문자 포함 11
            m_Cached = buffer; // 캐쉬에 저장
        }
        return m_Cached;
    }
};

Date date(20, 2, 10); // 20년 2월 10일
EXPECT_TRUE(date.GetText().compare("0020-02-10") == 0); 
EXPECT_TRUE(date.GetText().compare("0020-02-10") == 0); // 기존 캐쉬를 사용

date.SetYear(21); // 년도를 21년으로 변경. 캐쉬가 초기화됨
EXPECT_TRUE(date.GetText().compare("0021-02-10") == 0); // 문자열 생성      
```
