---
layout: single
title: "#7. [개체지향 원칙] 캡슐화(Encapsulation)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)는 **개체 사용자가 개체의 내부 상태의 구현 원리를 알 필요 없게 하라** 라는 원칙입니다.

조금 풀어 쓰면,

1. 개체의 멤버 변수나 함수는 꽁꽁 숨긴 뒤(은닉), 꼭 필요한 것만 외부에 public 으로 노출(*[최소 public](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-complete-class/#%EC%B5%9C%EC%86%8C-public)*)하고,
2. 단위 기능을 응집하고, 결합도는 낮춘뒤,
3. ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** 구현하라.

라는 뜻입니다. 

**위반 사례**

각도를 Degree 형태로 입력받는 `Rotate()` 함수를 구현한다 가정해 봅시다. 아마도 0~360의 범위 점검 후 위반시 `assert`하여 올바른 사용을 강제할 수 있습니다.

```cpp
class Shape {
private:
    float m_Angle; 
public:  
    float GetAngle() const {return m_Angle;}

    // delta 만큼 더 회전시킵니다.
    // delta : delta를 angle과 더했을때, 0~360 사이의 값이 되도록 전달해야 합니다.
    void Rotate(float delta) {
        float finalAngle = m_Angle + delta;
        assert(0 <= finalAngle && finalAngle < 360);
        
        // 회전시킵니다.
        m_Angle = finalAngle;
    }
};
```

하지만, 사용하는 입장에서 `delta`값 전달하기가 상당히 귀찮습니다. 기존 `m_Angle` 과 더했을때 0~360범위에 있어야 하니까요. 그래서, `Rotate()`를 호출하기 전에 `delta` 값을 검사하고, 보정한 뒤, 유효한 값을 전달하는 코드를 작성해야 합니다.

```cpp
Shape shape;
float delta = 10;

// shape의 현재 각도
float angle = shape.GetAngle();

// delta만큼 회전한 후의 각도
float finalAngle = angle + delta;

// filnalAngle이 0~360이 되도록 delta를 조정합니다.
if (finalAngle < 0) {
    delta = 360 + finalAngle - angle; 
}
else if (360 <= finalAngle) {
    delta = finalAngle - 360 - angle;
}

// delta만큼 더 회전시킵니다.
shape.Rotate(delta);
```

좀 지저분하지만 참을만 할까요? 한번은 참을만한데, `Rotate()` 함수를 호출할 때마다 값을 보정하기 위해 저리도 긴 `if`문을 쓰는건 견디기 힘든 중복입니다.

임시 방편으로 중복을 피하기 위해 `CalcShapeRotateDelta()`를 만들어 코드 중복은 어찌어찌 회피할 수는 있습니다.

```cpp
class Util {
public:
    // angle : shape의 각도
    // delta : 회전값
    static float CalcShapeRotateDelta(float angle, float delta) {

        // delta만큼 회전한 후의 각도
        float finalAngle = angle + delta;

        // filnalAngle이 0~360이 되도록 delta를 조정합니다.
        if (finalAngle < 0) {
            delta = 360 + finalAngle - angle; 
        }
        else if (360 <= finalAngle) {
            delta = finalAngle - 360 - angle;
        }

        return delta;
    }
};
```

이제 `Util::CalcShapeRotateDelta()` 함수로 기나긴 `if` 문을 피할 순 있습니다.

```cpp
// delta값을 보정합니다.
delta = Util::CalcShapeRotateDelta(shape.GetAngle(), delta);

// delta만큼 더 회전시킵니다.
shape.Rotate(delta);
```

하지만, `Rotate()` 호출전에 `delta`값을 보정하기 위해 `CalcShapeRotateDelta()` 을 항상 호출해야 하는 것도, 어찌보면 코드 중복이라 볼 수 있고, 호출자가 `Shape`과 `Util`을 모두 파악해야 하기에 복잡해 집니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1d1da5d1-4aad-4522-9245-1cd6006bafd6)

여전히 단위 기능의 응집도도 낮고, 결합도는 높으며, 호출자는 `Rotate()`함수가 `delta`를 처리하는 방식을 파악한 뒤, `CalcShapeRotateDelta()`를 꼭 호출해야 하기 때문에 사용하기 어렵습니다. 호출을 빼먹어서 잘못 사용하기 쉽고요. 캡슐화 위반입니다.

**준수 방법**

상기 예제는 Degree 형식을 처리하는 과정이 함수 외부와 내부에 흩어져 있기 때문에 발생한 문제입니다. `Rotate()`함수 외부에선 `delta`값을 보정해야 하고, 내부에서는 `m_Angle`과 `delta`를 더한 뒤 회전시키니까요. 서로 주거니 받거니 하는 값이 잘 맞아 떨어져야만 합니다.(책임이 분산되어 있으니, [단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/)도 위반했네요.)

이렇게 흩어진 처리방식을 해결하기 위해, 각도값을 처리하는 `Degree` 타입을 만들고, 데이터 보정 처리를 응집해 줍니다. 그러면 각도값 처리방법이 캡슐화 되고, 응집력은 높아지고, 결합도는 낮아집니다. 또한 함수 호출이 바르게 사용하기 쉽게 변경됩니다.

하기는 `Degree`의 구현 코드 입니다. 어떠한 수를 더하던, 빼던 0~360사이의 값을 유지합니다.

```cpp
// 0~360 의 값으로 강제하여 각도값을 처리하는 개체. 예를들어 370도는 10도이고, -10도는 350도임
class Degree {
private:
    float m_Value;
public:
    explicit Degree(float val = 0) :
        m_Value(Constrain(val)) {}
public:
    const Degree& operator =(float val) {
        m_Value = Constrain(val); 
        return *this; 
    }
    void operator +=(const Degree& other) {m_Value = Constrain(m_Value + other.GetValue());}
    void operator +=(float val) {m_Value = Constrain(m_Value + val);}
    void operator -=(const Degree& other) {m_Value = Constrain(m_Value - other.GetValue());}
    void operator -=(float val) {m_Value = Constrain(m_Value - val);}
    const Degree operator -() const {return Degree(-m_Value);}

    float GetValue() const {return m_Value;}
private:
    // 0~360 값으로 강제함
    static float Constrain(float val) {
        float min = 0.F;
        float max = 360.F;

        // 범위 바깥이라면 0~360 사이로 보정
        if (val < min || max <= val) {
            // 몫
            int quatient = static_cast<int>(val) / static_cast<int>(max);

            // 360의 배수값은 뺀 나머지값으로 조정
            val = val - max * quatient;

            // 음수라면 양수화 함
            if (val < min) {
                val = max + val; // val은 음수이며 -360 보다 작은값임
            }
        }

        assert(!(val < min));
        assert(!(max < val));

        return val;
    }
};

// ==
inline bool operator ==(const Degree& left, const Degree& right) {
    if (left.GetValue() != right.GetValue()) return false;
    return true;
}
inline bool operator ==(const Degree& left, float right) {
    return left == Degree(right);
}
inline bool operator ==(const float left,  const Degree& right) {
    return right == left;
}
// !=
inline bool operator !=(const Degree& left, const Degree& right) {
    return !(left == right);
}
inline bool operator !=(const Degree& left, float right) {
    return left != Degree(right);
}
inline bool operator !=(const float left,  const Degree& right) {
    return right != left;
}
// <, <=
inline bool operator <(const Degree& left, const Degree& right) {
    return left.GetValue() < right.GetValue();
}
inline bool operator <=(const Degree& left, const Degree& right) {
    return left.GetValue() <= right.GetValue();
}
// +
inline Degree operator +(const Degree& left, const Degree& right) {
    Degree result(left);
    result += right;
    return result;
}
inline Degree operator +(const Degree& left, float right) {
    Degree result(left);
    result += right;
    return result;
}
// -
inline Degree operator -(const Degree& left, const Degree& right) {
    Degree result(left);
    result -= right;
    return result;
}
inline Degree operator -(const Degree& left, float right) {
    Degree result(left);
    result -= right;
    return result;
}
```

`Degree` 클래스 내에서 0~360 로 값을 보정하므로(*여러 [연산자를 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)하여 믿고 쓸 수 있습니다*), `Shape` 클래스는 `Degree`로 좀더 간단하게 구현할 수 있습니다. `Rotate()`함수의 불필요한 주석과 `assert`가 사라졌습니다.

```cpp
class Shape {
private:
    Degree m_Angle; // 디폴트로 0도
public:  
    const Degree& GetAngle() const {return m_Angle;}

    // delta 만큼 더 회전시킵니다.
    void Rotate(const Degree& delta) {
        // 회전시킵니다. Degree 클래스이니 알아서 보정합니다. 
        // assert 검사할 필요도 없습니다.
        m_Angle += delta;
    }
};
```

이제 호출자는 `Rotate()`함수 내부구조를 파악할 필요도 없고, `delta`값을 보정할 필요도 없으므로, `Rotate()` 사용이 한결 쉬워졌습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7964b099-8d31-41a7-b183-69c3cd942c72)

