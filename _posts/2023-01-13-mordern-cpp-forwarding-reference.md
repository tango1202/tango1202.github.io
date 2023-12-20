---
layout: single
title: "#13. [모던 C++] (C++11~) 전달 참조와 완벽한 전달(forward())"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#23] std::move와 std::forward를 숙지하라.([move 원리](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#move-%EC%9B%90%EB%A6%AC), [상수 개체의 move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4%EC%9D%98-move). [forward() 와 완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC), [forward() 원리](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%9B%90%EB%A6%AC) 참고)

> * [MEC++#24] 전달 참조와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)를 구별하라.
> * [MEC++#25] [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)에는 std::move()를, 전달 참조에는 std::forward()를 사용하라.
>   * forward()가 적합한 곳에 move()를 잘못 사용하지 마라.([forward()가 적합한 곳에 move()의 잘못된 사용](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward%EA%B0%80-%EC%A0%81%ED%95%A9%ED%95%9C-%EA%B3%B3%EC%97%90-move%EC%9D%98-%EC%9E%98%EB%AA%BB%EB%90%9C-%EC%82%AC%EC%9A%A9) 참고)
>   * 전달 참조를 이용하면 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 수가 줄어든다.([전달 참조를 이용한 함수 오버로딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고)
>   * 값 타입 리턴은 리턴값 최적화를 한다. 괜히 move() 사용하지 마라.([값 타입 리턴에서 move()의 비효율성](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EA%B0%92-%ED%83%80%EC%9E%85-%EB%A6%AC%ED%84%B4%EC%97%90%EC%84%9C-move%EC%9D%98-%EB%B9%84%ED%9A%A8%EC%9C%A8%EC%84%B1) 참고)
>   * 인자를 리턴할때 move()를 고려하라.([값 타입 인자, 우측값 타입 인자를 리턴할 경우 move()의 효율성](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EA%B0%92-%ED%83%80%EC%9E%85-%EC%9D%B8%EC%9E%90-%EC%9A%B0%EC%B8%A1%EA%B0%92-%ED%83%80%EC%9E%85-%EC%9D%B8%EC%9E%90%EB%A5%BC-%EB%A6%AC%ED%84%B4%ED%95%A0-%EA%B2%BD%EC%9A%B0-move%EC%9D%98-%ED%9A%A8%EC%9C%A8%EC%84%B1) 참고)
> * [MEC++#26] 전달 참조에 대한 오버로딩을 피하라.([전달 참조를 이용한 함수 오버로딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고)
>   * 전달 참조 버전이 생각보다 광범위하게 매칭됩니다. 따라서 전달 참조를 사용하는 함수는 오버로딩 하지 않는게 좋습니다.
> * [MEC++#27] 전달 참조에 대한 오버로딩 대신 사용할 수 있는 기법들을 알아 두라.([전달 참조를 이용한 함수 오버로딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 참고)
>   * 전달 참조내에서 오버로딩 타입을 검사하여 Tag 개체를 이용하여 직접 분기하여 호출할 수 있습니다.
>   * enable_if를 이용하여 전달 참조 버전이 특정 조건에서 오버로딩 되지 않도록 제한할 수 있습니다.
> * [MEC++#28] [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)을 숙지하라.
> * [MEC++#30] 완벽 전달이 실패하는 경우들을 잘 알아두라.
>   * 중괄호 초기화는 전달 참조와 궁합이 잘 안맞습니다([전달 참조와 중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0%EC%99%80-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94) 참고)

> * (C++11~) [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 추가되어 포워딩 함수에서도 효율적으로 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 [완벽하게 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)할 수 있습니다.

# 개요

값 타입이나 포인터는 다른 변수에 전달할 때 타입이 변하지는 않습니다. [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 될 뿐이죠.

```cpp
int a;
int b = a; // 복사 대입 됩니다.
int* p1 = &a; 
int* p2 = p1; // 포인터가 가리키는 값 말고 포인터 값은 복사 대입됩니다.
```

하지만 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)의 경우는 타입이 변할 수 있습니다. 
다음 예에서 `f()`함수는 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `ref`를 전달해 봤자 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 값 타입으로 전달받습니다.(*[함수 템플릿 인수 추론](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 참고*)

```cpp
template<typename U, typename V, typename W>
void f(U, V, W) {}

int val = 0;
int* ptr = NULL;
int& ref = val;

f(val, ptr, ref); // f<int, int*, int>(int, int*, int). 참조자가 제거됩니다.
```

그뿐만이 아닙니다. [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)는 이름이 있는 변수로 전달 받는 순간 이름이 부여되서 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 됩니다.(*[이름이 부여된 우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%A6%84%EC%9D%B4-%EB%B6%80%EC%97%AC%EB%90%9C-%EC%9A%B0%EC%B8%A1%EA%B0%92) 참고*)

```cpp
class A {};

int f_11(A&) {return 1;} // 인자가 좌측값 참조이면 호출됩니다.
int f_11(A&&) {return 2;} // 인자가 우측값 참조이면 호출됩니다.

A lvalue;
A&& rvalue_11 = std::move(lvalue); // rvalue는 이름이 부여됐으므로 좌측값입니다.
EXPECT_TRUE(f_11(rvalue_11) == 1); // f_11(A&)를 호출합니다.
```

이렇게 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)는 다른 변수로 전달할때 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)가 변경되는 문제가 있습니다. 

이렇게 변경되는 것은 함수내에서 포워딩을 할때 특히 문제가 됩니다. [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로만 전달되다 보니 힘겹게 만든 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 물거품이 될 수 있거든요.

```cpp
class A {};

int f_11(A&) {return 1;}
int f_11(A&&) {return 2;}

int Forwarding_11(A&& param) { // 함수 인자는 이름이 부여됐으므로 좌측값입니다.
    return f_11(param); // 좌측값으로만 호출합니다. 이동 연산이 물거품이 될 수 있습니다.
}

A val;
EXPECT_TRUE(Forwarding_11(std::move(val)) == 1); // (△) 비권장. 우측값을 전달했지만 좌측값으로 호출됩니다.
```

따라서 [이름이 부여된 우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%A6%84%EC%9D%B4-%EB%B6%80%EC%97%AC%EB%90%9C-%EC%9A%B0%EC%B8%A1%EA%B0%92)에서 억지로 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 사용하는 방법을 소개해 드렸는데요,

```cpp
int Forwarding_11(A&& param) { // 함수 인자는 이름이 부여됐으므로 좌측값입니다.
    return f(std::move(param)); // 우측값으로 형변환하여 호출합니다.
}
```

[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)는 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)와 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)를 모두 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 바꿔버리기 때문에 포워딩에 적절하지는 않습니다.

포워딩에서는 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)을 활용한 [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수를 이용하여 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 전달하고, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)는 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 전달하는 [완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 해줘야 합니다.

# 참조 축약

[완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)을 공부하기 전에 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)의 개념을 알아두셔야 합니다.

원칙적으로 [포인터의 포인터](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)는 합법이지만, [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)의 참조는 불법입니다. [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 메모리상에 어떻게 구성되는지 표준에 정의되지 않았고, 그저 개체에 대한 별칭만 처리하는 걸로 되어 있거든요. 그래서 또다른 별칭을 만들뿐 포인터처럼 [다차원](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)적으로 구성되지는 않습니다.

```cpp
int val;
int* p;
int** pp = &p; // 포인터의 포인터는 합법입니다. 다차원적으로 구성됩니다.

int& r = val;
int& & rr = r; // (X) 컴파일 오류. 참조자의 참조는 불법입니다.
int& r2 = r; // 또다른 별칭일 뿐입니다.
```

하지만, [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/)을 사용하면 묘하게도, [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)에 참조를 더할 수 있습니다. 

[함수 템플릿 인수 추론](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)시에는 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)은 제거되지만, 명시적으로 지정하면 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용할 수 있는데요, 

```cpp
template<typename T>
void f(T) {}

int val = 0;
int& ref = val;
f(ref); // f<int>(int). 기본적으로 참조자가 제거됩니다.
f<int&>(ref); // f<int&>(int&). 명시적으로 지정하면 참조자로 사용됨
```

이 특징을 이용해서 [클래스 템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF)에 명시적으로 `T`의 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 지정하고, 다음처럼 [종속 타입](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%EC%A2%85%EC%86%8D-%ED%83%80%EC%9E%85)을 정의하여 명시적으로 지정한 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)에 참조를 더할 수 있습니다.

```cpp
template<typename T>
class Convert_11 {
public:
    using LRef = T&; // T 타입에 좌측값 참조를 더합니다.
    using RRef = T&&; // T 타입에 우측값 참조를 더합니다.
};
```
즉, `T`가 `int&`라면, `LRef`는 `int&` + `&`이며, `RRef`는 `int&` + `&&`이 됩니다. [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)에 참조를 더하게 된거죠.


[참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)에 참조를 더한 결과는 [포인터처럼 다차원적으로 동작](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EB%8B%A4%EC%B0%A8%EC%9B%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)하지는 않고, [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)나 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0) 중 하나로 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)됩니다.

1. 값 타입을 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 사용시

    ```cpp
    class A {};
    A obj;
    Convert_11<A>::LRef a = obj; // A&
    Convert_11<A>::RRef b = std::move(obj); // A&& 
    ```

2. [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 사용시 : `& + &` 과 `& + &&`

    ```cpp
    class A {};
    A obj;
    Convert_11<A&>::LRef a = obj; //A&, & + &
    Convert_11<A&>::RRef b = obj; // A&, & + &&
    ```

3. [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)를 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 사용시 :`&& + &` 과 `&& + &&`

    ```cpp
    class A {};
    A obj;
    Convert_11<A&&>::LRef a = obj; // A&, && + &
    Convert_11<A&&>::RRef b = std::move(obj);; // A&&, && + &&  
    ```

즉, 다음과 같이 [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)을 합니다. [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)끼리를 더한 경우를 제외하고는 모두 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)가 됩니다.

|항목|결과|
|--|--|
|`&` + `&`|`&`|
|`&` + `&&`|`&`|
|`&&` + `&`|`&`|
|`&&` + `&&`|`&&`|

[참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)은 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto), [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)와 [using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD), [decltype()](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#decltype), [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92), [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)에서 발생합니다.

# 전달 참조

보통 `A&`의 형태로 타입에 `&`을 붙이면, [좌측값을 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)할 수 있고(*[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)은 참조할 수 없죠.*),

```cpp
class A {};
A val;
A& ref = val; // 좌측값을 참조할 수 있습니다.
A& ref = std::move(val); // (X) 컴파일 오류. 우측값을 참조할 수 없습니다.
A& ref = A(); // (X) 컴파일 오류. 임시 개체도 우측값이므로 참조할 수 없습니다.   
```

`A&&`의 형태로 타입에 `&&`을 붙이면, [우측값을 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)할 수 있습니다.(*[좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)은 형변환해서 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 만들어 참조할 수 있습니다.*)

```cpp
class A {};
A val;
A& ref = val;

A&& ref_11 = val; // (X) 컴파일 오류. 좌측값을 참조할 수 없습니다.(형변환을 해야 합니다.)
A&& ref_11 = std::move(val); // A&&는 우측값만 받을 수 있습니다.

A&& ref_11 = ref; // (X) 컴파일 오류, 좌측값을 참조할 수 없습니다.(형변환을 해야 합니다.)
A&& ref_11 = std::move(ref); // A&&는 우측값만 받을 수 있습니다.
```

그런데, [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)는 특별히 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)와 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 모두 전달받을 수 있는 특별한 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)입니다.

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 만드려면 다음 조건을 만족해야 합니다.

