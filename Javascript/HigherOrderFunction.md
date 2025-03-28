# Higher-Order Functions in JavaScript

## What is a Higher-Order Function?
A **higher-order function** is a function that either:
- Takes one or more functions as arguments, or
- Returns a function as its output.

Higher-order functions enable better abstraction, reusability, and cleaner code by separating logic from operations.

## Examples of Higher-Order Functions

### 1. Functions Taking Another Function as an Argument

```javascript
// Example: Array's map method
const numbers = [1, 2, 3, 4];

const doubled = numbers.map((num) => num * 2);

console.log(doubled); // Output: [2, 4, 6, 8]
```

In this example, `map()` is a higher-order function because it accepts a callback function as an argument.

### 2. Functions Returning Another Function

```javascript
// Example: Function returning another function
const createMultiplier = (multiplier) => {
    return (num) => num * multiplier;
};

const double = createMultiplier(2);
console.log(double(5)); // Output: 10
```

Here, `createMultiplier` is a higher-order function because it returns another function.

## Common Higher-Order Functions in JavaScript

1. **map()** – Transforms each element of an array.
2. **filter()** – Returns a new array with elements that satisfy a condition.
3. **reduce()** – Reduces an array to a single value.
4. **forEach()** – Iterates over each element in an array (without returning a value).
5. **setTimeout()** – Executes a function after a specified delay.

### Example: filter() Usage

```javascript
const numbers = [1, 2, 3, 4, 5];

const evenNumbers = numbers.filter((num) => num % 2 === 0);

console.log(evenNumbers); // Output: [2, 4]
```

## Why Use Higher-Order Functions?

- **Cleaner Code:** Reduces boilerplate by abstracting common patterns.
- **Reusability:** Write reusable logic without duplicating code.
- **Functional Programming:** Emphasizes functions as first-class citizens.

## Custom Higher-Order Function Example

```javascript
const asyncHandler = (fn) => {
    return (req, res, next) => {
        Promise.resolve(fn(req, res, next)).catch(next);
    };
};
```

This function takes an asynchronous function and handles errors automatically using promises.

## Conclusion
Higher-order functions are a powerful feature of JavaScript that promote cleaner, more abstract, and functional code. Understanding them is essential for modern JavaScript development.