---
layout: single
title: "#19. [디자인 패턴-행위 패턴] Observer"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Observer](https://tango1202.github.io/pattern/pattern-observer/)는 특정 개체를 감시하며, 수정 사항이 발생했을때 변경을 통지 받습니다.

# 설명

데이터와 이를 표시하는 `View`의 경우, 데이터 변경을 `View`에 통지하면, 해당 `View`에서 변경 사항을 수집하고 그려주게 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/5c07cfc3-bc80-4aa0-9de1-4b49c4e7c404)

이러한 관계를 `Doc-View`, `Publish-Subscribe` 라고도 합니다. 이때 개체간에 직접 참조를 한다면, 상호 참조 문제가 발생할 수 있습니다. 이러한 경우 [Observer 패턴](https://tango1202.github.io/pattern/pattern-observer/)을 사용할 수 있습니다.

다음 그림에서 `ConcreteSubject`에 변경 사항이 생기면, `Notify()`를 통해 `Observer`에 변경되었음을 통지합니다. 그러면, 각 `ConcreteObserver`에서는 `GetState()`나 `SetState()`를 호출하여 `ConcreteSubject`에 접근할 수 있습니다. 

![Observer](https://github.com/tango1202/tango1202.github.io/assets/133472501/ab07553c-d402-40aa-8ac1-a8851631624b)

|항목|내용|
|--|--|
|`Subject`|내용이 변경되었음을 `Observer`에 통지합니다.|
|`Observer`|`Subject`를 감시합니다.|
|`ConcreteSubject`|`Subject`를 구체화한 개체입니다.|
|`ConcreteObserver`|`Observer`를 구체화한 개체입니다.|

# 특징

1개 이상의 `Observer`가 `Subject`를 감시할 수 있습니다.

# 예제

다음은 `MyDoc`에 데이터가 추가되었을때 데이터의 합계와 평균을 출력하는 예입니다. 합계를 출력하는 `SumObserver`와 평균을 출력하는 `AverObserver`는 `MyDoc`을 감시하며, 변경 사항이 있으면, `MyDoc`에서 값을 가져옵니다.

1. #1 : `IObserver`는 `Subject`를 감시합니다.
2. #2 : `Subject`는 `Attach()`와 `Detach()`로 `IObserver`를 등록/해제 할 수 있으며, 변경 사항이 있으면, 모든 `IObserver`의 `Update()`를 호출합니다.
3. #3 : `MyDoc`에서 데이터가 추가되면, 모든 `Observer`에 통지합니다.
4. #4 : `SumObserver`와 `AverObserver`는 `MyDoc`을 감시하며, 변경 사항이 있으면 합계와 평균을 갱신합니다. 
5. #5 : `AverObserver`감시를 해제한 후 데이터를 추가하면, 평균값은 갱신되지 않습니다.

```cpp
// ----
// #1. Subject를 감시합니다.
// ----
class IObserver {
protected:
    IObserver() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~IObserver() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    IObserver(const IObserver&) = delete;
    IObserver(IObserver&&) = delete;
    IObserver& operator =(const IObserver&) = delete;
    IObserver& operator =(IObserver&&) = delete;   
public:
    // Subject가 수정될때 호출됩니다.
    virtual void Update() = 0;
};
// ----
// #2. Attach()와 Detach()로 IObserver를 등록/해제 할 수 있으며, 변경 사항이 있으면, 모든 IObserver의 Update()를 호출합니다.
// ----
class Subject {
private:
    std::list<IObserver*> m_Observers;
protected:
    Subject() = default; // 상속해서만 사용하도록 protected
    ~Subject() = default; // 다형 소멸을 하지 않으므로 protected non-virtual
private:
    Subject(const Subject&) = delete;
    Subject(Subject&&) = delete;
    Subject& operator =(const Subject&) = delete;
    Subject& operator =(Subject&&) = delete;   
public:
    // Observer를 등록합니다.
    void Attach(IObserver* observer) {
        assert(observer);

        m_Observers.push_back(observer);
    }
    // Observer를 해제합니다.
    void Detach(IObserver* observer) {
        assert(observer);

        std::list<IObserver*>::iterator itr{std::find(m_Observers.begin(), m_Observers.end(), observer)};
        
        if (itr != m_Observers.end()) {
            m_Observers.erase(itr);
        }
    }

    // #2. 변경 사항이 있으면, 모든 Observer의 Update()를 호출합니다.
    void Notify() {
        for(auto observer : m_Observers) {
            observer->Update();
        }
    }
};
// ----
// #3. 데이터가 추가되면 모든 Observer에 통지합니다.
// ----
class MyDoc : public Subject {
    std::vector<int> m_Data;
public:
    MyDoc() = default;
    ~MyDoc() = default;
private:
    MyDoc(const MyDoc&) = delete;
    MyDoc(MyDoc&&) = delete;
    MyDoc& operator =(const MyDoc&) = delete;
    MyDoc& operator =(MyDoc&&) = delete;  
public:
    void Add(int val) {
        m_Data.push_back(val);
        Notify(); // #3
    }
    int GetSum() const {
        return std::accumulate(m_Data.begin(), m_Data.end(), 0);
    }
    int GetAver() const {
        assert(0 < m_Data.size());
        
        return GetSum() / m_Data.size();
    } 
};

// ----
// #4. MyDoc을 감시하며, 변경 사항이 있으면, Sum값을 업데이트 합니다.
// ----
class SumObserver : public IObserver {
    MyDoc& m_MyDoc;
    int m_Val;
public:
    explicit SumObserver(MyDoc& myDoc) : m_MyDoc{myDoc} {}

    virtual void Update() override {
        m_Val = m_MyDoc.GetSum(); // #4
    }

    int GetVal() const {return m_Val;}
};

// ----   
// #4. MyDoc을 감시하며, 변경 사항이 있으면, Aver값을 업데이트 합니다.
// ----
class AverObserver : public IObserver {
    MyDoc& m_MyDoc;
    int m_Val;
public:
    explicit AverObserver(MyDoc& myDoc) : m_MyDoc{myDoc} {}

    virtual void Update() override {
        m_Val = m_MyDoc.GetAver(); // #4
    }

    int GetVal() const {return m_Val;}
};

// ----
// 테스트 코드
// ----  
MyDoc myDoc;

SumObserver sumObserver{myDoc};
AverObserver averObserver{myDoc};

myDoc.Attach(&sumObserver); // #2. myDoc을 감시합니다.
myDoc.Attach(&averObserver);

myDoc.Add(1);
myDoc.Add(2);
myDoc.Add(3);

EXPECT_TRUE(sumObserver.GetVal() == 1 + 2 + 3);
EXPECT_TRUE(averObserver.GetVal() == (1 + 2 + 3) / 3);

myDoc.Detach(&averObserver); // #2. averObserver 감시를 해제 합니다.

myDoc.Add(10); // #5

EXPECT_TRUE(sumObserver.GetVal() == 1 + 2 + 3 + 10); // sum은 계속 감시중이어서 값이 갱신됩니다.
EXPECT_TRUE(averObserver.GetVal() == (1 + 2 + 3) / 3); // #5. aver는 감시를 해제하여 값이 갱신되지 않습니다.
```