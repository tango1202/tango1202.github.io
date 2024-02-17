---
layout: single
title: "#2. [Typescript] 기본 문법"
categories: "typescript"
tag: ["typescript"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 정적 타입

타입스크립트는 변수, 함수 인자, 함수 리턴, 클래스등을 선언할때 타입을 지정하고, 해당 타입만 할당할 수 있는 정적 타입 언어입니다.

타입으로 코딩 계약을 할 수 있기 때문에 실수할 소지가 줄어듭니다.

```typescript
const user = 'Kim'; // javascript
const user: string = 'Kim'; // typescript
```

지원하는 타입은 다음과 같습니다.

|항목|내용|
|--|--|
|`boolean`|참/거짓|
|`null`|값이 할당되지 않았다는 의미로 개발자가 할당한 값|
|`undefined`|값이 할당되지 않아 자바스크리브에서 초기화한 값|
|`number`|숫자|
|`string`|문자열|
|`symbol`|고유하고 수정불가능한 타입|
|`object`|개체|
|`array`|배열|
|`tuple`|데이터 집합인 고정 크기 배열|
|`enum`|열거형|
|`any`|어떤 타입도 할당 가능|
|`void`|반환값이 없음|
|`never`|결코 발생하지 않는값. 예를 들어 무조건 예외를 발생시키는 함수의 리턴값등|

# 변수 선언

변수명 뒤에 `: 변수 타입`을 작성합니다.

```typescript
const val: string = 'Kim'; // 일반 변수

const arr1: number[] = [1, 2, 3]; // 배열
const arr2: Array<number> = [1, 2, 3];

const tuple: [string, number] = ['Kim', 10]; // 튜플

enum Color {Red, Green, Blue}; // 열거형
const color: Color = Color.Red;

expect(val).toBe('Kim');
expect(arr1[0]).toBe(1);
expect(arr1[1]).toBe(2);
expect(arr1[2]).toBe(3);
expect(arr2[0]).toBe(1);
expect(arr2[1]).toBe(2);
expect(arr2[2]).toBe(3);

expect(tuple[0]).toBe('Kim');
expect(tuple[1]).toBe(10);
expect(color).toBe(Color.Red);
```

# 함수 선언

인자명 뒤에 `: 인자 타입`을 작성하고, 괄호 뒤에 `: 리턴 타입`을 작성합니다.

```typescript
// 함수 선언
function add1(a: number, b: number): number {
    return a + b;
}
// 함수 표현식
const add2 = function(a: number, b: number): number {
    return a + b;
}
// 화살표 함수
const add3 = (a: number, b: number): number => a + b;

expect(add1(1, 2)).toBe(3);
expect(add2(1, 2)).toBe(3);
expect(add3(1, 2)).toBe(3);
```

# 타입 추론

초기값으로부터 타입을 추론할 수 있다면, 굳이 타입을 명시할 필요는 없습니다. 타입스크립트가 추론하는게 더 정확하므로, 어지간하면 타입 명시없이 사용하는게 좋습니다.(*C++로 이와 비슷하게 `auto`를 선호합니다. [auto의 장점](https://tango1202.github.io/cpp/modern-cpp-auto-decltype/#auto%EC%9D%98-%EC%9E%A5%EC%A0%9)을 참고하세요.*)

```typescript
const val = 'Kim'; // val은 string 타입입니다.

expect(typeof val).toBe('string');
```

# 클래스

다음은 자바스크립트의 클래스 선언입니다. `constructor()`를 정의하여 `name`등 클래스의 속성들을 설정하는데요,

```javascript
// javascript
class User {
    constructor(name) {
        this.name();
    }

    getName() {
        return this.name;
    }
}

const user = new User('Kim');
user.getName();
```

타입스크립트는 클래스의 속성을 사전에 선언해야 합니다.

```typescript
// typescript
class User {
    name: string; // 속성을 사전에 선언합니다.
    constructor(name: string) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

const user = new User('Kim');
expect(user.getName()).toBe('Kim');
```

타입스크립트에서는 특별히 `public`, `protected`, `private` 접근 제한자와  `readOnly`, `static`을 제공하며, `extends`를 이용하여 상속할 수 있습니다.

```typescript
class User {
    private name: string; // 다른 개체에서 사용 불가
    protected address: string; // 상속한 개체에서 사용
    public phone: string; // 외부에서 사용
    readonly gender: string; // 읽기 전용
    static staticData: string; // 정적 변수. User.staticData로 접근합니다.

    constructor(name: string, gender: string) {
        this.name = name;
        this.gender = gender; // 읽기 전용은 생성자에서만 설정할 수 있습니다.
    }

    getName() {
        return this.name;
    }
    // 정적 함수입니다. User.staticMethod()로 접근합니다.
    static staticMethod(): number {
        return 10;
    }
}
// User를 상속합니다.
class Derived extends User {
    constructor(name: string, gender: string) {
        super(name, gender);
    }

    // getDerivedName() {
    // return this.name; // (X) 컴파일 오류. private는 접근할 수 없습니다.
    // }
    getAddress() {
        return this.address; // protected에 접근합니다.
    }
    // setGender(gender:string) {
    //  this.gender = gender; // (X) 컴파일 오류. readonly는 수정할 수 없습니다.
    // }
}

const derived = new Derived('Kim', 'man');
derived.phone = '123-4567'; // public에 접근할 수 있습니다.

// expect(derived.name).toBe('Kim'); // (X) 컴파일 오류. private는 접근할 수 없습니다.
expect(derived.getName()).toBe('Kim');
// expect(derived.address).toBe(''); // (X) 컴파일 오류. protected는 접근할 수 없습니다.
expect(derived.phone).toBe('123-4567'); // public은 접근할 수 있습니다.

// staticMethod는 클래스명으로 접근합니다.
expect(User.staticMethod()).toBe(10);
expect(Derived.staticMethod()).toBe(10);
// expect(derived.staticMethod()).toBe(10); // (X) 컴파일 오류. 클래스명으로 접근해야 합니다.
```

# 타입 캐스팅

`as`나 `<>`로 형변환할 수 있습니다.

```typescript
class Base {
    name:string;
    constructor(name: string) { 
        this.name = name;
    }
}
// 상속했습니다.
class Derived extends Base { 
    constructor(name: string) {
        super(name);
    }
}
const derived = new Derived('Kim');
const base1:Base = derived as Base; // 형변환
const base2:Base = <Base>derived; // 형변환

expect(derived instanceof Derived).toBe(true);
expect(base1 instanceof Base).toBe(true);
expect(base2 instanceof Base).toBe(true);

// Base로 형변환했지만, 여전히 Derived입니다.
expect(base1 instanceof Derived).toBe(true);
expect(base2 instanceof Derived).toBe(true);
```

# 추상 클래스

`abstract`를 이용하여 추상 클래스와 추상 메서드를 작성할 수 있습니다. `new`로 생성할 수 없으며 꼭 상속해서 추상 메서드를 구현해서 사용해야 합니다.

```typescript
abstract class Shape {
    // 추상 클래스입니다.
    width: number;
    height: number;

    constructor(w: number, h: number) {
        this.width = w;
        this.height = h;
    }

    getWidth(): number {
        return this.width;
    }
    getHeight(): number {
        return this.height;
    }

    abstract Draw(): number[]; // 추상 메서드는 자식 개체에서 구현해야 합니다.
}
class Rectangle extends Shape {
    constructor(w: number, h: number) {
        super(w, h);
    }
    // 추상 메서드를 구현합니다.
    Draw(): number[] {
        return [this.width, this.height];
    }
}

// const shape = new Shape(10, 20); // (X) 컴파일 오류. 추상 클래스는 생성할 수 없습니다.
const rectangle = new Rectangle(10, 20);
expect(rectangle.Draw()[0]).toBe(10);
expect(rectangle.Draw()[1]).toBe(20);
```

# 인터페이스

개체의 속성 규약을 정할 수 있습니다. 

```typescript
interface IUser {
    id: number;
    name: string;
}

// 인터페이스에서 선언한 속성들이 구현되어야 합니다.
const user: IUser = {
    id: 1,
    name: 'Kim'
};

expect(user.id).toBe(1);
expect(user.name).toBe('Kim');
```

인터페이스로 선언되지 않았더라도, 속성이나 메서드가 동일하면 대입할 수 있습니다.

```typescript
interface IUser {
    id: number;
    name: string;
}

// id와 name 속성이 있는 개체입니다.
const user1 = {
    id: 1,
    name: 'Kim',
};

// IUser 타입은 아니지만 속성이 같으므로 대입됩니다.
const user2: IUser = user1;

expect(user1 === user2).toBe(true);
```

함수의 인자와 리턴타입 규약을 정할 수 있습니다.

```typescript
interface IAdd {
    (a: number, b: number): number; // 숫자 2개를 전달받아 숫자를 리턴하는 함수
}

const addFunc: IAdd = function (a: number, b: number): number {
    return a + b;
};

expect(addFunc(1, 2)).toBe(3);
```

클래스의 규약을 정할 수 있습니다.

```typescript
interface IUser {
    id: number;
    name: string;

    getName(): string; // 메서드는 추상으로 선언됩니다. 
}

class User implements IUser {
    id: number;
    name: string;

    constructor(id: number, name: string) {
        this.id = id;
        this.name = name;
    }

    getName() {return this.name;}
}

const user = new User(0, 'Kim'); 

expect(user.getName()).toBe('Kim');
```

인터페이스는 상속해서 확장할 수 있습니다.

```typescript
interface IPerson {
    name: string;
}
// 인터페이스를 상속합니다.
interface IUser extends IPerson {
    id: number;
}

const user: IUser = {
    id: 1,
    name: 'Kim',
};

expect(user.id).toBe(1);
expect(user.name).toBe('Kim');
```

다중 상속도 지원합니다.

```typescript
interface IPerson {
    name: string;
}
interface ISalary {
    salary: number;
}
// 다중 상속해서 만든 인터페이스입니다.
interface IEmployee extends IPerson, ISalary {}

const employee: IEmployee = {
    name: 'Kim',
    salary: 100,
};

expect(employee.name).toBe('Kim');
expect(employee.salary).toBe(100);
```

# 타입 별칭

사용자 정의 타입을 선언할 수 있습니다.

```typescript
type User = {
    id: number;
    name: string;
};
// 개체를 초기값을 주어 생성합니다.
const user1: User = {
    id: 1,
    name: 'Kim',
};
expect(user1.id).toBe(1);
expect(user1.name).toBe('Kim');

// 빈 개체를 User로 생성한 후 값을 대입합니다.
const user2: User = {} as User;
user2.id = 2;
user2.name = 'Lee';
expect(user2.id).toBe(2);
expect(user2.name).toBe('Lee');
```

특정 값으로만 구성된 타입을 만들 수도 있습니다.

```typescript
type Week = '월' | '화' | '수' | '목' | '금'; 
const week1:Week = '월';
//const week2:Week = '토'; // (X) 컴파일 오류. '토'는 Week 타입이 아닙니다.

expect(week1).toBe('월');
```

# 선택적 속성

속성은 선택적으로 사용될 수 있습니다.

```typescript
interface IUser {
    id: number;
    name: string;
    addr?: string; // addr은 없을 수도 있습니다.
}

// addr이 없어도 IUser에 대입됩니다.
const user: IUser = {
    id: 1,
    name: 'Kim',
};

expect(user.id).toBe(1);
expect(user.name).toBe('Kim');
```

# 제네릭

제네릭은 인스턴스화 되는 시점에 타입이 결정되는 함수나 클래스입니다. 사용시 `<>`안에 타입을 지정하면 됩니다.

다음은 함수 제네릭입니다. `T`의 추론이 가능하다면 `<>`을 생략할 수 있습니다.

```typescript
// T 타입의 .length를 리턴합니다.
function getLength<T>(params: T[]): number {
    return params.length;
}

// 함수 인스턴스화시 타입이 결정됩니다.
expect(getLength<number>([1, 2])).toBe(2);
expect(getLength<string>(['a', 'b'])).toBe(2);

// 추론 가능하면 <>을 생략할 수 있습니다.
expect(getLength([1, 2])).toBe(2);
expect(getLength(['a', 'b'])).toBe(2);
```

다음은 클래스 제네릭을 이용하여 임의 타입을 배열로 관리하는 Queue입니다. 

```typescript
// 임의 타입을 배열로 관리하는 Queue입니다.
class Queue<T> {
    protected data: T[] = [];
    push(item: T) {
        this.data.push(item);
    }
    pop(): T | undefined {
        return this.data.shift();
    }
}

// number를 사용하는 Queue입니다.
const q1 = new Queue<number>();
q1.push(0);
q1.push(1);
expect(q1.pop()).toBe(0);

// string을 사용하는 Queue입니다.
const q2 = new Queue<string>();
q2.push('a');
q2.push('b');
expect(q2.pop()).toBe('a');
```
