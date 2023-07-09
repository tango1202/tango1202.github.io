---
layout: single
title: "#4. [고전 C++ 개체 지향] 멤버 함수, 상수 멤버 함수, 논리적 상수성, Getter, Setter"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

**개요**

|항목|내용|
|--|--|
|`T() {}`|생성자<br/>([생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 참고)|
|`~T() {}`|소멸자<br/>([소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)참고)|
|`T& operator =(const T& other) {}`|대입 연산자|
|`operator U() const {}`|형변환 연산자|
|`U f() {}`|비 정적 멤버 함수|
|`U f() const {}`|비 정적 상수 멤버 함수|
|`virtual U f() {}`|가상 함수|
|`virtual U f() = 0;`|순가상 함수|
|`static U f() {}`|정적 멤버 함수|



특성으로

getter, setter

class Date {
int d, m, y; public:
int day() const {return d;}
int month() const {return m;}
int year() const {return y;} void day(int val) {d = val;} void day(int val) const {d = val;} // 컴파일 오류
};
Date date1, date2; date1.day(10); date2.day(20); const Date& r = date1; r.day(); // (O) r.day(20); // (X) 컴파일 오류 const 정확도를 지켜라


멤버 변수를 수정하지 않는다면 반드
시 const 사용

상수성을 잘 지키면, 예외에 안정적이며, 상수 계약에 의
해 코딩할 수 있다. 자신의 행위가 객체를 변경시키는지
아닌지 항상 분명하게 인지할 것.


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