1. 정확히 `auto&&`과 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) `T`의 `T&&` 이어야 합니다.(*[const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)하나 붙인 `const auto&&`나 `const T&&`는 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아닙니다.*)

    ```cpp
    class A {};
    A val;

    auto&& a_11 = val; // A&. 전달 참조입니다. val 타입으로 추론합니다.
    // const auto&& b_11 = val; // (X) 컴파일 오류. const를 붙여서 전달 참조가 아닙니다. &&은 우측값만 받을 수 있습니다.
    // A&& c_11 = val; // (X) 컴파일 오류. 타입 추론을 안하므로 전달 참조가 아닙니다. &&은 우측값만 받을 수 있습니다.

    template<typename T>
    void f_11(T&& val) {} // 전달 참조입니다. val 타입으로 추론합니다.
    ```

2. 반드시 타입 추론이 되야 합니다.
 
    다음 예에서 [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/) `A`의 `f_11()`함수는 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 된 뒤에는 `f_11(int&& val)`로 구체화될 수 있기 때문에 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 라고 확신하기는 어렵습니다.

    하지만 [템플릿](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/) `A`의 `g_11()`함수는 함수 호출 시점에 전달한 타입으로 추론하기 때문에 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)입니다.

    ```cpp
    template<typename T> 
    class A {
    public:
        void f_11(T&& val) {} // 전달 참조가 아닙니다. A<int> val; 와 같이 인스턴스화 된 뒤에는 f(int&& val)로 구체화될 수 있기 때문에 val 타입으로 추론된다고 확신할 수 없습니다. 

        template<typename U> 
        void g_11(U&& val) {} // 전달 참조 입니다. val 타입으로 추론합니다.
    }; 
    ```
    또한 다음과 같이 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들이 여러개인 경우 `T&&`로 표현하면 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아닙니다. `param1`, `param2`중 어느 하나가 추론된 뒤에는 나머지는 추론된 것으로부터 구체화 될 수 있기 때문입니다. 따라서, `typename T, typename U`와 같이 [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 독립적으로 만들어야 합니다.

    ```cpp
    template<typename T>
    void f_11(T&& param1, T&& param2) {} // 전달 참조가 아닙니다. param1, param2중 어느 하나가 추론된 뒤에는 나머지는 추론된 것으로부터 구체화 될 수 있기 때문입니다. 

    template<typename T, typename U>
    void f_11(T&& param1, U&& param2) {} // param1, param2 모두 전달 참조입니다.
    ```

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)는 

