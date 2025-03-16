# React Context
___
## React Context consist three main components
1. Create Component
2. Create Provider
3. Wrap the component with provider and get data using useContext() hook

---
> ## Mostly these two way are  used
>
>### First by creaitng the context and contextprovider which will be wrap around the childern component that need the props inside the context
``` UserContext.jsx
// Creating the context

import React from 'react'

const UserContext = React.createContext()

export default UserContext;


```

```UserContextProvider.jsx
/* Creaing the context provider which will define all the props (can be anything eg. fetch database call) and can be used insider the children component */

import React from "react";
import UserContext from "./UserContext";


const UserContextProvider = ({children}) => {
    /*Here instead of children any other name can be used to define 'children'is mostly used by developers */
    const [user, setUser] = React.useState(null)
    return(
        <UserContext.Provider value={{user, setUser}}>
        {children}
        </UserContext.Provider>
    )
}


export default UserContextProvider
```
```Children_01.jsx
import React, {useContext} from 'react'
import UserContext from '../context/UserContext'

function Profile() {
/* Here useContext hook with context_name as arguement is used to get data form the context  */
    const {user,setUser} = useContext(UserContext)

    if (!user) return <div>please login</div>
    return <div>Welcome {user}</div>
}

export default Profile
```

```App.jsx
function App() {
/* UserContextProvider is used to wrap the children component so that UserContext data can be acces */

  return (
    <UserContextProvider>
      <h1>React with Chai and share is important</h1>
      <Children_01 />
    </UserContextProvider>
  )
}

export default App

```

>### Second way is less code and mostly used in corporator

```Context.js
/* Here we are taking theme mode example for context */

import { createContext, useContext } from "react";

/* Here we directly give data in {}  */
export const ThemeContext = createContext({
    themeMode: "light",
    darkTheme: () => {},
    lightTheme: () => {},
})

/* Creating the provider */
export const ThemeProvider = ThemeContext.Provider

export default function useTheme(){
    return useContext(ThemeContext)
}
```
