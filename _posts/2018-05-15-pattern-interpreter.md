---
layout: single
title: "#15. [디자인 패턴-행위 패턴] Interpreter"
categories: "pattern"
tag: ["디자인 패턴", "행위 패턴"]
author_profile: false
sidebar: 
    nav: "docs"
---

[Interpreter](https://tango1202.github.io/pattern/pattern-interpreter/)는 비교적 단순하게 설계된 임의의 언어를 해석하는 간단한 방법을 제공합니다.

# 설명

어떠한 기능을 구현하는데 있어서 좀더 자유도를 높이기 위해 임의의 언어를 설계하여 제공할 수 있습니다.

예를들어 도형의 너비와 높이를 곱한 값이 필요하다고 가정해 봅시다. 다음과 같이 계산할 수 있는데요,

```cpp
Rectangle rectangle{l, t, w, h};

int val = Calc(rectangle.GetWidth() + rectangle.GetHeight());
```

다음처럼 사용자가 입력한 문자열로부터 계산하는 것을 지원한다면, 좀더 자유도가 높아집니다.

```cpp
Rectangle rectangle{l, t, w, h};
std::string userInputString = "w * h";

int val = Calc(rectangle.GetWidth(), rectangle.GetHeight(), userInputString);
```

"w * h"를 계산하려면 `w`, `*`, `h`를 토큰으로 분리하고, 변수인 `w`와 `h` 대신 `rectangle.GetWidth()`와 `rectangle.GetHeight()`를 사용해야 합니다.

이러한 경우 [Interpreter 패턴](https://tango1202.github.io/pattern/pattern-interpreter/)을 이용하여 단순하게 설계된 임의의 언어를 해석하는 간단한 인터프리터를 만들 수 있습니다.

다음 그림에서 `AbstractExpression`은 `interpret()`함수를 통해 실행 결과를 리턴합니다. 이때 각 `AbstractExpression`은 트리처럼 배치되어 최상위 `AbstractExpression`만 실행하면, 하위의 `AbstractExpression`이 모두 실행됩니다.

![interpreter](https://github.com/tango1202/tango1202.github.io/assets/133472501/6f29ef01-e880-410d-b3c2-01d1757175a7)

예를 들어 `w * h * 4 / 2`는 각 토큰으로 분리되고, 각각 `TerminalExpression`과 `NonterminalExpression`으로 다음의 트리처럼 구성됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/fbfeb256-1367-47b6-91bb-9734e539f805)

|항목|내용|
|--|--|
|`Context`|인터프리터에 대한 포괄적인 정보를 제공합니다.|
|`AbstractExpression`|각 토큰이 의미하는 바를 실행합니다.|
|`TerminalExpression`|현 표현식에서 연산이 종결됩니다. 즉, 변수, 상수등 단독으로 연산할 수 있거나, 왼쪽에 있는 `AbstractExpression`을 이용합니다.|
|`NonterminalExpression`|현 표현식에서 연산이 종결되지 않고 다음 표현식을 사용합니다. 예를 들어 `*`, `/`와 같이 오른편의 `AbstractExpression`을 이용합니다.|
|`Client`|최상위 `AbstractExpression`을 실행합니다.|

# 예제 

다음은 내장 변수인 `w`와 `h`를 제공하고 `*` 과 `/`를 지원하는 인터프리터입니다. 각 토큰은 공백 문자로 구분합니다. 런타임에 선택한 사각형의 너비와 높이로부터 사용자가 입력한 임의의 수식을 계산할 수 있습니다.(*언어의 문법을 어떻게 설계하느냐에 따라 `Interpreter`의 구현은 달라질 수 있습니다.*)

1. #1 : `Context`에서는 `w`, `h`에 사용되는 포괄적인 정보를 제공합니다.
2. #2 : `Expression`의 기본 인터페이스를 제공합니다. `Interpret()`시 연산 결과값을 리턴해야 합니다.
3. #3 : `TerminalExpression`입니다. `Context`로부터 `w`, `h` 값을 읽어와 리턴하거나 상수값을 리턴합니다.
4. #4 : `NonterminalExpression`입니다. `*`, `/` 결과값을 리턴합니다.
5. #5 : 전달한 문자열을 `Parse()`하여 `Expression`을 트리 형태로 구성하고, 최상위 `Expression`을 구한뒤, 이의 `Interpret()`를 호출하여 결과값을 구합니다.
6. #6 : 런타임에 임의의 수식을 입력받아 `rectangle` 개체의 너비와 높이로부터 값을 구할 수 있습니다.

```cpp
// ----
// #1. `w`, `h`에 사용되는 포괄적인 정보를 제공합니다.
// ----
class Context {
    int m_Width;
    int m_Height;
    std::string m_Exp;
    public:
    Context(int w, int h, const char* exp) : m_Width{w}, m_Height{h}, m_Exp{exp} {}
    ~Context() = default;
private:
    Context(const Context&) = delete; 
    Context(Context&&) = delete; 
    Context& operator =(const Context&) = delete; 
    Context& operator =(Context&&) = delete;
public:
    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}
    const std::string& GetExp() const {return m_Exp;}
};
// ----
// #2. 기본 인터페이스입니다. Interpret()시 연산 결과값을 리턴해야 합니다.
// ----
class Expression {
protected:
    Expression() = default; // 다형 소멸을 제공하는 추상 클래스. 상속해서만 사용하도록 protected
public:
    virtual ~Expression() = default; // 다형 소멸 하도록 public virtual
private:
    Expression(const Expression&) = delete;
    Expression(Expression&&) = delete;
    Expression& operator =(const Expression&) = delete;
    Expression& operator =(Expression&&) = delete; 
public:
    virtual int Interpret(const Context& context) const = 0; // #2      
};
// ----
// #3. TerminalExpression입니다. Context로부터 w, h 값을 읽어와 리턴하거나, 상수값을 리턴합니다.
// ----
class WidthExpression : public Expression {
public:
    WidthExpression() = default;

    virtual int Interpret(const Context& context) const override{return context.GetWidth();}    
};
class HeightExpression : public Expression {
public:
    HeightExpression() = default;

    virtual int Interpret(const Context& context) const override {return context.GetHeight();} 
};
class ConstantExpression : public Expression {
    int m_Val;
public:
    explicit ConstantExpression(int val) : m_Val{val} {}

    virtual int Interpret(const Context& context) const override {return m_Val;} 
};
// ----
// #4. NonterminalExpression입니다. *, / 결과값을 리턴합니다.
// ----    
class MulExpression : public Expression {
    std::unique_ptr<Expression> m_Left;
    std::unique_ptr<Expression> m_Right;
public:
    MulExpression(std::unique_ptr<Expression> left, std::unique_ptr<Expression> right) : m_Left{std::move(left)}, m_Right(std::move(right)) {}
    
    virtual int Interpret(const Context& context) const override {return m_Left->Interpret(context) * m_Right->Interpret(context);} 
};
class DivExpression : public Expression {
    std::unique_ptr<Expression> m_Left;
    std::unique_ptr<Expression> m_Right;
public:
    DivExpression(std::unique_ptr<Expression> left, std::unique_ptr<Expression> right) : m_Left{std::move(left)}, m_Right(std::move(right)) {}
    
    virtual int Interpret(const Context& context) const override {return m_Left->Interpret(context) / m_Right->Interpret(context);} 
};
// ----
// #5. 전달한 문자열을 Parse()하여 Expression을 트리 형태로 구성하고, 최상위 Expression을 구한뒤, 이의 Interpret()를 호출하여 결과값을 구합니다.
// ----  
class Interpreter {
    const Context& m_Context;
public:
    explicit Interpreter(const Context& context) : m_Context{context} {}
    ~Interpreter() = default;
private:
    Interpreter(const Interpreter&) = delete; 
    Interpreter(Interpreter&&) = delete; 
    Interpreter& operator =(const Interpreter&) = delete; 
    Interpreter& operator =(Interpreter&&) = delete;
public:
    // #5. context를 파싱하고 실행 결과값을 리턴합니다.
    int Run() const {
        return Parse()->Interpret(m_Context); 
    }

private:
    // ----
    // w, h, 정수, *, / 가 공백 문자로 구분된 식을 계산합니다.
    // 예를 들어 "w * h * 4 / 2" 와 같이 식을 작성할 수 있습니다.
    // #5. context를 파싱하여 가상의 트리를 만들어 최상위 Expression을 리턴합니다.
    // ----
    std::unique_ptr<Expression> Parse() const {
        
        std::vector<std::string> tokens{CreateToken(m_Context.GetExp(), " ")}; // 공백 문자로 token화 합니다.
        std::stack<std::unique_ptr<Expression>> stack; // 수식을 차례대로 쌓아 가장 마지막 Expression이 트리의 root가 되게 합니다.

        for (int i{0}; i < tokens.size(); ++i) {
            if (IsVariable(tokens[i])) { // w, h
                stack.push(CreateTerminalExpression(tokens[i]));
            }
            else if (IsConstant(tokens[i])) { // 상수
                stack.push(CreateTerminalExpression(tokens[i]));
            }
            else if (IsMul(tokens[i])) { // *
                std::unique_ptr<Expression> left{stack.top().release()};
                stack.pop();
                stack.push(std::unique_ptr<Expression>{new MulExpression{std::move(left), CreateTerminalExpression(tokens[++i])}}); // 다음 토큰을 사용합니다.
            }
            else if (IsDiv(tokens[i])) { // /
                std::unique_ptr<Expression> left{stack.top().release()};
                stack.pop();    
                stack.push(std::unique_ptr<Expression>{new DivExpression{std::move(left), CreateTerminalExpression(tokens[++i])}}); // 다음 토큰을 사용합니다.
            }
            else {
                throw std::invalid_argument{"Invalid token. Need w, h, Integer, *, /."};     
            }
        }
        return std::unique_ptr<Expression>{stack.top().release()}; // 가장 마지막에 추가된 Expression을 리턴합니다.      
    } 
    static bool IsVariable(const std::string& str) {
        return str == "w" || str == "h" ? true : false;
    }
    static bool IsConstant(const std::string& str) {
        for (auto ch : str) {
            if (!std::isdigit(ch)) { // 0123456789로 구성되었는지 확인합니다.
                return false;
            }
        }
        return true;
    }
    static bool IsMul(const std::string& str) {
        return str == "*" ? true : false;
    }
    static bool IsDiv(const std::string & str) {
        return str == "/" ? true : false;
    }

    // w, h, 정수를 Expression으로 만듭니다. token이 유효하지 않으면 예외를 발생합니다.
    static std::unique_ptr<Expression> CreateTerminalExpression(const std::string& token) {
        assert(IsVariable(token) || IsConstant(token));

        if (IsVariable(token)) { // w, h
            if (token == "w") {
                return std::unique_ptr<Expression>{new WidthExpression{}};
            }
            else if (token == "h") {
                return std::unique_ptr<Expression>{new HeightExpression{}};
            }
            else {
                throw std::invalid_argument{"Invalid token. Need w, h."};
            }
        }
        else if (IsConstant(token)) {
            return std::unique_ptr<Expression>{new ConstantExpression{std::atoi(token.c_str())}};
        }
        else {
            throw std::invalid_argument{"Invalid token. Need w, h, Integer."};    
        }
    }

    // #5. str을 delimeter로 구분하여 리턴합니다.
    static std::vector<std::string> CreateToken(const std::string& str, const char* delimeter) {
        std::vector<std::string> result;

        size_t pos{0};
        size_t findPos{0};
        while((findPos = str.find(delimeter, pos)) != std::string::npos) {
            
            result.push_back(str.substr(pos, findPos - pos));

            pos = ++findPos; // 다음 위치에서부터 찾습니다.
        }
        // 마지막이 delimeter가 아닌 경우 문자열 끝까지 token으로 만듭니다.
        if (pos < str.length()) {
            result.push_back(str.substr(pos, str.length() - pos));
        }
        return result;
    }
};
class Rectangle {
    int m_Left;
    int m_Top;
    int m_Width;
    int m_Height;
public:
    Rectangle(int l, int t, int w, int h) : m_Left{l}, m_Top{t}, m_Width{w}, m_Height{h} {}

    int GetWidth() const {return m_Width;}
    int GetHeight() const {return m_Height;}
}; 

// ----
// 테스트 코드
// ----
Rectangle rectangle{0, 0, 10, 20};

// #6. 런타임에 임의의 수식을 입력받아 rectangle 개체의 너비와 높이로부터 값을 구할 수 있습니다.
Context context{
    rectangle.GetWidth(), 
    rectangle.GetHeight(), 
    "w * h * 4 / 2" // 공백 문자로 구분하며, w, h, 정수, * / 을 지원합니다.
};
Interpreter interpreter{context};
EXPECT_TRUE(interpreter.Run() == rectangle.GetWidth() * rectangle.GetHeight() * 4 / 2);
```