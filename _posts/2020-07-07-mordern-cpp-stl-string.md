---
layout: single
title: "#7. [모던 C++ STL] 문자열, (C++11~) u16string, u32string, (C++17~) string_view, 숫자/문자열 변환(to_chars(), from_chars())"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `string`은 [바이트 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)을 지원합니다.
> * `wstring`은 [와이드 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 지원합니다.
> * (C++11~)  [UTF-16]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9))을 지원하는 [u16string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되었습니다.
> * (C++11~) [UTF-32]((https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9))를 지원하는 [u32string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EA%B0%9C%EC%9A%94)이 추가되었습니다.
> * [strerror()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#strerror)는 [오류 번호](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%A4%EB%A5%98-%EB%B2%88%ED%98%B8)를 문자열로 출력해 줍니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.
> * (C++17~) [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view)가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 `string`복제가 없도록 해줍니다.
> * (C++17~) 숫자와 문자열간의 변환을 위한 [to_char(), from_char()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-%EC%88%AB%EC%9E%90%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%B3%80%ED%99%98) 함수가 제공되며, 기존 C스타일([atoi(), strtol()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98 )등) 보다 안전합니다.
> * (C++20~) [UTF-8](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)을 지원하는 [u8string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c20u8string)이 추가되었습니다.

# 개요

문자열을 처리하는 기본 클래스는 [basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)입니다. `char_traits`를 사용하여 각 문자 타입별로 문자열을 처리합니다.

|항목|내용|
|--|--|
|[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)|문자열의 기본 클래스입니다.|
|`char_traits`|[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)에서 각 문자를 처리하는 타입 특성입니다.|

[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)은 각 타입 별로 별칭을 사용합니다.

|항목|내용|정의|
|--|--|--|
|`string`|[바이트 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|`basic_string<char>`|
|`wstring`|[와이드 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%99%80%EC%9D%B4%EB%93%9C-%EB%AC%B8%EC%9E%90%EC%97%B4)|`basic_string<wchar_t>`|
|`u16string` (C++11~)|[UTF-16 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)|`basic_string<char16_t>`|
|`u32string` (C++11~)|[UTF-32 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)|`basic_string<char32_t>`|
|`u32string` (C++20~)|[UTF-8 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)|`basic_string<char8_t>`|

```cpp
// C++11
std::u16string str1_11{u"abc가나다"};
std::u32string str2_11{U"abc가나다"};

// C++20
// std::u8string str3_20{u8"abc가나다"};

EXPECT_TRUE(str1_11.size() == 6);
EXPECT_TRUE(str1_11[3] == u'가'); 

EXPECT_TRUE(str2_11.size() == 6);
EXPECT_TRUE(str2_11[3] == U'가');

// C++20
// EXPECT_TRUE(str3_20.size() == 6);
// EXPECT_TRUE(str3_20[3] == U'가');
```

자세한 사용 방법은 [문자열](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)을 참고하기 바랍니다.

# 널종료 문자열

이전 C스타일은 문자열의 끝에 널문자(`\0`)를 사용합니다. 따라서, C스타일 문자열 함수들은 문자열의 끝이 널문자(`\0`)라고 가정하고 개발되었습니다. 예를 들어 문자열의 길이를 구하는 `strlen()`함수는 널문자(`\0`)를 만날때까지 문자들을 카운팅합니다.

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

즉, 저장 공간은 널문자를 포함한 크기이며, 문자열의 길이는 널문자를 제외한 길이입니다.

```cpp
char str[] = "abc"; // (O) {'a', `b`, 'c', '\0'};
EXPECT_TRUE(str[0] == 'a');
EXPECT_TRUE(str[1] == 'b');
EXPECT_TRUE(str[2] == 'c');
EXPECT_TRUE(str[3] == '\0'); // 널문자가 추가됨
EXPECT_TRUE(sizeof(str) == 4); // 널문자까지 포함하여 4byte 크기 입니다.
EXPECT_TRUE(strlen(str)); // 문자열의 길이는 널문자를 제외한 3입니다.
```

# C++ 문자열

C++ 문자열은 C스타일의 문자열과의 호환성을 위하여 기본적으로 [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 지원합니다. [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)로 생성할 수 있으며, `c_str()`함수를 이용하여 [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)로 변환할 수 있습니다. 

`c_str()`함수의 변환 효율성을 위해 STL 구현에 따라 내부 메모리 블록에 널문자(`\0`)를 저장할 수도, 저장하지 않을 수도 있습니다. 따라서 내부 메모리 블록을 구하는 `data()`함수는 [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 리턴한다고 보장하지 않으니 주의해야 합니다.

```cpp
std::string str = "abc"; // 널종료 문자열로 생성할 수 있습니다.

EXPECT_TRUE(strlen(str.data()) == 3); // (△) 비권장. 내부 데이터 메모리 블록을 리턴합니다. STL 구현에 따라 끝에 널문자가 있을 수도, 없을 수도 있습니다.
EXPECT_TRUE(strlen(str.c_str()) == 3); // 널종료 문자열을 리턴하므로 안전합니다.
```

# 유니코드

**아스키 코드**

문자열을 구성하는 문자의 코드값은 정수값에 매핑되어 처리되는데요, 기본적으로 1byte 크기의 [아스키 코드(https://www.ascii-code.com/ASCII)](https://www.ascii-code.com/ASCII)를 사용합니다.

예를 들어 영문자 `a`는 정수 `0x61`(`97`)이며, 영문자 `b`는 정수 `0x62`(`98`)이고, 영문자 `c`는 정수 `0x63`(`99`)입니다.

따라서, 문자열 `"abc"`는 메모리에 다음과 같이 저장됩니다.

<img width="157" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/246351c7-bc63-4e42-8fd1-921d02b78b40">

**확장 아스키 코드**

하지만 [아스키 코드(https://www.ascii-code.com/ASCII)](https://www.ascii-code.com/ASCII)는 0 ~ 127(0111 1111) 까지만 정의되어 있어서, 다양한 국가의 다양한 문자를 표현하기엔 부족했습니다.

그래서 확장 아스키 코드로 128 ~ 255 영역을 사용하는 방법이 도입되었는데요, 0 ~ 127 의 값이면 [아스키 코드(https://www.ascii-code.com/ASCII)](https://www.ascii-code.com/ASCII)를 사용하고, 그 이상의 값이면 코드 페이지(`437`, `ISO-8859-1`, `Windows-1250`등)에 따라 다른 기호들을 매핑합니다. [아스키 코드(https://www.ascii-code.com/ASCII)](https://www.ascii-code.com/ASCII)를 보면, 코드 페이지에 따라 128 ~ 255 영역이 서로 다른 기호로 매핑되는걸 확인할 수 있습니다.

**국가별 코드 테이블**

확장 아스키 코드를 사용하는 방법은 라틴 문자를 사용하는 곳에서는 어느 정도 사용할 수 있으나, 한중일의 문자(`CJK`라고 합니다.)를 표현하기엔 턱없이 부족합니다. 

그래서 각 국가별로 자체적인 코드 테이블을 만들었고, 127 이상이면 추가 바이트를 사용합니다. 한글의 경우엔 2byte 크기의 코드 테이블과 매핑하며, 조합형 한글과 완성형 한글이 있습니다.

|항목|명칭|내용|
|--|--|--|
|조합형 한글|`KSSM`|총 2byte(16bit)로 한글의 초성, 중성, 종성을 표현합니다. 완성형 한글과 표준화 경합을 했으나 표준화되지 못했습니다.<br/> 1bit : 최상위 비트가 1이면 한글이고, 0이면 영문입니다.<br/>5bit : 초성<br/>5bit : 중성<br/>5bit : 종성|
|완성형 한글|`KS X 1001`(구 `KSC-5601`)|1987년에 표준화 되었으며, 한글 2,350자를 2byte로 정의합니다. 일부 한글을 표현하지 못하는 문제가 있습니다.|

**유니코드**

국가별 코드 테이블을 사용하면, 해당 국가의 문자를 표현할 수는 있으나, 여러 국가의 문자를 함께 표현할 수는 없습니다. 그래서, 전 세계의 모든 문자에 고유 숫자를 부여한 [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 만들었는데요, 이것도 점진적으로 추가되고, 하위 호환을 유지하다보니 처리 방식이 좀 복잡합니다.

[유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)는 기본적으로 2byte를 사용하도록 했으나, 아시아권 문자를 포함하다 보니 3byte가 필요하게 되었고, 다양한 추가 문자들을 지원하다 보니 4byte가 필요(악보 기호, 이모지등 특수 기호 지원)하게 되었습니다. 이러다 보니 [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)는 2byte다 4byte다 혼선이 있는데, 결과적으로는 "2byte 이상이다"가 맞겠습니다. 

[유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)에서는 현대 한글의 조합 가능한 모든 문자 11,172개를 2byte로 표현하며 `U+AC00 ~ U+D7A3`에 할당되어 있습니다.([유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)는 `U+16진수`의 형태로 표기합니다. 예를 들어 한글 "가"는 `AC00`(10진수의 `44032`)인데, `U+AC00`으로 표기합니다.)

|항목|명칭|내용|
|--|--|--|
|[유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C) 한글|`ISO/IEC 10646`|1996년 2.0이 제정 되었으며, 완성형 한글에서 표현 못하는 문자들을 포함하여 한글 11,172자를 2byte로 정의합니다.|


# 인코딩

사용하는 코드가 조합형인지, 완성형인지, [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)인지에 따라 동일한 문자라도 서로 다른 코드값을 갖게 됩니다.

다음은 한글 `가`, `나`, `다`의 코드 테이블 값입니다. 모두 서로 다르죠.

|코드 테이블|`가`|`나`|`다`|설명|
|--|--|--|--|--|
|조합형(`KSSM`)|`0x8861`|`0x9061`|`0x9461`|이젠 거의 쓰이지 않습니다.|
|완성형(`KS X 1001`)|`0xBOA1`|`0xB3AA`|`0xB4D9`|`euc-kr`, `cp-949`인코딩에서 사용합니다.|
|[유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)|`U+AC00`|`U+B098`|`U+B2E4`|`UTF-8`, `UTF-16`, `UTF-32`인코딩에서 사용합니다.|

따라서 완성형으로 저장된 문자를 [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)로 읽거나, [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)로 저장된 문자를 완성형으로 읽으려면 서로 코드 변환을 해야 합니다.(한글 코드간의 변환은 [charset.fandom.com/](https://charset.fandom.com/ko/wiki/%ED%95%9C%EA%B8%80_%EC%9D%B8%EC%BD%94%EB%94%A9_%EB%B3%80%ED%99%98_%ED%85%8C%EC%9D%B4%EB%B8%94)을 참고하시기 바랍니다.)

또한, [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)를 최종본(글쎄요, 더 확장될 수도 있겠죠. [유니코드 로드맵](https://www.unicode.org/roadmaps/) 참고)인 4byte로 표현하기엔 메모리 낭비가 심하므로 `UTF-8`, `UTF-16`, `UTF-32` 의 3가지 인코딩 방식을 사용하고 있습니다.

**euc-kr 인코딩**

`euc-kr`인코딩을 사용하면, 한글은 `KS X 1001`을 사용하고, 영문은 `KS X 1003`(구 `KSC-5636`)(아스키 코드에서 역슬래쉬(`\`)를 원(`₩`)으로 대체했습니다.)으로 처리합니다.

따라서 `가나다`를 저장하면 다음과 같이 저장됩니다.

<img width="237" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/9faee661-32c1-4471-b267-e68f880cb10e">

**cp-949 인코딩**

Microsoft에서 사용하는 확장 완성형으로서 `euc-kr`의 확장형입니다. `KS X 1001`에서 표현하지 못한 한글 8822자를 추가했습니다.

**UTF-8 인코딩**

웹의 기본 [인코딩](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 방식이며, 문자마다 다른 크기로 저장합니다. 이에 따라 문자 데이터 앞에 크기 정보가 필요하며, 첫째 바이트의 최상위 비트가 `0`이면 남은 7비트에 데이터를 저장하고, `110`이면 2byte, `1110`이면 3byte, `11110`이면 4byte에 데이터를 분산해서 저장합니다. 이때 추가 byte의 최상위 비트는 `10`으로 마킹합니다. 

|항목|내용|
|--|--|
|영어 및 기호|1byte|
|추가 라틴 및 중동|2byte|
|한글 및 아시아|3byte(한글은 코드값은 2byte이지만, 크기 정보를 포함하면 3byte가 필요합니다.)|
|추가 문자|4byte|

예를 들어 한글 `가`(`U+AC00`)는 다음과 같이 저장됩니다.

<img width="493" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/96f711f2-ea17-431e-822c-8fb7b7c3dd3f">

따라서 `가나다`를 저장하면 다음과 같이 저장됩니다.

<img width="345" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/a9e6f5cc-bec3-4f26-ae6a-f8077f0d06b4">

**UTF-16 인코딩**

JAVA 에서 기본으로 사용하며, 기본적으로 2byte로 처리하고, 2byte로 표현할 수 없는 것들은 4byte로 처리합니다.

**UTF-32 인코딩**

모든 문자를 4byte로 처리합니다. 메모리 낭비가 심하여 잘 사용하지 않습니다.

# 소스 코드와 인코딩

소스 코드가 어떤 [인코딩](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 방식을 사용하느냐에 따라 문자열 데이터는 완전히 달라집니다.

다음 예제는 `char* str = "가";` 를 `UTF-8`과 `euc-kr`로 저장했을때의 차이입니다. 문자열의 길이가 서로 다르며, 코드값도 다릅니다. 

**UTF-8**

```cpp
char* str = "가"; // UTF-8 가[0xEA 0xB0 0x80] 가 저장된 곳을 가리키는 포인터 입니다.

EXPECT_TRUE(strlen(str) == 3); // UTF-8에서 한글 1글자는 3byte 입니다.
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 0) == 0xEA);
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 1) == 0xB0);
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 2) == 0x80);
```

**euc-kr 또는 cp-949**

```cpp
char* str = "가"; // 완성형 가[0xB0 0xA1] 가 저장된 곳을 가리키는 포인터 입니다.

EXPECT_TRUE(strlen(str) == 2); // euc-kr에서 한글 1글자는 2byte 입니다.
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 0) == 0xB0);
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 1) == 0xA1);
```

따라서 [소스 코드의 인코딩](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)도 잘 결정해서 사용해야 하는데요, Windows는 `cp-949`를 기본적으로 사용하기 때문에, [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)와 다국어 처리에 적합하지 않습니다. 소스 코드 저장시에는 혼동이 없도록 `UTF-8`로 저장하시길 추천합니다.

# 바이트 문자열

영문자는 0 ~ 127 까지의 7bit 만으로 표현이 충분하기 때문에 1byte만으로도 저장할 수 있습니다. 이렇게 1byte 단위로 문자를 저장하는 문자열을 **바이트 문자열** 이라 합니다.

```cpp
char* str = "abc"; // 0x61 0x62 0x63 가 저장된 영역을 가리키는 포인터 입니다.

EXPECT_TRUE(strlen(str) == 3); // UTF-8에서 영문 3글자는 3byte입니다.
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 0) == 0x61); // 0x61. 아스키 코드 a
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 1) == 0x62); // 0x62. 아스키 코드 b
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 2) == 0x63); // 0x63. 아스키 코드 c
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 3) == 0x00); // 널문자
```
# 멀티 바이트 문자열

한글등 다양한 국가의 문자들은 1byte로 처리할 수 없으며, 파일의 [인코딩](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)에 따라 2byte이상의 코드값이 저장됩니다. 이처럼 1byte외 여러 byte를 혼용하는 문자열을 **멀티 바이트 문자열** 이라고 합니다. Microsoft에서 초창기에 만들어 사용했지만, 표준화 되지 않았고, 현재는 비권고 되고 있습니다.

다음은 `UTF-8`로 [인코딩](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9)된 파일에서 [멀티 바이트 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)을 사용하는 예입니다.

1. `char* str = "abc가나다";`는 [유니코드](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)로 [인코딩](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 되어 `abc[0x61 0x62 0x63] 가[0xEA 0xB0 0x80] 나[0xEB 0x82 0x98] 다[0xEB 0x8B 0xA4]`를 저장합니다.
2. 바이트 문자열용 [strlen()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)함수를 사용하면 아무 생각없이 널문자까지 카운트하므로, `12`가 됩니다.
3. [locale()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#c%EC%8A%A4%ED%83%80%EC%9D%BC-locale)함수를 호출하여 [멀티 바이트](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8) 함수 호출전에 [인코딩](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EC%9D%B8%EC%BD%94%EB%94%A9) 정보를 전달합니다.
4. [mblen()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8)함수를 이용하여 해당 주소의 문자가 몇 바이트 크기인지 구합니다.
5. [mbstowcs()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8)함수를 이용하여 해당 주소의 문자들의 코드를 `wchar_t`로 변환합니다.

```cpp
char* str = "abc가나다"; // abc[0x61 0x62 0x63] 가[0xEA 0xB0 0x80] 나[0xEB 0x82 0x98] 다[0xEB 0x8B 0xA4] 가 저장된 영역을 가리키는 포인터 입니다.
EXPECT_TRUE(strlen(str) == 12); // UTF-8에서 한글 1글자는 12byte입니다. a(1) + b(1) + c(1) + 가(3) + 나(3) + 다(3) 1 + 1 + 1 + 3 + 3 + 3 = 12 

EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 0) == 0x61); // 0x61. 아스키 코드 a
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 1) == 0x62); // 0x62. 아스키 코드 b
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 2) == 0x63); // 0x63. 아스키 코드 c

EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 3) == 0xEA); // 가
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 4) == 0xB0); 
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 5) == 0x80); 

EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 6) == 0xEB); // 나
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 7) == 0x82); 
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 8) == 0x98); 

EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 9) == 0xEB); // 다
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 10) == 0x8B); 
EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 11) == 0xA4); 

EXPECT_TRUE(*reinterpret_cast<unsigned char*>(str + 12) == 0x00); // 널문자  

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

**와이드 문자열** 은 영문자이건, 다국어 문자이건 모두 `wchar_t`로 관리하는 문자열입니다. 안타깝게도 Windows 에서는 2byte이고 리눅스에서는 4byte 이기 때문에 운영체제에 따라 다르게 동작할 수 있어 주의해야 합니다.([기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/) 참고)

```cpp
wchar_t* wstr = L"abc가나다"; // abc[0x61 0x62 0x63] 가[0xAC00] 나[0xB098] 다[0xB2E4] 가 저장된 영역을 가리키는 포인터 입니다.
EXPECT_TRUE(wcslen(wstr) == 6); // Windows에서는 2byte 단위로 저장합니다.

EXPECT_TRUE(wstr[0] == 0x0061); // 0x0061. 아스키 코드 a
EXPECT_TRUE(wstr[1] == 0x0062); // 0x0062. 아스키 코드 b
EXPECT_TRUE(wstr[2] == 0x0063); // 0x0063. 아스키 코드 c
EXPECT_TRUE(wstr[3] == 0xAC00); // 0xAC00. 유니코드 가
EXPECT_TRUE(wstr[4] == 0xB098); // 0xB098. 유니코드 나
EXPECT_TRUE(wstr[5] == 0xB2E4); // 0xB2E4. 유니코드 다
EXPECT_TRUE(wstr[6] == 0x0000); // 널문자   
```

