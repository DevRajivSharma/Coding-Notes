# JavaScript Object-Oriented Programming (OOP) Concepts

## 1. Objects
An object in JavaScript is a collection of key-value pairs (properties and methods). Objects allow you to store data and behavior together.

### Example:
```javascript
const person = {
    name: 'Rajiv',
    age: 21,
    greet() {
        console.log(`Hello, my name is ${this.name}`);
    }
};
person.greet();
```

## 2. Object Properties (Getters and Setters)
Getters retrieve a property value, and setters modify it. They offer controlled access to internal properties.

### Example:
```javascript
const user = {
    firstName: 'Rajiv',
    lastName: 'Sharma',

    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    },

    set fullName(name) {
        [this.firstName, this.lastName] = name.split(' ');
    }
};

console.log(user.fullName);  // Access via getter
user.fullName = 'John Doe';  // Modify via setter
console.log(user.fullName);
```

## 3. Constructor Functions
A constructor function is a blueprint to create multiple object instances with shared properties.

### Example:
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}

const person1 = new Person('Rajiv', 21);
console.log(person1.name);
```

## 4. Prototypes
Prototypes allow you to share properties and methods across instances. Every JavaScript object has a prototype, which is an object itself.

### Example:
```javascript
function Animal(name) {
    this.name = name;
}

Animal.prototype.speak = function() {
    console.log(`${this.name} makes a noise.`);
};

const dog = new Animal('Dog');
dog.speak();
```

## 5. Classes
Classes in JavaScript are syntactic sugar over constructor functions and prototypes, introduced in ES6 for better readability and organization.

### Example:
```javascript
class Car {
    constructor(brand) {
        this.brand = brand;
    }

    drive() {
        console.log(`${this.brand} is driving.`);
    }
}

const myCar = new Car('Toyota');
myCar.drive();
```

## 6. Static Properties and Methods
Static methods and properties are called on the class itself, not on instances.

### Example:
```javascript
class MathUtil {
    static add(a, b) {
        return a + b;
    }
}

console.log(MathUtil.add(5, 10));
```

## 7. Private Variables (Encapsulation)
Private fields are defined using the `#` symbol. They are only accessible within the class.

### Example:
```javascript
class BankAccount {
    #balance;

    constructor(balance) {
        this.#balance = balance;
    }

    getBalance() {
        return this.#balance;
    }
}

const account = new BankAccount(1000);
console.log(account.getBalance());
```

## 8. OOP Principles
JavaScript supports four core object-oriented principles:

### Abstraction
Hiding complex implementation details and exposing only essential features.

### Encapsulation
Bundling data and methods that operate on the data within objects, restricting direct access to some components.

### Inheritance
Allowing one class to inherit properties and methods from another.

### Polymorphism
Allowing methods to do different things based on the object calling them.

### Example (Inheritance and Polymorphism):
```javascript
class Animal {
    speak() {
        console.log('Animal sound');
    }
}

class Dog extends Animal {
    speak() {
        console.log('Woof!');
    }
}

const pet = new Dog();
pet.speak();
```

## 9. Object Methods

### `Object.getOwnPropertyDescriptor()`
This method returns a property descriptor object for a given property. It provides metadata like `value`, `writable`, `enumerable`, and `configurable`.

### Example:
```javascript
const obj = { age: 21 };
console.log(Object.getOwnPropertyDescriptor(obj, 'age'));
```

## 10. `call()`, `apply()`, and `bind()` Methods

### `call()`
The `call()` method invokes a function with a specified `this` context and individual arguments.

### Example:
```javascript
function greet() {
    console.log(`Hello, ${this.name}`);
}
const user = { name: 'Rajiv' };
greet.call(user); // "Hello, Rajiv"
```

### `apply()`
The `apply()` method is similar to `call()`, but it takes an array of arguments.

### Example:
```javascript
function introduce(name, age) {
    console.log(`My name is ${name} and I am ${age} years old.`);
}
const details = ['Rajiv', 21];
introduce.apply(null, details); // "My name is Rajiv and I am 21 years old."
```

### `bind()`
The `bind()` method returns a new function with a specified `this` context, which can be invoked later.

### Example:
```javascript
const boundGreet = greet.bind(user);
boundGreet(); // "Hello, Rajiv"
```