1. 이름이 있는 값 타입은 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 전달 받고, 이름이 없는 값 타입은 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 전달 받습니다.

    ```cpp
    class A {};
    A val;
    auto&& a_11 = val; // A&. val는 좌측값. 이를 참조로 만들면 A&인 좌측값 참조
    auto&& b_11 = A(); // A&&. A()는 이름이 없는 임시 개체인 우측값. 이를 참조로 만들면 A&&인 우측값 참조  
    ```

2. [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)를 모두 전달받을 수 있습니다.

    `rref_11`은 `A&&`로 선언했지만, 이름이 있기 때문에 좌측값입니다.(*[이름이 부여된 우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%A6%84%EC%9D%B4-%EB%B6%80%EC%97%AC%EB%90%9C-%EC%9A%B0%EC%B8%A1%EA%B0%92) 참고*) 따라서, `b_11`은 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `A&`입니다.

    ```cpp
    class A {};
    A val;
    A& ref = val;
    A&& rref_11 = static_cast<A&&>(val);   

    auto&& a_11 = ref; // A&. ref는 좌측값 참조
    auto&& b_11 = rref_11; // A&. rref_11는 이름이 있으니 좌측값
    auto&& c_11 = std::move(val); // A&&     
    ```

3. [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), 비상수 개체를 모두 전달받을 수 있습니다.

    ```cpp
    class A {};
    const A val;
    const A& ref = val;
    const A&& rref_11 = static_cast<const A&&>(val);   

    auto&& a_11 = val; // const T&. val는 좌측값. 이를 참조로 만들면 const T&
    auto&& b_11 = ref; // const T&. ref는 좌측값 참조
    auto&& c_11 = rref_11; // const T&. rref_11는 이름이 있으니 좌측값
    auto&& d_11 = std::move(val); // const T&& 
    ```

즉, [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)는 이름이 있는 값 타입, 이름이 없는 값 타입, [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)를 상수성을 유지한 채로 모두 전달 받을 수 있는 특별한 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)이며 다음과 같이 전달받습니다.

|항목|내용|
|--|--|
|이름이 있는 값 타입(좌측값)|`T&`, `const T&`|
|이름이 없는 값 타입(우측값)|`T&&`, `const T&&`|
|[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`T&`, `const T&`|
|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)|`T&&`, `const T&&`|

# move() 원리

[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)함수는 STL에 대략 다음처럼 구현됩니다.

```cpp
template<typename T>
typename std::remove_reference<T>::type&& move(T&& param) {
    using ReturnType = typename std::remove_reference<T>::type&&; // 참조성을 뗀 뒤 &&을 더합니다.

    return static_cast<ReturnType>(param);
}
```

코드를 보시면 아시겠지만, [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)함수의 실체는 무작정 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 캐스팅 하는게 아니라, ***전달된 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 뗐다가 `&&`을 붙여주는 함수*** 입니다. 

1. [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)함수의 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 `T&& param`로 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)입니다. 즉, 값 타입,  [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90), [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)를 모두 전달 받을 수 있고, [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), 비상수 개체를 모두 전달 받을 수 있습니다.

1. `remove_reference`는 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)던, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)던 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거한 타입을 구합니다.

    다음처럼 [템플릿 부분 특수화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B6%80%EB%B6%84-%ED%8A%B9%EC%88%98%ED%99%94)를 이용하여 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거할 수 있습니다.

    ```cpp
    template<typename T>
    struct remove_reference_11 { 
        using type = T; 
    };
    // 좌측값 참조인 경우 템플릿 부분 특수화. 좌측값 참조를 제거한 T를 type으로 사용합니다.
    template<typename T>
    struct remove_reference_11<T&> { 
        using type = T; 
    };
    // 우측값 참조인 경우 템플릿 부분 특수화. 우측값 참조를 제거한 T를 type으로 사용합니다.
    template<typename T>
    struct remove_reference_11<T&&> { 
        using type = T; 
    };
    ```

