---
layout: single
title: "#1. [모던 C++] 개요"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


In initialization, delimit the initializers. This kind of initialization is called list-initialization. (since C++11)

In a requires-expression, delimit the requirements. (since C++20)

In a compound requirement, delimit the expression. (since C++20)

In an export declaration, delimit the declarations. (since C++20)

In a lambda expression, delimit the captures. (since C++11)

In an attribute specifier, delimit the attributes. (since C++11)

In a structured binding declaration, delimit the identifier list. (since C++17)

In a typeid, sizeof, sizeof..., alignof, or noexcept (since C++11) expression, delimit the operand.

a lambda expression (since C++11)

a user-defined deduction guide (since C++17)

a requires-expression (since C++20)

In an if (including constexpr if) (since C++17), switch, while, do-while, or for(including range-based for) (since C++11) statement, delimit the controlling clause.

Part of a defined, __has_include (since C++17), __has_cpp_attribute (since C++20) preprocessing operator.

In a static_assert declaration, delimit the operands. (since C++11)

In a decltype specifier, noexcept specifier, alignas specifier, conditional explicit specifier (since C++20), delimit the operand. (since C++11)

In an attribute, delimit the attribute arguments. (since C++11)

Part of decltype(auto) specifier. (since C++14)

Delimit a fold expression. (since C++17)
Part of __VA_OPT__ replacement in a variadic macro definition. (since C++20)

a module declaration, import declaration, global module fragment introducer, or private module fragment introducer (since C++20)

a requirement (since C++20)

In a range-based for statement, separate the range-declaration and the range-initializer. (since C++11)

Introduce an enum base, which specifies the underlying type of the enum. (since C++11)

In an attribute specifier, separate the attribute-using-prefix and the attribute list. (since C++17)

In a module declaration or import declaration of module partition, introduce the module partition name. (since C++20)

Part of a private module fragment introducer (module :private;). (since C++20)

In the parameter list of a function declaratoror lambda expression (since C++11)or user-defined deduction guide (since C++17), signify a variadic function.

In a macro definition, signify a variadic macro. (since C++11)

Indicate pack declaration and expansion. (since C++11)

In an attribute, indicate attribute scope. (since C++11)

Part of nested namespace definition. (since C++17)

In aggregate initialization, introduce a designator. (since C++20)

Part of module name or module partition name. (since C++20)

In a function declarator or lambda expression, introduce the trailing return type. (since C++11)

In a user-defined deduction guide, introduce the result type. (since C++17)

In a compound requirement, introduce the return type requirement. (since C++20)

Part of reversed variant of a consteval if statement. (since C++23)

Part of *this in a lambda capture list, to capture the current object by copy. (since C++17)

In a lambda capture, indicate by-reference capture. (since C++11)

Ref-qualifier in member function declaration. (since C++11)

Simple assignment operator; part of operator= in operator overloading, which might be a special member function (copy assignment operatoror move assignment operator (since C++11)).

In initialization, indicate copy-initializationor copy-list-initialization (since C++11).

Capture default in lambda capture, to indicate by-copy capture. (since C++11)

Part of defaulted definition (=default;) or deleted definition (=delete;) in function definition. (since C++11)

In a type alias declaration, separate the alias and the aliased type. (since C++11)

In a type alias declaration, separate the alias and the aliased type. (since C++11)

In a concept definition, separate the concept name and the constraint expression. (since C++20)

a lambda expression (since C++20)

a __has_include preprocessing expression (since C++17)

an import declaration (since C++20)

<=> (since C++20)
Three-way comparison (spaceship) operator; part of operator<=> in operator overloading.

Rvalue-reference operator in a declarator or in a type-id. (since C++11)

Ref-qualifier in member function declaration. (since C++11)

Can be reparsed as two > in a static_cast, const_cast, reinterpret_cast, or dynamic_cast, a template argument list, or a template parameter list. (since C++11)

the argument list in a multi-argument subscript expression (since C++23)

