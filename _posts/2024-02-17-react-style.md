---
layout: single
title: "#5. [React] 스타일"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 인라인 스타일

JSX 에 스타일을 적용하려면 [React.CssProperties](https://use-form.netlify.app/interfaces/_node_modules__types_react_index_d_.react.cssproperties) 타입의 개체를 사용해야 하는데요, `css` 속성명을 camel 표기법으로 바꿔서 사용하면 됩니다.(*예를들어 `background-color`는 `backgroundColor`입니다.*)

```tsx
const MyInlineStyle = () => {
  return (
    <div style = {
      { // React.CSSProperties 를 사용합니다.
        backgroundColor: 'red'
      }
    }>
      {'인라인 스타일입니다.'}
    </div>
  );
};
```

만약 런타임에 동적으로 변경되지 않는다면, [useMemo()](https://tango1202.github.io/react/react-hook/#usememo)를 사용하여 최적화 할 수 있습니다.

```tsx
import { useMemo } from 'react';

const MyVarInlineStyle = () => {
  const style = useMemo(() => { // 최초 실행될때 리턴한 값을 재사용합니다.
    return {
      backgroundColor: 'green'
    };
  }, []); 
  return (
    <div style = {style}>
      {'변수를 이용한 인라인 스타일입니다.'}
    </div>
  );
};
```

# css 스타일

외부에 `css` 파일을 작성하고 `import`하여 사용할 수 있습니다. 하지만, `css` 개체들이 전역적으로 사용되기 때문에 이름 충돌의 우려가 있습니다.

```css
/* MyStyle.css 파일에서 */
.my-style {
    background-color: blue;
}
```

```tsx
import './MyStyle.css';

const MyCssStyle = () => {
  return (
    <div className = {'my-style'}>
      {'css를 import 했습니다.'}
    </div>
  );
};
```

# css 모듈

`css` 파일의 확장자를 `module.css`로 작성하면, 모듈 형태로 `import`할 수 있어 이름 충돌을 회피할 수 있습니다. 

```css
/* MyStyle.module.css 파일에서 */
.my-style {
    background-color: cyan;
}
```

```tsx
import styles from './MyStyle.module.css'; // #1. 모듈 형태로 import 합니다.

const MyCssModuleStyle = () => {
  return (
    // #2. style.css클래스명 으로 접근할 수 있습니다. 
    // css클래스명에 - 와 같이 자바스크립트 변수명으로 사용할 수 없는 문자를 사용했다면,
    // [css클래스명]와 같이 []로 표현할 수 있습니다.
    <div className = {styles['my-style']}>
      {'css를 모듈로 import 했습니다.'}
    </div>
  );
};
```

실제 변환된 이름은 다음과 같네요.

```javascript
`.MyStyle_my-style__j18mS {
    background-color: cyan;
}`
```

# styled 컴포넌트

기존에는 `Javascript`, `HTML`, `CSS`를 분할했었는데, 리액트 컴포넌트가 모듈 단위로 제공되다 보니, 이들을 결합하는 구조로 만들 수도 있습니다. 

이렇게 결합할 경우 [styled 컴포넌트](https://tango1202.github.io/react/react-style/#styled-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8)가 많이 사용됩니다.

1. 터미널에서 다음 명령을 입력하여 [styled 컴포넌트](https://tango1202.github.io/react/react-style/#styled-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8)를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

    ```
    npm install -D styled-components
    ```
2. 다음과 같이 `import` 하고, HTML 엘레먼트에 따라 스타일을 적용한 후 사용할 수 있습니다. 그외 자세한 내용은 [https://styled-components.com/](https://styled-components.com/)을 참고하세요.

    ```tsx
    import styled from "styled-components"; 

    // HTML 엘리먼트에 스타일 적용
    const MyDiv = styled.div`
      background-color: brown;
    `;

    const MyStyled = () => {
      return (
        <MyDiv>
          {'styled를 사용 했습니다.'}
        </MyDiv>
      );
    };
    ```