2. [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거한 것에 `&&`을 붙여 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 캐스팅하여 리턴합니다.

전달되는 타입이 `A` 계열 타입일 경우, 다음과 같은 원리로 `A&&`로 변환합니다.

|항목|형태|내부 연산|리턴값|
|--|--|--|--|
|이름이 있는 값 타입([좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value))|`T == A&`|`A&`-> `A` 후 + `&&`|`A&&`|
|이름이 없는 값 타입([우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value))|`T == A&&`|`A&&` -> `A` 후 + `&&`|`A&&`|
|[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`T == A&`|`A&`-> `A` 후 + `&&`| `A&&`|
|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)|`T == A&&`|`A&&`->`A` 후 + `&&`|`A&&`|

만약 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거하지 않고, `&&`을 더한다면, `T`가 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `&` 였을때  [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)에 의해 `& + &&`은 `&`가 됩니다. 따라서, [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거하고 `&&`을 더해준 거죠.

[const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)인 개체라면 다음과 같습니다.

|항목|형태|내부 연산|리턴값|
|--|--|--|--|
|이름이 있는 값 타입([좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value))|`T == const A&`|`const A&` -> `const A` 후 + `&&`|`const A&&`|
|이름이 없는 값 타입([우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value))|`T == const A&&`|`const A&&` -> `const A` 후 + `&&`|`const A&&`|
|[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`T == const A&`|`const A&` -> `const A` 후 + `&&`|`const A&&`|
|[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)|`T == const A&&`|`const A&&` -> `const A` 후 + `&&`|`const A&&`|

# 상수 개체의 move()

[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)함수는 전달된 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 뗐다가 `&&`을 붙여주는 함수이므로 `const T&` 를 전달하면 `const T&&`로 변경합니다. 

그런데, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 `T&&`를 사용하므로 `const T&&`로는 호출되지 않습니다. 이 덕에 [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)를 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)하면 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 호출할 수 없으니 복사 연산을 해줍니다.(*[암시적 이동 연산 변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0-%EB%B3%80%ED%99%98) 참고*)

[상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)는 [상수성 계약](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)에 의해 수정되면 안되므로, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 하면 안되죠. 그래서 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 대신 복사 연산을 호출하는 건데요, [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)을 호출했으니 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 될 것이라 착각하기 쉽습니다. 

그렇다고 만약에 `const T&&`이 없다고 컴파일 오류를 내뱉으면, [암시적 이동 연산 변환](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0-%EB%B3%80%ED%99%98)시 영문도 모른채 엄청나게 많은 컴파일 오류를 볼 수 있게 되니, 아쉽지만 어쩔 수 없는 선택이라 생각합니다. 이부분 주의하셔서 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)함수를 사용하시기 바랍니다.

# forward() 와 완벽한 전달

함수가 전달받은 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수에 전달하는 `Forwarding_11()` 함수를 구현한다고 합시다.

다음처럼 구현하면, 값 타입인 경우에는 불필요한 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 발생하고, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)인 경우에는 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 바껴버려서 컴파일 오류가 발생합니다.

```cpp
class A {};
void f_11(A param1, A& param2, A&& param3) {}

void Forwarding_11(A param1, A& param2, A&& param3) {
    f_11(
        param1, // (△) 비권장. A로 전달합니다. 복사 대입합니다.
        param2, // A&로 전달합니다.
        param3 // (X) 컴파일 오류. param3은 우측값을 참조하는 좌측값입니다. A&로 전달합니다.
    );
}
```

이런 문제를 해결하고자 C++11 에서는 함수가 전달받은 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다른 함수로 [완벽하게 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)하는 [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수를 제공합니다.

```cpp
class A {};
void f_11(A param1, A& param2, A&& param3) {}

void Forwarding_11(A param1, A& param2, A&& param3) {
    f_11(
        std::forward<A>(param1), // A&&로 전달합니다. param1은 Forwarding_11 함수에서 임시로 복사 대입 받은 것이므로, 임시 개체로 취급하고, f_11에 이동 대입한 후 버립니다.
        std::forward<A&>(param2), // A&로 전달합니다.
        std::forward<A&&>(param3) // A&&로 전달합니다. std::forward<A>(param3) 도 동일합니다.
    );
} 

A a;
A b;
A& ref = a;

Forwarding_11(a, ref, std::move(b));
```

`std::forward<A&&>(param3)`의 경우는 특별히 `std::forward<A>(param3)`와 같이 `&&`없이 사용할 수 있습니다.(*[forward() 원리](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%9B%90%EB%A6%AC) 참고*)

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 라면 다음과 같이 `forward<U>()`, `forward<V>()`, `forward<W>()`처럼 `&`나 `&&` 명시 없이 사용할 수 있습니다.(*[forward() 원리](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%9B%90%EB%A6%AC) 참고*)

```cpp
class A {};
void f_11(A param1, A& param2, A&& param3) {}

template<typename U, typename V, typename W>
void Forwarding_11(U&& param1, V&& param2, W&& param3) { // 전달 참조를 사용합니다.
    f_11(
        std::forward<U>(param1), // A&로 전달합니다.
        std::forward<V>(param2), // A&로 전달합니다.
        std::forward<W>(param3) // A&&로 전달합니다.
    );
}

A a;
A b;
A& ref = a;

Forwarding_11(a, ref, std::move(b));   
```

# forward() 원리

사실 [forward() 원리](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%9B%90%EB%A6%AC)를 굳이 이해해야 모던 C++을 사용할 수 있는건 아닙니다. "그냥 함수끼리 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 포워딩 할때는 [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수를 써야 하는구나, [템플릿 인자](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)는 `T`나 `T&`나 `T&&`를 쓰는데, 3개중에 컴파일 오류 안나는거 쓰면 되겠지 머." 라고 대충 사용하셔도 크게 문제될 일은 많이 없을 겁니다. 하지만, 이를 이해한다면, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)와 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)에 대한 통찰력이 생겨 좀더 효율적인 코드를 만들 수 있습니다. 무지하게 난해하지만, 도전할 가치가 있다고 봅니다.
 
[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수는 STL에 대략 다음처럼 구현됩니다.

```cpp
// #1. 기본 함수 템플릿
template<typename T> 
T&& forward(typename remove_reference<T>::type& param) {
    return static_cast<T&&>(param);
}

// #2. 우측값일 경우 오버로딩
template<typename T>
T&& forward(typename remove_reference<T>::type&& param) {
    return static_cast<T&&>(param);
}
```

언뜻 보면 `static_cast<T&&>(param);` 을 했으니, 무조건 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)하는 것처럼 보이는데요, [참조 축약](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%B0%B8%EC%A1%B0-%EC%B6%95%EC%95%BD)이 반영되어 절묘하게 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 전달에 최적화된 적절한 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)됩니다.

각 케이스별로 살펴 보겠습니다.

