## Why is dotenv needed?
- Node.js can access environment variables via process.env, but it cannot automatically read variables from a .env file.
- The dotenv package helps load these variables into process.env.

## Install the dotenv package:
```bash
npm install dotenv
```
##  Add your environment variables:
```.env
PORT=3000
DB_USER=myuser
DB_PASS=mypassword
```
## Import dotenv at the top of your file:
```bash
require('dotenv').config();
/* If using ESM */
/* import 'dotenv/config';*/
console.log(process.env.PORT);
console.log(process.env.DB_USER);
```

## What is Express.js?
- **Express.js** is a fast, minimal, and flexible **Node.js** web framework.
- It handles **HTTP requests and responses** efficiently.

## Why Use Express.js?
- Lightweight and easy to use.
- Supports **Middleware** for request handling.
- Built-in methods for **routing** and handling REST APIs.
- Integrates easily with databases like **MongoDB**.

## Basic Setup
```bash
# Install Express
npm init -y
npm install express
```

### Basic Server Example

```js
const express = require('express');
// import express from "express" ;
const app = express();

const port = 3000

// Home Route
app.get('/', (req, res) => {
    res.send('Hello, Express!');
});

// Start Server
app.listen(port, () => {
    console.log('Server running on http://localhost:3000');
});
```

## Core Concepts
### 1. **Routing**
- Handles different URL paths.
```js
app.get('/about', (req, res) => {
    res.send('About Page');
});
```

### 2. **Middleware**
- Functions that process requests before reaching the route.
```js
app.use(express.json()); // Parses JSON requests
```

### 3. **Request & Response**
- `req`: Incoming request object (e.g., `req.body`, `req.params`)
- `res`: Outgoing response object (e.g., `res.send()`, `res.json()`)

### 4. **Error Handling**
```js
app.use((err, req, res, next) => {
    res.status(500).send('Something broke!');
});
```

## Useful Methods
| Method       | Description                  |
|--------------|------------------------------|
| `app.get()`  | Handle GET requests          |
| `app.post()` | Handle POST requests         |
| `app.put()`  | Handle PUT requests          |
| `app.delete()`| Handle DELETE requests      |
| `app.use()`  | Apply middleware             |

## Common Middleware
| Middleware          | Purpose                        |
|---------------------|--------------------------------|
| `express.json()`    | Parse JSON request bodies      |
| `express.urlencoded()` | Parse URL-encoded data   |
| `morgan`            | Logging HTTP requests          |

## Quick Tips
1. Always restart the server after code changes.
2. Use **`nodemon`** for automatic restarts (`npm install -g nodemon`).
3. Keep routes modular using **Router** (`express.Router()`).

## Resources
- [Express Docs](https://expressjs.com/)
- [Node.js Docs](https://nodejs.org/)
