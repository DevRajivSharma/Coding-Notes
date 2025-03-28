# JavaScript Array Methods

## 1. Iteration Methods

### `.forEach()`
Executes a provided function once for each array element.
```javascript
const arr = [1, 2, 3];
arr.forEach(num => console.log(num));
```

### `.map()`
Creates a new array by applying a function to each element.
```javascript
const arr = [1, 2, 3];
const newArr = arr.map(num => num * 2);
```

### `.filter()`
Creates a new array with elements that pass a test.
```javascript
const arr = [1, 2, 3, 4];
const evenNumbers = arr.filter(num => num % 2 === 0);
```

### `.some()`
Checks if at least one array element meets a condition.
```javascript
const arr = [1, 2, 3];
const hasEven = arr.some(num => num % 2 === 0);
```

### `.every()`
Checks if all array elements meet a condition.
```javascript
const arr = [2, 4, 6];
const allEven = arr.every(num => num % 2 === 0);
```

### `.find()`
Returns the first element that meets a condition.
```javascript
const arr = [1, 2, 3];
const found = arr.find(num => num === 2);
```

### `.findIndex()`
Returns the index of the first element that meets a condition.
```javascript
const arr = [1, 2, 3];
const index = arr.findIndex(num => num === 2);
```

### `.reduce()`
Applies a function to reduce the array to a single value.
```javascript
const arr = [1, 2, 3];
const sum = arr.reduce((acc, num) => acc + num, 0);
```

### `.reduceRight()`
Similar to `.reduce()`, but works from right to left.
```javascript
const arr = [1, 2, 3];
const result = arr.reduceRight((acc, num) => acc + num, 0);
```

## 2. Adding & Removing Elements

### `.push()`
Adds one or more elements to the end of an array.
```javascript
const arr = [1, 2];
arr.push(3);
```

### `.pop()`
Removes the last element from an array.
```javascript
const arr = [1, 2, 3];
const last = arr.pop();
```

### `.unshift()`
Adds one or more elements to the beginning of an array.
```javascript
const arr = [2, 3];
arr.unshift(1);
```

### `.shift()`
Removes the first element from an array.
```javascript
const arr = [1, 2, 3];
const first = arr.shift();
```

### `.splice()`
Adds/removes elements from a specified index.
```javascript
const arr = [1, 2, 3, 4];
arr.splice(1, 2);
```

### `.slice()`
Returns a shallow copy of a portion of an array.
```javascript
const arr = [1, 2, 3, 4];
const newArr = arr.slice(1, 3);
```

### `.concat()`
Merges two or more arrays.
```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const newArr = arr1.concat(arr2);
```

## 3. Searching & Checking

### `.indexOf()`
Returns the first index of a specified value.
```javascript
const arr = [1, 2, 3];
const index = arr.indexOf(2);
```

### `.lastIndexOf()`
Returns the last index of a specified value.
```javascript
const arr = [1, 2, 3, 2];
const index = arr.lastIndexOf(2);
```

### `.includes()`
Checks if an array includes a specific value.
```javascript
const arr = [1, 2, 3];
const hasTwo = arr.includes(2);
```

## 4. Sorting & Reversing

### `.sort()`
Sorts the elements of an array.
```javascript
const arr = [3, 1, 2];
arr.sort((a, b) => a - b);
```

### `.reverse()`
Reverses the order of elements in an array.
```javascript
const arr = [1, 2, 3];
arr.reverse();
```

## 5. Copying & Filling

### `.copyWithin()`
Copies part of an array to another location in the same array.
```javascript
const arr = [1, 2, 3, 4];
arr.copyWithin(1, 2);
```

### `.fill()`
Fills elements of an array with a static value.
```javascript
const arr = [1, 2, 3];
arr.fill(0, 1, 2);
```

## 6. Iterators

### `.keys()`
Returns an iterator of array keys.
```javascript
const arr = ['a', 'b', 'c'];
const keys = arr.keys();
```

### `.values()`
Returns an iterator of array values.
```javascript
const arr = ['a', 'b', 'c'];
const values = arr.values();
```

### `.entries()`
Returns an iterator of key-value pairs.
```javascript
const arr = ['a', 'b', 'c'];
const entries = arr.entries();
```

## 7. Static Methods

### `Array.isArray()`
Checks if a value is an array.
```javascript
const isArray = Array.isArray([1, 2, 3]);
```

### `Array.from()`
Creates a new array from an iterable object.
```javascript
const arr = Array.from('abc');
```

### `Array.of()`
Creates a new array from a set of arguments.
```javascript
const arr = Array.of(1, 2, 3);
```
