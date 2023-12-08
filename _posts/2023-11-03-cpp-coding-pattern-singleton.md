---
layout: single
title: "#3. [C++ 코딩 패턴] Singleton(작성중)"
categories: "cpp-coding-pattern"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

```cpp
    private:
        explicit NewHandler(Mode mode, size_t reservedSize) : 
            m_Mode(mode),
            m_Reserved(reservedSize) {}
    public:
        // 전역적으로만 생성됩니다. BadAlloc을 사용하며 예약 공간은 0입니다.
        static const NewHandler& GetInstance() {
            static NewHandler s_NewHandler(BadAlloc, 0);
            return s_NewHandler;
        }
        static NewHandler& GetInstanceRef() {
            return const_cast<NewHandler&>(GetInstance());
        }
```