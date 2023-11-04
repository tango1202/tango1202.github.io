---
layout: single
title: "#6. [모던 C++] (C++11~) 우측값, 값 카테고리, 이동 연산, 이동 생성자, 이동 대입 연산자, move()"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#17] 특수 멤버 함수들의 자동 작성 조건을 숙지하라.([이동 연산에 따른 암시적 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%97%90-%EB%94%B0%EB%A5%B8-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98) 참고)
> * [MEC++#29] 이동 연산이 존재하지 않고, 저렴하지 않고, 적용되지 않는다고 가정하라.

> * (C++11~) [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 위해 [우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다.
> * (C++11~) [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가됨에 따라 [기본 생성자, 복사 생성자, 복사 대입 연산자, 이동 생성자, 이동 대입 연산자, 소멸자의 암시적 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%97%90-%EB%94%B0%EB%A5%B8-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98)가 재정립 되었습니다.
> * (C++14~) [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)는 주어진 값을 바꾸고 이전값을 리턴합니다. [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.

# 개요

C++의 STL을 보면 대부분 값 기반 복사/대입([컨테이너 요소 규칙](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9A%94%EC%86%8C-%EA%B7%9C%EC%B9%99) 참고)이 이루어져 속도 성능이 저하될 수 있습니다. [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 하니까요. 이런 문제를 해결하고자 C++11 부터 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 지원하게 되었으며, [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)과 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)에 대한 이해가 있어야 이를 활용할 수 있습니다. [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)도 어려웠습니다만, [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)과 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC)는 더욱 더 어렵습니다. 하지만 이를 이해하지 않고는 모던 C++를 활용할 수 없기에 꼭 이해해야만 합니다.

다른 언어에서는 값을 대입하려면 별다른 고민 없이 그냥 `=` 연산자로 넣어버리면 그만입니다. 하지만 C++에서는 다릅니다. 속도 성능을 중시하기에, 그리고, 비효율적인 것은 피를 토할 정도로 싫어하기에, 대입시 불필요한 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 있느냐 없느냐는 너무도 중요합니다. 차라리 프로그램이 다운되는 버그는 실수이니 너그럽게 이해할 수 있지만, 불필요한 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)은 절대 참을 수 없죠.

기존에는 모든걸 그저 개발자에게 모든걸 의존했지만, 이제는 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)과 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 통해 언어 차원에서 지원합니다. [기존 코드와 완전하게 호환성을 유지](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0-%EB%B3%80%ED%99%98%EC%9D%98-%EC%95%88%EC%A0%84%EC%84%B1)하면서 말이죠.

[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)과 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 컨셉은 버려질 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 알뜰하게 활용하자 입니다.

다음 코드를 예로 들면,

1. 오른쪽 표현식(`a + b`)의 결과 값은 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)입니다.
2. [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)는 어짜피 버려질 것이므로 굳이 `c`에 복사하지 않고 이동시켜 벼리면 속도가 조금이라도 빨라집니다.
3. 따라서, `a + b`로 만들어진 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 `c`에 복사하지 않고 이동시킵니다. `c`에 이동시키고 난 뒤에는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)이니 소멸되던, 버려지던, 쓰레기 값이 되던 더이상 신경쓰지 않습니다.

```cpp
T a, b, c; // a, b, c 는 좌측값
c = a + b; // a + b 연산 결과는 임시 개체로서 우측값. c에 이동시키고 난 뒤에는 임시 개체이니 소멸되던, 버려지던, 쓰레기 값이 되던 더이상 신경쓰지 않습니다.
```

# 좌측값(lvalue, left Value)과 우측값(rvalue, right value)

C언어에서의 정의는 다음과 같습니다.

|항목|내용|
|--|--|
|좌측값|메모리에 할당된 변수처럼 대입 연산시에 왼쪽 또는 오른쪽에 올 수 있는 표현식<br/>`int left;`<br/>`left = 10; // 왼쪽에 올 수 있음`<br/>`int another = left; // 오른쪽에 올 수 있음`|
|[우측값](?)|일련의 수식처럼 대입 연산시에 오른쪽에만 올 수 있는 표현식<br/>`int c = a + b;// c 에 대입되는 연산 결과는 임시 개체로서 우측값임.`<br/>`a + b = 10; // 우측값은 대입 연산의 왼쪽에 올 수 없음`|