# C스타일 문자열 함수

**문자 분류**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`isalnum()`|`iswalnum()`|(작성중)|
|`isalpha()`|`iswalpha()`|(작성중)|
|`islower()`|`iswlower()`|(작성중)|
|`islower()`|`iswupper()`|(작성중)|
|`isupper()`||(작성중)|
|`isdigit()`|`iswdigit()`|(작성중)|
|`isxdigit()`|`iswxdigit()`|(작성중)|
|`iscntrl()`|`iswcntrl()`|(작성중)|
|`isgraph()`|`iswgraph()`|(작성중)|
|`isspace()`|`iswspace()`|(작성중)|
|`isblank()` (C++11~)|`iswblank()` (C++11~)|(작성중)|
|`isprint()`|`iswprint()`|(작성중)| 
|`ispunct()`|`iswpunct()`|(작성중)| 
||`iswctype()`|(작성중)| 
||`wctype()`|(작성중)|  
||`locale()`|(작성중)| 
 
**문자 조작**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`tolower()`|`towlower()`|(작성중)|
|`toupper()`|`towupper()`|(작성중)|
||`towctrans()`|(작성중)|
||`wctrans()`|(작성중)|

**문자열 조작**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`strcpy()`|`wcscpy()`|(작성중)|
|`strncpy()`|`wcsncpy()`|(작성중)|
|`strcat()`|`wcscat()`|(작성중)|
|`strncat()`|`wcsncat()`|(작성중)|
|`strxfrm()`|`wcsxfrm()`|(작성중)|

