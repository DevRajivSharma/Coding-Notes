# Redux and [Redux Toolkit](https://redux-toolkit.js.org/introduction/getting-started)

## What is Redux ?
### It is an state mangement library can be use in any js

> ## Install redux and redix-toolkit (rtk)

```shell
npm install @reduxjs/toolkit
```
```shell
npm install react-redux
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

## Example Code (Redux Toolkit):


```jsx
import { configureStore, createSlice } from "@reduxjs/toolkit";
import { Provider, useDispatch, useSelector } from "react-redux";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1; },
    decrement: (state) => { state.value -= 1; }
  }
});

const store = configureStore({ reducer: { counter: counterSlice.reducer } });

export const { increment, decrement } = counterSlice.actions;
export const useCounter = () => useSelector((state) => state.counter.value);
export const useCounterDispatch = () => useDispatch();

export const ReduxProvider = ({ children }) => (
  <Provider store={store}>{children}</Provider>
);

```

## Usage
```jsx
const counter = useCounter();
const dispatch = useCounterDispatch();
dispatch(increment());

```