C++언어에서의 정의는 좀더 구체적으로 다음과 같습니다.

|항목|내용|
|--|--|
|좌측값|변수처럼 어떠한 메모리 위치를 가리키며 `&`로 그 위치를 가리킬 수 있는 표현식|
|[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)|좌측값이 아닌 값. 즉 위치를 참조할 수 없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)나 [리터럴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/)(*[문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)는 주소가 있으므로 좌측값*)|

즉, 다음처럼 `&`연산으로 ***포인터 주소값을 얻을 수 없으면 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)*** 입니다.

```cpp
int a = 0; 
int* ptr1 = &a; // a는 &로 위치 참조가 되므로 좌측값
int* ptr2 = &0; // (X) 컴파일 오류. 상수 0은 & 위치 참조가 안되므로 우측값
int b = &(a + 10); // (X) 컴파일 오류. 수식 (a + 10)은 & 위치 참조가 안되므로 우측값
```

혹은 ***[참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 받을 수 없으면 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)*** 입니다.

```cpp
int a = 0; 
int& ptr1 = a; // a는 &로 참조되므로 좌측값
int& ptr2 = 0; // (X) 컴파일 오류. 상수 0은 &로 참조가 안되므로 우측값
int& b = (a + 10); // (X) 컴파일 오류. 수식 (a + 10)은 & 참조가 안되므로 우측값
```

# 값 카테고리

C++에서는 표현식에 대해서 이름(*식별자*)가 있는지, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 지원되는지에 따라, 표현식의 값을 다음처럼 분류했습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f8e1a357-ab67-4186-8cad-c5fdcda930ef)

|항목|내용|
|--|--|
|`lvalue`(left value)|좌측값으로서 변수와 같이 식별자가 있는 항목입니다. `lvalue = 10;`처럼 좌측에 올 수도 있고 `var = lvalue;`와 같이 우측에 올 수도 있습니다. 다른곳에서 참조할 수도 있기에 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 지원되지 않습니다.(*변수, [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/), 함수, `&`포인터 연산, [전위 증감 연산](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A6%9D%EA%B0%90-%EC%97%B0%EC%82%B0%EC%9E%90), [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)등*)<br/>대입문의 좌측에 올 수 있고, `&`로 주소를 얻어올 수 있으며, 표현식이 끝나더라도 존재합니다.|
|`xvalue`(eXpiring value)|`lvalue`나 `prvalue`를 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)할때 순간적으로 관리되는 값으로서 만료되어 가는 값입니다. 해당 주소로 접근했을때 값이 있을 수도 있고, 없을 수도 있습니다. 컴파일러만 사용하므로 `&`로 주소를 얻을 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`prvalue`(pure rvalue)|순수한 `rvalue`로서 식별자가 없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)나 수식들입니다. 어짜피 임시로 생성된 것이므로 맘편히 삭제해도 되는 것들이며, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 지원됩니다.(*수식, [후위 증감 연산](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A6%9D%EA%B0%90-%EC%97%B0%EC%82%B0%EC%9E%90), [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)를 제외한 [리터럴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/), [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/) 등*)<br/>대입문의 우측에 올 수 있고, `&`로 주소를 얻어올 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`glvalue`(generalized lvalue)|`xvalue`와 `lvalue`를 통칭합니다.| 
|`rvalue`(right value)|`xvalue`와 `prvalue`를 통칭합니다.|  

# 이동 연산(move semantics)의 속도 성능

일반적으로 복사/대입보다는 이동이 성능이 좋을 수 있습니다.

다음은 일반적인 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 구현입니다. 

