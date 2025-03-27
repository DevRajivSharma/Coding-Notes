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
// If using ESM
// import 'dotenv/config';
console.log(process.env.PORT);
console.log(process.env.DB_USER);
```