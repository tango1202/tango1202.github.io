---
layout: single
title: "#26. [모던 C++ STL] (C++11~) 정규 표현식(regex_match(), regex_search(), regex_replace())"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `regex_match()`는 문자열의 전체가 정규 표현식과 일치하는지 검사합니다.
> * `regex_search()`는 문자열의 일부가 정규 표현식과 일치하는지 검사합니다.
> * `regex_iterator`는 문자열 전체에 대해 `regex_search()` 한 결과의 이터레이터입니다.
> * `regex_replace()`는 문자열에서 정규 표현식과 일치하는 부분을 수정합니다.

# 개요

C++11 부터 STL 에서는 정규 표현식 관련 개체와 유틸리티가 제공되어 패턴이 매칭되는 문자열을 쉽게 검사하고 수정할 수 있습니다.

|항목|내용|
|--|--|
|`basic_regex` (C++11~)|정규 표현식을 저장합니다.([regex_match()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_match) 참고)<br/>* `regex` : `basic_regex<char>` 입니다.<br/>* `wregex` : `basic_regex<wchar_t>` 입니다.|
|`sub_match` (C++11~)|(작성중)|
|`match_results` (C++11~)|정규 표현식의 매칭 결과를 저장합니다.([regex_search()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_search) 참고)<br/>* `cmatch` : `match_result<const char*>` 입니다.<br/>* `wcmatch` : `match_result<const wchar_t*>` 입니다.<br/>* `smatch` : `match_result<string::const_iterator>` 입니다.<br/>* `wsmatch` : `match_result<wstring::const_iterator>` 입니다.|
|[regex_match()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_match) (C++11~)|문자열의 전체가 정규 표현식과 일치하는지 검사합니다.|
|[regex_search()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_search) (C++11~)|문자열의 일부가 정규 표현식과 일치하는지 검사합니다.|
|[regex_replace()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_replace) (C++11~)|문자열에서 정규 표현식과 일치하는 부분을 수정합니다.|
|[regex_iterator](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-regex_match/#regex_iterator) (C++11~)|문자열 전체에 대해 `regex_search()` 한 결과의 이터레이터입니다.<br/>* `cregex_iterator` : `regex_iterator<const char*>` 입니다.<br/>* `wcregex_iterator` : `regex_iterator<const wchar_t*>` 입니다.<br/>* `sregex_iterator` : `regex_iterator<string::const_iterator>` 입니다.<br/>* `wsregex_iterator` : `regex_iterator<wstring::const_iterator>` 입니다.|
|`regex_token_iterator` (C++11~)|(작성중)|
|`regex_error` (C++11~)|(작성중)|
|`regex_traits` (C++11~)|(작성중)|
|`syntax_option_type` (C++11~)|(작성중)|
|`match_flag_type` (C++11~)|(작성중)|
|`error_type` (C++11~)|(작성중)|

# 정규 표현식

정규 표현식은 문자열이 특정한 패턴 규칙과 일치하는지 검사 및 수정하는 형식 언어입니다.

정규 표현식은 다음과 같은 메타 문자를 이용하여 패턴 규칙을 표현합니다.

|항목|내용|
|--|--|
|`.`|`\n`을 제외한 모든 문자를 나타냅니다.|
|`[]`|`[]`사이의 문자들과 매칭되는지를 나타냅니다.<br/>`[abc]`는 `a`, `b`, `c`중 한개의 문자를 뜻합니다.|
|`-`|두문자 사이의 범위를 나타냅니다.<br/>`[a-zA-Z]`는 알파벳 대소문자중 한개와 매칭되는지를 뜻합니다.|
|`^`|반대를 나타냅니다.<br/>`[^0-9]`는 숫자가 아닌 문자를 뜻합니다.|
|`\d`|숫자를 나타냅니다.<br/>`[0-9]`와 동일합니다.|
|`\D`|숫자가 아닌 것을 나타냅니다.<br/>`[^0-9]`와 동일합니다.|
|`\s`|공백 문자를 나타냅니다.<br/>`[ \t\n\r\f\v]`와 동일합니다.|
|`\S`|공백 문자가 아닌 문자를 나타냅니다.<br/>`[^ \t\n\r\f\v]`와 동일합니다.|
|`\w`|문자와 숫자를 나타냅니다.<br/>`[a-zA-Z0-9_]`와 동일합니다.|
|`\W`|문자와 숫자가 아닌문자를 나타냅니다.<br/>`[^a-zA-Z0-9_]`와 동일합니다.|
|`*`|이전 문자가 0부터 무한대로 반복되는 것을 나타냅니다.<br/>`.*`은 임의의 숫자나 문자가 무한히 반복되는 것을 뜻합니다.<br/>`hel*o`는 `helo`, `hello`, `helllo`등이 모두 매칭됩니다.|
|`{m}`|이전 문자가 `m`번 반복되는 것을 나타냅니다.<br/>`hel{3}o`은 `helllo`만 매칭됩니다.|
|`{m,n}`|이전 문자가 `m` ~ `n`번 반복되는 것을 나타냅니다.|
|`{m,}`|이전 문자가 `m`번 이상 반복되는 것을 나타냅니다.|
|`{,n}`|이전 문자가 `m`번 이하 반복되는 것을 나타냅니다.|
|`{,n}`|이전 문자가 `m`번 이하 반복되는 것을 나타냅니다.|
|`?`|`{0, 1}`과 동일합니다.|
|`|`|왼쪽과 오른쪽을 논리 OR로 평가합니다.|
|`()`|괄호내의 것을 문자 그룹으로 만듭니다.<br/>`(left|right)`는 `left`와 `right` 문자열중 하나만 일치하면 매칭됩니다.<br/>또한 괄호내의 것을 캡쳐합니다.|

# regex_match()

문자열의 전체가 정규 표현식과 일치하는지 검사합니다.

다음 코드는 `ab[,:].*`과 일치하는 문자열을 검사하는 예입니다. `ab,`나 `ab:`로 시작하는 모든 문자열을 검색합니다.

```cpp
std::vector<std::string> v{"abcd", "ab,cd", "ab:cd", "ab cd"}; 
std::vector<std::string> result;

// ab 다음에 `,` 또는 `:` 가 있는 문자열 검색하되, 모든 문자(.)가 0부터 무한대로 반복(*)됨.
std::regex regex{"ab[,:].*"}; 

for(auto str : v) {
    if (std::regex_match(str, regex)) {
        result.push_back(str);
    }    
}

EXPECT_TRUE(result.size() == 2 && result[0] == "ab,cd" && result[1] == "ab:cd");
```
# regex_search()

문자열의 일부가 정규 표현식과 일치하는지 검사합니다.

다음 코드는 전화번호가 있는 부분을 검사하는 예입니다.

`smatch`는 `match_result<string::const_iterator>` 로서 매칭 결과를 저장합니다. `str()`을 이용하여 매칭된 문자열을 구할 수 있으며, `suffix()`를 이용하여 매칭된 다음 위치부터 다시 검사합니다.

```cpp
std::string str{
    R"(Tango's phone number is 010-1234-5678. 
His home phone number is 02-123-4567.)"
}; 
std::vector<std::string> result;

// 010- 또는 02-로 시작하고, 숫자가 3~4개 반복, -, 숫자가 4개 반복
std::regex regex{R"((010|02)-[0-9]{3,4}-[0-9]{4})"}; // 특수 기호가 있으므로, Raw String 리터럴로 표기하는게 좋습니다.
std::smatch matchResult;

while(std::regex_search(str, matchResult, regex)) {
    result.push_back(matchResult.str()); 

    str = matchResult.suffix(); // matchResult 다음 위치의 문자열입니다. str에 대입하여 다음번 매칭을 찾습니다.
}

EXPECT_TRUE(result.size() == 2 && result[0] == "010-1234-5678" && result[1] == "02-123-4567");
```

# regex_iterator

문자열 전체에 대해 `regex_search()` 한 결과의 이터레이터입니다. 상기 예제에서는 `while()`과 `suffix()` 를 사용했지만, 이터레이터를 이용하여 다음과 같이 간소화 할 수 있습니다.

```cpp
std::string str{
    R"(Tango's phone number is 010-1234-5678. 
His home phone number is 02-123-4567.)"
}; 
std::vector<std::string> result;

std::regex regex{R"((010|02)-[0-9]{3,4}-[0-9]{4})"};

// 문자열 전체의 regex_search() 결과를 이터레이터로 리턴합니다. 
auto itr{std::sregex_iterator(str.begin(), str.end(), regex)};
auto endItr{std::sregex_iterator()};

for (; itr != endItr; ++itr) {
    result.push_back(itr->str());
}

EXPECT_TRUE(result.size() == 2 && result[0] == "010-1234-5678" && result[1] == "02-123-4567");
```

# regex_replace()

문자열에서 정규 표현식과 일치하는 부분을 수정합니다.

```cpp
std::string str{
    R"(Tango's phone number is 010-1234-5678. 
His home phone number is 02-123-4567.)"
}; 

std::regex regex{R"((010|02)-[0-9]{3,4}-[0-9]{4})"};

// 매칭된 결과를 수정합니다.
std::string result{std::regex_replace(str, regex, "xxx-xxxx-xxxx")};

EXPECT_TRUE(result == R"(Tango's phone number is xxx-xxxx-xxxx. 
His home phone number is xxx-xxxx-xxxx.)");
```

정규 표현식의 캡쳐를 이용하면, 매칭된 부분의 값을 이용하여 바꿀 수 있습니다.

다음 예제에서는 괄호를 이용하여 `([0-9]{4}_[0-9]{2}_[0-9]{2})`을 캡쳐하고, 매칭된 문자열을 바꿀때 `$1`을 이용하여 캡쳐된 값으로 바꿉니다.

```cpp
std::string str{R"(file_2020_01_01.txt, file_2020_01_02.txt)"}; 

// ([0-9]{4}_[0-9]{2}_[0-9]{2}) 을 캡쳐합니다.
std::regex regex{R"(file_([0-9]{4}_[0-9]{2}_[0-9]{2})[.]txt)"};

// 매칭된 결과에서 캡쳐된 내용인 $1을 출력합니다.
std::string result{std::regex_replace(str, regex, "$1_file.txt")};

EXPECT_TRUE(result == R"(2020_01_01_file.txt, 2020_01_02_file.txt)");   
```