* [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)에서는 새로운 메모리 공간을 할당해서 복사하고, 
* [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에서는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 생성한뒤 `swap()`으로 바꿔치기 합니다.(*[포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)과 [swap을 이용한 예외 보증 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고*)

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {
        std::cout << "Big : Default Constructor" << std::endl; 
    }

    // 소멸자
    ~Big() {
        std::cout << "Big : Destructor" << std::endl; 
        delete[] m_Ptr;
    }

    // 복사 생성자
    Big(const Big& other) :
        m_Size(other.m_Size) {
        std::cout << "Big : Copy Constructor" << std::endl; 

        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 복사 대입 연산자
    Big& operator =(const Big& other) {
        std::cout << "Big : operator =" << std::endl; 

        Big temp(other); // 복사 생성
        std::swap(m_Size, temp.m_Size); // 바꿔치기
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }

    size_t GetSize() const {return m_Size;}
}; 
Big a(10);
Big b(20);
a = b;
EXPECT_TRUE(a.GetSize() == 20);
EXPECT_TRUE(b.GetSize() == 20); 
```

실행 결과는 다음과 같습니다.

```cpp
Big : Default Constructor // a 생성
Big : Default Constructor // b 생성
Big : operator = 
Big : Copy Constructor // b를 temp에 복사
Big : Destructor // temp 소멸
Big : Destructor // b 소멸
Big : Destructor // a 소멸
```

여기서 만약 `b`가 `a`에 값을 대입하고, 더이상 사용되지 않는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(*[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)*)라면 어떨까요? 굳이 `temp`를 생성해서 복제본을 만들 필요없이 `b`의 값을 바로 `a`에 전달(*[이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)*)해도 됩니다. `a`가 기존 것을 버리고, `b` 데이터만 가지면 되니까요. 

다음 그림에서 `a = b;`등으로 `a`가 `b`의 데이터를 갖도록 만들때, [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)과 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 차이를 도식화 했습니다. [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 훨씬 간결한 것을 알 수 있습니다.(*다만, `b`가 참조하는 데이터는 없어집니다.*)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e4333269-db1f-41e5-8ec7-308db82869a2)

다음 코드에서 `Move()`함수는 데이터를 이동시킵니다. 기존 것은 삭제하고, `other`것을 참조한 뒤, 소유권 분쟁이 없도록 `other`는 초기화 합니다.

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {
        std::cout << "Big : Default Constructor" << std::endl; 
    }

    // 소멸자
    ~Big() {
        std::cout << "Big : Destructor" << std::endl; 
        delete[] m_Ptr;
    }

    // 복사 생성자
    Big(const Big& other) :
        m_Size(other.m_Size) {
        std::cout << "Big : Copy Constructor" << std::endl; 

        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 복사 대입 연산자
    Big& operator =(const Big& other) {
        std::cout << "Big : operator =" << std::endl; 

        Big temp(other);
        std::swap(m_Size, temp.m_Size);
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }

    // 이동 함수
    Big& Move(Big& other) {
        std::cout << "Big : Move()" << std::endl; 

        delete[] m_Ptr; // 기존 것은 삭제하고,
        m_Size = other.m_Size; // other 것을 저장한뒤
        m_Ptr = other.m_Ptr;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = nullptr;
        return *this;
    }
    
    size_t GetSize() const {return m_Size;}
}; 
Big a(10);
Big b(20);
a.Move(b);
EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 0);   
```

실행 결과는 다음과 같습니다. 쓸데없는 `temp`의 생성이 빠졌습니다. 생성 부하나 복사 부하가 상당히 큰 개체라면 이동을 통해 성능 향상을 기대할 수 있죠. 

```cpp
Big : Default Constructor // a 생성
Big : Default Constructor // b 생성
Big : Move()
Big : Destructor // b 소멸
Big : Destructor // a 소멸
```

# 이동 연산(이동 생성, 이동 대입) : 우측값 참조, 이동 생성자, 이동 대입 연산자

그냥 각자 `Move()`함수를 구현할 수도 있습니다만, C++ 표준화 위원회는 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 정립해서 표준으로 만들었습니다. 덕택에 우리가 눈치채지 못한 곳에서 스리슬쩍 실행됐던 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이  [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 대체되어 속도 향상이 있을 수 있습니다.

1. `=` 에 전달되는 인수가 식별자가 부여되지 않은(*이름이 없는*) [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 인 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)(`rvalue`) 이고, 
2. [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 정의되어 있다면,

3. `=`시 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 수행하고, 그렇지 않으면 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 수행합니다.

즉, 버려질 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 대입되면 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 수행하여 속도를 향상시키고, 그렇지 않으면 기존처럼 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 합니다. 따라서 기능 호환성도 보장됩니다.

이에 따라 C++11 부터는 

* [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 타입인 `&&`와 
* [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)인 `T(T&& other)`와
* [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)인 `T& operator =(T&& other)`가 제공됩니다.

또한 개체의 타입이 `T` 라고 할때 `T&`를 그냥 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 라고 했지만, 이젠 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)의 개념이 도입되면서 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)인지, [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)인지 구분합니다.

* `T&`는 개체의 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)라 하고,
* `T&&`는 개체의 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)라고 합니다.

다음 코드에서 `Move()`함수를 [우측값을 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하는 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 변경하였으며, 테스트 코드에서 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)을 사용하기 위해 다음 3가지 방법을 사용했습니다.


|항목|내용|
|--|--|
|[static_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|좌측값을 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 형변환합니다.|
|[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)|좌측값을 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 형변환합니다.<br/>[static_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)보다 가독성이 좋습니다.|
|`Big_11(40)`| [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)인 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)을 생성해서 대입했습니다.|

```cpp
class Big_11 {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big_11(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {}

    // 소멸자
    ~Big_11() {delete[] m_Ptr;}

    // 복사 생성자
    Big_11(const Big_11& other) :
        m_Size(other.m_Size) {
        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 복사 대입 연산자. 좌측값(lvalue) 대입
    Big_11& operator =(const Big_11& other) {
        Big_11 temp(other);
        std::swap(m_Size, temp.m_Size);
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }
    // 이동 대입 연산자. 우측값(rvalue) 이동
    Big_11& operator =(Big_11&& other) {
        delete[] m_Ptr; // 기존 것은 삭제하고,
        m_Size = other.m_Size; // other 것을 저장한뒤
        m_Ptr = other.m_Ptr;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = nullptr;
        return *this;
    }
    
    size_t GetSize() const {return m_Size;}
}; 

Big_11 a(10);
Big_11 b(20);

a = b; // b는 이름이 있는 lvalue. 복사 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 20);  

a = static_cast<Big_11&&>(b); // b 를 이름이 없는 rvalue로 변환. 이동 대입 연산자 호출

EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 0); // b는 이동되어 더이상 쓸 수 없음  

Big_11 c(30);
a = std::move(c); // c 를 이름이 없는 rvalue로 변환. static_cast<Big&&>(c)와 동일. 이동 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 30);

a = Big_11(40); // Big(40) 이름이 없는 rvalue를 생성. 이동 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 40);
```

또한, 같은 방법으로 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 만들 수 있습니다.

```cpp
// 이동 생성자
Big_11(Big_11&& other) : 
    m_Size(other.m_Size),
    m_Ptr(other.m_Ptr) {

    other.m_Size = 0; // other는 초기화 합니다.
    other.m_Ptr = nullptr;
}  

Big_11 d(50);
Big_11 e(std::move(d));
EXPECT_TRUE(d.GetSize() == 0); // d는 이동되어 더이상 쓸 수 없음  
EXPECT_TRUE(e.GetSize() == 50); 
```
> *(C++14~) [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)는 주어진 값을 바꾸고 이전값을 리턴합니다. [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.*

# 이동 생성자

[이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)는 다음과 같이 정의됩니다.

```cpp
T(T&& other);
```

[복사 생성자가 암시적으로 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)되듯이 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)도 암시적으로 생성되며, 멤버별 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.(*단, 멤버별 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)가 정의되지 않았다면, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.*)

