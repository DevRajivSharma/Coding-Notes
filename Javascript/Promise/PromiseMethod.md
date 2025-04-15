## Core Methods

### 1. **Creating Promises**
```js
// Basic Promise creation
const promise = new Promise((resolve, reject) => {
    // Async operation
    if (success) {
        resolve(value);
    } else {
        reject(error);
    }
});

// Quick creation with static methods
const resolved = Promise.resolve(value);
const rejected = Promise.reject(error);
```

### 2. **Promise Instance Methods**
```js
promise
    .then(result => {
        // Handle success
    })
    .catch(error => {
        // Handle error
    })
    .finally(() => {
        // Always executes
    });
```

### 3. **Static Promise Methods**
```js
// Promise.all - Wait for all promises
Promise.all([promise1, promise2, promise3])
    .then(values => console.log(values))
    .catch(error => console.log(error));

// Promise.race - First promise to settle
Promise.race([promise1, promise2])
    .then(value => console.log(value));

// Promise.allSettled - Wait for all to complete
Promise.allSettled([promise1, promise2])
    .then(results => console.log(results));

// Promise.any - First promise to fulfill
Promise.any([promise1, promise2])
    .then(value => console.log(value));
```

## Common Use Cases

### 1. **Fetch API**
```js
fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));
```

### 2. **Timeout Pattern**
```js
const timeout = (ms) => new Promise(resolve => setTimeout(resolve, ms));

timeout(2000)
    .then(() => console.log('2 seconds passed'));
```

### 3. **Parallel Operations**
```js
const urls = ['url1', 'url2', 'url3'];
const fetchAll = urls.map(url => fetch(url));

Promise.all(fetchAll)
    .then(responses => Promise.all(responses.map(res => res.json())))
    .then(data => console.log(data));
```

## Method Reference Table
| Method | Purpose | Returns |
|--------|---------|---------|
| `Promise.resolve()` | Creates resolved promise | Promise |
| `Promise.reject()` | Creates rejected promise | Promise |
| `Promise.all()` | Waits for all promises | Promise Array |
| `Promise.race()` | First to settle wins | Single Promise |
| `Promise.allSettled()` | Waits for all completions | Results Array |
| `Promise.any()` | First success wins | Single Promise |

## Common Patterns

### 1. **Sequential Execution**
```js
Promise.resolve()
    .then(() => task1())
    .then(() => task2())
    .then(() => task3());
```

### 2. **Parallel Execution**
```js
Promise.all([task1(), task2(), task3()])
    .then(([result1, result2, result3]) => {
        // Handle results
    });
```

### 3. **Error Handling**
```js
async function handleErrors() {
    try {
        const result = await riskyOperation();
        return result;
    } catch (error) {
        console.error('Error:', error);
        throw error;
    }
}
```

## Best Practices
1. Always handle rejections
2. Use async/await for cleaner code
3. Chain promises appropriately
4. Avoid nesting promises
5. Implement proper error handling
6. Use Promise.all for parallel operations

## Resources
- [MDN Promise Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [JavaScript.info Promises](https://javascript.info/promise-basics)
- [Promise A+ Specification](https://promisesaplus.com/)
```

The content has been reformatted to match the structure of your Express.js documentation, including:
1. Introduction sections
2. Core concepts
3. Code examples
4. Reference tables
5. Common patterns
6. Best practices
7. Resources

Would you like me to expand on any particular section?