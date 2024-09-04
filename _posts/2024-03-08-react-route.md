---
layout: single
title: "#8. [React] react-router-dom"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

리액트는 SPA(*Single Page Application*)을 개발하는 프레임워크입니다. 하지만, `react-router-dom`를 이용하면, 여러 페이지를 이동하는 프로그램을 만들 수 있으며, 페이지 이동 이력은 브라우저 히스토리에 저장되기 때문에, 이전 페이지나 다음 페이지로 이동할 수 있습니다.

# react-router-dom 설치

터미널에서 다음 명령을 입력하여 [Redux(https://ko.redux.js.org/)](https://react.i18next.com/)를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

```
npm install -D react-router-dom
```

# 페이지 이동

`react-router-dom`의 다음 컴포넌트들을 이용하여 페이지를 이동합니다.

|항목|내용|
|--|--|
|`<Link to='/XXX'>`|`루트 주소/XXX`로 이동합니다.|
|`<Navigate replace to='/XXX'>`|`루트 주소/XXX`로 이동합니다.|
|`<BrowserRouter>`|페이지 이동 처리를 위한 루트 컴포넌트 입니다.|
|`<Routes>`|`<Route>`들의 부모 컴포넌트 입니다.|
|`<Route path='/XXX' element={<MyComponent/>}>`|`루트 주소/XXX`로 접근하면 `<MyComponent/>`를 표시합니다.|

다음은 `<Link>`를 사용하여 `홈`, `목록`, `About`을 표시하고, 각 항목 클릭시 `/`, `/list`, `/about`으로 이동시키는 코드 입니다.

```typescript
<div>
  <Link to ='/'><button>{'홈'}</button></Link>
  <Link to ='/list'><button>{'목록'}</button></Link>
  <Link to ='/about'><button>{'About'}</button></Link>
</div>
```

다음은 `<Routes>`와 `<Route>`를 이용하여 특정 url이 입력되면, 지정된 컴포넌트를 표시하는 코드 입니다. 각 `<Route>`에서 `path`와 `element`를 지정하고 있습니다.

```typescript
<Routes>
  {/* #1. 정의하지 않으면, No routes matched location 경고 발생 */}
  {/* http://localhost:3000 */}
  <Route path='/' element={<Navigate replace to={"/"} />}/> 

  {/* http://localhost:3000/List */}
  <Route path='/list' element={<List />}/>

  {/* #2 */}
  {/* http://localhost:3000/Desc/id-01 */}
  {/* http://localhost:3000/Desc/id-02 */}
  <Route path='/desc/:id' element={<Desc />}/>

  {/* http://localhost:3000/about */}
  <Route path='/about' element={<About/>}/>
</Routes>
```

상기의 #1을 보면, `/`에 `<Navigate replace to={"/"} />`을 지정하여, `/` 경로를 `/` 로 이동시키게 했습니다. 같은 경로로 이동하라고 지정한게 좀 웃기긴 한데요, 아쉽게도 이 코드가 없으면, `No routes matched location "/"` 경고가 발생하기 때문에, 억지로 작성해 두었습니다.(*경고 발생 원인은 좀더 확인해 봐야겠습니다.*)

또한, 상기의 #2를 보면 `path='/Desc/:id'`와 같이 특별히 `:id`를 붙였는데요, 이는 지정한 컴포넌트에서 Url의 파라메터를 읽기 위함입니다.

예를 들어 `http://localhost:3000/Desc/id-01`와 같이 `:id`영역에 `id-01`을 전달하면, 이값을 읽어서 처리 할 수 있습니다.

다음은 `useParams()`를 이용하여 Url의 `:id` 값을 읽고 사용하는 예입니다.

```typescript
const Desc = () => {
  const {id} = useParams();
  return (
    <div>
      { id && 
        <div>{`${id}를 선택하셨습니다.`}</div> 
      }
    </div>
  );
};
```

전체적인 코드는 다음과 같습니다.

```typescript
import { BrowserRouter, Link, Navigate, Route, Routes, useParams } from "react-router-dom";

// id 목록을 표시합니다.
// localhost:3000/list
const List = () => {
  return (
    <div>
      <li>
        <Link to='/desc/id-01'>{'id-01'}</Link>
      </li>
      <li>
        <Link to='/desc/id-02'>{'id-02'}</Link>
      </li>
    </div>
  );
};

// url로 전달된 :id 값을 읽고 그값을 출력합니다.
// localhost:3000/desc/XXXX
const Desc = () => {
  const {id} = useParams();
  return (
    <div>
      { id && 
        <div>{`${id}를 선택하셨습니다.`}</div> 
      }
    </div>
  );
};

// about 내용을 표시합니다.
// localhost:300/about
const About = () => {
  return (
    <div>{'About Page입니다.'}</div>
  );
};

// 홈, 목록, About을 표시하고 클릭시 해당 컴포넌트를 보여줍니다.
// 홈(localhost:3000)
// 목록(localhost:3000/list) 
// About(localhost:3000/about) 
const MyRouter = () => {
 
  return (
    <BrowserRouter>
      <div>
        <Link to ='/'><button>{'홈'}</button></Link>
        <Link to ='/list'><button>{'목록'}</button></Link>
        <Link to ='/about'><button>{'About'}</button></Link>
      </div>
      <Routes>
        {/* #1 */}
        {/* http://localhost:3000 */}
        <Route path='/' element={<Navigate replace to={"/"} />}/> 

        {/* http://localhost:3000/List */}
        <Route path='/list' element={<List />}/>

        {/* #2 */}
        {/* http://localhost:3000/Desc/id-01 */}
        {/* http://localhost:3000/Desc/id-02 */}
        <Route path='/desc/:id' element={<Desc />}/>

        {/* http://localhost:3000/about */}
        <Route path='/about' element={<About/>}/>
      </Routes>
    </BrowserRouter>
  );
};
export default MyRouter;
```