다음과 같이 테스트를 작성할 수 있습니다.

```cpp
TEST(TestPrinciple, Encapsulation) {
    Degree _360(360.F);
    EXPECT_FLOAT_EQ(_360.GetValue(), 0.F);
        
    Degree _370(370.F);
    EXPECT_FLOAT_EQ(_370.GetValue(), 10.F);

    Degree _Minus10(-10);  
    EXPECT_FLOAT_EQ(_Minus10.GetValue(), 350.F); 

    // == 만 테스트
    EXPECT_TRUE(Degree(370.F) == Degree(10.F));
    EXPECT_TRUE(Degree(370.F) == 10.F);
    EXPECT_TRUE(10.F == Degree(370.F));  

    // Shape 테스트
    Shape shape;
    
    shape.Rotate(Degree(350.F));
    EXPECT_FLOAT_EQ(shape.GetAngle().GetValue(), 350.F);

    shape.Rotate(Degree(20.F));
    EXPECT_FLOAT_EQ(shape.GetAngle().GetValue(), 10.F); // 350 + 20 = 370도, 즉 10도

    shape.Rotate(Degree(-20.F));
    EXPECT_FLOAT_EQ(shape.GetAngle().GetValue(), 350.F); // 10 - 20 = -10도, 즉 350도
}
```