단, 다음 조건에서는 [암시적 이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)를 생성하지 않습니다.

* [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우

# 이동 대입 연산자

[이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 다음과 같이 정의됩니다.

```cpp
T& operator =(T&& other);
```

[복사 대입 연산자가 암시적으로 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)되듯이 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)도 암시적으로 생성되며, 멤버별 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출합니다.(*단, 멤버별 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 정의되지 않았다면, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출합니다.*)

단, 다음 조건에서는 [암시적 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)를 생성하지 않습니다.

* [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우
* [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 타입의 멤버 변수가 있는 경우
* 참조 타입의 멤버 변수가 있는 경우

# 이동 연산에 따른 암시적 정의

[클래스의 암시적 정의](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-implicit-definition/)에 언급한 것처럼, 기존에는 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)만 암시적으로 정의했는데요, C++11 부터는 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)와 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 암시적 정의도 추가되어 약간 변경되었습니다. 눈여겨 볼 점은 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 암시적 정의 규칙인데요, [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 정의하면 암시적으로 정의되지 않습니다.

암시적으로 정의되는 조건은 다음과 같습니다.

|항목|내용|
|--|--|
|[기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)|기존과 동일하게 사용자 정의한 다른 생성자가 없으면 암시적으로 정의합니다.|
|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|기존과 동일하게 사용자 정의한 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 없으면 암시적으로 정의됩니다. ***단, C++11에 추가된 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 정의되면 암시적으로 정의되지 않습니다.***|
|[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|기존과 동일하게 사용자 정의한 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 없으면 암시적으로 정의됩니다. ***단, C++11에 추가된 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 정의되면 암시적으로 정의되지 않습니다.***|
|[이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 사용자 정의한 경우에는 정의되지 않습니다.(*세부적인 규칙은 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 참고하세요.*)|
|[이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 사용자 정의한 경우에는 정의되지 않습니다.(*세부적인 규칙은 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 참고하세요.*)|

# 암시적 이동 생성자와 암시적 이동 대입 연산자의 default 정의

[암시적 이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)와 [암시적 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)의 암시적 정의 조건은 상당히 까다롭습니다. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 중 한개라도 정의되면 안되니까요.

이렇게 까다로운 이유는, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)/[이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)/[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)/[이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)/[소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)중 한개라도 정의되었다는 의미는 개체의 생성/대입/이동/소멸중 뭔가 사용자가 특별히 처리한 부분이 있다는 뜻이기 때문입니다.

괜히 암시적 버전을 어설프게 제공했다가 호환이 안될 수 있죠. 따라서 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)시에는 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 없을 경우(*혹은 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입이 달라 호출할 수 없는 경우*), 그냥 기존의 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출합니다. 그러면 속도 성능은 개선되지 않지만, 기존처럼 동작 하니까요. 괜히 무리해서 암시적 버전을 제공하는게 아니라 아예 제공하지 않음으로서  동작 호환성을 보장합니다.(*[이동 연산 변환의 안전성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0-%EB%B3%80%ED%99%98%EC%9D%98-%EC%95%88%EC%A0%84%EC%84%B1) 참고*)

하지만 이 부분은 리팩토링하다 보면 [암시적 이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)와 [암시적 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)가 의도치 않게 가려지는 사이드 이펙트를 유발합니다.

예를 들어 데이터를 관리하는 `CopyableMoveable` 개체가 있다고 합니다. `T`타입의 개체를 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하거나 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)합니다.

```cpp
// 관리하는 개체를 복사 생성이나 이동 생성 합니다.
template<typename T>
class CopyableMoveable_11 {
    T* m_Data;
public:
    CopyableMoveable_11() : m_Data(nullptr) {}

    explicit CopyableMoveable_11(T* data) : m_Data(data) {}
    
    // 복사 생성
    CopyableMoveable_11(const CopyableMoveable_11& other) : 
        m_Data(other.m_Data != nullptr ? new T(*other.m_Data) : nullptr) {
        std::cout << "CopyableMoveable_11 : Copy Constructor" << std::endl;       
    } 

    // 이동 생성
    CopyableMoveable_11(CopyableMoveable_11&& other) : 
        m_Data(other.m_Data) {
        std::cout << "CopyableMoveable_11 : Move Constructor" << std::endl;   

        other.m_Data = nullptr; // other는 초기화 합니다.
    }  

    ~CopyableMoveable_11() {delete m_Data;}
};
```

다음과 같이 `A_11`에서 사용하면 별 문제 없어 보입니다.

```cpp
class A_11 {
private:
    CopyableMoveable_11<int> m_Data;
public:
    explicit A_11(int* data) : m_Data(data) {}
};

A_11 a(new int(10));
A_11 b(a); // 복사 생성자 호출
A_11 c(std::move(a)); // 이동 생성자 호출
```

실행 결과 입니다. 잘 호출되는군요.

```cpp
CopyableMoveable_11 : Copy Constructor
CopyableMoveable_11 : Move Constructor
```

하지만, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 정의하는 순간 [암시적 이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)와 [암시적 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)는 만들어지지 않습니다.

```cpp
class A_11 {
private:
    CopyableMoveable_11<int> m_Data;
public:
    explicit A_11(int* data) : m_Data(data) {}
    ~A_11() {} // 암시적 이동 생성자와 암시적 이동 대입 연산자가 만들어 지지 않습니다.
};

A_11 a(new int(10));
A_11 b(a); // 복사 생성자 호출
A_11 c(std::move(a)); // (△) 비권장. 복사 생성자 호출
```

실행 결과 입니다. [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)은 복사 연산으로 소리 소문없이 바껴버렸습니다. 성능 개선을 꿈꾸고 `CopyableMoveable_11`을 공들여 만들었지만, 물거품이 되버렸습니다. 문제는 이러한 변경을 컴파일러가 감지해 주지 않고, 작성하는 개발자도 쉽게 감지하지 못한다는 겁니다. 

```cpp
CopyableMoveable_11 : Copy Constructor
CopyableMoveable_11 : Copy Constructor
```

따라서 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 사용하는 클래스라면 암시적 버전을 그대로 사용하더라도, 향후 리팩토링시 발생하는 사이드 이펙트를 없애기 위해 미리 `default`를 이용하여 명시적으로 정의해 두는게 좋습니다. 단, [이동 연산에 따른 암시적 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%97%90-%EB%94%B0%EB%A5%B8-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98)의 언급에서 처럼 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 정의되면 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)도 정의해야 합니다.

```cpp
class A_11 {
private:
    CopyableMoveable_11<int> m_Data;
public:
    explicit A_11(int* data) : m_Data(data) {}
    A_11(A_11&) = default; // 암시적 복사 생성자를 사용합니다.
    A_11(A_11&&) = default; // 암시적 이동 생성자를 사용합니다.
    ~A_11() {} // 암시적 이동 생성자와 암시적 이동 대입 연산자가 만들어 지지 않습니다.
};

A_11 a(new int(10));
A_11 b(a); // 복사 생성자 호출
A_11 c(std::move(a)); // 이동 생성자 호출
```
실행 결과는 다음과 같습니다.

```cpp
CopyableMoveable_11 : Copy Constructor
CopyableMoveable_11 : Move Constructor
```

# 암시적 이동 연산 변환

C++11의 컴파일러는 기존 복사 연산을 가능하면 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 대체하여 성능을 향상시키는데요,(***가능하면 이동하되, 필요하면 복사합니다.***)


다음 경우에는 대체하지 않고, 그냥 기존의 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출합니다.



1. [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 없을 경우

2. [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)와  [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입이 다른 경우(*예를 들면 [상수 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4)를 전달한 경우*)

3. 기존 코드와의 [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/) 호환성이 보장되지 않는 경우 

    [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 `push_back()`은 새로운 요소를 추가할때, 용량이 부족하다면, 새로운 메모리 영역을 할당하고, 기존 요소를 새로운 메모리 영역에 복사하고, 새로운 요소를 추가합니다.(*[size와 capacity](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#size%EC%99%80-capacity) 참고*) 이때 요소의 복사 과정에서 예외가 발생하더라도, 원본 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)는 그대로 이므로 `push_back()`은 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 된다고 할 수 있습니다. 

    하지만 이를 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 하면 어떨까요?

    기존 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 요소들을 이동하는 중에 예외가 발생한다면, 원본 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)는 이미 수정된 상태이기에 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 된다고 할 수 없습니다. 따라서, 컴파일러가 마음 놓고 복사 연산을 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 바꿀 수는 없습니다. 이전 코드의 [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/)을 해치니까요.

    그래서, 컴파일러는 ***[이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)이 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 선언되어 예외를 방출하지 않는 경우에만 복사 연산을  [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 대체***합니다.

   따라서, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 위한 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)와 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 최대한 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 만드는게 좋습니다. 그리고, [nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94) 도요.(*궁극적으로 모든 함수가 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)면 어떨까... 상상만 해봅니다.*)

**암시적 성능 향상**

대체적으로 다음의 경우에 기존 복사 연산을  암시적으로 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)으로 대체하여 성능을 향상시킵니다.

1. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 를 사용자 정의하지 않았고,

2. [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)로 대입한 경우


# 이동 연산을 이용한 리팩토링

기존 코드들에 `=`가 많이 사용되고 있을 텐데요, 

다음과 같이 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 정의된 `T` 클래스를 생각해 봅시다. 

* [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 정의되어 [암시적 이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)가 정의되지 않았고(*[이동 연산에 따른 암시적 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%97%90-%EB%94%B0%EB%A5%B8-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98) 참고*), 
* [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)인 `T()`를 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하더라도 [암시적 이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EC%9D%98-default-%EC%A0%95%EC%9D%98)가 없으니 [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 합니다.

```cpp
class T {
public:
    // 복사 생성자를 정의합니다. 이에 따라 암시적 이동 생성자는 정의되지 않습니다.
    T& operator =(const T& other) {
        std::cout << "T : Copy" << std::endl;
        return *this;
    }
};

T a;
T b;

b = a; // 복사 대입 합니다.
b = T(); // T()는 임시 개체인 우측값입니다. 하지만, 복사 대입 연산자가 정의되어 이동 대입 연산자가 암시적으로 정의되지 않았고, 따라서 복사 대입 합니다.
```

상기 코드에 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 선언된 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)만 추가해 두면, 이름이 없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(*[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)*)가 대입될때, [복사 대입](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 대신 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 하게 되어 속도가 향상될 수 있습니다.

```cpp
class T_11 {
public:
    // 복사 생성자를 정의합니다.
    T_11& operator =(const T_11& other) {
        std::cout << "T : Copy" << std::endl;
        return *this;
    }
    // 이동 생성자를 정의합니다.
    T_11& operator =(T_11&& other) noexcept {
        std::cout << "T : Move" << std::endl;
        return *this;        
    }
};

T_11 a;
T_11 b;

b = a; // 복사 대입 합니다.
b = T_11(); // T()는 임시 개체인 우측값입니다. 이동 대입 합니다.
```

또한 명시적으로 [이동 대입](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 호출하여 성능을 향상시킬 수 있습니다.

전통적인 `Swap()`은 다음과 같은데요,

```cpp
void Swap(T& left, T& right) {
    T temp(right); // left가 덮어쓰기 전에 temp에 임시 저장
    right = left;
    left = temp;
}
```

다음과 같이 변경하면, 내부적으로 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 수행하기 때문에 속도를 개선할 수 있습니다. 만약 `T`에 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)나 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 없다면, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출하기 때문에 기존 코드와 동일하게 작동됩니다.

```cpp
void Swap_11(T& left, T& right) {
    T temp(std::move(right)); // right 값을 temp에 옮겨두고,
    right = std::move(left); // left를 right에 옮기고,
    left = std::move(temp); // temp를 left에 옮김
}
```

# move()

[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)는 좌측값(`lvalue`) 을 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)(`rvalue`)로 형변환 합니다. 

다음처럼 [static_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)로도 형변환 할 수 있으나, 가독성을 위해 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 사용합니다. 

```cpp
T t;
static_cast<T&&>(t); // (△) 비권장. 가독성이 떨어집니다.
std::move(t); // (O) 이동 연산을 위해 rvalue로 변환합니다.
```

다음은 `Func_11()`을 `lvalue`와 `rvalue`를 오버로드 한 예입니다. `lvalue`인지 `rvalue`인지에 따라 다른 버전이 호출 됩니다.

```cpp
class A{};
class T {
public:
    static int Func_11(A&) {return 1;} // #1. lvalue
    static int Func_11(A&&) {return 2;} // #2. rvalue
};

A a;

EXPECT_TRUE(T::Func_11(a) == 1); // 이름이 있는 lvalue
EXPECT_TRUE(T::Func_11(static_cast<A&&>(a)) == 2); // 강제 형변환 해서 rvalue
EXPECT_TRUE(T::Func_11(std::move(a)) == 2); // lvalue를 move 해서 rvalue

A& b = a;
EXPECT_TRUE(T::Func_11(b) == 1);  
EXPECT_TRUE(T::Func_11(static_cast<A&&>(b)) == 2);
EXPECT_TRUE(T::Func_11(std::move(b)) == 2);

EXPECT_TRUE(T::Func_11(A()) == 2); // 임시 개체는 rvalue
EXPECT_TRUE(T::Func_11(std::move(A())) == 2); // 임시 개체를 move 해도 rvalue
```

# move_if_noexcept()

`move_if_noexcept()` 는 [noexcept 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)이 되는 경우에만 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 형변환 합니다. 

다음 코드를 보면 `A_11`는 [예외 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/)이 안되어 있고, `B_11`는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 지정되어 있는데요,

1. `A_11`의 경우는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 없어 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출되고,
2. `B_11`의 경우는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 있어 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출됩니다.

```cpp
class A_11 {
public:
    A_11() {}
    // noexcept가 없어서 예외가 발생할 수도 있습니다.
    A_11(const A_11&) {std::cout << "A_11 : Copy Constructor" << std::endl;}
    A_11(A_11&&) {std::cout << "A_11 : Move Constructor" << std::endl;;} 
};
class B_11 {
public:
    B_11() {}
    // nothrow 보증합니다.
    B_11(const B_11&) noexcept {std::cout << "B_11 : Copy Constructor" << std::endl;;}
    B_11(B_11&&) noexcept {std::cout << "B_11 : Move Constructor" << std::endl;;} 
};

A_11 a1;
A_11 a2 = std::move_if_noexcept(a1); // A_11(A_11&&)가 nothrow 예외 보증이 되지 않아 그냥 A_11(const A_11&)를 호출합니다.

B_11 b1;
B_11 b2 = std::move_if_noexcept(b1); // B_11(B_11&&)가 nothrow 예외 보증이 되어 B_11(B_11&&)를 호출합니다.
```

# 이름이 부여된 우측값

[우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에 이름이 부여되면 좌측값이 됩니다. 이 무슨 말장난인가 싶을 수도 있지만, 조금 생각해 보면 이해는 됩니다. 이름이 부여된 순간 더이상 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 아니니까요. 이름이 부여되어 있으니, 어디서든 참조할 수 있고, 함부로 이동시켜서 데이터를 훼손하면 안됩니다.

즉, 다음 `rvalue_11`은 [우측값을 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)하는 좌측값입니다.

```cpp
class A {};

int f_11(A&) {return 1;} // 인자가 좌측값 참조이면 호출됩니다.
int f_11(A&&) {return 2;} // 인자가 우측값 참조이면 호출됩니다.

A lvalue;
A&& rvalue_11 = std::move(lvalue); // rvalue는 이름이 부여됐으므로 좌측값입니다.
EXPECT_TRUE(f_11(rvalue_11) == 1); // f_11(A&)를 호출합니다.
EXPECT_TRUE(f_11(std::move(lvalue)) == 2); // f_11(A&&)를 호출합니다.
```

따라서 모든 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 이름이 있으므로 좌측값입니다. 그러다 보니 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 전달 받은 함수에서 또다시 [우측값 참조](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 전달 받는 함수를 호출하면 의외의 결과가 나옵니다.

다음 예에서 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)버전의 `g_11(A&& val)`를 호출하더라도, 좌측값 버전인 `f_11(A& val)`을 호출하는 걸 알 수 있습니다.

```cpp
class A {};

int f_11(A& val) {return 10;}
int f_11(A&& val) {return 20;}

int g_11(A& val) {
    return f_11(val) + 1;
}
int g_11(A&& val) {
    return f_11(val) + 2; // 여기서 val은 이름이 있으니 우측값이 아닙니다. 그래서 f_11(A& val)을 호출합니다.
}

A lvalue;

// g_11(A& val)와 f_11(A& val)가 호출되어 11입니다.
EXPECT_TRUE(g_11(lvalue) == 11); 

// g_11(A&& val)와 f_11(A& val)가 호출되어 12입니다.
EXPECT_TRUE(g_11(std::move(lvalue)) == 12); 
```

따라서, [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달 받은 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value) 은 다시 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 형변환 해야 합니다.

```cpp
class A {};

int f_11(A& val) {return 10;}
int f_11(A&& val) {return 20;}

int g_11(A& val) {
    return f_11(val) + 1;
}
int g_11(A&& val) {
    return f_11(std::move(val)) + 2; // 우측값을 참조하는 좌측값인 val을 다시 우측값으로 형변환 합니다.
}

A lvalue;

// g_11(A& val)와 f_11(A& val)가 호출되어 11입니다.
EXPECT_TRUE(g_11(lvalue) == 11); 

// g_11(A&& val)와 f_11(A&& val)가 호출되어 22입니다.
EXPECT_TRUE(g_11(std::move(lvalue)) == 22); 
```

이렇게 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [값 카테고리](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EA%B0%92-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC) 변경없이 그대로 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달하는 것을 [완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#forward-%EC%99%80-%EC%99%84%EB%B2%BD%ED%95%9C-%EC%A0%84%EB%8B%AC) 이라고 합니다. 이또한, 상당히 어렵지만 잘 알아두어야 하는 부분입니다. [전달 참조와 완벽한 전달](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/)을 참고하세요.