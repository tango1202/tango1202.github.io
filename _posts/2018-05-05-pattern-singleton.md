---
layout: single
title: "#5. [디자인 패턴-생성 패턴] Singleton"
categories: "pattern"
tag: ["디자인 패턴", "생성 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Singleton](https://tango1202.github.io/pattern/pattern-singleton/)은 개체가 오직 1번만 생성된다는 것을 코딩 계약으로 보장합니다.

# 설명

리소스 관리등 전역적으로 1개의 인스턴스만 필요한 개체들이 있습니다. 이런 개체들은 실수로 여러개 생성되지 않도록 코딩 계약을 맺어두는게 좋습니다. ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게***요.

다음 그림은 [Singleton](https://tango1202.github.io/pattern/pattern-singleton/)의 일반적인 구조입니다. 

`Singleton`은 생성자들을 `private`로 접근 차단합니다. 그뒤 [정적 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)인 `GetInstance()`를 통해서만 생성할 수 있게 합니다. 만약 여러번 호출된다면 `s_Instance`가 [함수내 정적 지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 이기 때문에 오직 1회만 생성한다는 것을 보장합니다.

![Slngleton](https://github.com/tango1202/tango1202.github.io/assets/133472501/b86a8252-13a9-42bc-a44d-62bd731f8685)

# 특징

주로 리소스 관리등 전역적으로 관리되는 `Manager`개체를 [Singleton](https://tango1202.github.io/pattern/pattern-singleton/)으로 만들면 좋습니다.

# 예제

다음은 `ErrorCode`를 문자열로 제공해주는 `ErrorCodeManager`의 예입니다. [Singleton](https://tango1202.github.io/pattern/pattern-singleton/)으로 만들어져 전역적으로 1개만 있음을 보장합니다.

1. #1 : `s_ErrorCodeManager`는 [함수내 정적 지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)이므로 1회만 생성됩니다. 또한, `GetInstance()`는 [정적 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)이기 때문에 어디서든 손쉽게 접근 가능합니다. 이때 `GetInstance()`로만 개체를 참조할 수 있도록, #2와 같이 생성자를 `private`로 만듭니다.
2. #3 : `errorCode`에 따라 메시지를 Get/Set합니다.
3. #4 : `GetInstance()`를 여러번 호출하더라도, 유일한 `ErrorCodeManager` 개체가 사용되는 것을 알 수 있습니다.

```cpp
enum class ErrorCode {Unknown, InvalidArgument, OutOfRange};

class ErrorCodeManager {
    std::map<ErrorCode, const char*> m_Map;
private:
    ErrorCodeManager() { // #2. 외부에서는 생성지 못하게 합니다.
        m_Map.insert(std::make_pair(ErrorCode::Unknown, "Unknown"));
    }
    ErrorCodeManager(const ErrorCodeManager&) = delete; 
    ErrorCodeManager(ErrorCodeManager&&) = delete; 
    ErrorCodeManager& operator =(const ErrorCodeManager&) = delete; 
    ErrorCodeManager& operator =(ErrorCodeManager&&) = delete;   

public:
    // ----
    // #1. Singleton 입니다. 1회만 생성됩니다.
    // ----
    static ErrorCodeManager& GetInstance() {
        static ErrorCodeManager s_ErrorCodeManager; // #1. 함수내 정적 지역 변수여서 함수 호출시 1회만 생성됩니다.

        return s_ErrorCodeManager;
    } 

    // #3. 동일한 ErrorCode가 있으면 수정하고, 없으면 추가합니다.
    void SetMessage(ErrorCode errorCode, const char* message) {
        assert(message);

        auto result = m_Map.insert(std::make_pair(errorCode, message));
        
        // 동일한 ErrorCode가 있어서 insert에 실패하면 message를 변경합니다.
        if (result.second == false) {
            (*(result.first)).second = message;
        }
    } 
    // #3. errorCode인 문자열을 리턴하고, 없으면 nullptr을 리턴합니다.
    const char* GetMessage(ErrorCode errorCode) const {
        auto result = m_Map.find(errorCode);
        if (result == m_Map.end()) {
            return nullptr;
        } 
        return (*result).second;
    }         
};

// ----
// 테스트 코드
// ----
// #4. 등록하지 않은 것들을 등록합니다.
ErrorCodeManager::GetInstance().SetMessage(ErrorCode::InvalidArgument, "Invalid Argument");
ErrorCodeManager::GetInstance().SetMessage(ErrorCode::OutOfRange, "Out Of Range");

// #4. 원래 입력했던 Unknown 과 새롭게 등록한 Invalid Argument, Out Of Range가 있습니다.
EXPECT_TRUE(ErrorCodeManager::GetInstance().GetMessage(ErrorCode::Unknown) == "Unknown");
EXPECT_TRUE(ErrorCodeManager::GetInstance().GetMessage(ErrorCode::InvalidArgument) == "Invalid Argument");
EXPECT_TRUE(ErrorCodeManager::GetInstance().GetMessage(ErrorCode::OutOfRange) == "Out Of Range");
```
