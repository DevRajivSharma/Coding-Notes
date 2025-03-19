# Redux and [Redux Toolkit](https://redux-toolkit.js.org/introduction/getting-started)

## What is Redux ?
### It is an state mangement library can be use in any js

> ## Install redux and redix-toolkit (rtk)

```shell
npm install @reduxjs/toolkit react-redux
```

## Important thing we  need to know
> 1. Store
> 2. Reducer [It is used to do operations on store]
> 3. useSelector [To fetch data form store using reducer]
> 4. useDispatch [To dispatch/add/update store data using reducer]

### Create Store
```Store.js
/* Here add all the reducer that you need to connect through this store */

import {configureStore} from '@reduxjs/toolkit';
import todoReducer from '../features/todo/todoSlice';

export const store = configureStore({
    reducer: todoReducer
})
```

### After creating store we create slice which is a given name for creating feature , it should cosist name,initial state and reducer like given in this code
>* Whene we create reducer we always get state and action

## Create Slice:


```jsx
import {createSlice, nanoid } from '@reduxjs/toolkit';

const initialState = {
    todos: [{id: 1, text: "Hello world"}]
}

export const todoSlice = createSlice({
    name: 'todo',
    initialState,
    reducers: {
        addTodo: (state, action) => {
            const todo = {
                id: nanoid(),
                text: action.payload
            }
            state.todos.push(todo)
        },
        removeTodo: (state, action) => {
            state.todos = state.todos.filter((todo) => todo.id !== action.payload )
        },
    }
})

export const {addTodo, removeTodo} = todoSlice.actions

export default todoSlice.reducer

```

## Usage
```usage.jsx
    // useSelector is used to get current state of the store
    const todos = useSelector(state => state.todos)

    // useDispatch is used to manipulate the store but using reducers

    const [input, setInput] = useState('')
    const dispatch = useDispatch()

    const addTodoHandler = (e) => {
        e.preventDefault()
        dispatch(addTodo(input))
        setInput('')
    }

```