1. **이름이 있는 값 타입이 전달된 경우**

    다음과 같이 `Forwarding_11()` 함수에 `A` 타입의 값을 전달한 경우 입니다. 

    ```cpp
    class A {};
    void f(A val) {}
    void Forwarding_11(A param) {
        f(std::forward<A>(param));
    }

    A a;
    Forwarding_11(a);
    ```

    `#1` [함수 템플릿 오버로딩](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)이 사용되며, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수에 `T == A`을 전달하여 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)합니다. 
    
    ```cpp
    // #1. 템플릿 전개
    A + && forward(typename A + & param) {
        return static_cast<A + &&>(param);
    }

    // [참조 축약]을 반영하면 다음과 같습니다. 즉 `A&` 를 전달받아 `A&&`로 리턴합니다.
    A&& forward(typename A& param) {
        return static_cast<A&&>(param);
    }
    ``` 

    즉, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수는 `Forwarding_11.param == A`를 `A&`로 전달받아 `A&&`로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)합니다. 

    눈여겨 볼 점은, 값 타입으로 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달 받았는데, 전달 과정에서 또다시 [복사 대입](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하지 않도록 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 전달한다는 것입니다.

    따라서 다음과 같이 [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)로 이동된 `param`을 다시 사용한다면 낭패이니 주의하세요.

    ```cpp
    void f(T val) {}
    void Forwarding_11(T param) {
        f(std::forward<T>(param)); // 우측값 참조로 전달합니다.

        param.GetData(); // (X) 오동작. param은 이동되었습니다. 사용하시면 안됩니다. 
    }   
    ```

2. **이름이 없는 값 타입인 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value) 타입이 전달된 경우**

    다음과 같이 `Forwarding_11()`함수 내에서 직접 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)등으로 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)한 경우입니다.

    ```cpp
    class A {};
    void f(A&& val) {}
    void Forwarding_11(A& param) {
        f(std::forward<A&&>(std::move(param))); // move() 함수로 우측값으로 형변환 합니다.
    }

    A a;
    Forwarding_11(a);
    ```

    이경우에는 `#2` [함수 템플릿 오버로딩](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)이 사용되며, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수는 `T == A&&`을 전달하여 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)합니다.
    
    ```cpp
    // #1. 템플릿 전개
    A&& + && forward(typename A + && param) {
        return static_cast<A&& + &&>(param);
    }

    // [참조 축약]을 반영하면 다음과 같습니다. 즉 `A&&` 를 전달받아 `A&&`로 리턴합니다.
    A&& forward(typename A&& param) {
        return static_cast<A&&>(param);
    }
    ```

    즉, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수는 `Forwarding_11.param == A&`를 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)한 `A&&`를 전달받아 `A&&`로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)합니다. 

    또한, `T == A&&` 말고 `T == A`로 전달하는 경우인 `std::forward<A>(param)` 로 전달하는 경우도 살펴보면,

    ```cpp
    // #1. 템플릿 전개
    A + && forward(typename A + && param) {
        return static_cast<A + &&>(param);
    }

    // 참조 축약을 반영하면 다음과 같습니다. 즉 `A&&` 를 전달받아 `A&&`로 리턴합니다.
    A&& forward(typename A&& param) {
        return static_cast<A&&>(param);
    }
    ```

    `std::forward<A&&>(param)` 와 동일하게 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)됩니다.

    즉, `forward<A>`와 `forward<A&&>`는 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 결과가 동일하므로, 구분해서 호출할 것 없이 `forward<A>`로 호출해도 됩니다.    

3. **좌측값 참조 타입이 전달된 경우**

    다음과 같이 `Forwarding_11()` 함수에 `A&` 타입의 값을 전달한 경우 입니다. 

    ```cpp
    class A {};
    void f(A& val) {}
    void Forwarding_11(A& param) {
        f(std::forward<A&>(param));
    }

    A a;
    A& ref = a;
    Forwarding_11(ref);
    ```

    `#1` [함수 템플릿 오버로딩](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)이 사용되며, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수는 `T == A&`을 전달하여 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)합니다. 
        
    ```cpp
    // #1. 템플릿 전개
    A& + && forward(typename A + & param) {
        return static_cast<A& + &&>(param);
    }

    // [참조 축약]을 반영하면 다음과 같습니다. 즉 `A&` 를 전달받아 `A&`로 리턴합니다.
    A& forward(typename A& param) {
        return static_cast<A&>(param);
    }
    ```

    즉, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수는 `Forwarding_11.param == A&`를 `A&`로 전달받아 `A&`로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)합니다. 

4. **우측값 참조 타입이지만, 이름이 부여된 좌측값이 전달된 경우**

    다음과 같이 `Forwarding_11()` 함수에 `A&&` 타입의 값을 전달한 경우입니다. 

    ```cpp
    class A {};
    void f(A&& val) {}
    void Forwarding_11(A&& param) {
        f(std::forward<A&&>(param)); // param은 우측값을 참조하는 좌측값입니다.
    }

    A a;
    Forwarding_11(std::move(a));
    ```
    `#1` [함수 템플릿 오버로딩](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-specialization/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)이 사용되며, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수는 `T == A&&`을 전달하여 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)합니다. 
        
    ```cpp
    // #1. 템플릿 전개
    A&& + && forward(typename A + & param) {
        return static_cast<A&& + &&>(param);
    }

    // 참조 축약을 반영하면 다음과 같습니다. 즉 `A&` 를 전달받아 `A&&`로 리턴합니다.
    A&& forward(typename A + & param) {
        return static_cast<A&&>(param);
    }
    ```

    `Forwarding_11()`이 전달받은 `param`은 `A&&` 이지만 이름이 부여됐기 때문에 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 `A&` 입니다.(*[이름이 부여된 우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%A6%84%EC%9D%B4-%EB%B6%80%EC%97%AC%EB%90%9C-%EC%9A%B0%EC%B8%A1%EA%B0%92) 참고*)

    그래서 `Forwarding_11.param == A&`이며, [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)함수는 `Forwarding_11.param == A&`를 `A&`로 전달받아 `A&&`로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)합니다. 

    또한, `T == A&&` 말고 `T == A`로 전달하는 경우인 `std::forward<A>(param)` 로 전달하는 경우도 살펴보면,

    ```cpp
    // #1. 템플릿 전개
    A + && forward(typename A + & param) {
        return static_cast<A + &&>(param);
    }

    // 참조 축약을 반영하면 다음과 같습니다. 즉 `A&` 를 전달받아 `A&&`로 리턴합니다.
    A&& forward(typename A& param) {
        return static_cast<A&&>(param);
    }
    ```

    `std::forward<A&&>(param)` 와 동일하게 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)됩니다.

    즉, `forward<A>`와 `forward<A&&>`는 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94) 결과가 동일하므로, 구분해서 호출할 것 없이 `forward<A>`로 호출해도 됩니다.


