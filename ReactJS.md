<h1 align="center" style="font-size: 35px">:book: ReactJS + React Query Documents :book:</h1>

# Một số hook phổ biến

## useState()

useState() cho phép bạn khởi tạo và sử dụng state trong một component. Khi sử dụng useState(), bạn sẽ truyền vào giá trị khởi tạo của state và hook sẽ trả về một mảng với hai phần tử, phần tử đầu tiên là giá trị hiện tại của state và phần tử thứ hai là một hàm để cập nhật giá trị của state (giống như hàm setState cũ vậy).

> Khi sử dụng class component

```js
constructor(props) {
    super(props);
    this.state = { isLoading: false }
 }

 onClick() {
    this.setState({
        isLoading: true,
    })
 }
```

> Khi sử dụng hook useState()

```js
const [isLoading, setLoading] = useState(false);

onClick() {
    setLoading(true)
 }
```

## useEffect()

Nó giúp chúng ta xử lý các side effect trong functional component, useEffect sẽ tương đương với các hàm componentDidMount, componentDidUpdate và componentWillUnMount trong LifeCycle

```js
const App = ({ callApi, data }) => {
  useEffect(() => {
    callApi('some_payload_')
  }, [])

  return <div>ABC</div>
}
```

Có thể thấy trong useEffect ta cũng có thể thực hiện công việc call API giống như hàm ComponentDidMount ngày trước. Để tránh việc hàm useEffect luôn chạy vào mỗi khi có thay đổi State thì ta có thể truyền vào tham số thứ 2 trong useEffect đó là 1 array, trong array này ta có thể truyền vào đó những giá trị mà useEffect sẽ subcribe nó, tức là chỉ khi nào những giá trị đó thay đổi thì hàm useEffect mới được thực thi. Hoặc bạn cũng có thể truyền vào 1 array rỗng thì khi đó nó sẽ chỉ chạy 1 lần đầu tiên sau khi render giống với hàm ComponentDidMount.

```js
useEffect(
  () => {
    const subscription = props.source.subscribe()
    return () => {
      subscription.unsubscribe()
    }
  },
  [props.source] // giá trị được subscribe
)
```

Còn 1 vấn đề nữa đó là trong hàm useEffect ta có thể return về 1 function (chú ý là bắt buộc phải return về function) thì khi làm điều này nó sẽ tương đương với việc ta sử dụng hàm LifeCycle componentWillUnMount.

Tổng kết lại thì đây là những gì ta cần nhớ trong hàm useEffect:

```js
useEffect(() => {
  // almost same as componentDidMount
  console.log('mounted!')
  return () => {
    // almost same as componentWillUnmount
    console.log('unmount!')
  }
}, [])
```

## useMemo()

useMemo giúp ta kiểm soát việc được render dư thừa của các component con, nó khá giống với hàm shouldComponentUpdate trong LifeCycle. Bằng cách truyền vào 1 tham số thứ 2 thì chỉ khi tham số này thay đổi thì thằng useMemo mới được thực thi. Ví dụ:

```js
import React, { useMemo } from 'react'

function MyComponent({ a, b }) {
  const result = useMemo(() => {
    return a + b
  }, [a, b])

  return <div>{result}</div>
}
```

Trong ví dụ này, result là một giá trị được tạo ra bởi useMemo() và được trả về trong component. useMemo() sẽ lưu trữ phiên bản của result trong bộ nhớ và trả về nó cho mỗi lần component được render lại, tránh việc tính toán lại giá trị này mỗi lần component được render lại. Các đối số a và b được truyền vào useMemo() để nó biết khi nào cần tính toán lại giá trị.

> Không sử dụng useMemo:

```js
const NotUsingMemo = ({ products }) => {
  const soldoutProducts = products.filter((x) => x.isSoldout === true) // soldoutProducts sẽ luôn luôn thực thi mỗi khi NotUsingMemo được re-render
}
```

> Có sử dụng useMemo:

```js
const UsingMemo = ({ products }) => {
  const soldoutProducts = useMemo(
    () => products.filter((x) => x.isSoldout === true), // / soldoutProducts sẽ chỉ thực thi khi props products thay đổi
    [products] // watch products
  )
}
```

## useCallback()

useCallback có nhiệm vụ tương tự như useMemo nhưng khác ở chỗ function truyền vào useMemo bắt buộc phải ở trong quá trình render trong khi đối với useCallback đó lại là function callback của 1 event nào đó như là onClick chẳng hạn. Ví dụ:

```js
import React, { useCallback } from 'react'

function MyComponent({ onClick }) {
  const handleClick = useCallback(() => {
    onClick('clicked')
  }, [onClick])

  return <button onClick={handleClick}>Click me</button>
}
```

## useRef()

Trong React Hook, useRef() là một trong những hook quan trọng được sử dụng để tham chiếu đến một phần tử DOM hoặc lưu trữ một biến không thay đổi giữa các lần render của component.

useRef() trả về một đối tượng được gọi là ref object, được bao bọc bởi một current property, giá trị của current property sẽ được lưu trữ và truy cập giữa các lần render của component. Khi giá trị của current property được cập nhật bên trong component, React không thể nhận ra sự thay đổi đó và không render lại component.

Bạn có thể sử dụng useRef() để lưu trữ một tham chiếu đến một phần tử DOM và thay đổi thuộc tính của phần tử đó trong component.

```js
import React, { useRef, useEffect } from 'react'

function MyComponent() {
  const myRef = useRef(null)

  useEffect(() => {
    myRef.current.textContent = 'Hello, world!'
  }, [])

  return <div ref={myRef}></div>
}
```

