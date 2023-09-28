---
layout: single
title: "#26. [모던 C++ STL] (C++11~) 정규 표현식(regex_match(), regex_search(), regex_replace())"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

C++11 부터 STL 에서는 정규 표현식 관련 개체와 유틸리티가 제공되어 패턴이 매칭되는 문자열을 쉽게 비교할 수 있습니다.

|항목|내용|
|--|--|
|`basic_regex` (C++11~)|정규 표현식을 저장합니다.|
|`regex` (C++11~)|`basic_regex<char>` 입니다.|
|`wregex` (C++11~)|`basic_regex<wchar_t>` 입니다.|
|`sub_match` (C++11~)|(작성중)|
|`match_results` (C++11~)|(작성중)|
|`regex_match()` (C++11~)|대상이 정규 표현식과 일치하는지 검사합니다.|
|`regex_search()` (C++11~)|대상의 일부가 정규 표현식과 일치하는 부분이 있는지 검사합니다.|
|`regex_replace()` (C++11~)|대상에서 정규 표현식과 일치하는 부분을 바꿉니다.|
|`regex_iterator` (C++11~)|(작성중)|
|`regex_token_iterator` (C++11~)|(작성중)|
|`regex_error` (C++11~)|(작성중)|
|`regex_traits` (C++11~)|(작성중)|
|`syntax_option_type` (C++11~)|(작성중)|
|`match_flag_type` (C++11~)|(작성중)|
|`error_type` (C++11~)|(작성중)|

# 정규 표현식

정규 표현식은 문자열이 특정한 규칙에 부합하는지 검사 및 대체하는 형식 언어입니다.

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
|`{m, n}`|이전 문자가 `m` ~ `n`번 반복되는 것을 나타냅니다.|
|`{m,}`|이전 문자가 `m`번 이상 반복되는 것을 나타냅니다.|
|`{,n}`|이전 문자가 `m`번 이하 반복되는 것을 나타냅니다.|
|`{,n}`|이전 문자가 `m`번 이하 반복되는 것을 나타냅니다.|
|`?`|`{0, 1}`과 동일합니다.|


# regex_match()

주어진 정규 표현식과 문자열이 완전히 매칭되면 `true`를 리턴합니다.

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

# regex_replace()