**문자열 검사**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`strlen()`|`wcslen()`|(작성중)|
|`strcmp()`|`wcscmp()`|(작성중)|
|`strncmp()`|`wcsncmp()`|(작성중)|
|`strcoll()`|`wcscoll()`|(작성중)|
|`strchr()`|`wcschr()`|(작성중)|
|`strrchr()`|`wcsrchr()`|(작성중)|
|`strspn()`|`wcsspn()`|(작성중)|
|`strcspn()`|`wcscspn()`|(작성중)|
|`strpbrk()`|`wcspbrk()`|(작성중)|
|`strstr()`|`wcsstr()`|(작성중)|
|`strtok()`|`wcstok()`|(작성중)|

**메모리 조작**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`memchr()`|`wmemchr()`|(작성중)|
|`memcmp()`|`wmemcmp()`|(작성중)|
|`memset()`|`wmemset()`|(작성중)|
|`memcpy()`|`wmemcpy()`|(작성중)| 
|`memmove()`|`wmemmove()`|(작성중)| 

**숫자 포맷**

|바이트 문자열|와이드 문자열|내용|
|--|--|--|
|`atof()`||(작성중)|
|`atoi()`<br/>`atol()`<br/>`atoll()` (C++11~)||(작성중)|
|`strtol()`<br/>`strtoll()` (C++11~)|`wcstol()`<br/>`wcstoll()`|(작성중)|
|`strtoul()`<br/>`strtoull()` (C++11~)|`wcstoul()`<br/>`wcstoull()`|(작성중)|
|`strtof()`<br/>`strtod()`<br/>`strtold()`|`wcstof()`<br/>`wcstod()`<br/>`wcstold()`|(작성중)|
|`strtoimax()` (C++11~)<br/>`strtoumax()` (C++11~)|`wcstoimax()` (C++11~)<br/>`wcstoumax()` (C++11~)|(작성중)|