a lambda capture list (since C++11)

an attribute list (since C++11)

the declarator list in a using-declaration (since C++17)

the identifier list in a structured binding declaration (since C++17)

In a static_assert declaration, separate the arguments. (since C++11)

The only place they can be used as non-keywords is in an attribute-token. (e.g. [[private]] is a valid attribute)
(since C++11)


the identifiers with special meaning (final, import, module (since C++20) and override) are used explicitly in a certain context rather than being regular identifiers;
Unless otherwise specified, any ambiguity as to whether a given identifier has a special meaning is resolved to interpret the token as a regular identifier.
(since C++11)

Identifiers that appear as appearing as a token or preprocessing token (i.e., not in user-defined-string-literal like operator ""id) (since C++11) of one of the following forms are reserved:

In literal operators:
literal suffix identifiers that do not start with an underscore are reserved for future standardization;
literal suffix identifiers that contain double underscore are reserved for use by implementations.
(since C++11)

If the entity named by the (unqualified) identifier is a local entity, and would result in an intervening lambda expression capturing it by copy if it were named outside of an unevaluated operand in the declarative region in which the identifier appears, then the type of the expression is the type of a class member access expression naming the non-static data member that would be declared for such a capture in the closure object of the innermost such intervening lambda expression.
void f()
{
    float x, &r = x;
 
    [=]
    {
        decltype(x) y1;        // y1 has type float
        decltype((x)) y2 = y1; // y2 has type float const& because this lambda
                               // is not mutable and x is an lvalue
        decltype(r) r1 = y1;   // r1 has type float&
        decltype((r)) r2 = y2; // r2 has type float const&
    };
}
(since C++11)

If the entity named is a template parameter object for a template parameter of type T, the type of the expression is const T.
(since C++20)


a user-defined literal operator name, such as operator "" _km;
(since C++11)


the character ~ followed by a decltype specifier, such as ~decltype(str).
(since C++11)


an enumeration name;
a decltype specifier denoting a class or enumeration type.
(since C++11)


a user-defined literal operator name (operator ""_km);
(since C++11)

std::nullptr_t (since C++11)



long long - target type will have width of at least 64 bits.
(since C++11)

std::size_t is the unsigned integer type of the result of the sizeof operator as well as the sizeof... operator and the alignof operator (since C++11).

Extended integer types (since C++11)
The extended integer types are implementation-defined. Note that fixed width integer types are typically aliases of the standard integer types.

For every value of type unsigned char in range [0, 255], converting the value to char and then back to unsigned char produces the original value. (since C++11) 



char16_t - type for UTF-16 character representation, required to be large enough to represent any UTF-16 code unit (16 bits). It has the same size, signedness, and alignment as std::uint_least16_t, but is a distinct type.

char32_t - type for UTF-32 character representation, required to be large enough to represent any UTF-32 code unit (32 bits). It has the same size, signedness, and alignment as std::uint_least32_t, but is a distinct type.
(since C++11)



char8_t - type for UTF-8 character representation, required to be large enough to represent any UTF-8 code unit (8 bits). It has the same size, signedness, and alignment as unsigned char (and therefore, the same size and alignment as char and signed char), but is a distinct type.
(since C++20)

Extended floating-point types (since C++23)
The extended floating-point types are implementation-defined. They may include fixed width floating-point types.

**Temporary object lifetime**

Temporary objects are created when a prvalue is materialized so that it can be used as a glvalue, which occurs (since C++17) in the following situations:

**위치지정 생성**
```cpp
struct C
{
    int i;
    void f();
    const C& operator=(const C&);
};
 
const C& C::operator=(const C& other)
{
    if (this != &other)
    {
        this->~C();          // lifetime of *this ends
        new (this) C(other); // new object of type C created
        f();                 // well-defined
    }
    return *this;
}
 
C c1;
C c2;
c1 = c2; // well-defined
c1.f();  // well-defined; c1 refers to a new object of type C
```