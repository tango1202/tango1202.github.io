---
layout: single
title: "#16. [디자인 패턴-행위 패턴] Iterator"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Iterator](https://tango1202.github.io/pattern/pattern-iterator/)는 집합 개체에서 요소에 접근하는 방법을 제공합니다. 집합 개체의 내부 구현을 외부에 은닉하기 때문에 집합 개체의 구현 방식을 비교적 안전하게 변경할 수 있습니다. C++의 경우 STL의 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)는 모두 [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)를 제공하며, `begin()`, `end()`, `++`, `*` 등의 인터페이스를 사용합니다.

# 설명

[Iterator](https://tango1202.github.io/pattern/pattern-iterator/)는 집합 개체의 내부 구현 방식을 은닉하고 각 요소에 접근하는 방법을 제공합니다. 따라서, 서로 내부 구조가 다르더라도 동일한 방식으로 요소에 접근할 수 있습니다. 

다음 코드를 보면 [vector](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-vector/)의 이터레이터와 [list](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/#%EC%8B%9C%ED%80%80%EC%8A%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)의 이터레이터가 모두 동일한 방식으로 각 요소에 접근합니다.

```cpp
{
    std::vector<int> v;
    std::vector<int>::iterator itr{v.begin()};
    std::vector<int>::iterator endItr{v.end()};

    for (;itr != endItr; ++itr) {
        // vector 의 각 요소에 접근합니다.
    }
}
{
    std::list<int> l;
    std::list<int>::iterator itr{l.begin()};
    std::list<int>::iterator endItr{l.end()};

    for (;itr != endItr; ++itr) {
        // list 의 각 요소에 접근합니다.
    }
}
```

다음 그림은 [Iterator 패턴](https://tango1202.github.io/pattern/pattern-iterator/)의 기본 구조입니다. 기본 구조가 [Factory Method 패턴](https://tango1202.github.io/pattern/pattern-factory-method/)과 동일합니다. `Aggregate`에서 `CreateIterator()`를 호출하면, 자식 개체인 `ConcreteAggregate`에서 실제 이터레이터인 `ConcreteIterator`를 생성해 줍니다. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/598b3733-3743-4793-a652-b39fbbdb9c90)

|항목|내용|
|--|--|
|`Aggregate`|집합의 요소를 관리하는 부모 클래스입니다. 자식 개체에서 `CreateIterator()`를 구체화 해야 합니다.|
|`Iterator`|`CreateIterator()`에서 생성하며, `Aggregate` 요소에 접근하는 인터페이스를 정의합니다.|
|`ConcreteAggregate`|`Aggregate`를 구체화한 개체입니다.|
|`ConcreteIterator`|`Iterator`를 구체화한 개체입니다.|

# 특징

C++의 경우 다른 [STL 알고리즘](https://tango1202.github.io/cpp-stl/modern-cpp-stl-algorithm/)과의 호환성을 위해 STL의 [컨테이너](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-container/)에서 제공하는 [이터레이터](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-iterator/)와 동일한 인터페이스를 제공하는게 좋습니다.

# 예제

다음은 [Iterator 패턴](https://tango1202.github.io/pattern/pattern-iterator/)의 구현 예입니다. `MyIterator`를 이용하여 `MyAggregate`의 각 요소에 접근합니다.

1. #1 : `Iterator` 입니다. `IAggregate`의 각 요소에 접근하는 함수들을 제공합니다.
2. #2 : `IAggregate`는 집합 개체로서 `CreateIterator()`를 제공해야 합니다.
3. #3 : `MyAggregate`는 `IAggregate`를 구체화하며, `CreateIterator()`시 `MyIterator`를 생성합니다.
4. #4 : `MyIterator`는 `Iterator`를 구체화합니다. `m_CurrentIndex`로 현재 요소의 위치를 관리합니다.

```cpp
// ----
// #1. Iterator 입니다. IAggregate 각 요소에 접근하는 함수들을 제공합니다.
// ----
class Iterator {
protected:
    Iterator() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Iterator() = default; // 다형 소멸 하도록 public virtual
private:
    Iterator(const Iterator&) = delete; 
    Iterator(Iterator&&) = delete; 
    Iterator& operator =(const Iterator&) = delete; 
    Iterator& operator =(Iterator&&) = delete; 
public:
    virtual void First() = 0; // 집합 개체의 처음으로 이동합니다.
    virtual void Next() = 0; // 다음 위치로 이동합니다.
    virtual bool IsDone() const = 0; // 끝인지 검사합니다. 
    virtual int GetCurrent() const = 0; // 현 위치의 값을 리턴합니다.
};

// ----
// #2. 집합 개체로서 CreateIterator()를 제공해야 합니다.   
// ----
class IAggregate {
protected:
    IAggregate() = default; // 인터페이스여서 상속해서만 사용하도록 protected
    ~IAggregate() = default; // 인터페이스여서 다형 소멸을 하지 않으므로 protected non-virtual
private:
    IAggregate(const IAggregate&) = delete;
    IAggregate(IAggregate&&) = delete;
    IAggregate& operator =(const IAggregate&) = delete;
    IAggregate& operator =(IAggregate&&) = delete;    
public:
    virtual std::unique_ptr<Iterator> CreateIterator() = 0; // 이터레이터를 생성합니다.
    virtual size_t GetCount() const = 0; // 요소의 갯수를 구합니다.
    virtual void Add(int val) = 0; // 요소를 추가합니다.
    virtual void Remove(int index) = 0; // 요소를 삭제합니다.
};
// ----
// #3. IAggregate를 구체화하며, CreateIterator()시 MyIterator를 생성합니다.
// ----
class MyAggregate : public IAggregate {
    std::vector<int> m_Container;
public:
    MyAggregate() = default;

    // 내부적으로 MyInterator를 사용하여, 선언과 정의를 분리했습니다.
    virtual std::unique_ptr<Iterator> CreateIterator() override; // 이터레이터를 생성합니다. 
    virtual size_t GetCount() const override {return m_Container.size();} // 요소의 갯수를 구합니다.
    virtual void Add(int val) override {m_Container.push_back(val);} // 요소를 추가합니다.
    virtual void Remove(int index) override { // 요소를 삭제합니다.
        assert(index < m_Container.size());

        m_Container.erase(m_Container.begin() + index);
    } 

    int GetAt(int index) const {
        assert(index < m_Container.size());

        return m_Container[index];
    }
};
// ----
// #4. Iterator를 구체화합니다. m_CurrentIndex로 현재 요소 위치를 관리합니다.
// ----
class MyIterator : public Iterator {
    MyAggregate& m_MyAggregate;
    int m_CurrentIndex; // 현재 요소의 위치입니다.
public:
    explicit MyIterator(MyAggregate& myAggregate) : m_MyAggregate{myAggregate}, m_CurrentIndex{0} {}

    virtual void First() override {m_CurrentIndex = 0;} // 집합 개체의 처음으로 이동합니다.
    virtual void Next() override {++m_CurrentIndex;} // 다음 위치로 이동합니다.
    virtual bool IsDone() const override {return m_CurrentIndex < m_MyAggregate.GetCount() ? false : true;} // 끝인지 검사합니다. 
    virtual int GetCurrent() const override { // 현 위치의 값을 리턴합니다.
        assert(m_CurrentIndex < m_MyAggregate.GetCount());
        
        return m_MyAggregate.GetAt(m_CurrentIndex);
    } 
};

std::unique_ptr<Iterator> MyAggregate::CreateIterator() {
    return std::unique_ptr<Iterator>{new MyIterator{*this}}; // #3
}

// ----
// 테스트 코드
// ----        
MyAggregate aggregate;
aggregate.Add(10);
aggregate.Add(20);
aggregate.Add(30);

std::unique_ptr<Iterator> itr{aggregate.CreateIterator()}; // #3

itr->First();
if (!itr->IsDone()) {EXPECT_TRUE(itr->GetCurrent() == 10);}

itr->Next();
if (!itr->IsDone()) {EXPECT_TRUE(itr->GetCurrent() == 20);}

itr->Next();
if (!itr->IsDone()) {EXPECT_TRUE(itr->GetCurrent() == 30);}
```