# 와이드 문자열

**타입**

|항목|내용|
|--|--|
|`wctrans_t`|(작성중)|
|`wctype_t`|(작성중)|
|`wint_t`|(작성중)| 

**매크로**

|항목|내용|
|--|--|
|`WEOF`|(작성중)|
|`WCHAR_MIN`|(작성중)|
|`WCHAR_MAX`|(작성중)| 

# 멀티 바이트

**멀티 바이트 문자열과 와이드 문자열간 변환**

|항목|내용|
|--|--|
|[mblen(str)](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|`str` 주소의 문자가 몇 바이트 크기인지 구합니다.|
|`mbtowc()`|(작성중)|
|`wctomb()`|(작성중)|
|[mbstowcs()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|해당 주소의 문자들의 코드를 `wchar_t`로 변환합니다.|
|`wcstombs()`|(작성중)|
|`wcstombs()`|(작성중)|
|`mbsinit()`|(작성중)|
|`btowc()`|(작성중)|
|`wctob()`|(작성중)|
|`mbrlen()`|(작성중)| 
|`mbrtowc()`|(작성중)| 
|`wcrtomb()`|(작성중)| 
|`mbsrtowcs()`|(작성중)| 
|`wcsrtombs()`|(작성중)| 
|`mbrtoc8()` (C++20~)|(작성중)| 
|`c8rtomb()` (C++20~)|(작성중)| 
|`mbrtoc16()` (C++11~)|(작성중)| 
|`c16rtomb()` (C++11~)|(작성중)| 
|`mbrtoc32()` (C++11~)|(작성중)| 
|`c32rtomb()` (C++11~)|(작성중)| 

**타입**

|항목|내용|
|--|--|
|`mbstate_t`|(작성중)|

**매크로**

|항목|내용|
|--|--|
|`MB_LEN_MAX`|(작성중)|
|[MB_CUR_MAX](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%A9%80%ED%8B%B0-%EB%B0%94%EC%9D%B4%ED%8A%B8-%EB%AC%B8%EC%9E%90%EC%97%B4)|현재 [locale()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-locale/#c%EC%8A%A4%ED%83%80%EC%9D%BC-locale)에서 멀티 바이트 문자의 최대 크기입니다.|
|`__STDC_UTF_16__` (C++11~)|(작성중)| 
|`__STDC_UTF_32__` (C++11~)|(작성중)| 

# strerror()

[errorno](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%98%A4%EB%A5%98-%EB%B2%88%ED%98%B8)를 문자열로 출력해 줍니다.

# basic_string

대부분 `vector`와 동일하며, `string`에 필요한 기능이 추가되었습니다. 

**정적 멤버 변수**

|항목|내용|
|--|--|
|`npos`|"찾을 수 없음"이나 "나머지 모든 문자"를 나타내는 의미로 사용합니다.|

```cpp
static const size_type npos = -1;
```

**생성자**

|항목|내용|
|--|--|
|`basic_string()`|빈 `string` 개체를 생성합니다.|
|`basic_string(const string& other)`|복사 생성합니다.|
|`basic_string(const value_type* ptr)`|C 언어 스타일의 문자열로부터 문자들을 복사하여 `string` 개체를 생성합니다.|
|`basic_string(const string& other, size_type, offset, size_type count = npos)`<br/>`basic_string(const value_type* ptr, size_type count)`<br/>|`offset` 위치 부터 `count` 갯수만큼 문자들을 복사하여 `string`개체를 생성합니다.|
|`basic_string(size_type count, value_type char_value)`|`char_value`를 `count`만큼 채운 `string`을 생성합니다.|
|`basic_string(const_iterator first, const_iterator last)`|`first`부터 `last` 직전까지의 요소(반개방 구조)를 복사하여 `string` 개체를 생성합니다.|

**연산자**

|항목|내용|
|--|--|
|`operator =(const basic_stiring& other)`|`other`를 복사 대입합니다.|
|`operator =(const basic_stiring&& other) noexcept` (C++11~)|`other`를 이동 대입합니다.|
|`+`|두 문자열을 연결하여 새로운 문자열을 만듭니다.|
|`+=`|문자열에 문자나 문자열을 추가합니다.|
|`==`<br/>`!=` (~C++20)|두 문자열이 같은지, 다른지 검사합니다. 단순히 포인터 비교하는게 아니라 실제 문자들을 비교합니다.|
|`<, <=, >, >=` (~C++20)<br/>`<=>` (C++20~)|두 문자열을 대소 비교합니다.|
|`<<, >>`|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

**할당과 문자열 관리**

|항목|`string` 특화|내용|
|--|--|--|
|`assign()`||`string`의 기존 내용을 지우고 새로운 문자열을 복사합니다.|
|`assign_range()` (C++11~)||(작성중)|
|`get_allocator()` (C++23~)||(작성중)|
|`resize()`||`string`의 새 크기를 지정하며, 필요에 따라 문자를 추가하거나 지웁니다.|
|`size()`<br/>`length()`|O|`string`의 문자 갯수를 리턴합니다.<br/>`string`에서는 `length`를 추가로 제공합니다.|
|`empty()`||`string`이 비었는지 확인합니다.|
|`capacity()`||메모리를 더 할당하지 않고 `string`에 저장할 수 있는 문자 갯수를 리턴합니다.(`string`은 `vector`와 동일하게 메모리 할당이 빈번히 발생하지 않도록 실제 문자 갯수보다 큰 크기를 할당하기도 하고, 삽입/삭제에 따라 실제 문자 갯수보다 더 많은 메모리를 관리할 수 있습니다.)|
|`shrink_to_fit()`||(작성중)|
|`reserve()`||`string`의 메모리 공간을 최소한 `count` 갯수 만큼 문자를 저장할 수 있도록 예약합니다.|
|`max_size()`||`string`이 저장할 수 있는 최대 문자 갯수를 리턴합니다.|
|`swap()`||두 `string`의 내부 데이터를 바꿔치기 합니다.|

**문자 검색**

|항목|`string` 특화|내용|
|--|--|--|
|`[]`||요소에 접근합니다.|
|`at()`||`position`위치의 요소 참조자를 리턴합니다. `position`이 잘못된 위치이면 `[]` 과 달리 예외를 발생시키며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`begin(), end()`||순방향 이터레이터를 리턴합니다.|
|`rbegin(), rend()`||역방향 이터레이터를 리턴합니다.|
|`cbegin(), cend()`||방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin() crend()`||역방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`data()`||컨테이너가 관리하는 메모리 블록을 리턴합니다. STL 구현에 따라 끝에 널문자가 있을 수도 있지만, 표준이 아니기 때문에 [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 구할 때는 `c_str()`을 이용해야 합니다.|
|`c_str()`|O|c 스타일의 [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 리턴합니다.(끝에 널문자가 있습니다.)|
|`front()`||첫번째 요소의 참조자를 리턴합니다. `string`이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`back()`||마지막 요소의 참조자를 리턴합니다. `string`이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`find()`<br/>`rfind()`|O|지정된 문자 시퀀스와 일치하는 첫번째 인덱스를 찾습니다.|
|`find_first_of()`<br/>`find_first_not_of()`|O|지정된 문자들중 일치하는 문자가 있는 첫번째 인덱스를 찾습니다.|
|`find_last_of()`<br/>`find_last_not_of()`|O|지정된 문자들중 일치하는 문자가 있는 마지막 인덱스를 찾습니다.|
|`operator basic_string_view()` (C++17~)|O|(작성중)|

**요소 삽입/삭제/비교/추출**

|항목|`string` 특화|내용|
|--|--|--|
|`clear()`||모든 요소를 지웁니다. 이때 메모리 영역은 그대로 입니다.|
|`erase()`||`position`위치의 요소를 삭제하거나 `first`와 `last` 직전까지의 요소(반개방 구조)를 삭제합니다. 이터레이터가 유효하지 않다면, 아무 생각없이 실행되어 오동작 합니다. |
|`erase_if()` (C++20~)||(작성중)|
|`pop_back()`||마지막 요소를 삭제합니다. `string`이 비었다면 아무 동작 안합니다.|
|`push_back()`||`string`끝에 요소를 추가합니다.|
|`insert()`||`position`으로 지정한 위치 앞에 삽입합니다.|
|`insert_range()` (C++23~)||(작성중)|
|`append()`|O|문자열에 문자나 문자열을 추가합니다.|
|`append_range()` (C++23~)||(작성중)|
|`compare()`|O|문자열을 대소 비교합니다.|
|`starts_with()` (C++20~)|O|(작성중)|
|`ends_with()` (C++20~)|O|(작성중)|
|`contains()` (C++23~)|O|(작성중)|
|`replace()`|O|`string`의 요소를 지정한 문자나 다른 시퀀스로 바꿉니다.|
|`replace_with_range()` (C++23~)|O|(작성중)|
|`substr()`|O|`string`에서 하위 문자열을 추출합니다.|
|`copy()`|O|`string`에서 하위 문자열을 문자 배열에 복사합니다.|
|`getline()`|O|IO 스트림으로부터 데이터를 읽습니다.|

# 숫자 변환

|항목|내용|
|--|--|
|`stoi(), stol(), stoll()` (C++11~)|부호 있는 정수를 문자열로 바꿉니다.|
|`stoul(), stoull()` (C++11~)|부호 없는 정수를 문자열로 바꿉니다.| 
|`stof(), stod(), stold()` (C++11~)|실수를 문자열로 바꿉니다.| 
|`to_string()` (C++11~)|`string`을 정수 또는 실수로 바꿉니다.| 
|`to_wstring()` (C++11~)|`wstring`을 정수 또는 실수로 바꿉니다.|  

# (C++17~) string_view

문자열에 대한 읽기만 필요한 함수가 있는 경우 `string`이나 `const char*`로 전달받을 수 있는데요, 둘다 문제가 좀 있습니다.

1. `string`으로 인자를 만든 경우 : 암시적으로 `string`개체가 생성됩니다.

    ```cpp
    std::size_t Func(const std::string& str) {return str.length();}

    std::string str1{"Hello"};
    const char* str2{"World"};

    EXPECT_TRUE(Func(str1) == 5);
    EXPECT_TRUE(Func(str2) == 5); // (△) 비권장. 암시적으로 string 개체가 생성됩니다.
    ```

2. `const char*` 으로 인자를 만든 경우 : [널종료 문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)을 강제로 만들어야 합니다.  
    
    ```cpp
    std::size_t Func(const char* str) {
        std::size_t i{0};
        while(str[i] != '\0') {
            ++i;
        } 
        return i;
    }

    std::string str1{"Hello"};
    const char* str2{"World"};

    EXPECT_TRUE(Func(str1.c_str()) == 5); // (△) 비권장. 널종료 문자열을 만듭니다.
    EXPECT_TRUE(Func(str2) == 5); // (△) 비권장. 문자열의 길이는 매번 다시 '\0'까지 카운팅해야 합니다.
    ```

C++17 부터는 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view)가 추가되어 문자열을 읽기 전용으로 사용할 때 불필요한 `string`복제가 없도록 해줍니다.

내부적으로 `string`이나 `const char*`를 참조만 하여 메모리 낭비를 없애고, 읽기 전용 함수들만 제공하여 `string` 관련 편의 기능들을 그대로 사용할 수 있습니다.

```cpp
std::size_t Func(std::string_view sv) {
    return sv.length();
}

std::string str1{"Hello"};
const char* str2{"World"};

EXPECT_TRUE(Func(str1) == 5); // (O) 불필요하게 string 개체를 생성하지 않습니다.
EXPECT_TRUE(Func(str2) == 5); // (O) 불필요하게 string 개체를 생성하지 않습니다.
```

[basic_string](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#basic_string)처럼 기본 클래스는 `basic_string_view` 이며, 처리하는 문자 타입에 따라 재정의한 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view), `wstring_view`등을 사용합니다.

|항목|내용|
|--|--|
|[string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view) (C++17~)|`basic_string_view<char>`|
|`wstring_view` (C++17~)|`basic_string_view<wchar_t>`|
|`u16string_view ` (C++17~)|`basic_string_view<char16_t>`|
|`u32string_view ` (C++17~)|`basic_string_view<char32_t>`|
|`u8string_view ` (C++20~)|`basic_string_view<char16_t>`|

**생성자**

|항목|내용|
|--|--|
|`basic_string_view()` (C++17~)|빈 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view) 개체를 생성합니다.|
|`basic_string_view(const basic_string_view& other)` (C++17~)|복사 생성합니다. 이때 내부 관리 문자열은 참조만 하고 복제하지는 않습니다.|
|`basic_string_view(const value_type* ptr)` (C++17~)|C 언어 스타일의 문자열을 참조합니다.|
|`basic_string_view(const value_type* ptr, size_type count)` (C++17~)|`count`길이(널문자 포함)인 C 언어 스타일의 문자열을 참조합니다.|

**연산자**

|항목|내용|
|--|--|
|`operator =(const basic_string_view& other)` (C++17~)|`other`를 복사 대입합니다.|
|`==` (C++17~)<br/>`!=` (~C++20)|두 문자열이 같은지, 다른지 검사합니다. 단순히 포인터 비교하는게 아니라 실제 문자들을 비교합니다.|
|`<, <=, >, >=` (~C++20)<br/>`<=>` (C++20~)|두 문자열을 대소 비교합니다.|
|`<<, >>` (C++17~)|출력 스트림에 출력하거나, 입력 스트림에서 문자열을 추출합니다.|

**할당과 문자열 관리**

|항목|내용|
|--|--|
|`size()` (C++17~)<br/>`length()` (C++17~)|문자 갯수를 리턴합니다.|
|`empty()` (C++17~)|문자열이 비었는지 확인합니다.|
|`max_size()` (C++17~)|저장할 수 있는 최대 문자 갯수를 리턴합니다.|
|`swap()` (C++17~)|두 [string_view](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-string_view)의 내부 데이터를 바꿔치기 합니다.|

**문자 검색**

|항목|내용|
|--|--|
|`[]` (C++17~)|요소에 접근합니다.|
|`at()` (C++17~)|`position`위치의 요소 참조자를 리턴합니다. `position`이 잘못된 위치이면 `[]` 과 달리 예외를 발생시키며, 검사 코드가 추가되어 상대적으로 속도 부하가 있습니다.|
|`begin(), end()` (C++17~)|순방향 이터레이터를 리턴합니다.|
|`rbegin(), rend()` (C++17~)|역방향 이터레이터를 리턴합니다.|
|`cbegin(), cend()` (C++17~)|방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`crbegin() crend()` (C++17~)|역방향 이터레이터를 리턴합니다. 이때 요소를 수정할 수 없습니다.|
|`data()` (C++17~)|컨테이너가 관리하는 메모리 블록을 리턴합니다. 끝에 널문자가 없을 수도 있습니다.|
|`front()` (C++17~)|첫번째 요소의 참조자를 리턴합니다. 문자열이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`back()` (C++17~)|마지막 요소의 참조자를 리턴합니다. 문자열이 비었다면 아무 생각없이 실행되어 오동작 합니다.|
|`find()` (C++17~)<br/>`rfind()` (C++17~)|지정된 문자 시퀀스와 일치하는 첫번째 인덱스를 찾습니다.|
|`find_first_of()` (C++17~)<br/>`find_first_not_of()` (C++17~)|지정된 문자들중 일치하는 문자가 있는 첫번째 인덱스를 찾습니다.|
|`find_last_of()` (C++17~)<br/>`find_last_not_of()` (C++17~)|지정된 문자들중 일치하는 문자가 있는 마지막 인덱스를 찾습니다.|

**요소 삽입/삭제/비교/추출**

|항목|내용|
|--|--|
|`compare()` (C++17~)|문자열을 대소 비교합니다.|
|`starts_with()` (C++20~)|(작성중)|
|`ends_with()` (C++20~)|(작성중)|
|`contains()` (C++23~)|(작성중)|
|`substr()` (C++17~)|`string`에서 하위 문자열을 추출합니다.|
|`copy()` (C++17~)|`string`에서 하위 문자열을 문자 배열에 복사합니다.|

# (C++17~) 숫자/문자열 변환

C++17 부터 숫자와 문자열간의 변환을 위한 [to_char(), from_char()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c17-%EC%88%AB%EC%9E%90%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%B3%80%ED%99%98) 함수가 제공되며, 기존 C스타일([atoi(), strtol()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98 )등) 보다 안전합니다.

|항목|내용|
|--|--|
|`to_chars()` (C++17~)|정수, 실수를 문자열로 변환합니다.|
|`to_chars_result` (C++17~)|`to_chars()`의 리턴값입니다. 변환된 문자열을 끝 포인터(`ptr`)와 에러 코드(`ec`, [errc](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98) 타입)로 구성됩니다.|
|`from_chars()` (C++17~)|문자열을 정수, 실수로 변환합니다.|
|`from_chars_result` (C++17~)|`from_chars()`의 리턴값입니다. 인자로 전달된 문자열에서 숫자로 해석할 수 없는 위치(`ptr`)와 에러 코드(`ec`, [errc](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%98%A4%EB%A5%98) 타입)로 구성됩니다.|
|`chars_format` (C++17~)|`scientific, fixed, hex, general(fixed 와 scientific)` 옵션을 제공합니다.|

```cpp
char buf[10];
{      
    // 11을 10진수 문자열로 변환    
    auto [ptr, ec]{std::to_chars(buf, buf + sizeof(buf), 11, 10)}; // 구조화된 바인딩. std::to_chars_result result{std::to_chars()} 와 동일
    if (ec == std::errc{}) {
        EXPECT_TRUE(std::string(buf, ptr - buf) == "11");
    }
}
{
    // 11을 16진수 문자열로 변환
    auto [ptr, ec]{std::to_chars(buf, buf + sizeof(buf), 11, 16)};
    EXPECT_TRUE(std::string(buf, ptr - buf) == "b");
}
{
    char str[]{"11year"}; // 숫자와 일반 문자로 구성됩니다.
    int result{0};
    auto [ptr, ec]{std::from_chars(str, str + sizeof(str), result)};
    if (ec == std::errc{}) {
        EXPECT_TRUE(result == 11); // 숫자 부분만 잘 변환합니다.
    }
    EXPECT_TRUE(ptr == &str[2]); // year는 숫자가 아니므로 첫 문자 주소를 리턴합니다.
}
```

# (C++20~)u8string

C++20 부터는 UTF8 을 관리할 수 있도록 `u8string`이 추가되었습니다.