결론적으로 전달되는 타입에 따라 다음과 같이 변환합니다. [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인 경우만 빼고는 모두 `forward<A>`의 형태로 `&`, `&&` 명시 없이 호출할 수 있습니다.

|항목|형태|[리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)|
|--|--|--|
|이름이 있는 값 타입([좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value))|`forward<A>(값타입)`|`A&&`|
|이름이 없는 값 타입([우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value))|`forward<A&&>(우측값)`<br/>`forward<A>(우측값)`|`A&&`|
|[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`forward<A&>(좌측값 참조)`| `A&`|
|[함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)처럼 [우측값을 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)하는 [좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`forward<A&&>(좌측값 참조)`<br/>`forward<A>(좌측값 참조)`|`A&&`|


[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)인 경우는 다음과 같이 `forward<U>()`, `forward<V>()`, `forward<W>()`처럼 모두 `&`나 `&&` 명시 없이 사용할 수 있습니다.

```cpp
class A {};
void f(A param) {}
void g(A& param) {}
void h_11(A&& param) {}

template<typename U, typename V, typename W>
void Forwarding_11(U&& param1, V&& param2, W&& param3) {

    // 전달 참조는 이름이 있는 값 타입을 좌측값 참조로 받습니다.
    // 따라서, U == A&, param1 == A& 이며,
    // forward<U>는 T == A&로 템플릿 인스턴스화 합니다.
    // template<typename T> 
    // A& + && forward(A + & param) {
    //    return static_cast<A& + &&>(param);
    // }
    // 즉, A&를 A&로 형변환 합니다.
    f(std::forward<U>(param1));

    // 전달 참조는 좌측값 참조 타입을 좌측값 참조로 받습니다.
    // 따라서, V == A&, param1 == A& 이며,
    // forward<V>는 T == A&로 템플릿 인스턴스화 합니다.
    // template<typename T> 
    // A& + && forward(A + & param) {
    //    return static_cast<A& + &&>(param);
    // }
    // 즉, A&를 A&로 형변환 합니다.
    g(std::forward<V>(param2));

    // 전달 참조는 우측값 참조 타입을 우측값 참조로 받습니다.
    // 또는 이름이 없는 값 타입인 임시 개체도 우측값 참조로 받습니다.
    // 따라서, W == A&&, param3 == A& 이며,(param3은 이름이 부여됐으므로 좌측값입니다.)
    // forward<W>는 T == A&&로 템플릿 인스턴스화 합니다.
    // template<typename T> 
    // A&& + && forward(A + & param) {
    //    return static_cast<A&& + &&>(param);
    // }
    // 즉, A&를 A&&로 형변환 합니다.
    h_11(std::forward<W>(param3));
}

A a;
A b;
A& ref = a;

Forwarding_11(a, ref, std::move(b));
```

결론적으로는 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 사용한 경우에 [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수는 다음과 같이 변환합니다.

|`Forwarding_11()`에 전달하는 항목|[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)|[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수의 [템플릿 인스턴스화](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)|[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 함수의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)|
|--|--|--|--|
|이름이 있는 값 타입([좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value))|`U == A&, param1 == A&`|`T == A&, param == A&`|`T&`|
|[좌측값 참조](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)|`V == A&, param2 == A&`|`T == A&, param == A&`| `T&`|
|이름이 없는 값 타입([우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)), 혹은 `move()`로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)된 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)|`W == A&&, param3 == A&(우측값을 참조하는 좌측값)`|`T == A&&, param == A&`|`T&&`|

이름이 있는 값 타입인 경우 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 와 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아닌 경우가 서로 다른데요,

* [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) : 값 타입을 `T&`로 변환
* [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아닌 경우: 값 타입을 `T&&`로 변환

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 사용하는게 포워딩 관점과 유지보수 측면에서 더 좋은 선택입니다.

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)의 `Forwarding1_11()`함수는 전달된 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 아무런 수정없이 그대로 `f()`함수에 전달하고, `f()`가 `f(A val)`와 같이 값 타입으로 정의되어 있어 `f()`에서 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)합니다.

일반 버전의 `Forwarding2_11()`은 `param`에 [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)한 후 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0)로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)한뒤 `f(A val)`에 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)합니다. 

속도 성능은 동일하겠지만, 포워딩 함수가 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달한다는 역할 측면에서 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 버전이 좀더 충실하게 역할을 수행한다고 볼 수 있습니다. 또한 향후에 `f(A val)`를 리팩토링 하여 `f(A& val)`로 바꾼다면, `Forwarding2_11(A param)`도 `Forwarding2_11(A& param)`로 수정해야 하죠. 그래서 유지보수성도 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 버전이 좋습니다.

```cpp
class A {};
void f(A val) {}

// #1. 전달 참조 버전
template<typename U>
void Forwarding1_11(U&& param) { // 좌측값 참조를 받아서
    f(std::forward<U>(param));  // val이 값 타입 이기 때문에 임시 개체를 생성하고 복사합니다.
}
// #2. 일반 버전
void Forwarding2_11(A param) { // param을 복사 생성하고
    f(std::forward<A>(param)); // 우측값으로 val에 이동시킵니다.
}
```

# 전달 참조를 이용한 함수 오버로딩

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 이용하면 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 갯수를 줄일 수 있습니다.

예를 들어 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)개체를 세팅하는 함수는 다음의 3가지 버전이 필요합니다.

```cpp
class A_11 {
    std::string m_String;
public:
    void SetString(const std::string& str) {m_String = str;} // 좌측값으로 세팅
    void SetString(std::string&& str) {m_String = str;} // 임시 개체이면 이동 연산을 위해 우측값으로 세팅
    void SetString(const char* str) {m_String = str;} // const char*로 세팅.    
};
```

