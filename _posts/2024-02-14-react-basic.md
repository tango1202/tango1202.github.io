---
layout: single
title: "#2. [React] 기본 문법"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 컴포넌트

1. 리액트는 마크업을 리턴하는 컴포넌트의 집합으로 구성됩니다. 

    `src`폴더에 `MyDiv.tsx`파일을 생성하고 다음과 같이 작성합니다.

    ```tsx
    // 사용자 정의 태그입니다.
    function MyDiv() {
      return <div>안녕하세요</div>;
    }
    export default MyDiv; // 내보내기 합니다.
    ```

    `MyDiv`는 사용자가 정의한 마크업 태그명입니다. 사용하려는 곳에서 `MyDiv.tsx`를 `import` 하여 사용할 수 있습니다.

    `App.tsx`에 다음과 같이 `import MyDiv from './MyDiv';`하여 `import` 하고 여러번 사용할 수 있습니다.

    ```tsx
    import React from 'react';
    import logo from './logo.svg';
    import './App.css';
    import MyDiv from './MyDiv'; // 불러오기 합니다.

    function App() {
      return (
        <div className="App">
          <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p>안녕하세요.반가워요</p>
            <a
              className="App-link"
              href="https://reactjs.org"
              target="_blank"
              rel="noopener noreferrer"
            >
              Learn React
            </a>
            <MyDiv />
            <MyDiv />
            <MyDiv />
          </header>
        </div>
      );
    }

    export default App;
    ```

2. 컴포넌트는 관습적으로 파스칼 표기법으로 작성하며, 다음과 같이 화살표 함수를 사용할 수도 있습니다.

    ```tsx
    const MyDiv = () => {
      return <div>안녕하세요</div>;
    };
    export default MyDiv; // 내보내기 합니다.
    ```

3. `test`폴더 하위에 `MyDiv.test.tsx` 파일을 만들어 다음과 같이 테스트 할 수 있습니다.(*리액트 컴포넌트 테스트를 할 경우 확장자를 `tsx`로 해야 합니다. `ts`로 할 경우 단순한 타입스크립트로 판단하기 때문에 오동작할 수 있습니다.*)

    ```tsx
    import { render, screen } from '@testing-library/react';
    import MyDiv from '../MyDiv';

    test('MyDiv 테스트', () => {
      render(<MyDiv />);
      const element = screen.getByText('안녕하세요');
      expect(element).toBeInTheDocument();
    });
    ```

# JSX

리액트에서 화면을 수정하고 갱신하는 것은 결국은 HTML DOM을 수정하는 것입니다. DOM이 수정되면 브라우저가 알아서 다시 렌더링 하니까요.

하지만, HTML DOM을 수정하는 것은 전체 페이지 레이아웃을 다시 계산해야 하기 때문에 부하가 큰 작업입니다. 리액트는 이를 최소화 하기 위해 HTML DOM을 직접 수정하지 않고, 가상의 DOM을 만든뒤, HTML DOM에서 변경해야할 최소 요소만 추출하여 수정합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/179647ee-a2af-4d52-90a8-bb24658cf8e3)

