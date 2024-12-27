# Tic-Tac-Toe
## App.js
- App.js 안의 코드는 컴포넌트를 생성
- React에서 **컴포넌트**란, UI의 한 부분을 표현하는 재사용할 수 있는 코드의 한 조각
```javascript
// js 키워드인 export는 이 함수가 이 파일 외부에서 액세스할 수 있도록 함
// default 키워드는 코드를 사용하는 다른 파일들에게 이 코드가 파일의 중요 기능임을 알려주는 역할
export default function Square() {
  // 버튼을 반환, return 키워드는 함수 호출자 이후에 어떤 값을 반환할 지를 의미
  // <button>은 JSX 요소
  // JSX 요소는 JS 코드와 HTML 태그의 조합
  // className="square" 는 버튼 속성 혹은 버튼을 어떻게 스타일링 할지 CSS에게 말하는 prop
  return <button className="square">X</button>;
}
```
## style.css
- 이 파일은 React 앱의 스타일을 정의
- 처음 두 개의 CSS 선택자(* 와 body)들은 앱의 가장 큰 부분들의 스타일을 정의
```javascript
* {
  box-sizing: border-box;
}
body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}
```

## index.js
- App.js에서 생성한 컴포넌트와 웹 브라우저 간의 다리 라고 생각하면 됨
```javascript
// 필요한 모든 요소를 같이 묶어줌
// 파일의 나머지 부분은 모든 부분을 모아서 index.html(public folder에 있는)에 최종 결과물을 삽입
import React, { StrictMode } from "react"; // React
import { createRoot } from "react-dom/client";
import "./styles.css";

import App from "./App";
```

## Building the board
```javascript
export default function Square() {
  // 오류 발생
  // React 컴포넌트는 인접한 여러 개의 JSX 요소가 아니라 단일 JSX 요소를 반환해야만 함
  return <button className="square">X</button><button className="square">X</button>;

  // 아래와 같이 해결할 수 있음
  return (
    <>
      <button className="square">X</button>
      <button className="square">X</button>
      ...
    </>
  );
}
```
- 위와 같이 코드를 짜면 버튼을 여러개 생성 가능
- 단락을 나누려면 아래와 같이 div로 group을 묶어줌
```javascript
// Square -> Board
export default function Board() {
  return (
    <>
      <div className="board-row">
          <button className="square">1</button>
          <button className="square">2</button>
          <button className="square">3</button>
      </div>
      <div className="board-row">
          <button className="square">4</button>
          <button className="square">5</button>
          <button className="square">6</button>
      </div>
      <div className="board-row">
          <button className="square">7</button>
          <button className="square">8</button>
          <button className="square">9</button>
      </div>
    </>
  );
}
```

## Passing data through props
- 사용자가 네모를 선택할 때 값이 비어있으면 X로 바꾸고 싶을 때 어떻게 바꿀지 알아보자
- 9개 일일이 복사 붙여넣기 대신 React 컴포넌트 아키텍처를 이용하여 재사용한 컴포넌트를 만들어보자
```javascript
function Square({ value }) {
  return <button className="square">{value}</button>;
}

export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square value="1"/>
        <Square value="2"/>
        <Square value="3"/>
      </div>
      <div className="board-row">
        <Square value="4"/>
        <Square value="5"/>
        <Square value="6"/>
      </div>
      <div className="board-row">
        <Square value="7"/>
        <Square value="8"/>
        <Square value="9"/>
      </div>
    </>
  );
}
```

## Making an interactive component
- 클릭했을 때 Square 컴포넌트를 X로 채워보자
```javascript
function Square({ value }) {
  function handleClick() {
    console.log("clicked!");
  }

  return (
    <button className="square" onClick={handleClick}>
      {value}
    </button>
  );
}
```
- 그리고 이 Square 컴포넌트가 클릭되었는지 기억하기 위해서 컴포넌트들은 state를 사용
- React는 **useState**라는 특별한 함수를 사용하는데, 컴포넌트에서 호출하여 기억할 수 있도록 함
```javascript
import { useState } from 'react';

function Square() {
  // value는 값을 저장하고 setValue는 값을 바꾸는 데 사용될 함수
  // useState에 전달된 null은 상태 변수의 초기값으로 사용되므로, 값이 null로 시작함
  const [value, setValue] = useState(null);

  function handleClick() {
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```
- onClick 핸들러에서 set 함수를 호출하면서, React에게 버튼이 클릭될 때 Square를 다시 렌더링하라고 말하고 있는 것
- 각각의 Square는 자신만의 state를 가지고 있는데, 각 Square에 저장된 value들은 완전히 독립적인 값
- 컴포넌트안에서 set 함수를 호출할 때, React는 value와 내부 자식 컴포넌트들을 자동으로 업데이트 함

## Lifting state up
- 최선의 접근은 게임의 상태를 각각의 Square 컴포넌트가 아닌 부모 Board 컴포넌트에 저장하는 것
- Board 컴포넌트는 각각의 Square에게 숫자를 전달한 것과 같이 표시할 내용을 전달할 수 있음
```javascript
import { useState } from 'react';

function Square({ value }) {
  return <button className="square">{value}</button>;
}

export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```
- 이로써 각각의 Square는 valu 속성을 가지게 됨
