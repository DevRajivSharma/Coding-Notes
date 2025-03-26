## Mongoose: An Overview
- Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides a schema-based solution to interact with MongoDB databases using JavaScript or TypeScript.

### Install Mongoose
```bash
npm install mongoose

```
### Install Mongoose
```javascript
// const mongoose = require('mongoose');
import mongoose from 'mongoose';

mongoose.connect('mongodb://localhost:27017/mydatabase')
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error('Connection error:', err));

const userSchema = new mongoose.Schema(
    {
      name: {
        type: String,
        required: true
      },
      email: {
        type: String, unique: true,
        required: true
      },
      age: {
        type: Number,
        min: 18
      },
    }
);

const User = mongoose.model('User', userSchema);


```
> ### Note : In MongoDB field are saved in lowercase with 's' appended in its end

### Crude Opertation
- Create:
```javascript
 const newUser = new User({ name: 'Rajiv', email: 'rajiv@example.com', age: 22 });
await newUser.save();
```
- Read:
```javascript
const users = await User.find();
console.log(users);
```
- Update:
```javascript
await User.updateOne({ name: 'Rajiv' }, { age: 23 });

```
- Delete:
```javascript
await User.deleteOne({ name: 'Rajiv' });

```
