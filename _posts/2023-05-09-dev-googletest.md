---
layout: single
title: "#9. [개발설정] googleTest로 C++ 단위 테스트 만들기"
categories: "dev-setting"
tag: ["개발설정", "visual studio code", "C++", "GoogleTest", "단위 테스트", "TDD"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

# 테스트케이스용 파일 구조(TEST())

GoogleTest의 테스트케이스는 하기와 같은 구조를 가집니다.

```cpp
#include "gtest/gtest.h"

TEST(TestSuiteName, TestName) {
    ...statements...   
}
```

|항목|내용|
|--|--|
|TEST|테스트 함수 정의|
|TestSuiteName|개별 테스트 집합의 이름|
|TestName|TestSuite에서 제공하는 개별 테스트의 이름|

일반적으로 GoogleTest의 폴더 구조는 하기와 같습니다.

    ```cpp
    +--language_test
        +--cpp
            +--src
                +--테스트하고 싶은 소스 코드들
        +--test
            +-- 테스트케이스 파일들
        +--googletest-1.13.0
            +--.github
            +--ci
            +--docs
            +--googlemock
            +--googletest
                +--include
                    +--gtest
                        +--gtest.h
    ```

`test`폴더에 테스트케이스들을 작성하기 때문에 `gtest.h`를 `include`하려면 `#include "../googletest-1.13.0/googletest/include/gtest/gtest.h"` 로 접근해야 합니다. 

이러한 불편을 막고자 `googletest-1.13.0/googletest/CMakelists.txt`를 보면 하기와 같이 빌드 구성시 `include_directories`를 사용하여  `googletest-1.13.0/googletest/include` 를 사전 설정해 두었습니다.

이에 따라 `#include "gtest/gtest.h"` 만 작성해도 include 가 가능합니다.

```bash
# Where Google Test's .h files can be found.
set(gtest_build_include_dirs
    "${gtest_SOURCE_DIR}/include"
    "${gtest_SOURCE_DIR}")
include_directories(${gtest_build_include_dirs})
```

# 테스트용 매크로

|항목|내용|
|--|--|
|EXPECT_TRUE(condition)||
|ASSERT_TRUE(condition)||
|EXPECT_FALSE(condition)||
|ASSERT_FALSE(condition)||
|EXPECT_THAT(value,matcher)||
|ASSERT_THAT(value,matcher)||
EXPECT_EQ(val1,val2)
ASSERT_EQ(val1,val2)
EXPECT_NE(val1,val2)
ASSERT_NE(val1,val2)
EXPECT_NE(val1,val2)
ASSERT_NE(val1,val2)
EXPECT_GT(val1,val2)
ASSERT_GT(val1,val2)
EXPECT_GE(val1,val2)
ASSERT_GE(val1,val2)
EXPECT_STREQ(str1,str2)
ASSERT_STREQ(str1,str2)
EXPECT_STRNE(str1,str2)
ASSERT_STRNE(str1,str2)
EXPECT_STRCASEEQ(str1,str2)
ASSERT_STRCASEEQ(str1,str2)
EXPECT_STRCASENE(str1,str2)
ASSERT_STRCASENE(str1,str2)
EXPECT_FLOAT_EQ(val1,val2)
ASSERT_FLOAT_EQ(val1,val2)
EXPECT_DOUBLE_EQ(val1,val2)
ASSERT_DOUBLE_EQ(val1,val2)
EXPECT_NEAR(val1,val2,abs_error)
ASSERT_NEAR(val1,val2,abs_error)
EXPECT_THROW(statement,exception_type)
ASSERT_THROW(statement,exception_type)
EXPECT_ANY_THROW(statement)
ASSERT_ANY_THROW(statement)
EXPECT_NO_THROW(statement)
ASSERT_NO_THROW(statement)
EXPECT_PRED1(pred,val1...)
ASSERT_PRED1(pred,val1...)
EXPECT_PRED_FORMAT1(pred_formatter,val1...)
ASSERT_PRED_FORMAT1(pred_formatter,val1...)
EXPECT_HRESULT_SUCCEEDED(expression)
ASSERT_HRESULT_SUCCEEDED(expression)
EXPECT_HRESULT_FAILED(expression)
ASSERT_HRESULT_FAILED(expression)

EXPECT_DEATH(statement,matcher)
ASSERT_DEATH(statement,matcher)
EXPECT_DEATH_IF_SUPPORTED(statement,matcher)
ASSERT_DEATH_IF_SUPPORTED(statement,matcher)
EXPECT_DEBUG_DEATH(statement,matcher)
ASSERT_DEBUG_DEATH(statement,matcher)
EXPECT_EXIT(statement,predicate,matcher)
ASSERT_EXIT(statement,predicate,matcher)