[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 이용하면 다음 한개의 함수로 구현할 수 있습니다. 코드량을 현저히 줄일 수 있죠.

```cpp
class A_11 {
    std::string m_String;
public:
    template<typename T>
    void SetString_11(T&& param) { // 전달 참조
        // const T& 를 전달받으면 const T& 전달
        // T&& 를 전달받으면 T&& 전달
        // const char* 를 전달받으면 cosnt char* 전달
        m_String = std::forward<T>(param); 
    }
}; 

A_11 a;

std::string str = "Hello";
a.SetString_11(str); // 값타입으로 전달. string&로 전달합니다.
a.SetString_11(std::move(str)); // 우측값 참조로 전달, string&&로 전달
a.SetString_11("World"); // (const char*)& 로 전달
```

하지만, [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 사용한 함수는 거의 대부분의 타입과 매칭되어 원하지 않는 타입까지 호출해버리는 문제가 있습니다. 

다음 코드를 보면, `short`, `char`등 `int`로 [암시적 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)되는 타입들도 `Func_11(int)`이 아닌 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 버전을 호출하는데요,

```cpp
class A_11 {
public:
    template<typename T>
    int Func_11(T&& param) {return 1;} // 어지간 하면, 다 전달 참조 버전이 호출됩니다.
    int Func_11(int) {return 2;} // int 만 호출됩니다.
}; 

A_11 a;

EXPECT_TRUE(a.Func_11(1) == 2); // int 버전이 호출됩니다. 
EXPECT_TRUE(a.Func_11((short)1) == 1); // (△) 비권장. 전달 참조 버전이 호출됩니다.
EXPECT_TRUE(a.Func_11((char)'a') == 1); // (△) 비권장. 전달 참조 버전이 호출됩니다.
```

이는 [오버로딩된 함수 탐색 규칙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9-%ED%95%A8%EC%88%98-%ED%83%90%EC%83%89-%EA%B7%9C%EC%B9%99)에서 언급한 바와 같이 [암시적 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 보다는 타입 완전 일치가 우선이기 때문입니다. [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)의 추론 버전은 타입이 완전히 일치하거든요.

따라서 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 제공하는 함수는 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 하지 않는게 좋습니다. 

하지만, 꼭 해야 하는 경우가 생긴다면, [CloneTraits 구현](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-meta-programming/#clonetraits-%EA%B5%AC%ED%98%84)에서 처럼 `Tag`를 이용하여 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 할 수 있습니다.

다음은 기본적으로 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 버전을 호출한뒤 함수내에서 정수 계열인지 아닌지 검사하여 `FuncInternal_11(T&&, false_type)`와 `FuncInternal_11(T&&, true_type)`으로 분기합니다.(*`is_integral`, `true_type`, `false_type`은 [타입 특성 클래스](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-traits/)를 참고하시기 바랍니다.*)

```cpp
class A_11 {
public:
    template<typename T>
    int Func_11(T&& param) {
        FuncInternal_11(
            std::forward<T>(param),
            std::is_integral<typename std::remove_reference<T>::type>() // 정수 타입이면 true_type, 아니면 false_type
        );
    }

    template<typename T> 
    int FuncInternal_11(T&& param, std::false_type) {return 1;} // 정수 외 계열이 호출합니다.

    int FuncInternal_11(int, std::true_type) {return 2;} // 정수 계열이 호출합니다.
}; 

A_11 a;

EXPECT_TRUE(a.Func_11(1) == 2); // (O) 정수 계열은 FuncInternal_11(int, true_type)이 호출됩니다.
EXPECT_TRUE(a.Func_11((short)1) == 2); // (O) 정수 계열은 FuncInternal_11(int, true_type)이 호출됩니다.
EXPECT_TRUE(a.Func_11((char)'a') == 2); // (O) 정수 계열은 FuncInternal_11(int, true_type)이 호출됩니다.  
EXPECT_TRUE(a.Func_11("Hello") == 1);  
```

또한, [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)로 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)를 만들면 좀더 심각해 집니다.

다음 코드에서 `A_11 b(a);` 와 같이 실행해도 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 실행되지 않고 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 버전이 실행되어 버립니다. [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)는 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 `const A_11&` 이고 `a`는 `A_11&` 여서, 사실 타입이 완전히 일치하는 건 아니거든요. 그래서 `const_cast<const A_11&>`로 억지로 [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)해야 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출할 수 있습니다.

```cpp
class A_11 {
public:
    int m_Val;
public:
    A_11() {m_Val = 0;}

    template<typename T>
    A_11(T&& param) {m_Val = 1;} // 어지간 하면, 다 전달 참조 버전이 호출됩니다.
    
    A_11(const A_11& other) {m_Val = 2;} // 복사 생성자
}; 

A_11 a;

A_11 b(a); // (△) 비권장. 복사 생성자를 불러봐도 A_11&로 평가되어 전달 참조 버전이 호출됩니다.
EXPECT_TRUE(b.m_Val == 1);

A_11 c(const_cast<const A_11&>(a)); // 강제로 const A_11& 로 바꾸면 복사 생성자가 호출됩니다.
EXPECT_TRUE(c.m_Val == 2);    
```

[const_cast](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)를 써야 하다니, [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)를 안쓰고 말지, [형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/)은 정말 싫죠. 다행히 우회할 방법이 있습니다.

[enable_if](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#enable_if)를 이용하면 되는데요,

1. `typename = enable_if<조건>::type`으로 조건이 거짓이면 [SFINAE](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)에 의해 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 후보 목록에서 제외됩니다.
2. [is_integral](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%EA%B8%B0%EB%B3%B8-%ED%83%80%EC%9E%85-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC) 로 `T`가 정수 타입인지 검사합니다.
3. [decay](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type_traits/#%EA%B8%B0%ED%83%80-%EB%B3%80%ED%99%98)를 이용하여 `T`의 [참조성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거하고 검사합니다.

다음에서는 `A_11 b(a)`을 하면 [SFINAE](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-template-argument-deduction/#sfinaesubstitution-failure-is-not-an-error)에 의해 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 버전 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)가 제외되어 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출되고, `A_11 c(val)`와 같이 `int`계열을 전달하면 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0) 버전 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)가 호출됩니다.

```cpp
class A_11 {
public:
    int m_Val;
public:
    A_11() {m_Val = 0;}

    template< 
        typename T, 
        typename = typename std::enable_if< // 조건이 거짓이면 type이 정의되지 않아 오버로딩 함수 후보 목록에서 제외됩니다.
            std::is_integral< 
                typename std::decay<T>::type // 전달된 T의 참조성을 제거한 타입을 검사합니다.
            >::value // 조건. 정수형 타입이면 true 입니다.
        >::type // 조건이 true인 경우에만 enable_if<>::type이 정의됩니다.
    >
    A_11(T&& param) {m_Val = 1;} // 정수 타입만, 전달 참조 버전이 호출되도록 합니다.
    
    A_11(const A_11& other) {m_Val = 2;} // 복사 생성자
}; 

A_11 a;

A_11 b(a); // 복사 생성자가 호출됩니다.
EXPECT_TRUE(b.m_Val == 2);

int val;
A_11 c(val); // 전달 참조 버전이 호출됩니다.
EXPECT_TRUE(c.m_Val == 1);

A_11 d((char)'a'); // 전달 참조 버전이 호출됩니다.
EXPECT_TRUE(d.m_Val == 1);
```

# 전달 참조와 중괄호 초기화

[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)는 대입하는 타입에 맞게 초기값을 추론하다 보니 
[전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)와는 궁합이 맞지 않습니다.

다음과 같이 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)가 아니라면, [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)는 `const vector<int>& v`로 전달하기 위해 암시적으로 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)를 생성하여 전달합니다.(*[initializer_list의 암시적 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list%EC%9D%98-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%83%9D%EC%84%B1) 참고*)

```cpp
int f(const std::vector<int>& v) {return v.size();}

EXPECT_TRUE(f({1, 2, 3}) == 3); // {1, 2, 3}는 암시적으로 vector<int> 입니다.  
```

하지만 `f()`함수를 [전달 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%A0%84%EB%8B%AC-%EC%B0%B8%EC%A1%B0)로 바꾸면,  [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)는 어떤 타입으로 생성해야 할지 모르므로, 컴파일 오류가 발생합니다.

```cpp
template<typename T>
int f_11(T&& param) {return param.size();}

EXPECT_TRUE(f_11({1, 2, 3}) == 3); // (X) 컴파일 오류. 중괄호 초기화를 무슨 타입으로 생성할지 모릅니다.
```
따라서 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)를 사용할 경우에는 다음과 같은 2가지 방법을 이용합니다.

1. 명시적으로 `vector<int>`개체를 전달합니다.

2. [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)로 초기화합니다. 이때 [auto의 중괄호 초기화 특수 추론 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto%EC%9D%98-%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8A%B9%EC%88%98-%EC%B6%94%EB%A1%A0-%EA%B7%9C%EC%B9%99)에 따라 `v_11`은 `initializer_list<int>` 로 추론됩니다. `v_11`을 `f_11()` 함수에 전달하면, `initializer_list<int>`로부터 암시적으로 `vector<int>`를 생성합니다. 

```cpp
// 1. 명시적으로 vector<int> 개체를 전달합니다.
std::vector<int> v_11{1, 2, 3};
EXPECT_TRUE(f_11(v_11) == 3);   

// 2. auto로 초기화한 후 auto를 전달합니다.
auto v_11 = {1, 2, 3}; // initializer_list로 초기화 됩니다.
EXPECT_TRUE(f_11(v_11) == 3); // initializer_list로부터 암시적으로 vector를 생성하여 전달합니다.
```

# forward()가 적합한 곳에 move()의 잘못된 사용

[forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC)가 적합한 곳에 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 잘못 사용하면, [좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)까지 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)되어 데이터가 무효화되어 사용할 수 없게 됩니다. 


```cpp
class A_11 {
    std::string m_String;
public:
    template<typename T>
    void SetString_11(T&& str) { // 전달 참조
        // (△) 비권장. forward()가 적합한데, move()를 사용했습니다. 무조건 이동 연산 합니다.
        m_String = std::move<T>(str); 
    }
}; 

A_11 a;

std::string str = "Hello";
a.SetString_11(str); // (△) 비권장. str은 이동 연산되어 무효화됩니다.

std::string str2 = str; // (△) 비권장. 이동 연산되어 무효화된 값으로 str2를 초기화했습니다.
EXPECT_TRUE(str2.empty() == true); 
```

따라서, 함수의 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 그냥 전달하는 것인지, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 하는 것인지 잘 구분해서 적용해야 합니다.

# 값 타입 리턴에서 move()의 비효율성

다음과 같이 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 정의한 `A_11` 클래스가 있다고 합시다. 

```cpp
class A_11 {
public:
    A_11() {std::cout << "A_11 : Default Constructor" << std::endl;}    
    A_11(const A_11&) {std::cout << "A_11 : Copy Constructor" << std::endl;}  
    A_11(A_11&&) noexcept {std::cout << "A_11 : Move Constructor" << std::endl;}  
    
    A_11& operator =(const A_11& other) = delete;
    A_11& operator =(A_11&& other) noexcept = delete;
};
```

이를 리턴하는 함수 `f()`를 다음과 같이 구현하여 사용할 수 있는데요, 

```cpp
A_11 f() {
    A_11 result; // 지역 변수를 정의합니다.
    return result; 
}
A_11 a = f(); // 리턴하면서 a 개체를 복사 생성합니다.
``` 

 `f()`에서 `result`를 생성하면서 1회, `f()`에서 리턴한 값으로 `a`를 생성하면서 2회 생성될 것 같지만, [리턴값 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)에 의해 리턴할 개체 `result`를 그냥 `a`로 사용하게 하여 1회만 생성됩니다. 

```cpp
A_11 : Default Constructor
```

하지만, 불필요한 복사 연산을 최소화 하겠다며, 직접 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 이용하여 리턴하면,  

```cpp
A_11 f_11() {
    A_11 result; // 지역 변수를 정의합니다.
    return std::move(result); // 리턴하면서 개체를 이동합니다.
} 
A_11 a = f_11(); // f()가 리턴한 임시 개체로 a를 생성합니다 
```

다음과 같이 [리턴값 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)는 동작하지 않고, [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90) 1회와 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90) 1회가 호출됩니다.

```cpp
A_11 : Default Constructor
A_11 : Move Constructor
```

불필요한 복사 연산은 없지만, [리턴값 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)할때보다 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)이 추가되어 오히려 비효율적입니다. 따라서 값 타입을 리턴할때는 직접 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 사용하여 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 하기 보다는 그냥 [리턴값 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)를 사용하는 편이 훨씬 낫습니다.

더군다나, 혹여나 컴파일러가 [리턴값 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)를 할 수 없다면, 리턴되는 개체를 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 취급한다고 하니, 굳이 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 사용할 필요는 없습니다. 굳이 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 사용해서 컴파일러의 [리턴값 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo) 기회를 빼앗지 마세요.


> *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.*

# 값 타입 인자, 우측값 타입 인자를 리턴할 경우 move()의 효율성

[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 다시 리턴하는 함수라면 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move) 사용을 고려하는게 좋습니다. 값 타입이나 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)을 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 받았다면, [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)함수로 값 타입을 리턴하는 것이 효율적입니다.

```cpp
A f_11(A param) {
    return std::move(param); // (O) param은 복사 대입 받아 f_11 함수에서만 사용하므로, 리턴시에 이동시켜도 무방합니다.
} 
A g_11(A& param) {
    return std::move(param); // (△) 비권장. param은 다른 곳에서 사용할 수도 있는 좌측값 참조이므로 함부로 이동시키면 안됩니다.
}
A h_11(A&& param) {
    return std::move(param); // (O) param은 우측값이므로 임시 개체입니다. 리턴시에 이동시켜도 무방합니다.
} 
```

