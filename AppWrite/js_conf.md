# Boiler Plate for Appwrite in js

## Authentication part
```Auth.js
import { Client, Account ,ID} from "appwrite";
import env_conf from "../env_conf/env_conf.js";

class Auth {
    client = new Client();
    account;
    constructor(){
        try {
            this.client
                .setEndpoint("") // Your API Endpoint
                .setProject(""); // Your project ID
            this.account = new Account(this.client);
        }
        catch (e) {
            throw e;
        }
    }

    async createAccount({email, password, name}){
        try {
            const user = await this.account.create(
                ID.unique() ,// userId
                email, // email
                password, // password
                name // name (optional)
            );
            if (user) {
                return this.login(email,password);
            }
            else{
                return user;
            }
        }
        catch(error){
            throw error;
        }
    }

    async deleteAccount({id}){
        try{
            return await this.account.delete(id);
        }
        catch (e) {
            throw e;
        }
    }

    async login({email, password}){
        try {
            const result = await this.account.createEmailPasswordSession(email, password);
            return result;
        }
        catch(error){
            throw error;
        }
    }

    async logout(){
        try {
            return await this.account.deleteSessions();
        }
        catch(error){
            throw error;
        }
    }

    async islogin(){
        try {
            return await this.account.get();
        }
        catch(error){
            throw error;
        }
    }


}

const auth = new Auth();

export default auth;
```

## Database part [Here we take an example for post]
```Database.js
import {Client, Databases ,Storage,ID} from "appwrite";
import env_conf from "../env_conf/env_conf.js";

class DB {
    client = new Client();
    databases;
    constructor(){
        try {
            this.client
                .setEndpoint(env_conf.appwrite_url) // Your API Endpoint
                .setProject(env_conf.appwrite_project_id); // Your project ID
            this.databases = new Databases(this.client);
        }
        catch (e) {
            throw e;
        }
    }

    async createPost({p_title, slug, p_post, status, user_id, p_images}){
        try {
            return await this.databases.createDocument(
                env_conf.appwrite_database_id, // databaseId
                env_conf.appwrite_collection_id, // collectionId
                slug, // documentId
                {
                    p_title,
                    p_post,
                    status,
                    user_id,
                    p_images
                }, // data
            );
        }
        catch (e) {
            throw e;
        }

    }

    async updatePost(slug,{p_title,p_post,status,user_id,p_images}){
        try {
            return await this.databases.updateDocument(
                env_conf.appwrite_database_id, // databaseId
                env_conf.appwrite_collection_id, // collectionId
                slug, // documentId
                {
                    p_title,
                    p_post,
                    status,
                    p_images
                }, // data
            );
        }
        catch (e) {
            throw e;
        }
    }

    async deletePost(slug){
        try {
            await this.databases.deleteDocument(
                env_conf.appwrite_database_id, // databaseId
                env_conf.appwrite_collection_id, // collectionId
                slug // documentId
            );
            return true;
        }
        catch (e) {
            throw e;
        }

    }

    async listPost(query=[]) {
        try {
            return await this.databases.listDocuments(
                env_conf.appwrite_database_id, // databaseId
                env_conf.appwrite_collection_id, // collectionId
                query // queries (optional)
            );
        }
        catch (e){
            throw e;
        }
    }

    async getPost(slug) {
        try {
            return await this.databases.getDocument(
                env_conf.appwrite_database_id, // databaseId
                env_conf.appwrite_collection_id, // collectionId
                slug, // documentId
                [] // queries (optional)
            );
        }
        catch (e){
            throw e;
        }
    }



}

const db = new DB()
export default db;
```

## Storage part
```storage.js
// Can be used for separate storage configuration when required



/*
import {Client, Storage,ID} from "appwrite";
import env_conf from "../env_conf/env_conf.js";

class bucket {
    client = new Client();
    storage;
    constructor(){
        try {
            this.client
                .setEndpoint(env_conf.appwrite_url) // Your API Endpoint
                .setProject(env_conf.appwrite_project_id); // Your project ID
            this.storage = new Storage(this.client);
        }
        catch (e) {
            throw e;
        }
    }

    async uploadFile(file){
        return  await storage.createFile(
            env_conf.appwrite_bucket_id, // bucketId
            ID.unique(), // fileId
            file // file
        );
    }

    async deleteFile(file_id){
        try {
            await this.storage.deleteFile(
                env_conf.appwrite_bucket_id, // bucketId
                file_id, // fileId
            );
            return true;
        }
        catch (e){
            throw e;
        }

    }

    getFilePreview(file_id){
        return this.storage.getFilePreview(
            env_conf.appwrite_bucket_id, // bucketId
            file_id, // fileId
        );
    }



}

const storage = new bucket();
*/

```