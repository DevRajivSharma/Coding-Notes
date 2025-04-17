### Two important thing to always remember while connecting through database
1. Use try-catch/promise to handle the error
2. Use async-await

>MongoDBAltas  Connection
  - First Approch
      ```index.js
    import express from "express"
    const app = express()
    ( async () => {
        try {
            await mongoose.connect(`${process.env.MONGODB_URI}/${DB_NAME}`)
            app.on("errror", (error) => {
                console.log("ERRR: ", error);
                throw error
            })

            app.listen(process.env.PORT, () => {
                console.log(`App is listening on port ${process.env.PORT}`);
            })

        } catch (error) {
            console.error("ERROR: ", error)
            throw err
        }
    })()
      ```
- Second Approach
    ```db/index.js
    const connectDB = async () => {
        try {
            const connectionInstance = await mongoose.connect(`${process.env.MONGODB_URI}/${DB_NAME}`)
            console.log(`\n MongoDB connected !! DB HOST: ${connectionInstance.connection.host}`);
        } catch (error) {
            console.log("MONGODB connection FAILED ", error);
            process.exit(1)
        }
    }

    export default connectDB
    ```
    ```index.js
    import connectDB from './db/index.js';
    import 'dotenv/config';
    import { app } from "./app.js";

    connectDB()
      .then(
        app.listen(process.env.PORT || 8000,()=>{
          console.log("Server is running on port 8080");
        })
      )
      .catch(error =>{
        console.log(error);
      });
    ```
Great question! Here's a **clear comparison** between `mongoose.connect()` and `mongoose.createConnection()` — both are used to connect to MongoDB, but serve slightly different purposes.

---

##  `mongoose.connect()`

###  What it does:
- Connects **Mongoose globally** to a MongoDB database.
- Uses a **singleton connection**.
- Ideal for most applications.

###  Example:
```javascript
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/mydb', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});
```

###  Use Case:
- When your app uses **only one MongoDB connection**.
- You define models globally using `mongoose.model()`.

---

##  `mongoose.createConnection()`

###  What it does:
- Creates a **separate, independent connection** to MongoDB.
- Does **NOT affect the global `mongoose` instance**.
- Returns a connection instance (`conn`) on which you define models.

###  Example:
```javascript
const mongoose = require('mongoose');

const conn = mongoose.createConnection('mongodb://localhost:27017/otherdb', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const User = conn.model('User', new mongoose.Schema({ name: String }));
```

###  Use Case:
- When you need **multiple database connections**.
- Useful in **multi-tenant applications** or **microservices**.

---

## 🧠 Summary Table:

| Feature                | `mongoose.connect()`           | `mongoose.createConnection()`       |
|------------------------|--------------------------------|--------------------------------------|
| Connection Type        | Global (singleton)             | Independent (multiple connections)   |
| Model Usage            | `mongoose.model()`             | `conn.model()`                       |
| Use Case               | Single DB apps                 | Multiple DBs, multi-tenant setups    |
| Sets global connection | ✅ Yes                         | ❌ No                                 |

