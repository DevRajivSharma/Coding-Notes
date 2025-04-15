# Promises in JavaScript


## What is Promise?
- A **Promise** is a proxy for a value that will eventually become available.
- It represents the **completion or failure** of an asynchronous operation.
- Provides better handling of asynchronous operations than callbacks.

## Why Use Promises?
- Cleaner asynchronous code handling
- Better error management
- Chainable operations
- Built-in methods for concurrent operations
- Avoids callback hell
- Integrates well with async/awaitto avoid callback hell.

### Promise States:
1. **Pending**: Initial state; the promise is neither fulfilled nor rejected.
2. **Fulfilled**: The operation completed successfully.
3. **Rejected**: The operation failed.

## Creating a Promise
```javascript
const myPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("Data fetched successfully!");
    }, 2000);
});
```

## Consuming a Promise
### Using `.then()`
```javascript
myPromise.then((data) => {
    console.log(data); // Output: "Data fetched successfully!"
});
```
- `.then()` takes two optional arguments:
  1. Success callback (when resolved)
  2. Error callback (when rejected)

Example with error handling in `.then()`:
```javascript
myPromise.then(
    (data) => console.log(data),
    (error) => console.error(error)
);
```

### Using `.catch()`
```javascript
myPromise
    .then((data) => console.log(data))
    .catch((error) => console.error(error));
```
- `.catch()` is preferred for handling errors as it catches any errors in the entire promise chain.

### Using `.finally()`
```javascript
myPromise
    .then((data) => console.log(data))
    .catch((error) => console.error(error))
    .finally(() => console.log("Operation completed"));
```
- `.finally()` runs after the promise is settled (either resolved or rejected).

## `Promise.resolve()` vs `new Promise()`

1. **`Promise.resolve()`**: Returns a promise that is already resolved with the provided value.
```javascript
const resolvedPromise = Promise.resolve("Resolved value");
resolvedPromise.then(console.log); // Output: "Resolved value"
```

2. **`new Promise()`**: Used to manually create a new promise and control when it resolves or rejects.
```javascript
const newPromise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("Resolved after 2 seconds"), 2000);
});
newPromise.then(console.log);
```

## Error Handling with Promises
### Basic Error Handling:
```javascript
const faultyPromise = new Promise((_, reject) => {
    reject("Error occurred");
});

faultyPromise.catch((error) => console.error(error));
```

### Handling Errors in `async/await`
```javascript
const fetchData = async () => {
    try {
        const data = await myPromise;
        console.log(data);
    } catch (error) {
        console.error(error);
    }
};

fetchData();
```

## Avoiding Callback Hell
Promises flatten nested callbacks, making asynchronous code easier to read.

### Without Promises (Callback Hell):
```javascript
fs.readFile('file1.txt', (err, data) => {
    if (err) throw err;
    fs.readFile('file2.txt', (err, data) => {
        if (err) throw err;
        console.log("All files read");
    });
});
```

### With Promises:
```javascript
const readFilePromise = (file) => {
    return new Promise((resolve, reject) => {
        fs.readFile(file, (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    });
};

readFilePromise('file1.txt')
    .then(() => readFilePromise('file2.txt'))
    .then(() => console.log("All files read"))
    .catch(console.error);
```

## Conclusion
Promises provide a cleaner and more manageable approach to handling asynchronous operations, reducing callback hell and offering better error handling with `.catch()` and `async/await`.