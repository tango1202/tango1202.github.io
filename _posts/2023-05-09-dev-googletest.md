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
GoogleTest는 매크로를 통해 테스트 함수를 작성하고, 매크로 함수들을 통해 테스트를 수행합니다.

# 테스트케이스용 파일 구조

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

일반적으로 GoogleTest를 사용하는 프로젝트의 폴더 구조는 하기와 같습니다. 보통 상기 테스트케이스 파일은 `my_project_root/test` 폴더에서 관리합니다.

```cpp
+--my_project_root
    +--cpp
        +--src
            +--테스트하고 싶은 소스 코드들
    +--test
        +--테스트케이스 파일들
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

`test`폴더에 있는 테스트케이스 코드에서 `gtest.h`를 include 하려면 `#include "../googletest-1.13.0/googletest/include/gtest/gtest.h"` 로 접근해야 합니다. 

이러한 불편을 막고자 `googletest-1.13.0/googletest/CMakelists.txt`를 보면 하기와 같이 빌드 구성시 `include_directories` 를 사용하여  `googletest-1.13.0/googletest/include` 를 사전 설정해 두었습니다.

이에 따라 `#include "gtest/gtest.h"` 만 작성해도 include 가 가능합니다.

**googletest-1.13.0/googletest/CMakelists.txt**

```bash
...
# Where Google Test's .h files can be found.
set(gtest_build_include_dirs
    "${gtest_SOURCE_DIR}/include"
    "${gtest_SOURCE_DIR}")
include_directories(${gtest_build_include_dirs})
...
```

# 테스트용 매크로 함수

테스트를 수행하는 매크로는 `EXPECT` 계열과 `ASSERT` 계열이 있습니다. `EXPECT` 계열은 테스트가 실패하더라도 다음 테스트를 진행하지만, `ASSERT` 계열은 테스트가 실패하면 즉시 중단됩니다.(`EXPECT` 계열의 모든 것에 대응하는 `ASSERT` 계열이 있습니다.)

**일반화된 검증**

|항목|내용|
|--|--|
|EXPECT_THAT(actual_value, matcher)| actual_value와 macher가 일치하는지 검증. `macher`의 종류는 [Macher(http://google.github.io/googletest/reference/matchers.html)](http://google.github.io/googletest/reference/matchers.html)을 참고.|

**bool 검증**

|항목|내용|
|--|--|
|`EXPECT_TRUE(condition)`|condition == `true` 검증|
|`EXPECT_FALSE(condition)`|condition == `false` 검증|

**binary 검증**

|항목|내용|
|--|--|
|`EXPECT_EQ(val1,val2)`|val1 == val2 검증|
|`EXPECT_NE(val1,val2)`|val1 != val2 검증|
|`EXPECT_LT(val1,val2)`|val1 < val2 검증|
|`EXPECT_LE(val1,val2)`|val1 <= val2 검증|
|`EXPECT_GT(val1,val2)`|val1 > val2 검증|
|`EXPECT_GE(val1,val2)`|val1 >= val2 검증|

**string 검증**

|항목|내용|
|--|--|
|`EXPECT_STREQ(str1,str2)`|str1 == str2 검증|
|`EXPECT_STRNE(str1,str2)`|str1 != str2 검증|
|`EXPECT_STRCASEEQ(str1,str2)`|대소문자 무시하고 str1 == str2 검증|
|`EXPECT_STRCASENE(str1,str2)`|대소문자 무시하고 str1 != str2 검증|

**floating-point 검증**

|항목|내용|
|--|--|
|`EXPECT_FLOAT_EQ(val1,val2)`|float 값이 4ULP 범위 내에서 거의 동일한지 검증 |
|`EXPECT_DOUBLE_EQ(val1,val2)`|double 값이 4ULP 범위 내에서 거의 동일한지 검증|
|`EXPECT_NEAR(val1,val2,abs_error)`|abs_error 범위 내에서 동일한지 검증|

**그외 검증**

그외에 throw 검증, 서술문 검증, HRESULT 검증, death 검증이 있으며, 자세한 내용은 [GoogleTest Assertions(http://google.github.io/googletest/reference/assertions.html)](http://google.github.io/googletest/reference/assertions.html)을 참조하세요.

# Test Fixture : 여러 테스트에 동일 데이터 사용

유사 개체를 사용하는 여러개의 테스트가 있는 경우, 매번 개체를 새로 만드는 코드를 작성하면 번거로울 수 있습니다. 이럴 경우 Test Fixture를 작성하여, 테스트 개체 생성 코드를 재활용 할 수 있습니다.

Fixture는 하기와 같이 `::testing::Test`를 상속받아야 하며, `Setup`과 `TearDown`을 `override` 합니다.

```cpp
#include <vector>
// 테스트하려는 클래스 입니다.
class MyVector {
private:
    std::vector<int> data;
public:
    void Add(int a) { data.push_back(a); }
    int GetAt(int i) const { return data[i];  }
    int GetCount() const { return data.size(); }
};

// MyVector를 사용하는 Fixture 입니다.
class MyFixture : public ::testing::Test {
protected:
    // protected여서 이를 상속받은 테스트에서 접근 가능합니다.
    MyVector myData;
protected:
    // 각 테스트에 사용할 데이터를 준비합니다.
    void SetUp() override {
        myData.Add(1); 
        myData.Add(2);
    }
    // 테스트가 종료되면 데이터를 정리합니다.
    void TearDown() override {
        // MyVector는 vector만 있으므로 소멸자에서 알아서 정리됩니다.
    }
};
```

하기와 같이 `TEST_F`를 사용하여 Fixture를 사용할 수 있습니다.(`TEST`를 사용하면 Fixture를 인식하지 못합니다.) 

```cpp
#include "gtest/gtest.h"

TEST_F(TestFixtureName, TestName) {
    ...statements...   
}
```

```cpp
// MyFixture 는 사용하는 Fixture의 클래스 이름입니다.
TEST_F(MyFixture, Test1) {
    EXPECT_TRUE(myData.GetAt(0) == 1);
    EXPECT_TRUE(myData.GetCount() == 2);
    myData.Add(3); 
    EXPECT_TRUE(myData.GetCount() == 3);
}
// MyFixture 는 사용하는 Fixture의 클래스 이름입니다.
TEST_F(MyFixture, Test2) {
    EXPECT_TRUE(myData.GetAt(1) == 2);
    EXPECT_TRUE(myData.GetCount() == 2); // Test1에서 1개 더 넣었지만, Fixture는 새롭게 생성된 것입니다. 그러므로 2
}

```

호출 흐름은 다음과 같습니다.

1. `Test1` 호출되면 `MyFixture` 개체 생성
2. `MyFixture 생성자` 호출
3. `MyFixture::SetUp()` 호출
4. 본문 테스트 수행
5. `MyFixture::TearDown()` 호출
6. `MyFixture 소멸자` 호출
7. `Test2` 호출되면 `MyFixture` 개체 생성
8. 2 ~ 6 반복

