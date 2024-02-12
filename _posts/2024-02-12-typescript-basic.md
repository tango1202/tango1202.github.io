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

지원하는 타입은 다음과 같습니다,

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

```

# 함수 선언

인자명 뒤에 `: 인자 타입`을 작성하고, 괄호 뒤에 `: 리턴 타입`을 작성합니다.

```typescript
function add1(a: number, a: number): number {
    return a + b;
}

const add2 = function(a: number, a: number): number {
    return a + b;
}

const add3 = (a: number, a: number): number => a + b;
```

# 타입 추론

초기값으로부터 타입을 추론할 수 있다면, 굳이 타입을 명시할 필요는 없습니다.

```typescript
const val = 'Kim'; // val은 string 타입입니다.
```

# 타입 캐스팅

`<>`이나 `as`로 형변환할 수 있습니다.

```typescript
const $input = document.querySelector('input["type="text"]') as HTMLInputElement;
const val = $input.value;

const $input = <HTMLInputElement>document.querySelector('input["type="text"]');
const val = $input.value;
```

# 클래스

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

타입스크립트는 클래스에 속성을 사전에 선언해야 합니다.

```typescript
// typescript
class User {
    name: string; // 속성을 사전에 선언합니다.
    constructor(name:string) {
        this.name = name;
    }
    
    getName() {
        return this.name;
    }
}

const user = new User('Kim');
user.getName();
```

타입스크립트에서는 특별히 `public`, `protected`, `private` 접근 제한자와  `readOnly`, `static`을 제공합니다.

```typescript
class User {
    private name: string; // 다른 개체에서 사용 불가
    protected address: string; // 상속한 개체에서 사용
    public phone: string; // 외부에서 사용
    readonly gender: string; // 읽기 전용
    static staticData:string; // 정적 변수. User.staticData로 접근합니다.

    constructor(name:string, gender:string) {
        this.name = name;
        this.gender = gender; // 읽기 전용은 생성자에서만 설정할 수 있습니다.
    }
    
    getName() {
        return this.name;
    }
    // 정적 함수입니다. User.staticMethod()로 접근합니다.
    static staticMethod() { 

    }
}
```

# 추상 클래스

```typescript
abstract class Shape {
    width: number;
    height: number;

    constructor(w: number, h: number) {
        this.width = w;
        this.height = h;
    }
 
    getWidth(): number {return this.width;}
    getHeight(): number {return this.height;}

    abstract Draw(): void; // 자식 개체에서 구현해야 합니다.
}
class Rectangle extends Shape {
        constructor(w: number, h: number) {
        super(w, h);
    }
    Draw(): void {
        console.log(this.w, this.h);
    }
}
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
    name: 'Kim'
};

// IUser 타입은 아니지만 속성이 같으므로 대입됩니다.
const user1: IUser = user21;
```

함수의 인자와 리턴타입 규약을 정할 수 있습니다.

```typescript
interface IAdd {
    (a: number, b: number): number; // 숫자 2개를 전달받아 숫자를 리턴하는 함수
}

const addFunc: IAdd = function(a: number, b: number): number {
    return a + b;
}
```

클래스의 규약을 정할 수 있습니다.

```typescript
interface IUser {
    id: number;
    name: string;

    getName(): void; // 메서드는 추상으로 선언됩니다. 
}

class User implements IUser {
    // 속성 선언이 생략됩니다.
    constructor(id: number, name: string) {
        this.id = id;
        this.name = name;
    }

    getName() {return this.name;}
}
```

인터페이스는 상속해서 확장할 수 있습니다.

```typescript
interface IPerson {
    name: string;
}
interface IUser extends IPerson {
    id: number;
}

const user: IUser {
    id: 1,
    name: 'Kim'
}

// 인터페이스는 다중 상속도 가능합니다.
interface ISalary {
    salary: number;
}
interface IEmployee extends IPerson, ISalary {

}

const employee: IEmployee = {
    name : 'Kim',
    salary : 100
}; 
```

# 타입 별칭

사용자 정의 타입을 선언할 수 있습니다.

```typescript
type User = {
    id: number,
    name: string
};
const user1:User = {
    id: 1,
    name: 'Kim'
};
const user2: User = {} as User; // 빈 개체를 User로 변환합니다.
user2.id = 2;
user2.name = 'Lee';
```

특정 값으로만 구성된 타입을 만들 수도 있습니다.

```typescript
type Week = '월' | '화' | '수' | '목' | '금'; 
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
    id : 1,
    name: 'Kim'
};
```

# 제네릭

제네릭은 인스턴스화 되는 시점에 타입이 결정되는 함수나 클래스입니다.

다음은 함수 제네릭입니다.

```typescript
function add<T>(a: T, b: T): T {
    return a + b;
} 

console.log(add<number>(1, 2));
console.log(add<string>('a', 'b'));
```

다음은 클래스 제네릭입니다.

```typescript
class Queue<T> {
  protected data: Array<T> = [];
  push(item: T) {
    this.data.push(item);
  }
  pop(): T | undefined {
    return this.data.shift();
  }
}

const q = new Queue<number>();
q.push(0);
q.push(1);

// ?는 옵셔널 체이닝
console.log(q.pop()?.toFixed()); // 0
console.log(q.pop()?.toFixed()); // 1
console.log(q.pop()?.toFixed()); // undefined
```