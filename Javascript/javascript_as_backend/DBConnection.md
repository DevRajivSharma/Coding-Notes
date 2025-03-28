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