가상의 DOM은 컴포넌트가 리턴하는 마크업으로 구성되며 JSX라고 합니다. 형태는 HTML과 유사하며, 내부적으로 `{}`을 이용하여 코드 표현식을 사용할 수 있습니다.

  1. HTML 보다 엄격하게 닫는 태그를 검사합니다.
  2. 마크업 내부에 `{}`을 사용하여 코드 표현식을 사용할 수 있습니다.
  
      ```tsx
      const MyVal = () => {
        const val = '안녕';
        return <div>{val}</div>;
      };
      export default MyVal; 
      ```

      데이터를 기반으로 마크업을 만들 수 있습니다. 이때 동적으로 생성되는 마크업은 꼭 유일한 값으로 구분되는 `key` 속성이 있어야 합니다.

      ```tsx
      const MyUserList = () => {
        // 데이터 입니다.
        const users = [
          { id: 0, name: 'Kim' },
          { id: 1, name: 'Lee' },
          { id: 2, name: 'Park' },
        ];

        // 데이터로 마크업을 만듭니다.
        // 동적으로 생성되는 마크업은 꼭 유일한 값으로 구분되는 key 속성이 있어야 합니다.
        const listItems = users.map((user) => {
          return <li key={user.id}>{user.name}</li>;
        });
        
        return <ul>{listItems}</ul>;
      };
      export default MyUserList;
      ```
  3. `return`이 한줄에 있지 않으면 `()`로 감쌉니다.
      ```tsx
      return ( 
        <div/> 
          <div/>
        <div/>
      );
      ```  

  4. 주석도 `{}` 안에 작성합니다.
      ```tsx
      return ( 
        <div/> 
          {/* 주석입니다. */}
          <div/>
        <div/>
      );
      ```

  5. JSX는 단일 태그를 리턴해야 합니다.

      ```tsx
      // (X) 컴파일 오류. 단일 태그가 아닙니다.
      return ( 
        <div/> 
        <div/>
      );
      ```

      이럴 경우 다음과 같이 `<>`(*Framement*) 로 묶으면 됩니다.

      ```tsx
      return ( 
        <>
          <div/> 
          <div/>
        </>
      );
      ```

  6. `HTML`에서 대시를 포함하는 이름이나 `class`와 같은 예약어는 다른 이름으로 대체됩니다. 다음에서 `className`은 `HTML`의 `class` 입니다. 자세한 내용은 [Common components(https://react.dev/reference/react-dom/components/common)](https://react.dev/reference/react-dom/components/common)를 참고하세요.

      ```tsx
      <img 
        src="https://i.imgur.com/yXOvdOSs.jpg" 
        alt="Hedy Lamarr" 
        className="photo"
      />
      ```

  7. `null`을 리턴하여 무시할 수 있습니다.

      ```tsx
      <div>
        {toggle ? <MyDiv /> : null}
      </div>
      ```

  8. 삼항 연산자나 `&&`을 이용하여 조건부 작성을 할 수 있습니다.

      ```tsx
      <div> 
        { isOk ? <MyDiv /> : <YourDiv /> }
        { isOk && <MyDiv /> } 
      </div>
      ```

# 이벤트

컴포넌트 개체의 이벤트를 받을 수 있습니다.

```tsx
const MyButton = () => {
  const onClick = () => {
    alert('버튼을 클릭했습니다.');
  };

  return <button onClick={onClick}>클릭해 주세요.</button>;
};
export default MyButton;
```

# State

다음 예에서 `onClick()`이벤트 핸들러는 `++count;`로 `count`변수값을 증가시킵니다. 

이때 `onClick()`이벤트 핸들러는 [클로저](https://tango1202.github.io/javascript/javascript-basic/#%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EC%A0%95%EB%B3%B4-%EC%9D%80%EB%8B%89)이기 때문에 외부의 `count`변수에 접근하고 사용할 수 있습니다. 그래서 증가된 값을 잘 표시하죠.

하지만 JSX의 `<p>`가 표시하는 것은 초기에 전달한 `0`입니다. 또한 버튼을 클릭했을때 다시 렌더링하지도 않고요.

```tsx
const MyState = () => {
  let count = 0;
  console.log('MyState 이 호출되었습니다.');
  const onClick = () => {
    ++count;
    // 증가한 값을 잘 표시합니다.
    alert(`버튼을 ${count}회 클릭했습니다.`);
  };

  return (
    <div>
      <button onClick={onClick}>카운트합니다.</button>
      <p>
        {/* 계속 0을 표시합니다. */}
        count = {count}
      </p>
    </div>
  );
};
export default MyState;
```

이렇게 수정된 값을 JSX에 반영하고 다시 렌더링하기 위해서는 [State](https://tango1202.github.io/react/react-basic/#state)라는 특별한 데이터를 사용합니다. [State](https://tango1202.github.io/react/react-basic/#state)는 컴포넌트에서 사용하는 내부 데이터로서, 값이 수정되면 수정된 값으로 JSX를 다시 렌더링하는 역할을 합니다. [Observer 패턴](https://tango1202.github.io/pattern/pattern-observer/)과 유사하죠. 

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/378c1024-541c-4ee8-a158-f90744551aaa)

1. #1 : `State`를 사용하기 위해 `useState`를 가져옵니다.
2. #2 : `countState` 라는 이름으로 초기값이 `0`인 `State`를 만듭니다. 이때 `setCountState()`라는 setter도 함께 만들어 집니다.
3. #3 : `countState`를 수정할 때는 항상 `setCountState()`를 이용해서 수정해야 렌더링을 다시 합니다.
4. #4 : `count`는 변하지 않고, `countState`는 변합니다.

```tsx
import { useState } from 'react'; // #1

const MyState = () => {
  let count = 0; // 일반 변수 입니다. 수정해도 JSX에 반영되지 않습니다.

  const [countState, setCountState] = useState(0); // #2
  // const [countState, setCountState] = useState<number>(0); // useState<타입>으로 타입을 명시할 수 있습니다.

  console.log('MyState 이 호출되었습니다.');
  const onClick = () => {
    ++count; // 수정해도 JSX에 반영되지 않습니다.
    // ++countState; // #3. state는 setter를 이용해서 수정해야만 렌더링을 다시 합니다.
    setCountState(countState + 1);
    alert(`버튼을 ${count}회 클릭했습니다.`);
  };

  return (
    <div>
      <button onClick={onClick}>카운트합니다.</button>
      <p>
        {/* #4. count는 변하지 않고, countState는 변합니다. */}
        count = {count} countState = {countState}
      </p>
    </div>
  );
};
export default MyState;
```

# 개체/배열 State

`setter`로 수정할 때는 복제본을 사용해야 합니다. 그래야 `State`가 원본에서 변경된 것을 알 수 있습니다. `number`와 같은 기본 타입인 경우 대입시 알아서 복제본이 사용됩니다만, 개체나 배열을 사용할 경우, 명시적으로 복제본을 사용해야 합니다.

만약 개체나 배열을 다음과 같이 `const arr2 = arr1;`로 단순 대입하여 같은 개체를 참조하게 한 후 `arr2`를 `push()`하여 수정해 봈자 여전히 `arr1 === arr2`입니다. `State`가 원본에서 변경된 것을 감지 하지 못하기 때문에 다시 렌더링하지 못합니다. 

```typescript
const arr1 = [1, 2, 3];
const arr2 = arr1;
arr2.push(4);

console.log(arr1 === arr2); // true입니다.
```

다음은 `State`로 배열을 사용한 예입니다. 배열의 복제본을 사용해야 다시 렌더링 하는 것을 확인할 수 있습니다.

1. #1 : `arr[0]`요소의 `x`를 직접 수정하면 렌더링을 다시 하지 않습니다.
2. #2 : `arr[0]`을 수정하고 `setArr(arr)`을 하더라도 렌더링을 다시 하지 않습니다.
3. #3 : 배열을 복제합니다. 이때 [Spread](https://tango1202.github.io/javascript/javascript-basic/#spreadecmascript6)를 이용하여 얕은 복사 합니다.
  
    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0985674e-28c5-4c82-895a-5b314cfeaa96)

4. #4 : 배열의 복제본을 만들어 `setArr()`을 호출하면 렌더링을 다시 합니다. 

```tsx
import { useState } from 'react';

const MyArrayState = () => {
  const [arr, setArr] = useState([
    { x: 1, y: 2 },
    { x: 10, y: 20 },
  ]);
  const onXClick = () => {
    arr[0].x = 100;
    setArr(arr); // #1. 렌더링을 다시 하지 않습니다.
  };
  const onElementClick = () => {
    arr[0] = { x: 100, y: 2 };
    setArr(arr); // #2. 렌더링을 다시 하지 않습니다.
  };
  const onArrayClick = () => {
    const clone = [...arr];
    clone[0].x = 100; // #3. 복제본을 수정합니다.
    console.log(arr[0].x === clone[0].x); // #3. 배열의 각 요소는 앝은 복사됩니다.
    setArr(clone); // #4. 렌더링을 다시 합니다.
  };
  return (
    <div>
      <button onClick={onXClick}>x 값을 변경합니다.</button>
      <button onClick={onElementClick}>arr[0]을 변경합니다.</button>
      <button onClick={onArrayClick}>arr을 변경합니다.</button>
      <p>{`arr[0] : ${arr[0].x}, ${arr[0].y} arr[1] : ${arr[1].x}, ${arr[1].y}}`}</p>
    </div>
  );
};
export default MyArrayState;
```

정리하면 배열은 다음처럼 복제본을 사용합니다.

```typescript
setArr(arr.concat(item)); // item을 추가한 새로운 배열
setArr([...arr, item]); // item을 추가한 새로운 배열

setArr(arr.map(user) => user.id === id ? user.name = changedName : user); // user.id === id 인 항목의 name을 changedName으로 변경한 새로운 배열

setArr(arr.filter(user => user.id !== id)); // user.id !== id로 구성된 새로운 배열. 즉, id인 것만 삭제된 배열
```

개체의 경우는 다음처럼 복제본을 사용합니다.

```typescript
setObj({x: 1, y: 2}); // 새로운 개체값으로 변경
setObj({...obj, y: 2}); // 개체의 기존값을 그대로 사용하되 y만 2로 변경
```

# Props

컴포넌트 개체는 HTML의 attribute와 유사한 형태로 외부에서 데이터(*개체나 함수*)를 전달 받을 수 있습니다. 이를 `Props`라고 하는데요, `Props`가 변경되면, 컴포넌트는 다시 렌더링됩니다.

1. #1 : 컴포넌트 `Props`의 인터페이스를 선언합니다.
2. #2 : `Props`는 인자로 전달받습니다. 관습적으로 `props`라는 이름을 사용합니다.
3. #3 : `Props`는 관습적으로 [구조 분해](https://tango1202.github.io/javascript/javascript-basic/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)해서 사용합니다.
4. #4 : `Props`는 수정할 수 있으나, 컴포넌트 내부에서 수정하는건 좋지 않습니다. 

    컴포넌트는 전달된 `Props`로 단지 렌더링만 하는 [단일 책임]((https://tango1202.github.io/principle/principle-single-responsibility/))만 갖는게 좋습니다. 만약 내부에서 `Props`를 수정하면, 외부에서 동일한 데이터를 전달했을때 다르게 렌더링 될 수 있습니다. 이렇게 되면, 리액트에서 가상 DOM으로 부터 HTML DOM을 생성할때 최적화가 제대로 되지 않아 쓸데없는 화면 갱신이 빈번해 질 수 있습니다.

    수정이 필요한 데이터가 있다면 [State](https://tango1202.github.io/react/react-basic/#state)를 사용하시기 바랍니다.

5. #5 : `Props`가 있는 컴포넌트는 해당 값을 전달해야 합니다. 만약 선택적으로 전달하고 싶다면, `name?`와 같이 [선택적 속성](https://tango1202.github.io/typescript/typescript-basic/#%EC%84%A0%ED%83%9D%EC%A0%81-%EC%86%8D%EC%84%B1)으로 선언해야 합니다.

```tsx
// #1. 속성의 인터페이스 입니다.
interface IProps {
  id: number;
  name: string;
}

// #2. 속성은 함수의 인자로 전달 받습니다.
const User = (props: IProps) => {
  const { id, name } = props; // #3. 구조 분해
  // id = 100; // #4. 속성은 수정할 수 있으나 하지 마세요.
  return (
    <div>
      id = {id} name = {name}
    </div>
  );
};

// 함수 인자 선언시 바로 구조 분해할 수 있습니다.
// const User = ({id, name}: IProps) => {
//   return (
//     <div>
//       id = {id} name = {name}
//     </div>
//   );
// };

const MyProps = () => {
  // #5. User를 사용하려면 id와 name을 전달해야 합니다.
  return <User id={0} name={'Lee'} />;
};
export default MyProps;
```

또한 기본값을 사용할 수도 있습니다.

```tsx
const User = ({id, name = 'Kim'}: IProps) => {
  ...
}; 

// name에 기본값으로 'Kim'이 사용됩니다.
<User id={0}/>
```

[Spread](https://tango1202.github.io/javascript/javascript-basic/#spreadecmascript6)로 [Props](https://tango1202.github.io/react/react-basic/#props)을 손쉽게 포워딩할 수 있습니다.

```tsx
const Pareant = (props: IProps) {
  return <Child {...props} />;
}
```

# Props을 복사한 State

다음과 같이 `-`, `+` 버튼을 누를때마다 값이 변경되어 출력되는 컴포넌트를 생각해 봅시다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/662f0c91-3a8c-4312-9ec8-4312f9e344db)

외부에서 초기값을 전달하고, 컴포넌트 내에서 값을 수정하려고 합니다.

초기값은 [Props](https://tango1202.github.io/react/react-basic/#props)로 전달하면 됩니다. 그런데 `+`, `-` 버튼 클릭시 수정이 필요하고 [Props](https://tango1202.github.io/react/react-basic/#props)는 수정하면 안되므로, ***[Props](https://tango1202.github.io/react/react-basic/#props)를 [State](https://tango1202.github.io/react/react-basic/#state)에 복제***하고, `+`, `-` 버튼 클릭시 [State](https://tango1202.github.io/react/react-basic/#state)를 수정해 보았습니다.

`+`, `-`버튼 클릭시 잘 동작하는 것처럼 보입니다.

하지만, `OnResetClick`에서 `MyCounter`에 전달한 초기값을 다시 `0`으로 바꾸고 [Props](https://tango1202.github.io/react/react-basic/#props)를 전달했을때 값이 반영되지 않습니다. 이는 [Props](https://tango1202.github.io/react/react-basic/#props)를 복제해서 사용했기 때문입니다.

1. #1: [Props](https://tango1202.github.io/react/react-basic/#props)를 [State](https://tango1202.github.io/react/react-basic/#state)에 복제합니다.
2. #2 : 버튼 클릭시 [State](https://tango1202.github.io/react/react-basic/#state)를 수정합니다.
3. #3 : [State](https://tango1202.github.io/react/react-basic/#state) 인 `value`를 표시합니다.
4. #4 : 외부에서 [Props](https://tango1202.github.io/react/react-basic/#props)를 변경하더라도 #1에서 초기에 전달한 값을 복제했기 때문에 반영되지 않습니다.

```tsx
import { useState } from 'react';

interface IProps {
  init: number;
}

const MyCounter = (props: IProps) => {
  const { init } = props;
  const [value, setValue] = useState(init); // #1. Props를 State에 복제합니다.

  const onMinusClick = () => {
    setValue(value - 1); // #2. State를 수정합니다.
  };
  const onPlusClick = () => {
    setValue(value + 1); // #2. State를 수정합니다.
  };
  return (
    <>
      <button onClick={onMinusClick}>{'-'}</button>
      <span>{value}</span> {/* #3. State를 표시합니다. */}
      <button onClick={onPlusClick}>{'+'}</button>
    </>
  );
};

const MyPropsState = () => {
  const [value, setValue] = useState(0);

  const onResetClick = () => {
    setValue(0); // #4. MyCounter의 0으로 리셋하고 다시 렌더링합니다. 하지만, MyCounter가 초기에 전달한 값을 복제해서 사용하는 바람에 0으로 리셋이 안됩니다.
  };

  return (
    <div>
      <button onClick={onResetClick}>{'MyCounter가 초기에 전달한 값을 복제해서 사용하는 바람에 0으로 리셋이 안됩니다.'}</button>
      <MyCounter init={value} />
    </div>
  );
};
export default MyPropsState;
```

# 상위 - 하위 컴포넌트간 데이터 공유

[Props을 복사한 State](https://tango1202.github.io/react/react-basic/#props%EC%9D%84-%EB%B3%B5%EC%82%AC%ED%95%9C-state) 에서 예시한 `MyCount`를 리액트스럽게 수정해 보도록 하겠습니다.

리액트는 [Props](https://tango1202.github.io/react/react-basic/#props)를 이용하여 기본적으로 상위 컴포넌트에서 하위 컴포넌트로 데이터를 전달합니다. 하지만, 이벤트를 이용한다면 역방향으로 데이터를 전달할 수도 있습니다.

다음과 같은 구조로 상위 개체에서 `value`를 [State](https://tango1202.github.io/react/react-basic/#state)로 생성하고, 수정하도록 관리하고, 하위 개체는 출력만 담당하는게 좋습니다.

하위 개체에서 버튼이 클릭될 때마다 상위 개체에서 이벤트를 수신받고 `value`를 수정한뒤, 이를 출력할 개체에 [Props](https://tango1202.github.io/react/react-basic/#props)로 전달합니다. 이때 하위 개체의 이벤트를 수신하기 위해 개체의 [Props](https://tango1202.github.io/react/react-basic/#props)에 콜백 함수(*이벤트 핸들러*)를 전달합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/7064c997-8af3-490e-ae3c-984d899c0e3a)


1. #1 : `MyCounter`는 `value`와 `onMinusClick()`, `onPlusClick()`을  [Props](https://tango1202.github.io/react/react-basic/#props)으로 사용합니다.
2. #2 : [Props](https://tango1202.github.io/react/react-basic/#props)로 전달된 `value`를 표시합니다.
2. #3 : `button`의 이벤트 핸들러로 [Props](https://tango1202.github.io/react/react-basic/#props)로 전달된 `onMinusClick()`, `onPlusClick()`을 이용합니다. 
4. #4 : 콜백 함수가 호출되면, [State](https://tango1202.github.io/react/react-basic/#state)인 `value`를 수정합니다. 그러면, 이값이 `MyCounter`의 [Props](https://tango1202.github.io/react/react-basic/#props)로 전달되어 화면이 다시 렌더링됩니다.
5. #5 : `value`를 `0`으로 리셋합니다. 그러면, 이값이 `MyCounter`의 [Props](https://tango1202.github.io/react/react-basic/#props)로 전달되어 화면이 다시 렌더링됩니다.

```tsx
import { useState } from 'react';

interface IProps { // #1.
  value: number;
  onMinusClick(): void;
  onPlusClick(): void;
}

const MyCounter = (props: IProps) => {
  return (
    <>
      <button onClick={props.onMinusClick}>{'-'}</button> {/* #3. Props에 전달된 콜백 함수를 호출합니다. */}
      <span>{props.value}</span> {/* #2. value를 표시합니다. */}
      <button onClick={props.onPlusClick}>{'+'}</button> {/* #3. Props에 전달된 콜백 함수를 호출합니다. */}
    </>
  );
};

const MyCounterCallback = () => {
  const [value, setValue] = useState(0);

  const onResetClick = () => {
    setValue(0); // #5. 0으로 리셋하고 다시 렌더링합니다.
  };
  const onMinusClick = () => {
    setValue(value - 1); // #4. State를 수정합니다.
  };
  const onPlusClick = () => {
    setValue(value + 1); // #4. State를 수정합니다.
  };

  return (
    <div>
      <button onClick={onResetClick}>{'0으로 리셋합니다.'}</button>
      <MyCounter value={value} onMinusClick={onMinusClick} onPlusClick={onPlusClick} />
    </div>
  );
};

export default MyCounterCallback;
```

# 주요 개념 정리

* 리액트는 렌더링 컴포넌트를 트리 형태로 구성합니다.

* 동일한 [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)에서는 동일한 결과가 리턴되어야 합니다. 이와 같이 입력값에 대해 동일한 결과를 리턴하는 함수를 `Pure Function`이라고 합니다. 리액트는 동일한 [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)일때 동일한 JSX를 리턴한다고 가정하고 성능 최적화하기 때문에, 꼭 준수해야 합니다. 준수하지 않으면, 뜻하지 않게 화면이 쉼없이 재 렌더링 되거나, 아예 렌더링을 안할 수 있습니다.

* 렌더링은 [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)변경에 따라 재시도 되고, 가상 DOM을 이용하여 동적으로 렌더링 요소들을 구성한뒤 변경된 것만 갱신합니다.

* [Props](https://tango1202.github.io/react/react-basic/#props)를 이용하여 상위 컴포넌트에서 하위 컴포넌트로 데이터가 전달됩니다. 단, [Props](https://tango1202.github.io/react/react-basic/#props)에 콜백 함수를 전달하여 역방향 흐름으로 만들 수 있습니다.

* [State](https://tango1202.github.io/react/react-basic/#state)를 만들어 컴포넌트의 정보를 관리할 수 있습니다. 

# 렌더링 최적화

컴포넌트는 다음 두가지 상황일때 렌더링됩니다.

1. 처음 호출되었을때 루트 컴포넌트 렌더링을 호출하고 하위 컴포넌트 렌더링을 호출합니다.

2. [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)가 변경되었을때 해당 컴포넌트 렌더링을 호출하고 하위 컴포넌트 렌더링을 호출합니다.

    [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)가 변경되면, 렌더링이 대기열에 추가되며, 번경된 것들을 계산하여 가상 DOM을 수정합니다. 이때 최신 렌더링 출력과 일치하도록 최소한의 필수 렌더링만 수행합니다.