# React 공부( #1 To Do List )
```javascript
// App.jsx
import {useState, useEffect} from 'react';

function App() {
    const [toDo, setToDo] = useState("");  // input에 입력하는 값
    const [toDos, setToDos] = useState([]);  // 추가한 ToDo 들을 저장하기 위한 배열

    // input에 입력하는 값이 바뀔때마다, toDo의 state를 setToDo를 사용하여 변경
    const onChange = (event) => {
        setToDo(event.target.value);
    };

    // form 데이터를 전송할 때, 새로고침이 되지 않도록 하고 빈 값을 전송하면 함수 실행하지 않도록
    // 추가한 toDo 값들은 toDos 리스트에 저장하는데, state값을 기존 값들 뒤에 추가하는 형식으로 state를 변경
    // 추가했다면 input 값은 초기화 
    const onSubmit = (event) => {
        event.preventDefault();

        if (toDo === "") {
            return;
        }
        
        setToDos(() => [...toDos, toDo]);
        setToDo("");
    };

    return (
        <div>
            <h1>My To Dos ({toDos.length})</h1>
            <form onSubmit={onSubmit}>
                <input onChange={onChange} value={toDo} placeholder="Write your to do.."/>
                <button>Add To Do</button>
            </form>
            <hr/>
            <ul>
                // map()을 사용하여 toDos 를 순회하며 <li>를 반환하도록 하여 To Do List를 표현하도록 함
                {toDos.map((item, index) => {
                    return <li key={index}>
                        {item}
                    </li>;
                })}
            </ul>
        </div>
    );
}

export default App
```
출처 : 노마드 코더
