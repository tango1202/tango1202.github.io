---
layout: single
title: "#17. [고전 C++ 가이드] 문자열"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 수정될 필요가 없는 문자열 데이터는 `const char*` 나 `const wchar_t*`로 관리하라.(*[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)이나 `string`, `wstring` 을 쓰면 복제된다.*)
> * [멀티 바이트 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)은 권장하지 않는다. 사용하지 마라.
> * 소스 코드 저장시에는 다국어 처리에 적합하도록 [UTF-8 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9)로 저장하라.

> **모던 C++**
> * (C++11~) [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8"", u"", U"", u''(문자), U''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.
> * (C++11~) [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)과 [UTF-32 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9)을 지원하는 [u16string, u32string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되었습니다.
> * (C++17~) [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 지원하는 [u8''(문자) 리터럴](https://tango1202.github.io/mordern-cpp/mordern-cpp-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EB%A6%AC%ED%84%B0%EB%9F%B4)이 추가되었습니다.
> * (C++20~) [UTF-8 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9)을 지원하는 [u8string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c20u8string)이 추가되었습니다.

# 개요

[문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)에서 언급한 것처럼 문자열은 `const char*`나 `const wchar_t*`로 [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)가 있는 영역을 참조하거나, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 이용하여 복사할 수 있습니다. 

또한, STL 에서 `string`, `wstring`개체를 제공하여 문자열을 저장 및 관리 할 수 있습니다.(*[문자열](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/) 참고*)

# 널종료 문자열

C언어에서는 문자열의 끝에 [널문자(*정수 0인 문자, `'\0'`*)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)를 사용합니다. 따라서, [C스타일 문자열 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)들은 문자열의 끝이 [널문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)라고 가정하고 개발되었습니다. 예를 들어 문자열의 길이를 구하는 [strlen()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)함수는 [널문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)를 만날때까지 문자들을 카운팅합니다.

```cpp
size_t MyStrlen(const char * str) {
    size_t length = 0;
    while (*str != '\0') { // 널문자를 만날때까지 카운팅합니다.
        ++str;
        ++length;
    }
    return length;
}
EXPECT_TRUE(MyStrlen("abc") == 3);
```

즉, 저장 공간은 [널문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)를 포함한 크기이며, 문자열의 길이는 [널문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)를 제외한 길이입니다.

```cpp
char str[] = "abc"; // (O) {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str[0] == 'a');
EXPECT_TRUE(str[1] == 'b');
EXPECT_TRUE(str[2] == 'c');
EXPECT_TRUE(str[3] == '\0'); // 널문자가 추가됨
EXPECT_TRUE(sizeof(str) == 4); // 널문자까지 포함하여 4byte 크기 입니다.
EXPECT_TRUE(sizeof(str) / sizeof(char) == 4); // 배열 갯수는 널문자를 포함하여 4 입니다.
EXPECT_TRUE(strlen(str) == 3); // 문자열의 길이는 널문자를 제외한 3입니다.
```

# 문자열 상수 수정

[문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)는 rodata 영역에 할당(*[데이터 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8) 참고*)되기 때문에 수정할 수 없습니다. 따라서 포인터를 통해서 받은 [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)를 수정하려 하면, 예외가 발생합니다. 하지만, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)로 저장하면 복제본이므로 수정할 수 있습니다.

```cpp
const char* str1 = "abc"; // 문자열 상수
char* temp = const_cast<char*>(str1);
// (X) 예외 발생. 문자열 상수는 rodata에 있기 때문에 수정할 수 없습니다.
*temp = 'd';

char str2[] = "abc"; // {'a', `b`, 'c', '\0'};
// (O) 배열은 문자열 상수의 복제본이어서 항목을 수정할 수 있습니다.
str2[0] = 'd';
EXPECT_TRUE(str2[0] == 'd');
```

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/4f98f2cc-9dff-428f-a639-42d42d9f701b)

[문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)의 내용을 수정할 필요가 없다면, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)이나 `string`, `wstring`에 저장하지 마세요. 불필요하게 복제되어 복사 부하만 생깁니다.

# 아스키 코드

컴퓨터는 이진수로 처리되며, 문자열을 구성하는 문자들도 사실은 정수값에 매핑됩니다. 최초에는 [아스키 코드(https://www.ascii-code.com/ASCII)](https://www.ascii-code.com/ASCII)를 사용했다가, 다양한 문자 처리를 위해 현재는 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 사용하고 있습니다.

[아스키 코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%95%84%EC%8A%A4%ED%82%A4-%EC%BD%94%EB%93%9C)는 기본적으로 1byte 크기이며, 영문자 `a`는 정수 `0x61`(`97`)이며, 영문자 `b`는 정수 `0x62`(`98`)이고, 영문자 `c`는 정수 `0x63`(`99`)입니다.

따라서, 문자열 `"abc"`는 메모리에 다음과 같이 저장됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/246351c7-bc63-4e42-8fd1-921d02b78b40)

# 확장 아스키 코드

하지만 [아스키 코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%95%84%EC%8A%A4%ED%82%A4-%EC%BD%94%EB%93%9C)는 0 ~ 127(*0111 1111*) 까지만 정의되어 있어서, 다양한 국가의 다양한 문자를 표현하기엔 부족했습니다.

그래서 [확장 아스키 코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%ED%99%95%EC%9E%A5-%EC%95%84%EC%8A%A4%ED%82%A4-%EC%BD%94%EB%93%9C)로 128 ~ 255 영역을 사용하는 방법이 도입되었는데요, 0 ~ 127 의 값이면 [아스키 코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%95%84%EC%8A%A4%ED%82%A4-%EC%BD%94%EB%93%9C)를 사용하고, 그 이상의 값이면 코드 페이지(*`437`, `ISO-8859-1`, `Windows-1250`등*)에 따라 다른 기호들을 매핑합니다. [아스키 코드(https://www.ascii-code.com/ASCII)](https://www.ascii-code.com/ASCII)를 보면, 코드 페이지에 따라 128 ~ 255 영역이 서로 다른 기호로 매핑되는걸 확인할 수 있습니다.

# 국가별 코드 테이블(조합형 한글, 완성형 한글)

[확장 아스키 코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%ED%99%95%EC%9E%A5-%EC%95%84%EC%8A%A4%ED%82%A4-%EC%BD%94%EB%93%9C)를 사용하는 방법은 라틴 문자를 사용하는 곳에서는 어느 정도 사용할 수 있으나, 한중일의 문자(*`CJK`라고 합니다.*)를 표현하기엔 턱없이 부족합니다. 

그래서 각 국가별로 자체적인 코드 테이블을 만들었고, 127 이상이면 추가 바이트를 사용합니다. 한글의 경우엔 2byte 크기의 코드 테이블과 매핑하며, [조합형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)과 [완성형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)이 있습니다.

|항목|명칭|내용|
|--|--|--|
|[조합형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)|`KSSM`|총 2byte(16bit)로 한글의 초성, 중성, 종성을 표현합니다. 완성형 한글과 표준화 경합을 했으나 표준화되지 못했습니다.<br/> 1bit : 최상위 비트가 1이면 한글이고, 0이면 영문입니다.<br/>5bit : 초성<br/>5bit : 중성<br/>5bit : 종성|
|[완성형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)|`KS X 1001`(*구 `KSC-5601`*)|1987년에 표준화 되었으며, 한글 2,350자를 2byte로 정의합니다. 일부 한글을 표현하지 못하는 문제가 있습니다.|

# 유니코드

[국가별 코드 테이블](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)을 사용하면, 해당 국가의 문자를 표현할 수는 있으나, 여러 국가의 문자를 함께 표현할 수는 없습니다. 그래서, 전 세계의 모든 문자에 고유 숫자를 부여한 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 만들었는데요, 이것도 점진적으로 추가되고, 하위 호환을 유지하다보니 처리 방식이 좀 복잡합니다.

[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)는 기본적으로 2byte를 사용하도록 했으나, 아시아권 문자를 포함하다 보니 3byte가 필요하게 되었고, 다양한 추가 문자들을 지원하다 보니 4byte가 필요(*악보 기호, 이모지등 특수 기호 지원*)하게 되었습니다. 이러다 보니 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)는 2byte다 4byte다 혼선이 있는데, 결과적으로는 ***2byte 이상이다***가 맞겠습니다. 

[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)에서는 현대 한글의 조합 가능한 모든 문자 11,172개를 2byte로 표현하며 `U+AC00 ~ U+D7A3`에 할당되어 있습니다.(*[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)는 `U+16진수`의 형태로 표기합니다. 예를 들어 한글 "가"는 `AC00`(10진수의 `44032`)인데, `U+AC00`으로 표기합니다.*)

|항목|명칭|내용|
|--|--|--|
|[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C) 한글|`ISO/IEC 10646`|1996년 2.0이 제정 되었으며, [완성형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)에서 표현 못하는 문자들을 포함하여 한글 11,172자를 2byte로 정의합니다.|

# 인코딩

사용하는 코드가 [조합형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)인지, [완성형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)인지, [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)인지에 따라 동일한 문자라도 서로 다른 코드값을 갖게 됩니다.

다음은 한글 `가`, `나`, `다`의 코드 테이블 값입니다. 모두 서로 다르죠.

|코드 테이블|`가`|`나`|`다`|설명|
|--|--|--|--|--|
|[조합형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)(*`KSSM`*)|`0x8861`|`0x9061`|`0x9461`|이젠 거의 쓰이지 않습니다.|
|[완성형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)(*`KS X 1001`*)|`0xBOA1`|`0xB3AA`|`0xB4D9`|`euc-kr`, `cp-949`인코딩에서 사용합니다.|
|[유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)|`U+AC00`|`U+B098`|`U+B2E4`|[UTF-8 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9), [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9), [UTF-32 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9)에서 사용합니다.|

따라서 [완성형 한글](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EA%B5%AD%EA%B0%80%EB%B3%84-%EC%BD%94%EB%93%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EC%A1%B0%ED%95%A9%ED%98%95-%ED%95%9C%EA%B8%80-%EC%99%84%EC%84%B1%ED%98%95-%ED%95%9C%EA%B8%80)로 저장된 문자를 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)로 읽거나, [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)로 저장된 문자를 완성형으로 읽으려면 서로 코드 변환을 해야 합니다.(*한글 코드간의 변환은 [charset.fandom.com/](https://charset.fandom.com/ko/wiki/%ED%95%9C%EA%B8%80_%EC%9D%B8%EC%BD%94%EB%94%A9_%EB%B3%80%ED%99%98_%ED%85%8C%EC%9D%B4%EB%B8%94)을 참고하시기 바랍니다.*)

또한, [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 최종본(*[유니코드 로드맵](https://www.unicode.org/roadmaps/) 참고*)인 4byte로 표현하기엔 메모리 낭비가 심하므로 [UTF-8 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9), [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9), [UTF-32 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9) 의 3가지 인코딩 방식을 사용하고 있습니다.

# euc-kr 인코딩

[euc-kr 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#euc-kr-%EC%9D%B8%EC%BD%94%EB%94%A9)을 사용하면, 한글은 `KS X 1001`을 사용하고, 영문은 `KS X 1003`(*구 `KSC-5636`. 아스키 코드에서 역슬래쉬(`\`)를 원(`₩`)으로 대체했습니다.*)으로 처리합니다.

따라서 `가나다`를 저장하면 다음과 같이 저장됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9faee661-32c1-4471-b267-e68f880cb10e)

# cp-949 인코딩

Microsoft에서 사용하는 확장 완성형으로서 [euc-kr 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#euc-kr-%EC%9D%B8%EC%BD%94%EB%94%A9)의 확장형입니다. `KS X 1001`에서 표현하지 못한 한글 8822자를 추가했습니다.

# UTF-8 인코딩

웹의 기본 [인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 방식이며, 문자마다 다른 크기로 저장합니다. 이에 따라 문자 데이터 앞에 크기 정보가 필요하며, 첫째 바이트의 상위 비트가 `0`이면 남은 7비트에 데이터를 저장하고, 이진수 `110`이면 2byte, 이진수 `1110`이면 3byte, 이진수 `11110`이면 4byte에 데이터를 분산해서 저장합니다. 이때 추가 byte의 상위 비트는 이진수 `10`으로 마킹합니다. 

|항목|내용|
|--|--|
|영어 및 기호|1byte|
|추가 라틴 및 중동|2byte|
|한글 및 아시아|3byte(*한글은 코드값은 2byte이지만, 크기 정보를 포함하면 3byte가 필요합니다.*)|
|추가 문자|4byte|

예를 들어 한글 `가`(`U+AC00`)는 다음과 같이 저장됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/96f711f2-ea17-431e-822c-8fb7b7c3dd3f)

따라서 `가나다`를 저장하면 다음과 같이 저장됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/a9e6f5cc-bec3-4f26-ae6a-f8077f0d06b4)

# UTF-16 인코딩

JAVA 에서 기본으로 사용하며, BMP(*Basic Multilingual Plane*)라 불리는 기본적인 문자들은 2byte로 처리(*한글은 2byte입니다.*)하고, 2byte로 표현할 수 없는 확장된 것들은 4byte로 처리합니다. 이렇게 확장된 영역을 서로게이트(*Surrogate*) 영역이라 하며 상위 2byte를 상위 서로게이트라 하고, 하위 2byte를 하위 서로게이트라고 합니다.

BMP 에서는 `0xD800(1101 0000 0000 0000)` ~ `0xDFFF(1101 1111 1111 1111 1111)`을 사용하지 않으며, 이에 따라 서로게이트를 표현할때 상기 범위의 값으로 변환하여 처리합니다.

예를 들어 2byte로 표현할 수 없는 `𐐷`(`U+10437`)의 변환 방법은 다음과 같습니다.

1. 상위 1byte에서 하위 5bit에서 1을 빼서 4bit로 만듭니다.
2. 상위 서로게이트 영역을 이진수 `1101 10`, 하위 서로게이트 영역을 이진수 `1101 11`로 마킹하고 데이터를 채웁니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/3d57e656-83dc-4745-b069-efe49899bf3b)


따라서 `𐐷`을 [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)으로 저장하면 `0xD801DC37` 이 되는데요, 빅 엔디안(*낮은 주소에 상위 바이트를 저장*)이냐 리틀 엔디안(*낮은 주소에 하위 바이트 저장*)이냐에 따라 다음과 같이 저장됩니다.

* 빅 엔디안(*UTF-16 BE*) : `0xD801DC37`
* 리틀 엔디안(*UTF-16 LE*) : `0x01D837DC`

# UTF-32 인코딩

모든 문자를 4byte로 처리합니다. 메모리 낭비가 심하여 잘 사용하지 않습니다.

# 소스 코드와 인코딩

소스 코드가 어떤 [인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 방식을 사용하느냐에 따라 문자열 데이터의 값은 완전히 달라집니다.

다음 예제는 `const char* str = "가";` 를 `UTF-8`과 `euc-kr`로 저장했을때의 차이입니다. 문자열의 길이가 서로 다르며, 코드값도 다릅니다. 

**UTF-8 인코딩으로 저장한 경우**

```cpp
const char* str = "가"; // UTF-8 가[0xEA 0xB0 0x80] 가 저장된 곳을 가리키는 포인터 입니다.

EXPECT_TRUE(strlen(str) == 3); // UTF-8에서 한글 1글자는 3byte 입니다.
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 0) == 0xEA);
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 1) == 0xB0);
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 2) == 0x80);
```

**euc-kr 또는 cp-949 인코딩으로 저장한 경우**

```cpp
const char* str = "가"; // 완성형 가[0xB0 0xA1] 가 저장된 곳을 가리키는 포인터 입니다.

EXPECT_TRUE(strlen(str) == 2); // euc-kr에서 한글 1글자는 2byte 입니다.
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 0) == 0xB0);
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 1) == 0xA1);
```

따라서 [소스 코드의 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%86%8C%EC%8A%A4-%EC%BD%94%EB%93%9C%EC%99%80-%EC%9D%B8%EC%BD%94%EB%94%A9)도 잘 결정해서 사용해야 하는데요, `euc-kr`이나 Windows의 `cp-949`는 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)와의 호환성이 없기에 다국어 처리에 적합하지 않습니다. 소스 코드는 `UTF-8`로 저장하시길 추천합니다.

# 바이트 문자열

영문자는 0 ~ 127 까지의 7bit 만으로 표현이 충분하기 때문에 1byte만으로도 저장할 수 있습니다. 이렇게 1byte 단위로 문자를 저장하는 문자열을 [바이트 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4) 이라 합니다. 

C++에서 기본적으로 사용하는 처리방식이며, 영문자만 처리됩니다.

```cpp
const char* str = "abc"; // 0x61 0x62 0x63 가 저장된 영역을 가리키는 포인터 입니다.

EXPECT_TRUE(strlen(str) == 3); // UTF-8에서 영문 3글자는 3byte입니다.
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 0) == 0x61); // 0x61. 아스키 코드 a
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 1) == 0x62); // 0x62. 아스키 코드 b
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 2) == 0x63); // 0x63. 아스키 코드 c
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 3) == 0x00); // 널문자
```
# 멀티 바이트 문자열

한글등 다양한 국가의 문자들은 1byte로 처리할 수 없으며, 파일의 [인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)에 따라 2byte이상의 코드값이 필요합니다. 이처럼 1byte외 여러 byte를 혼용하는 문자열을 [멀티 바이트 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4) 이라고 합니다. Microsoft에서 초창기에 만들어 사용했지만, 표준화 되지 않았고, 현재는 비권고 되고 있습니다.

다음은 `UTF-8`로 [인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)된 파일에서 [멀티 바이트 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)을 사용하는 예입니다.

1. `const char* str = "abc가나다";`는 `UTF-8`로 [인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 되어 `abc[0x61 0x62 0x63] 가[0xEA 0xB0 0x80] 나[0xEB 0x82 0x98] 다[0xEB 0x8B 0xA4]`이 저장되어 있습니다.
2. [바이트 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)용 [strlen()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)함수를 사용하면 아무 생각없이 [널문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)까지 카운트하므로, `12`가 됩니다.
3. [locale()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#c%EC%8A%A4%ED%83%80%EC%9D%BC-locale)함수를 호출하여 [멀티 바이트](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8) 함수 호출전에 [인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 정보를 전달합니다.
4. [mblen()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8)함수를 이용하여 해당 주소의 문자가 몇 바이트 크기인지 구합니다.
5. [mbstowcs()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8)함수를 이용하여 해당 주소의 문자들의 코드를 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)로 변환하여 `wstr`에 저장합니다.

```cpp
const char* str = "abc가나다"; // abc[0x61 0x62 0x63] 가[0xEA 0xB0 0x80] 나[0xEB 0x82 0x98] 다[0xEB 0x8B 0xA4] 가 저장된 영역을 가리키는 포인터 입니다.
EXPECT_TRUE(strlen(str) == 12); // UTF-8에서 한글 1글자는 12byte입니다. a(1) + b(1) + c(1) + 가(3) + 나(3) + 다(3) 1 + 1 + 1 + 3 + 3 + 3 = 12 

EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 0) == 0x61); // 0x61. 아스키 코드 a
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 1) == 0x62); // 0x62. 아스키 코드 b
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 2) == 0x63); // 0x63. 아스키 코드 c

EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 3) == 0xEA); // 가
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 4) == 0xB0); 
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 5) == 0x80); 

EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 6) == 0xEB); // 나
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 7) == 0x82); 
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 8) == 0x98); 

EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 9) == 0xEB); // 다
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 10) == 0x8B); 
EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 11) == 0xA4); 

EXPECT_TRUE(*reinterpret_cast<const unsigned char*>(str + 12) == 0x00); // 널문자  

std::setlocale(LC_ALL, "en_US.utf8");
EXPECT_TRUE(mblen(str + 0, MB_CUR_MAX) == 1); // a 문자는 1byte 크기임
EXPECT_TRUE(mblen(str + 1, MB_CUR_MAX) == 1); // b 문자는 1byte 크기임
EXPECT_TRUE(mblen(str + 2, MB_CUR_MAX) == 1); // c 문자는 1byte 크기임
EXPECT_TRUE(mblen(str + 3, MB_CUR_MAX) == 3); // 가 문자는 3byte 크기임
EXPECT_TRUE(mblen(str + 6, MB_CUR_MAX) == 3); // 나 문자는 3byte 크기임
EXPECT_TRUE(mblen(str + 9, MB_CUR_MAX) == 3); // 다 문자는 3byte 크기임

wchar_t wstr[7];
std::mbstowcs(wstr, str, 7); // UTF-8로 저장되어 있는 데이터를 디코딩하여 문자 1개씩 유니코드로 변경하여 저장합니다.

EXPECT_TRUE(wstr[0] == 0x0061); // 0x0061. 아스키 코드 a
EXPECT_TRUE(wstr[1] == 0x0062); // 0x0062. 아스키 코드 b
EXPECT_TRUE(wstr[2] == 0x0063); // 0x0063. 아스키 코드 c
EXPECT_TRUE(wstr[3] == 0xAC00); // 0xAC00. 유니코드 가
EXPECT_TRUE(wstr[4] == 0xB098); // 0xB098. 유니코드 나
EXPECT_TRUE(wstr[5] == 0xB2E4); // 0xB2E4. 유니코드 다
EXPECT_TRUE(wstr[6] == 0x0000); // 널문자
```

# 와이드 문자열

[와이드 문자열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)은 영문자이건, 다국어 문자이건 모두 `wchar_t`로 관리하는 문자열입니다. 안타깝게도 Windows 에서는 2byte이고 리눅스에서는 4byte 이기 때문에 운영체제에 따라 다르게 동작할 수 있어 주의해야 합니다.(*[기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 참고*)

문자열의 코드값은 OS에 따라 [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)이나 [UTF-32 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9)으로로 저장됩니다.

|항목|`wchar_t` 크기|인코딩|
|--|--|--|
|Windows|2byte|[UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)|
|Windows 외 운영체제|4byte|[UTF-32 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-32-%EC%9D%B8%EC%BD%94%EB%94%A9)|

한글의 경우는 [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)의 BMP(*Basic Multilingual Plane*) 영역이므로 서로게이트(*Surrogate*) 처리없이 간단하게 사용할 수 있습니다.

다음은 Windows 에서 [UTF-8 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-8-%EC%9D%B8%EC%BD%94%EB%94%A9)로 저장한 소스 코드의 실행예입니다. 

메모리에 [UTF-16 인코딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#utf-16-%EC%9D%B8%EC%BD%94%EB%94%A9)으로 저장되어 있으며, `wchar_t` 단위로 읽었을때 [유니코드](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)값이 잘 저장되어 있습니다.

```cpp
const wchar_t* wstr = L"abc가나다"; // Windows는 UTF-16. abc[0x61 0x62 0x63] 가[0xAC00] 나[0xB098] 다[0xB2E4] 가 저장된 영역을 가리키는 포인터 입니다.
EXPECT_TRUE(wcslen(wstr) == 6); // Windows에서는 2byte 단위로 저장합니다.

EXPECT_TRUE(wstr[0] == 0x0061); // 0x0061. UTF-16 인코딩. 아스키 코드 a
EXPECT_TRUE(wstr[1] == 0x0062); // 0x0062. UTF-16 인코딩. 아스키 코드 b
EXPECT_TRUE(wstr[2] == 0x0063); // 0x0063. UTF-16 인코딩. 아스키 코드 c
EXPECT_TRUE(wstr[3] == 0xAC00); // 0xAC00. UTF-16 인코딩. 유니코드 가
EXPECT_TRUE(wstr[4] == 0xB098); // 0xB098. UTF-16 인코딩. 유니코드 나
EXPECT_TRUE(wstr[5] == 0xB2E4); // 0xB2E4. UTF-16 인코딩. 유니코드 다
EXPECT_TRUE(wstr[6] == 0x0000); // 널문자   
```
