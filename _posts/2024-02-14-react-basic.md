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

컴포넌트가 리턴하는 마크업을 JSX라고 합니다.

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

  3. JSX는 단일 태그를 리턴해야 합니다.

      ```tsx
      return ( 
        <div/> // 단일 태그가 아닙니다.
        <div/>
      );
      ```

      이럴 경우 다음과 같이 `<>`(*Framement*) 로 묶으면 됩니다.

      ```tsx
      return ( 
        <>
          <div/> /
          <div/>
        </>
      );
      ```
  4. `return`이 한줄에 있지 않으면 `()`로 감쌉니다.

  5. `HTML`에서 대시를 포함하는 이름이나 `class`와 같은 예약어는 다른 이름으로 대체됩니다. 다음에서 `className`은 `HTML`의 `class` 입니다. 자세한 내용은 [Common components(https://react.dev/reference/react-dom/components/common)](https://react.dev/reference/react-dom/components/common)를 참고하세요.

      ```tsx
      <img 
        src="https://i.imgur.com/yXOvdOSs.jpg" 
        alt="Hedy Lamarr" 
        className="photo"
      />
      ```

  6. `null`을 리턴하여 무시할 수 있습니다.

  7. 삼항 연산자나 `&&`을 이용하여 조건부 작성을 할 수 있습니다.

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
  function onClick() {
    alert('버튼을 클릭했습니다.');
  }

  return <button onClick={onClick}>클릭해 주세요.</button>;
};
export default MyButton;
```

# 상태

다음 예에서 `onClick()`이벤트 핸들러는 `++count;`로 `count`변수값을 증가시킵니다. 

이때 `onClick()`이벤트 핸들러는 [클로저](https://tango1202.github.io/javascript/javascript-basic/#%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EC%A0%95%EB%B3%B4-%EC%9D%80%EB%8B%89)이기 때문에 외부의 `count`변수에 접근하고 사용할 수 있습니다. 그래서 증가된 값을 잘 표시하죠.

하지만 JSX의 `<p>`가 표시하는 것은 초기에 전달한 `0`입니다. 또한 버튼을 클릭했을때 다시 랜더링하지도 않고요.

```tsx
const MyState = () => {
  let count = 0;
  console.log('MyState 이 호출되었습니다.');
  function onClick() {
    ++count;
    // 증가한 값을 잘 표시합니다.
    alert(`버튼을 ${count}회 클릭했습니다.`);
  }

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

이런 경우 `state`사용합니다. `state`는 컴포넌트에서 사용하는 내부 데이터로서, 값이 수정되면 수정된 값으로 JSX를 다시 렌더링 합니다. [Observer 패턴](https://tango1202.github.io/pattern/pattern-observer/)과 유사하죠. 

1. #1 : `state`를 사용하기 위해 `useState`를 가져옵니다.
2. #2 : `stateCount` 상태 변수를 선언하고, `setStateCount()` 함수를 선언합니다. 이때 초기값은 `0`을 사용합니다.
3. #3 : 상태 변수를 수정할때는 항상 `setStateCount`와 같이 `setter`를 이용해서 수정해야만 랜더링을 다시 합니다.
4. #4 : `count`는 변하지 않고, `stateCount`는 변합니다.

```tsx
import { useState } from 'react'; // #1

const MyState = () => {

  const [stateCount, setStateCount] = useState(0); // #2
  // const [stateCount, setStateCount] = useState<number>(0); // useState<타입>으로 타입을 명시할 수 있습니다.
  let count = 0;
  console.log('MyState 이 호출되었습니다.');
  function onClick() {
    ++count;
    // ++stateCount; // #3. state는 setter를 이용해서 수정해야만 랜더링을 다시 합니다.
    setStateCount(stateCount + 1);
    alert(`버튼을 ${count}회 클릭했습니다.`);
  }

  return (
    <div>
      <button onClick={onClick}>카운트합니다.</button>
      <p>
        {/* #4. count는 변하지 않고, stateCount는 변합니다. */}
        count = {count} stateCount = {stateCount} 
      </p>
    </div>
  );
};
export default MyState;
```

`setter`로 수정할 때는 복제본을 사용해야 합니다. 특히 개체나 배열을 사용할 경우, 복제된 개체나 배열을 사용해야 합니다.

다음 예는 `state`로 배열을 사용한 예입니다.

1. #1 : `arr[0]`을 수정하고 `setArr(arr)`을 하면 렌더링을 다시 하지 않습니다.

1. #2 : 배열의 복제본을 만들어 `setArr()`을 호출하면 렌더링을 다시 합니다. 

```tsx
import { useState } from 'react'; 

const MyArrayState = () => {
  const [arr, setArr] = useState([1, 2, 3]); 
  function onElementClick() {
    arr[0] = 10;
    setArr(arr); // #1. 렌더링을 다시 하지 않습니다.
  }
  function onArrayClick() {
    setArr([20, arr[1], arr[2]]); // #2. 렌더링을 다시 합니다.
  }
  return (
    <div>
      <button onClick={onElementClick}>각 요소값을 변경합니다.</button>
      <button onClick={onArrayClick}>array를 변경합니다.</button>
      <p>
        arr[0] = {arr[0]} arr[1] = {arr[1]} arr[2] = {arr[2]}
      </p>
    </div>
  );
};
export default MyArrayState;
```

# 속성

컴포넌트 개체는 속성을 사용할 수 있으며, 외부에서 개체나 함수를 전달 받을 수 있습니다. 속성이 변경되면, 컴포넌트는 다시 렌더링합니다.

1. #1 : 컴포넌트 속성의 인터페이스를 선언합니다.
2. #2 : 속성은 인자로 전달받습니다. 관습적으로 `props`라는 이름으로 전달받습니다.
3. #3 : `props`는 관습적으로 [구조 분해](https://tango1202.github.io/javascript/javascript-basic/#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4ecmascript6)해서 사용합니다.
4. #4 : 속성은 수정할 수 있으나, 컴포넌트 내부에서 수정하지 마세요. 컴포넌트는 전달된 속성으로 랜더링하는 [단일 책임]((https://tango1202.github.io/principle/principle-single-responsibility/))만 있습니다. 내부에서 속성을 수정하면, 외부에서 동일한 [속성](??)을 전달했을때 다르게 렌더링 될 수 있습니다. 내부에서 수정할 값이 있다면 [상태](??)를 사용하시기 바랍니다.
5. #5 : `props`가 있는 컴포넌트는 사용시 꼭 값을 전달해야 합니다.

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

[Spread](??)로 속성을 전달할 수 있습니다.


```tsx
const Pareant = (props) {
  return <Child {...props} />;
}
```

# 상위 - 하위 컴포넌트간 데이터 공유

리액트는 [속성](??)을 이용하여 상위 컴포넌트에서 하위 컴포넌트로 데이터가 이동하며, [속성](??)번경에 따라 컴포넌트가 갱신됩니다. 하지만, 이벤트를 이용한다면 역방향으로 정보 전달을 할 수도 있습니다.

다음과 같이 숫자를 세는 컨트롤을 생각해 봅시다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/662f0c91-3a8c-4312-9ec8-4312f9e344db)

`-`, `+` 버튼을 누를때마다 값이 변경되어 출력되어야 합니다. 

이러한 경우 다음과 같은 구조로 상위 개체에서 `value`를 `state`로 만들고 관리하는게 좋습니다.

하위 개체에서 버튼이 클릭될 때마다 상위 개체에서 이벤트를 수신받고 `value`를 수정한뒤, 이를 표시할 개체에 속성으로 전달합니다. 이때 하위 개체의 이벤트를 수신하기 위해 개체의 속성에 콜백 함수(*이벤트 핸들러*)를 전달합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/0c0d700f-e98c-4dfa-85b5-dbac9f85f337)


```jsx
import { useState } from 'react';

// 버튼은 caption과 onClick 이벤트 핸들러를 사용합니다.
interface IMyButtonProps {
  caption: string;
  onClick(): void;
}
const MyButton = (props: IMyButtonProps) => {
  const { caption, onClick } = props;
  return <button onClick={onClick}>{caption}</button>;
};

// 값을 출력합니다. value가 변경되면 다시 렌더링 합니다.
interface IMyValueProps {
  value: number;
}
const MyValue = (props: IMyValueProps) => {
  const { value } = props;
  return <div>{value}</div>;
};

// 버튼 클릭시 value를 수정합니다. 하위 컴포넌트를 다시 랜더링 합니다.
const MyCounter = () => {
  const [value, setValue] = useState(0);

  function onMinusClick() {
    setValue(value - 1);
  }
  function onPlusClick() {
    setValue(value + 1);
  }

  return (
    <>
      <MyButton caption={'-'} onClick={onMinusClick} />
      <MyValue value={value} />
      <MyButton caption={'+'} onClick={onPlusClick} />
    </>
  );
};
export default MyCounter;
```