Trong ví dụ này, chúng ta sử dụng useRef() để lưu trữ một tham chiếu đến một phần tử DOM. Sau đó, chúng ta sử dụng useEffect() để thay đổi thuộc tính textContent() của phần tử đó thành "Hello, world!" khi component được render lần đầu tiên. Cuối cùng, chúng ta truyền tham chiếu đến phần tử đó cho thuộc tính ref của div bằng cách sử dụng myRef.current.

Bạn cũng có thể sử dụng useRef() để lưu trữ một biến không thay đổi giữa các lần render của component.

```js
import React, { useRef } from 'react'

function MyComponent() {
  const countRef = useRef(0)

  const incrementCount = () => {
    countRef.current += 1
    console.log(`Count is ${countRef.current}`)
  }

  return (
    <div>
      <button onClick={incrementCount}>Increment count</button>
    </div>
  )
}
```

Trong ví dụ này, chúng ta sử dụng useRef() để lưu trữ một biến countRef() và khởi tạo giá trị ban đầu của nó là 0. Sau đó, chúng ta sử dụng một hàm incrementCount để tăng giá trị của countRef() lên 1 và in ra giá trị mới của nó trên console

## useContext()

useContext() là một API của React Hook cho phép chúng ta truy cập và sử dụng giá trị được chia sẻ từ một Provider component đến các component con của nó.

useContext() nhận vào một đối tượng context, được tạo bởi hàm createContext(), và trả về giá trị hiện tại của context. Nếu giá trị context thay đổi, tất cả các component sử dụng useContext() để truy cập context đó sẽ được cập nhật lại.

Ví dụ, hãy xem xét một ứng dụng Todo với một context để lưu trữ danh sách các công việc. Đầu tiên, chúng ta tạo một context với createContext():

```js
import { createContext } from 'react'

export const TodoContext = createContext([])
```

Sau đó, chúng ta tạo một Provider component, cung cấp giá trị context cho tất cả các component con của nó:

```js
import { useState } from 'react'
import { TodoContext } from './TodoContext'

function TodoProvider({ children }) {
  const [todos, setTodos] = useState([])

  const addTodo = (newTodo) => {
    setTodos([...todos, newTodo])
  }

  return (
    <TodoContext.Provider value={{ todos, addTodo }}>
      {children}
    </TodoContext.Provider>
  )
}

export default TodoProvider
```

Ở đây, chúng ta sử dụng useState() để tạo state todos và hàm setTodos để cập nhật state đó. Chúng ta cũng tạo một hàm addTodo để thêm một công việc mới vào danh sách. Cuối cùng, chúng ta truyền giá trị todos và addTodo vào Provider component.

Sau đó, chúng ta có thể sử dụng useContext() để truy cập giá trị context này từ bất kỳ component con nào của Provider component:

```js
import { useContext } from 'react'
import { TodoContext } from './TodoContext'

function TodoList() {
  const { todos } = useContext(TodoContext)

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  )
}

export default TodoList
```

Ở đây, chúng ta sử dụng useContext() để truy cập danh sách todos từ context. Sau đó, chúng ta có thể sử dụng giá trị todos này để hiển thị danh sách công việc.

## useReducer()

useReducer() là một API của React Hook cho phép chúng ta quản lý state của một component bằng cách sử dụng reducer pattern giống như trong Redux.

Khi sử dụng useReducer(), chúng ta cần truyền vào một reducer function và một initial state cho state của component. Reducer function nhận vào state hiện tại và một action và trả về state mới.

Ví dụ, hãy xem xét một ứng dụng Todo, chúng ta có thể sử dụng useReducer() để quản lý state của danh sách các công việc:

```js
import { useReducer } from 'react'

const initialTodos = []

function reducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, action.payload]
    case 'TOGGLE_TODO':
      return state.map((todo) =>
        todo.id === action.payload
          ? { ...todo, completed: !todo.completed }
          : todo
      )
    case 'DELETE_TODO':
      return state.filter((todo) => todo.id !== action.payload)
    default:
      return state
  }
}

function TodoList() {
  const [todos, dispatch] = useReducer(reducer, initialTodos)

  const addTodo = (newTodo) => {
    dispatch({ type: 'ADD_TODO', payload: newTodo })
  }

  const toggleTodo = (id) => {
    dispatch({ type: 'TOGGLE_TODO', payload: id })
  }

  const deleteTodo = (id) => {
    dispatch({ type: 'DELETE_TODO', payload: id })
  }

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          <span
            onClick={() => toggleTodo(todo.id)}
            style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
          >
            {todo.text}
          </span>
          <button onClick={() => deleteTodo(todo.id)}>Delete</button>
        </li>
      ))}
    </ul>
  )
}

export default TodoList
```

Ở đây, chúng ta khai báo reducer function và initialTodos. Sau đó, chúng ta sử dụng useReducer() để khởi tạo state todos và hàm dispatch để gửi các action đến reducer function. Chúng ta cũng khai báo các hàm addTodo, toggleTodo và deleteTodo để thêm, đánh dấu hoàn thành hoặc xóa một công việc.

Sau đó, chúng ta sử dụng todos và dispatch để hiển thị danh sách công việc và xử lý các sự kiện như đánh dấu hoàn thành hoặc xóa một công việc. Khi dispatch một action, reducer function sẽ được gọi và trả về state mới, dẫn đến cập nhật lại UI của component.

## Tham khảo một số hook khác tại [Built-in React Hooks](https://react.dev/reference/react)
