# Promise Error Handling in JavaScript

### Understanding Promise Error Handling:

When handling asynchronous operations with promises, error management is crucial. Below is a breakdown of how promise error handling works in different scenarios.

### 1. **Synchronous Function and `Promise.resolve()`**

If you pass a synchronous function that throws an error to `Promise.resolve()`, it will not catch the error because the error occurs before a promise is created.

```javascript
function add() {
    throw new Error('Error found');
}

Promise.resolve(add()).catch(err => console.log('Caught Error:', err));

// This will NOT catch the error because add() throws synchronously.
```

### 2. **Asynchronous Function and `Promise.resolve()`**

If the function is `async`, it always returns a promise. Any error thrown inside the `async` function is converted into a rejected promise, which can be caught by `.catch()`.

```javascript
async function add() {
    throw new Error('Error found');
}

Promise.resolve(add()).catch(err => console.log('Caught Error:', err));

// This will catch the error: "Caught Error: Error: Error found"
```

### 3. **Using `asyncHandler` for Express Middleware**

The `asyncHandler` utility ensures that errors in asynchronous middleware are passed to the next middleware for centralized error handling.

```javascript
const asyncHandler = (requestHandler) => {
    return (req, res, next) => {
        Promise.resolve(requestHandler(req, res, next)).catch(next);
    };
};
```

- **Why use `Promise.resolve()`?**
  - It ensures that both synchronous and asynchronous errors are handled uniformly.
  - If `requestHandler` is an async function, its rejection is caught.
  - If `requestHandler` throws synchronously, it is also handled.

### 4. **Example Usage in Express**

```javascript
const express = require('express');
const app = express();

app.get('/', asyncHandler(async (req, res) => {
    throw new Error('Something went wrong');
}));

app.use((err, req, res, next) => {
    res.status(500).json({ success: false, message: err.message });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

This pattern is widely used to simplify error handling in Express applications.
