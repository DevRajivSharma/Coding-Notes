## Mongoose: An Overview
- Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides a schema-based solution to interact with MongoDB databases using JavaScript or TypeScript.


> ### [Click](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbXI1b1E3QVQyeTF0aUlhdDhCMS0xZHY4V1kyd3xBQ3Jtc0ttU0RsMlNUODRMa3NOTV9mSnVDdFRYZVZPWHBEcjZrbFJqZTlUamNNVU5KYndoVm9KbXcwSkFtOUhXSDAycUtkMWFkR25RQm5wdmpBS0xvT05DU2dBbi1VM1plcHF1RVlQWVBPTk9mcm4wZ2hldzAtTQ&q=https%3A%2F%2Fstackblitz.com%2Fedit%2Fstackblitz-starters-mg2tiy%3Ffile%3Dmodels%252Ftodos%252Fuser.models.js&v=VbGl3msgce8) for code reference

### Install Mongoose
```bash
npm install mongoose
```
### BoilerPlate
```javascript
// const mongoose = require('mongoose');
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema(
    {},
    {timestamps:true}
);

export const User = mongoose.model('User', userSchema);


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


### More complex Concept
- Schema
```javascript
const userSchema = new mongoose.Schema({
    name: { type: String, required: true },
    email: { type: String, unique: true, required: true },
    age: { type: Number, min: 18 },
    address: {
        street: { type: String },
        city: { type: String },
        country: { type: String }
    },
    posts: [
        {
            title: { type: String, required: true },
            content: { type: String },
            tags: [{ type: String }],
            comments: [
                {
                    user: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
                    message: { type: String },
                    createdAt: { type: Date, default: Date.now }
                }
            ],
            createdAt: { type: Date, default: Date.now }
        }
    ],
    friends: [{ type: mongoose.Schema.Types.ObjectId, ref: 'User' }],
    settings: {
        darkMode: { type: Boolean, default: false },
        notifications: { type: Boolean, default: true }
    },
    createdAt: { type: Date, default: Date.now }
});

```