# 캡슐화에 따른 함수 설계

함수는 일반적으로 코드 중복을 재활용하기 위해 만들어 지는데요, 

함수 설계에서도 

* 단위 기능을 응집하고, 결합도는 낮춘뒤,
* ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** 구현하라는

[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)의 철학이 필요합니다.

[단일 책임 원칙](https://tango1202.github.io/principle/principle-single-responsibility/) 이라며 무조건 함수를 작은 단위로 쪼개지 말고, 코드 중복이 발생하는 시점에 다음 내용을 고민하여 함수를 쪼갤지 고민하는게 좋습니다.

|너무 작은 단위로 쪼갠 경우|너무 큰 단위인 경우|
|--|--|
|* 함수 수정시 호출되는 곳이 많아 사이드 이팩트의 우려가 있습니다.<br/>* 함수를 분석하기 위해 여러 호출 단계를 거치기에 절차적 코드 분석에 비용이 많이 듭니다.<br/>* 코드 자체는 가독성이 좋고, 재활용성이 향상됩니다.|* 함수 수정에 따른 사이드 이팩트가 그리 많지 않습니다.<br/>* 관련 코드가 너무 길어 힘들지만 절차적 코드 분석이 비교적 용이합니다.<br/>* 코드의 가독성이 떨어져 유지보수성이나 재활용성은 낮습니다.|


