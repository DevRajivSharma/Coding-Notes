# Mongoose — Complete MERN Stack Notes
> **Purpose:** Practical notes from beginner to advanced: connecting MongoDB, schemas, models, CRUD, queries, relationships, aggregation, middleware, indexes, transactions, performance, and production patterns.
>
> **Stack:** Node.js + Express.js + MongoDB + Mongoose using modern JavaScript (ES modules).
>
> Official references:
> - Mongoose documentation: https://mongoosejs.com/docs/
> - Mongoose API: https://mongoosejs.com/docs/api/mongoose.html
> - MongoDB aggregation: https://www.mongodb.com/docs/manual/aggregation/
> - MongoDB query operators: https://www.mongodb.com/docs/manual/reference/operator/query/

---

## Contents

1. [What is Mongoose?](#1-what-is-mongoose)
2. [Install and connect to MongoDB](#2-install-and-connect-to-mongodb)
3. [Schema, model, and document](#3-schema-model-and-document)
4. [Schema field types and options](#4-schema-field-types-and-options)
5. [Creating documents](#5-creating-documents)
6. [Reading and querying documents](#6-reading-and-querying-documents)
7. [Updating documents](#7-updating-documents)
8. [Deleting documents](#8-deleting-documents)
9. [Validation and custom validation](#9-validation-and-custom-validation)
10. [Schema methods, statics, and query helpers](#10-schema-methods-statics-and-query-helpers)
11. [Middleware / hooks](#11-middleware--hooks)
12. [Relationships, ObjectId, populate](#12-relationships-objectid-populate)
13. [Aggregation pipeline](#13-aggregation-pipeline)
14. [Indexes and performance](#14-indexes-and-performance)
15. [Pagination, sorting, and field selection](#15-pagination-sorting-and-field-selection)
16. [Lean queries and performance](#16-lean-queries-and-performance)
17. [Transactions and sessions](#17-transactions-and-sessions)
18. [Virtuals, aliases, timestamps, and serialization](#18-virtuals-aliases-timestamps-and-serialization)
19. [Discriminators and subdocuments](#19-discriminators-and-subdocuments)
20. [Express integration and error handling](#20-express-integration-and-error-handling)
21. [Security and production best practices](#21-security-and-production-best-practices)
22. [Common mistakes and troubleshooting](#22-common-mistakes-and-troubleshooting)
23. [Quick reference cheat sheet](#23-quick-reference-cheat-sheet)
24. [Interview questions](#24-interview-questions)

---

## 1. What is Mongoose?

**Mongoose** is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides schemas, validation, models, query helpers, middleware, and convenient ways to work with MongoDB documents.

MongoDB stores BSON documents in collections. Mongoose adds an application-level structure and behavior on top.

### Important terms

| Term | Meaning |
|---|---|
| Database | Container for collections |
| Collection | Group of documents, similar to a table |
| Document | One MongoDB record, similar to a row |
| Schema | Defines the shape, types, defaults, and rules for documents |
| Model | Class created from a schema; used to query and modify a collection |
| Instance/document | One object created from a model |
| `_id` | Unique identifier MongoDB normally assigns to each document |
| `ObjectId` | Common BSON type used for MongoDB document IDs and references |

**Remember:** A schema is a definition; a model is the interface to a collection; a document is one record.

---

## 2. Install and connect to MongoDB

### 2.1 Install

```bash
npm install mongoose dotenv
```

For an Express application:

```bash
npm install express mongoose dotenv
```

### 2.2 Configure environment variables

Create `.env`:

```env
PORT=3000
MONGODB_URI=mongodb://127.0.0.1:27017/mern_notes
```

For MongoDB Atlas, use the connection string copied from Atlas and keep its credentials private. URL-encode special characters in a database password when required.

Add `.env` to `.gitignore`:

```gitignore
node_modules/
.env
```

### 2.3 Connect once when the application starts

`src/config/db.js`:

```js
import mongoose from "mongoose";

export async function connectDB() {
  try {
    const connection = await mongoose.connect(process.env.MONGODB_URI);
    console.log(`MongoDB connected: ${connection.connection.host}`);
  } catch (error) {
    console.error("MongoDB connection failed:", error.message);
    throw error; // Let startup fail instead of pretending the DB is available.
  }
}
```

`src/server.js`:

```js
import "dotenv/config";
import express from "express";
import { connectDB } from "./config/db.js";

const app = express();
app.use(express.json());

app.get("/", (req, res) => {
  res.json({ message: "API is running" });
});

const PORT = process.env.PORT || 3000;

try {
  await connectDB();

  app.listen(PORT, () => {
    console.log(`Server listening on port ${PORT}`);
  });
} catch {
  process.exit(1);
}
```

Use `"type": "module"` in `package.json` for the `import` syntax above.

### 2.4 Why connect before `app.listen()`?

This prevents the application from accepting requests before the database connection has been established. Mongoose can buffer operations in some circumstances, but buffering is not a substitute for deliberate startup and failure handling.

### 2.5 Connection lifecycle events

```js
import mongoose from "mongoose";

mongoose.connection.on("connected", () => {
  console.log("Mongoose connected");
});

mongoose.connection.on("disconnected", () => {
  console.warn("Mongoose disconnected");
});

mongoose.connection.on("error", (error) => {
  console.error("MongoDB connection error:", error);
});
```

Register listeners during application setup. In a long-running production service, define a deliberate policy for database outages, readiness checks, and shutdown.

### 2.6 Close the connection gracefully

```js
async function shutdown() {
  try {
    await mongoose.connection.close();
    process.exit(0);
  } catch (error) {
    console.error("Error while closing MongoDB:", error);
    process.exit(1);
  }
}

process.on("SIGINT", shutdown);
process.on("SIGTERM", shutdown);
```

Avoid creating a new connection for every incoming request. Create the connection at application startup and reuse it.

---

## 3. Schema, model, and document

### 3.1 Define a schema

`src/models/User.js`:

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
    trim: true,
    minlength: 3,
  },
  email: {
    type: String,
    required: true,
    lowercase: true,
    trim: true,
  },
  age: {
    type: Number,
    min: 18,
  },
});

export const User = mongoose.model("User", userSchema);
```

Mongoose normally pluralizes and lowercases the model name to determine the collection name. For example, `User` commonly maps to `users`. You can explicitly choose a collection:

```js
const userSchema = new mongoose.Schema(
  { username: String },
  { collection: "app_users" }
);
```

### 3.2 Create a document

```js
const user = new User({
  username: "rajiv",
  email: "rajiv@example.com",
  age: 22,
});

await user.save();
```

### 3.3 Schema vs model vs document

```js
const userSchema = new mongoose.Schema({ username: String }); // definition
const User = mongoose.model("User", userSchema);              // model/class
const user = new User({ username: "Rajiv" });                 // document
await user.save();                                             // persist it
```

### 3.4 Reuse models safely in environments that reload modules

In most normal Node.js applications, one model definition per model name is sufficient. In some hot-reload or test environments, a model may already be registered:

```js
export const User =
  mongoose.models.User || mongoose.model("User", userSchema);
```

Use this pattern only when the environment requires it; it is not mandatory in every project.

---

## 4. Schema field types and options

### 4.1 Common types

```js
const exampleSchema = new mongoose.Schema({
  name: String,
  age: Number,
  isActive: Boolean,
  joinedAt: Date,
  tags: [String],
  scores: [Number],
  metadata: mongoose.Schema.Types.Mixed,
  manager: mongoose.Schema.Types.ObjectId,
  address: {
    city: String,
    country: String,
  },
});
```

Common Mongoose schema types include `String`, `Number`, `Boolean`, `Date`, `Buffer`, `ObjectId`, `Array`, `Map`, `Mixed`, and nested schemas.

### 4.2 Common field options

```js
const productSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, "Product name is required"],
    trim: true,
    minlength: [2, "Name is too short"],
    maxlength: 100,
  },
  price: {
    type: Number,
    required: true,
    min: 0,
    max: 1_000_000,
  },
  category: {
    type: String,
    enum: ["electronics", "books", "clothing"],
    default: "books",
  },
  sku: {
    type: String,
    unique: true, // creates/declares a unique index; not a validator by itself
    lowercase: true,
    trim: true,
  },
  isPublished: {
    type: Boolean,
    default: false,
  },
  createdAt: {
    type: Date,
    default: Date.now, // pass the function, not Date.now()
  },
});
```

Important options:

- `required`: value must be present for normal document validation.
- `default`: value used when the field is `undefined`.
- `enum`: restricts values to an allowed list.
- `min` / `max`: numeric bounds or date bounds.
- `minlength` / `maxlength`: string length bounds.
- `trim`, `lowercase`, `uppercase`: string setters.
- `match`: regular-expression validation for strings.
- `unique`: requests a unique database index; duplicate errors are database errors, not ordinary Mongoose validation errors.
- `select: false`: excludes a field from query results by default; it is not an authorization system.
- `immutable: true`: prevents normal changes to a field after it has been set, with caveats around operations and trusted database access.

### 4.3 Arrays and nested objects

```js
const profileSchema = new mongoose.Schema({
  name: { type: String, required: true },
  skills: {
    type: [String],
    default: [],
  },
  address: {
    city: String,
    pincode: String,
  },
  education: [{
    degree: String,
    institution: String,
    year: Number,
  }],
});
```

Access nested values:

```js
const profile = await Profile.findById(profileId);

console.log(profile.address.city);
console.log(profile.skills[0]);
console.log(profile.education[0]?.degree);
```

For a nested object, a nested schema is useful when it needs its own validation, middleware, or other schema behavior.

### 4.4 Schema options

```js
const userSchema = new mongoose.Schema(
  {
    name: { type: String, required: true },
  },
  {
    timestamps: true,
    versionKey: "__v",
    strict: true,
  }
);
```

- `timestamps: true` automatically manages `createdAt` and `updatedAt`.
- `strict: true` (the normal default) prevents undeclared fields from being persisted through normal Mongoose document operations.
- `versionKey` controls the document version key, commonly `__v`. Do not disable versioning casually if your application relies on concurrency protection.

### 4.5 Custom schema types and Mixed

`Schema.Types.Mixed` accepts arbitrary data, but Mongoose cannot fully track changes inside Mixed values automatically. If you mutate a nested Mixed property, you may need:

```js
doc.metadata.someKey = "updated";
doc.markModified("metadata");
await doc.save();
```

Prefer explicit schemas when the shape is known.

---

## 5. Creating documents

### 5.1 `new Model()` and `save()`

```js
const user = new User({
  username: "rajiv",
  email: "rajiv@example.com",
});

await user.save();
```

`save()` validates the document by default before writing.

### 5.2 `Model.create()`

```js
const user = await User.create({
  username: "rajiv",
  email: "rajiv@example.com",
});
```

For multiple documents:

```js
const users = await User.create([
  { username: "Asha", email: "asha@example.com" },
  { username: "Dev", email: "dev@example.com" },
]);
```

### 5.3 `insertMany()`

```js
const insertedUsers = await User.insertMany([
  { username: "Asha", email: "asha@example.com" },
  { username: "Dev", email: "dev@example.com" },
]);
```

`insertMany()` is designed for batch insertion and is often faster than saving each document individually. It does **not** run normal `save` middleware for each inserted document. Review the options and error behavior for your installed Mongoose version before using it for imports.

### 5.4 Check existence before creating?

A common but race-prone pattern is:

```js
const existing = await User.findOne({ email });

if (!existing) {
  await User.create({ email, username });
}
```

Two requests can both pass the check. For fields that must be unique, create a database unique index and handle duplicate-key errors. A pre-check can improve the user experience, but it cannot replace the unique index.

---

## 6. Reading and querying documents

Most query methods return a Mongoose `Query` that can be awaited. Queries are thenable; do not assume they behave exactly like a normal reusable Promise. Execute a query once, and use `.exec()` when you want an explicit Promise.

### 6.1 Basic read methods

```js
const allUsers = await User.find();
const activeUsers = await User.find({ isActive: true });
const oneUser = await User.findOne({ email: "rajiv@example.com" });
const userById = await User.findById(userId);
const count = await User.countDocuments({ isActive: true });
const exists = await User.exists({ email: "rajiv@example.com" });
```

- `find()` returns an array, possibly empty.
- `findOne()` returns one document or `null`.
- `findById(id)` is shorthand for finding by `_id`.
- `countDocuments(filter)` counts matching documents.
- `estimatedDocumentCount()` estimates the total collection count and does not accept a filter.

### 6.2 Comparison operators

```js
// age > 18
await User.find({ age: { $gt: 18 } });

// age >= 18 and age <= 30
await User.find({ age: { $gte: 18, $lte: 30 } });

// age is not 18
await User.find({ age: { $ne: 18 } });

// age is one of these values
await User.find({ age: { $in: [18, 21, 25] } });

// age is not in this list
await User.find({ age: { $nin: [18, 21] } });
```

Common comparison operators: `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`, `$in`, `$nin`.

### 6.3 Logical operators

```js
await User.find({
  $and: [
    { isActive: true },
    { age: { $gte: 18 } },
  ],
});

await User.find({
  $or: [
    { role: "admin" },
    { score: { $gte: 90 } },
  ],
});

await User.find({
  $nor: [
    { isBanned: true },
    { isDeleted: true },
  ],
});
```

Implicit AND is usually simpler:

```js
await User.find({ isActive: true, age: { $gte: 18 } });
```

### 6.4 Field existence, arrays, and strings

```js
// Field exists
await User.find({ phone: { $exists: true } });

// Array contains a value
await User.find({ skills: "Node.js" });

// Array contains all specified values
await User.find({ skills: { $all: ["Node.js", "MongoDB"] } });

// Array has at least one object matching all conditions
await User.find({
  education: { $elemMatch: { degree: "MCA", year: { $gte: 2025 } } },
});

// Regular expression search (case-insensitive)
await User.find({ username: { $regex: "raj", $options: "i" } });
```

Be careful with regex queries on large collections; they may be expensive and may not use indexes effectively, depending on the pattern.

### 6.5 Select specific fields

```js
const users = await User.find()
  .select("username email -_id")
  .exec();
```

Alternative projection object:

```js
await User.find({}, { username: 1, email: 1 });
```

Inclusion and exclusion projections generally cannot be mixed, except for excluding `_id` from an inclusion projection.

### 6.6 Sort, limit, and skip

```js
const users = await User.find({ isActive: true })
  .sort({ createdAt: -1 }) // newest first; 1 = ascending, -1 = descending
  .skip(20)
  .limit(10)
  .exec();
```

Large `skip()` values can be inefficient. For deep pagination, consider cursor/range pagination.

### 6.7 Query chaining

```js
const users = await User.find()
  .where("age").gte(18).lte(30)
  .where("isActive").equals(true)
  .select("username age")
  .sort({ age: 1 })
  .limit(20)
  .exec();
```

### 6.8 Null and missing fields

MongoDB queries such as `{ field: null }` can match documents where the field is `null` **or missing**. Use `$exists` as well if you need to distinguish those cases.

```js
// Explicitly null
await User.find({ middleName: { $type: 10 } });

// Missing
await User.find({ middleName: { $exists: false } });
```

---

## 7. Updating documents

### 7.1 Load a document, change it, save it

```js
const user = await User.findById(userId);

if (!user) {
  throw new Error("User not found");
}

user.username = "new_username";
user.age = 23;

await user.save();
```

Advantages: document validation and `save` middleware run. This is useful when business logic depends on the loaded document.

### 7.2 `updateOne()`

```js
const result = await User.updateOne(
  { _id: userId },
  { $set: { username: "new_username" } },
  { runValidators: true }
);

console.log(result.matchedCount);
console.log(result.modifiedCount);
```

### 7.3 `findByIdAndUpdate()`

```js
const updatedUser = await User.findByIdAndUpdate(
  userId,
  { $set: { username: "new_username" } },
  {
    new: true,          // return updated document in common Mongoose versions
    runValidators: true,
  }
);
```

`returnDocument: "after"` is the newer, explicit option in supported Mongoose versions; check your installed version. Many codebases still use `new: true`.

**Important:** Update validators are not enabled by default for common update operations. Use `runValidators: true` when appropriate. Update validators have different behavior from full document validation and do not validate every possible update scenario.

### 7.4 Update operators

```js
// Replace/set selected fields
await User.updateOne({ _id: userId }, {
  $set: { isActive: true },
});

// Remove a field
await User.updateOne({ _id: userId }, {
  $unset: { temporaryCode: 1 },
});

// Increment a number
await User.updateOne({ _id: userId }, {
  $inc: { loginCount: 1 },
});

// Add a value only if it is not already present
await User.updateOne({ _id: userId }, {
  $addToSet: { skills: "MongoDB" },
});

// Add a value, allowing duplicates
await User.updateOne({ _id: userId }, {
  $push: { skills: "MongoDB" },
});

// Remove matching array values
await User.updateOne({ _id: userId }, {
  $pull: { skills: "OldSkill" },
});
```

### 7.5 Upsert

`upsert: true` inserts a document if no document matches the filter:

```js
const result = await User.updateOne(
  { email: "asha@example.com" },
  { $set: { username: "asha" }, $setOnInsert: { isActive: true } },
  { upsert: true, runValidators: true }
);
```

Be careful with untrusted filters and update objects. Do not pass arbitrary client-supplied MongoDB operators directly into database operations.

### 7.6 Replace vs update

- `updateOne()` changes selected fields/operators.
- `replaceOne()` replaces the document's non-`_id` content with the replacement document.
- `findOneAndUpdate()` / `findByIdAndUpdate()` can return the document before or after the update, depending on options.

Avoid replacing a document when you only intend to change one field.

---

## 8. Deleting documents

```js
// Delete one matching document
const result = await User.deleteOne({ _id: userId });

// Delete every matching document
const manyResult = await User.deleteMany({ isActive: false });

// Find and return the deleted document
const deletedUser = await User.findByIdAndDelete(userId);
```

Check `deletedCount` where relevant:

```js
console.log(result.deletedCount);
```

Deletion is permanent unless your application implements a backup or soft-delete policy. A common soft-delete design uses fields such as `isDeleted` and `deletedAt`, then ensures all relevant queries consistently filter deleted records.

---

## 9. Validation and custom validation

### 9.1 Built-in validators

```js
const employeeSchema = new mongoose.Schema({
  name: { type: String, required: true, trim: true, minlength: 2 },
  age: { type: Number, min: 18, max: 65 },
  department: { type: String, enum: ["engineering", "sales", "hr"] },
  email: {
    type: String,
    required: true,
    lowercase: true,
    match: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
  },
});
```

This email regex is a basic format check, not proof that the address exists or belongs to the user.

### 9.2 Custom validator

```js
const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
    validate: {
      validator(value) {
        return value.length >= 3 && !value.includes(" ");
      },
      message: "Username must be at least 3 characters and contain no spaces",
    },
  },
});
```

### 9.3 Validate explicitly

```js
const user = new User({ username: "ab" });

try {
  await user.validate();
} catch (error) {
  console.log(error.name); // ValidationError
  console.log(error.errors); // field-specific validation errors
}
```

### 9.4 Validation errors vs duplicate-key errors

- Mongoose validation failures usually have `error.name === "ValidationError"`.
- Duplicate key violations commonly have MongoDB error code `11000`.
- Cast errors occur when a value cannot be converted to the schema type, such as an invalid ObjectId.

Handle these separately in your API.

---

## 10. Schema methods, statics, and query helpers

### 10.1 Instance methods

An instance method operates on one document:

```js
userSchema.methods.getDisplayName = function () {
  return this.username;
};

const user = await User.findById(userId);
console.log(user?.getDisplayName());
```

Use a regular `function` when you need Mongoose's `this`; an arrow function does not bind its own `this`.

### 10.2 Static methods

A static belongs to the model:

```js
userSchema.statics.findActive = function () {
  return this.find({ isActive: true });
};

// Usage:
const activeUsers = await User.findActive();
```

### 10.3 Query helpers

A query helper extends query chaining:

```js
userSchema.query.active = function () {
  return this.where({ isActive: true });
};

// Usage:
const activeUsers = await User.find().active();
```

Define methods, statics, and query helpers **before** compiling the schema into a model.

---

## 11. Middleware / hooks

Middleware lets you run logic before or after selected Mongoose operations. The exact hook behavior depends on whether the operation is document, query, model, or aggregate middleware.

### 11.1 `pre('save')`

```js
userSchema.pre("save", function () {
  this.username = this.username.trim();
});
```

An async pre-hook can also be used:

```js
userSchema.pre("save", async function () {
  // await an operation if required
});
```

Do not use an arrow function if you need document `this`.

### 11.2 `post('save')`

```js
userSchema.post("save", function (doc) {
  console.log("Saved user:", doc._id);
});
```

### 11.3 Query middleware

```js
userSchema.pre("find", function () {
  // Example soft-delete filter. Apply only if your app's policy requires it.
  this.where({ isDeleted: { $ne: true } });
});
```

This affects operations using the relevant `find` query hook, but not every possible way of reading data. Do not rely on one hook as your only authorization or data-isolation boundary.

### 11.4 `save()` middleware is not the same as update middleware

`findOneAndUpdate()` does not run document `save` middleware. If you need hooks for it, use the appropriate query middleware such as:

```js
userSchema.pre("findOneAndUpdate", function () {
  // this is the query, not the document being updated
});
```

Update middleware does not automatically provide a fully hydrated document as `this`. If business rules require document state and full validation, load the document, change it, and call `save()`.

### 11.5 Middleware ordering

Register middleware before calling `mongoose.model()`. Hooks added after model compilation may not apply as expected.

---

## 12. Relationships, ObjectId, populate

MongoDB supports embedded documents and references. Choose based on access patterns, document growth, and consistency requirements.

### 12.1 Reference one user from a post

```js
// models/User.js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
});

export const User = mongoose.model("User", userSchema);
```

```js
// models/Post.js
import mongoose from "mongoose";

const postSchema = new mongoose.Schema(
  {
    title: { type: String, required: true },
    body: { type: String, required: true },
    author: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User",
      required: true,
    },
  },
  { timestamps: true }
);

export const Post = mongoose.model("Post", postSchema);
```

### 12.2 Populate a reference

```js
const posts = await Post.find()
  .populate("author", "name email")
  .exec();
```

Example result shape (simplified):

```js
{
  title: "Learning Mongoose",
  author: {
    _id: "...",
    name: "Rajiv",
    email: "rajiv@example.com"
  }
}
```

`populate()` performs additional query work to replace references with matching documents. It is not the same as a relational SQL join and does not automatically enforce foreign-key constraints.

### 12.3 Nested populate

```js
const comments = await Comment.find()
  .populate({
    path: "post",
    select: "title author",
    populate: {
      path: "author",
      select: "name",
    },
  });
```

### 12.4 Populate with match

```js
const posts = await Post.find().populate({
  path: "author",
  match: { isActive: true },
  select: "name",
});
```

If an author does not match, a single reference generally becomes `null`; an array reference generally becomes an empty array. `match` does not filter out the parent post itself.

### 12.5 Embed vs reference

**Embed** when:
- Child data is small and naturally belongs to the parent.
- It is usually read with the parent.
- The child does not need an independent lifecycle.

**Reference** when:
- The related document is large or shared by many documents.
- It has its own lifecycle or is queried independently.
- Embedding would cause unbounded document growth.

Avoid unbounded arrays of references in one document. For large one-to-many relationships, storing the parent reference on the “many” side is often more scalable.

### 12.6 Many-to-many

A common pattern is an array of ObjectIds:

```js
const courseSchema = new mongoose.Schema({
  title: String,
  students: [{ type: mongoose.Schema.Types.ObjectId, ref: "User" }],
});
```

For a large relationship set or when the relationship has its own data (role, enrollment date, status), use a separate join/association collection.

---

## 13. Aggregation pipeline

Aggregation processes documents through ordered stages. Each stage receives the output of the previous stage.

```js
const result = await Order.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$customer", totalSpent: { $sum: "$amount" } } },
  { $sort: { totalSpent: -1 } },
]);
```

**Important:** Mongoose aggregation results are plain JavaScript objects, not hydrated Mongoose documents. Mongoose does not automatically cast aggregation pipeline values the same way it casts normal query filters. For example, convert a string ID to an ObjectId yourself.

### 13.1 Sample schema for examples

```js
const orderSchema = new mongoose.Schema({
  customer: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    required: true,
  },
  items: [{
    product: { type: mongoose.Schema.Types.ObjectId, ref: "Product" },
    quantity: { type: Number, min: 1 },
    price: { type: Number, min: 0 },
  }],
  amount: { type: Number, min: 0, required: true },
  status: {
    type: String,
    enum: ["pending", "completed", "cancelled"],
    default: "pending",
  },
  createdAt: { type: Date, default: Date.now },
});
```

### 13.2 `$match` — filter documents

```js
const orders = await Order.aggregate([
  { $match: { status: "completed", amount: { $gte: 100 } } },
]);
```

Put selective `$match` stages early where practical so later stages process fewer documents.

### 13.3 `$group` — group and calculate

```js
const stats = await Order.aggregate([
  {
    $group: {
      _id: "$status",
      totalOrders: { $sum: 1 },
      totalRevenue: { $sum: "$amount" },
      averageOrder: { $avg: "$amount" },
      highestOrder: { $max: "$amount" },
      lowestOrder: { $min: "$amount" },
    },
  },
]);
```

- `_id` is the group key. Use `null` to aggregate all documents into one group.
- `$sum: 1` counts documents in each group.
- Other common accumulators: `$avg`, `$min`, `$max`, `$push`, `$addToSet`, `$first`, `$last`.

### 13.4 `$project` — shape output

```js
const result = await Order.aggregate([
  {
    $project: {
      status: 1,
      amount: 1,
      amountWithTax: { $multiply: ["$amount", 1.18] },
      _id: 0,
    },
  },
]);
```

`$project` can include/exclude fields and compute new fields. Inclusion and exclusion cannot normally be mixed except for `_id`.

### 13.5 `$sort`, `$skip`, `$limit`

```js
const result = await Order.aggregate([
  { $match: { status: "completed" } },
  { $sort: { createdAt: -1, _id: -1 } },
  { $skip: 0 },
  { $limit: 10 },
]);
```

Use a stable secondary sort key such as `_id` when consistent ordering matters.

### 13.6 `$unwind` — expand array items

Given an order with `items: [{ product, quantity, price }, ...]`:

```js
const result = await Order.aggregate([
  { $unwind: "$items" },
  {
    $project: {
      orderId: "$_id",
      product: "$items.product",
      quantity: "$items.quantity",
      lineTotal: { $multiply: ["$items.quantity", "$items.price"] },
    },
  },
]);
```

Each array element becomes a separate pipeline document. Empty or missing arrays are dropped by default; use `preserveNullAndEmptyArrays: true` when appropriate.

### 13.7 `$lookup` — join collections

```js
const result = await Order.aggregate([
  {
    $lookup: {
      from: "users",        // actual collection name
      localField: "customer",
      foreignField: "_id",
      as: "customerDetails",
    },
  },
  {
    $unwind: {
      path: "$customerDetails",
      preserveNullAndEmptyArrays: true,
    },
  },
  {
    $project: {
      amount: 1,
      status: 1,
      "customerDetails.name": 1,
      "customerDetails.email": 1,
    },
  },
]);
```

The `from` field uses the **collection name**, not necessarily the Mongoose model name. `$lookup` usually produces an array; `$unwind` can convert a one-element array to an object.

### 13.8 `$addFields` / `$set`

```js
const result = await Order.aggregate([
  {
    $set: {
      isLargeOrder: { $gte: ["$amount", 1000] },
      amountWithTax: { $multiply: ["$amount", 1.18] },
    },
  },
]);
```

`$set` adds or overwrites fields in pipeline documents; `$addFields` is an equivalent stage alias in common MongoDB versions.

### 13.9 `$count`

```js
const result = await Order.aggregate([
  { $match: { status: "completed" } },
  { $count: "completedOrders" },
]);
```

The output is an array. If no documents match, the result is an empty array rather than an object with a zero count.

### 13.10 `$facet` — multiple aggregations in one pipeline

Useful for returning a page of records and a total count together:

```js
const [result] = await Order.aggregate([
  { $match: { status: "completed" } },
  {
    $facet: {
      data: [
        { $sort: { createdAt: -1, _id: -1 } },
        { $skip: 0 },
        { $limit: 10 },
        { $project: { amount: 1, status: 1, createdAt: 1 } },
      ],
      metadata: [{ $count: "total" }],
    },
  },
]);

const orders = result.data;
const total = result.metadata[0]?.total ?? 0;
```

`$facet` can consume substantial memory for large data sets. Design and index the filtering stages carefully.

### 13.11 `$bucket` — group numeric ranges

```js
const result = await Order.aggregate([
  {
    $bucket: {
      groupBy: "$amount",
      boundaries: [0, 100, 500, 1000, 5000],
      default: "5000+",
      output: {
        count: { $sum: 1 },
        revenue: { $sum: "$amount" },
      },
    },
  },
]);
```

Buckets use lower-inclusive, upper-exclusive boundaries. Confirm that the boundaries match the business meaning you need.

### 13.12 `$sortByCount`

```js
const result = await Post.aggregate([
  { $unwind: "$tags" },
  { $sortByCount: "$tags" },
]);
```

This is shorthand for grouping by a value, counting, and sorting by count descending.

### 13.13 `$merge` and `$out`

These stages write aggregation results to a collection. They are useful for materialized summaries and reporting, but they have important write and deployment implications. Read the MongoDB version-specific documentation before using them in production.

### 13.14 Aggregation pipeline design tips

1. Filter early with `$match`.
2. Use indexes that support the early filter and sort.
3. Project away fields you do not need when it improves processing and clarity.
4. Understand whether `$unwind` changes the count of documents.
5. Remember that aggregate output is plain data; schema getters, document methods, and normal document validation do not automatically apply.
6. Convert string IDs to ObjectIds explicitly when matching ObjectId fields.
7. Inspect query plans with MongoDB explain tools when performance matters.
8. Respect MongoDB memory and document-size limits; use supported disk-spill options only when needed and available in your server version.

### 13.15 Casting an ObjectId in aggregation

```js
const { Types } = mongoose;

const customerId = new Types.ObjectId(customerIdString);

const orders = await Order.aggregate([
  { $match: { customer: customerId } },
]);
```

Validate that the string is a valid ObjectId before constructing one.

---

## 14. Indexes and performance

Indexes help MongoDB find and sort documents without scanning the entire collection. Indexes use disk and memory and add work to inserts, updates, and deletes, so create indexes based on actual query patterns.

### 14.1 Define indexes

```js
userSchema.index({ email: 1 }, { unique: true });
userSchema.index({ createdAt: -1 });
userSchema.index({ role: 1, createdAt: -1 });
```

A compound index's field order matters. An index `{ role: 1, createdAt: -1 }` is particularly useful for queries that filter by `role` and sort by `createdAt`.

### 14.2 Unique index

```js
userSchema.index({ email: 1 }, { unique: true });
```

Normalize values consistently if uniqueness should be case-insensitive. A unique index cannot be created successfully if existing data violates its constraints. In production, manage index creation through a deliberate migration/deployment process rather than assuming application startup will safely create every index.

### 14.3 Text index (basic example)

```js
postSchema.index({ title: "text", body: "text" });

const posts = await Post.find({
  $text: { $search: "mongoose aggregation" },
});
```

MongoDB text indexes have specific language and query behavior. For advanced search requirements, evaluate MongoDB Search or a dedicated search system.

### 14.4 Explain query plans

```js
const plan = await User.find({ email: "rajiv@example.com" }).explain("executionStats");
console.dir(plan, { depth: null });
```

Look for examined keys/documents, returned document count, and whether the plan uses an appropriate index. A collection scan (`COLLSCAN`) is not automatically wrong for tiny collections, but can be a warning for large ones.

### 14.5 General performance practices

- Select only required fields.
- Use pagination and stable sort keys.
- Index frequent filters and sorts, not every field.
- Avoid unbounded `$regex` searches on large collections.
- Avoid unnecessary `populate()` chains and N+1 query patterns.
- Use `lean()` for read-only endpoints when you do not need document behavior.
- Measure real queries; do not add indexes solely by intuition.

---

## 15. Pagination, sorting, and field selection

### 15.1 Page-based pagination

```js
const page = Math.max(1, Number(req.query.page) || 1);
const limit = Math.min(50, Math.max(1, Number(req.query.limit) || 10));
const skip = (page - 1) * limit;

const [data, total] = await Promise.all([
  User.find({ isActive: true })
    .select("username email createdAt")
    .sort({ createdAt: -1, _id: -1 })
    .skip(skip)
    .limit(limit)
    .lean(),
  User.countDocuments({ isActive: true }),
]);

res.json({
  data,
  pagination: {
    page,
    limit,
    total,
    totalPages: Math.ceil(total / limit),
  },
});
```

Validate query-string values: `req.query` is user-controlled input. The example clamps the page size to a maximum of 50.

### 15.2 Range/cursor pagination

For deep pages, use a last-seen sort value. Example using `_id` as a descending cursor:

```js
const filter = lastId
  ? { _id: { $lt: new mongoose.Types.ObjectId(lastId) } }
  : {};

const data = await User.find(filter)
  .sort({ _id: -1 })
  .limit(20)
  .lean();
```

Validate `lastId` first. If sorting by a non-unique field, use a stable compound cursor (for example, `createdAt` plus `_id`) to avoid skipped or duplicated results.

---

## 16. Lean queries and performance

Normal Mongoose queries return hydrated documents with methods, getters/setters, change tracking, and `.save()`.

```js
const user = await User.findById(userId); // Mongoose document
user.username = "updated";
await user.save();
```

`lean()` returns plain JavaScript objects:

```js
const users = await User.find().lean();
```

Advantages:
- Lower hydration overhead for read-heavy endpoints.
- Often lower memory usage.
- Convenient when you only serialize results.

Limitations:
- No document `.save()`.
- No normal document change tracking.
- Document methods, getters/setters, and virtual behavior may not be available in the same way unless supported by additional configuration/plugins.
- It does not make a slow, unindexed database query automatically fast.

Use `lean()` for read-only API responses after checking which schema features your response relies on.

---

## 17. Transactions and sessions

A transaction lets multiple database operations commit or roll back together when supported by the MongoDB deployment (for example, a replica set or supported sharded cluster). A standalone local MongoDB server may not support multi-document transactions.

```js
const session = await mongoose.startSession();

try {
  await session.withTransaction(async () => {
    await Account.updateOne(
      { _id: fromAccountId },
      { $inc: { balance: -100 } },
      { session }
    );

    await Account.updateOne(
      { _id: toAccountId },
      { $inc: { balance: 100 } },
      { session }
    );

    await Transfer.create(
      [{
        from: fromAccountId,
        to: toAccountId,
        amount: 100,
      }],
      { session }
    );
  });
} finally {
  await session.endSession();
}
```

This is a simplified illustration, **not a complete money-transfer implementation**. Production transfers must validate balances, prevent overdrafts and duplicate requests, and enforce all business rules within a transaction-safe design.

Important:
- Pass the session to every operation that must participate.
- Keep transactions short.
- Do not perform external side effects such as sending email inside a transaction callback that the driver may retry. Use an outbox or another idempotent pattern.
- Handle transaction errors and retry behavior according to the MongoDB driver documentation.

---

## 18. Virtuals, aliases, timestamps, and serialization

### 18.1 Virtual property

A virtual is computed from document data and is not stored as a normal database field.

```js
const personSchema = new mongoose.Schema(
  {
    firstName: String,
    lastName: String,
  },
  {
    toJSON: { virtuals: true },
    toObject: { virtuals: true },
  }
);

personSchema.virtual("fullName").get(function () {
  return `${this.firstName} ${this.lastName}`;
});
```

Usage:

```js
const person = await Person.findById(id);
console.log(person.fullName);
```

### 18.2 Virtual populate

```js
userSchema.virtual("posts", {
  ref: "Post",
  localField: "_id",
  foreignField: "author",
});

const user = await User.findById(userId).populate("posts");
```

Virtual populate is useful when a parent should expose related records without storing an ever-growing array of child IDs on the parent. Configure virtual serialization if you want it included in JSON output.

### 18.3 Timestamps

```js
const schema = new mongoose.Schema(
  { title: String },
  { timestamps: true }
);
```

Mongoose manages `createdAt` and `updatedAt` for supported operations. If your database has custom timestamp field names, the option can be configured.

### 18.4 Transform JSON output

```js
const userSchema = new mongoose.Schema(
  {
    username: String,
    passwordHash: String,
  },
  {
    toJSON: {
      transform(doc, ret) {
        delete ret.passwordHash;
        delete ret.__v;
        return ret;
      },
    },
  }
);
```

Do not rely on serialization transforms as the only protection for secrets. Prefer explicit field selection and DTO/response shaping too.

---

## 19. Discriminators and subdocuments

### 19.1 Discriminators

Discriminators let several model types share one collection and base schema while adding type-specific fields.

```js
const eventSchema = new mongoose.Schema(
  { time: { type: Date, default: Date.now } },
  { discriminatorKey: "kind", timestamps: true }
);

const Event = mongoose.model("Event", eventSchema);

const ClickedLink = Event.discriminator(
  "ClickedLink",
  new mongoose.Schema({ url: { type: String, required: true } })
);

const SignedUp = Event.discriminator(
  "SignedUp",
  new mongoose.Schema({ user: { type: mongoose.Schema.Types.ObjectId, ref: "User" } })
);
```

### 19.2 Subdocuments

```js
const addressSchema = new mongoose.Schema({
  city: String,
  country: String,
});

const customerSchema = new mongoose.Schema({
  name: String,
  address: addressSchema,
  addresses: [addressSchema],
});
```

A single nested subdocument and an array of subdocuments can have their own validation and middleware. An array subdocument generally has its own `_id` by default; disable it with `{ _id: false }` if you do not need one.

---

## 20. Express integration and error handling

### 20.1 Basic route example

```js
import express from "express";
import mongoose from "mongoose";
import { User } from "./models/User.js";

const router = express.Router();

router.post("/users", async (req, res, next) => {
  try {
    const { username, email } = req.body;

    const user = await User.create({ username, email });

    res.status(201).json({
      message: "User created",
      data: {
        id: user._id,
        username: user.username,
        email: user.email,
      },
    });
  } catch (error) {
    next(error);
  }
});

router.get("/users/:id", async (req, res, next) => {
  try {
    if (!mongoose.isValidObjectId(req.params.id)) {
      return res.status(400).json({ message: "Invalid user ID" });
    }

    const user = await User.findById(req.params.id)
      .select("username email createdAt")
      .lean();

    if (!user) {
      return res.status(404).json({ message: "User not found" });
    }

    res.json({ data: user });
  } catch (error) {
    next(error);
  }
});

export default router;
```

### 20.2 Centralized error middleware

Put this **after** routes:

```js
app.use((err, req, res, next) => {
  if (res.headersSent) {
    return next(err);
  }

  if (err.name === "ValidationError") {
    return res.status(400).json({
      message: "Validation failed",
      errors: Object.values(err.errors).map((item) => item.message),
    });
  }

  if (err.name === "CastError") {
    return res.status(400).json({ message: "Invalid value for a database field" });
  }

  if (err.code === 11000) {
    return res.status(409).json({ message: "A record with this value already exists" });
  }

  console.error(err);
  return res.status(500).json({ message: "Internal server error" });
});
```

In production, log useful diagnostic details securely, but do not expose stack traces, database credentials, raw query data, or internal error messages to clients.

### 20.3 Avoid mass assignment

Unsafe pattern:

```js
// Avoid: client can submit fields you did not intend to allow.
await User.create(req.body);
```

Safer pattern:

```js
const { username, email } = req.body;
await User.create({ username, email });
```

Use request validation (for example, Zod), allowlists for update fields, and authorization checks before accessing or changing records.

---

## 21. Security and production best practices

1. **Validate input:** validate request body, route parameters, query parameters, and ObjectIds.
2. **Allowlist update fields:** do not accept arbitrary MongoDB operators from clients.
3. **Use authorization:** a valid ObjectId does not prove that the requester owns the document.
4. **Protect secrets:** keep connection strings in environment variables or a secret manager.
5. **Create real unique indexes:** `unique: true` is not a validator; handle error code `11000`.
6. **Do not return password hashes or tokens:** use explicit projections and safe response objects.
7. **Use least-privilege database credentials:** application credentials should not have unnecessary administrative permissions.
8. **Configure network access:** restrict MongoDB access to trusted networks/services.
9. **Use TLS:** especially for remote connections; follow Atlas or your hosting provider's connection guidance.
10. **Limit result sizes:** clamp user-provided pagination limits.
11. **Avoid unbounded queries:** do not expose unrestricted `find()` on large collections.
12. **Design indexes intentionally:** monitor query plans and index size.
13. **Handle outages:** implement startup failure, readiness checks, logging, and graceful shutdown.
14. **Manage schema changes:** plan migrations for existing records and index changes.
15. **Avoid logging sensitive data:** especially credentials, tokens, password hashes, and personal information.
16. **Know your Mongoose version:** options and middleware details can change between major versions.

---

## 22. Common mistakes and troubleshooting

### `MongooseServerSelectionError`

Possible causes:
- MongoDB is not running locally.
- The URI or credentials are incorrect.
- Atlas IP/network access is not configured.
- DNS, firewall, TLS, or network connectivity problems.

Check the URI, database availability, network rules, and server logs. Do not paste secrets into public logs or code.

### `ValidationError`

A document failed schema validation. Inspect:

```js
console.log(error.errors);
```

Fix the input or the schema rule as appropriate.

### `CastError`

A value cannot be cast to the schema type, commonly an invalid ObjectId. Validate IDs before querying:

```js
if (!mongoose.isValidObjectId(id)) {
  // Respond with HTTP 400.
}
```

### `E11000 duplicate key error`

A unique index rejected a duplicate value. Handle it as a conflict, often HTTP `409`, and ensure your data normalization matches the intended uniqueness rule.

### `populate()` returns `null`

Check:
- Is the referenced document present?
- Does `ref` match the registered model name?
- Is the stored reference a valid ObjectId?
- Did a `match` condition exclude the referenced record?
- Are you populating the correct path?

### Update validators are not running

Use `{ runValidators: true }` where supported and remember update validators differ from full document validation. For complex business rules, load the document and use `save()`.

### Hook is not running

Check whether the operation is `save`, `updateOne`, `findOneAndUpdate`, `insertMany`, or aggregation. Different operation types trigger different middleware. Register middleware before model compilation.

### Aggregation returns no matches

Check the actual stored field names and BSON types. Aggregation pipeline values are not automatically cast like normal Mongoose query filters. Convert string IDs to `ObjectId` when required.

### `unique: true` does not give a normal validation error

`unique` declares an index. The MongoDB server enforces it, and duplicates usually produce error code `11000`. Make sure the index exists and handle the database error.

### Data is not being saved

Check that:
- You awaited `save()`, `create()`, or the update operation.
- The connection is established.
- The code path reaches the database operation.
- Validation did not fail.
- You are querying the expected database and collection.
- Errors are logged or passed to error middleware.

---

## 23. Quick reference cheat sheet

| Task | Example |
|---|---|
| Create one | `await User.create({ username, email })` |
| Find all | `await User.find()` |
| Find by filter | `await User.find({ isActive: true })` |
| Find one | `await User.findOne({ email })` |
| Find by ID | `await User.findById(id)` |
| Count | `await User.countDocuments(filter)` |
| Update one | `await User.updateOne(filter, { $set: { isActive: true } })` |
| Find and update | `await User.findByIdAndUpdate(id, update, { new: true, runValidators: true })` |
| Delete one | `await User.deleteOne({ _id: id })` |
| Delete and return | `await User.findByIdAndDelete(id)` |
| Select fields | `User.find().select("username email")` |
| Sort | `User.find().sort({ createdAt: -1 })` |
| Limit | `User.find().limit(10)` |
| Populate | `Post.find().populate("author", "name email")` |
| Lean read | `User.find().lean()` |
| Aggregate | `Order.aggregate([{ $match: { status: "completed" } }])` |
| Validate ID | `mongoose.isValidObjectId(id)` |
| Validate document | `await doc.validate()` |
| Save changes | `doc.field = value; await doc.save()` |

### Useful MongoDB update operators

| Operator | Purpose |
|---|---|
| `$set` | Set a field |
| `$unset` | Remove a field |
| `$inc` | Increment/decrement a number |
| `$mul` | Multiply a number |
| `$min` / `$max` | Set only if lower/higher than current value |
| `$push` | Append to an array |
| `$addToSet` | Add to array only if not already present |
| `$pull` | Remove matching array elements |
| `$pop` | Remove first or last array item |
| `$currentDate` | Set a field to the current date/time |

### Useful aggregation stages

| Stage | Purpose |
|---|---|
| `$match` | Filter documents |
| `$project` | Select or compute fields |
| `$set` / `$addFields` | Add or replace fields |
| `$group` | Group and calculate |
| `$sort` | Sort documents |
| `$skip` / `$limit` | Pagination |
| `$unwind` | Expand array elements |
| `$lookup` | Join another collection |
| `$count` | Count pipeline results |
| `$facet` | Run multiple sub-pipelines |
| `$bucket` | Group values into ranges |
| `$sortByCount` | Group and sort by count |
| `$replaceRoot` / `$replaceWith` | Replace the current document shape |
| `$merge` / `$out` | Write pipeline results to a collection |

---

## 24. Interview questions

### 1. What is Mongoose?
An ODM for MongoDB and Node.js that provides schemas, models, validation, middleware, and query APIs.

### 2. What is the difference between a schema and a model?
A schema defines a document's structure and rules. A model is compiled from a schema and provides methods for creating, querying, updating, and deleting documents.

### 3. What is the difference between `find()` and `findOne()`?
`find()` returns an array of matching documents. `findOne()` returns one matching document or `null`.

### 4. What is the difference between `save()` and `findByIdAndUpdate()`?
`save()` operates on a hydrated document and runs document save middleware and normal document validation. `findByIdAndUpdate()` is a query update, has different middleware behavior, and requires `runValidators: true` for update validators.

### 5. What does `populate()` do?
It resolves stored references to documents from another collection. It is convenient but is not a database foreign-key constraint.

### 6. What is aggregation?
A pipeline that transforms and calculates data using stages such as `$match`, `$group`, `$project`, `$lookup`, and `$sort`.

### 7. What is the difference between `populate()` and `$lookup`?
`populate()` is a Mongoose feature that performs reference resolution. `$lookup` is a MongoDB aggregation stage that joins data inside an aggregation pipeline. Choose based on query shape, performance, and transformation needs.

### 8. What is `lean()`?
It tells Mongoose to return plain objects instead of hydrated documents, reducing overhead for many read-only use cases.

### 9. What does `unique: true` do?
It declares a unique index. The database enforces uniqueness; duplicates commonly raise error code `11000`.

### 10. What is middleware?
Functions executed around selected Mongoose operations. Document, query, model, and aggregate middleware have different contexts and behavior.

### 11. What is an index?
A data structure that can speed up queries and sorting at the cost of extra storage and write overhead.

### 12. Why use transactions?
To group supported database operations so they commit or roll back together. Transactions require a supported MongoDB deployment and careful session handling.

### 13. What is an ObjectId?
A BSON identifier type commonly used for MongoDB document `_id` fields and references.

### 14. Does Mongoose cast aggregation values automatically?
Do not assume so. Aggregation pipeline values are not automatically cast like normal Mongoose query filters; convert values such as ObjectIds explicitly when needed.

### 15. How do you prevent mass assignment?
Validate request data, allowlist accepted fields, avoid passing `req.body` or client-provided filters directly into database operations, and enforce authorization.

---

## Recommended learning order

1. Connect to MongoDB and define a schema/model.
2. Practice CRUD and query operators.
3. Learn validation, update operators, and error handling.
4. Build references and use `populate()`.
5. Learn aggregation stages with a small sample collection.
6. Add indexes and pagination; inspect query plans.
7. Learn middleware, virtuals, lean queries, and transactions.
8. Apply security, testing, and migration practices in an Express API.

**Final reminder:** Mongoose makes database access more convenient, but it does not replace database design, input validation, authorization, indexes, or MongoDB's own operational requirements.
