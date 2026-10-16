# Multer — Easy Notes for Express.js

## 1. What is Multer?

**Multer** is Express.js middleware used to handle file uploads sent using `multipart/form-data`.

Common examples:
- Uploading a profile photo.
- Uploading documents or PDFs.
- Uploading images for a post.

Multer processes the uploaded file and makes its information available on the request object, such as `req.file` or `req.files`.

Detailed reference: [Multer on npm](https://www.npmjs.com/package/multer)

## 2. Install Multer

```bash
npm install multer
```

For an ES module project:

```js
import multer from "multer";
```

For CommonJS:

```js
const multer = require("multer");
```

Use the import style that matches your project's configuration.

## 3. Basic single-file upload

This example uses Multer's default memory storage.

```js
import express from "express";
import multer from "multer";

const app = express();
const upload = multer();

app.post("/upload", upload.single("image"), (req, res) => {
    console.log(req.file);
    res.json({
        message: "File received successfully",
        filename: req.file.originalname
    });
});

app.listen(5000, () => {
    console.log("Server running on port 5000");
});
```

### How does it work?

1. The client sends a file in a `multipart/form-data` request.
2. `upload.single("image")` tells Multer to expect one file in the field named `image`.
3. Multer processes the file and adds its details to `req.file`.
4. The route sends a response.

**Important:** The form field name must match `"image"`.

Example HTML form:

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="file" name="image" />
    <button type="submit">Upload</button>
</form>
```

The `enctype="multipart/form-data"` setting is required for a normal HTML form file upload.

## 4. Default storage vs. `diskStorage`

### A. Default Multer storage (memory)

```js
const upload = multer();
```

By default, Multer uses memory storage.

- File content is available as `req.file.buffer`.
- Multer does not save the file to a local file.
- Convenient when you want to upload a small file directly to a cloud service.
- Large or frequent uploads can consume significant RAM.

### B. `diskStorage` (save to server disk)

```js
import multer from "multer";

const storage = multer.diskStorage({
    destination: "./uploads",
    filename: (req, file, cb) => {
        cb(null, `${Date.now()}-${file.originalname}`);
    }
});

const upload = multer({ storage });
```

With disk storage, Multer saves the file to the configured directory.

For example, your route can read:

```js
app.post("/upload", upload.single("image"), (req, res) => {
    console.log(req.file.path);     // Path to the saved file
    console.log(req.file.filename); // Name used on disk

    res.json({ message: "File saved successfully" });
});
```

Make sure the destination directory exists and is writable. In production, use safe generated filenames rather than trusting the original filename.

### Quick comparison

| Feature | Default memory storage | `diskStorage` |
|---|---|---|
| Where file content is stored | RAM | Server filesystem |
| File content | `req.file.buffer` | Read from the saved file |
| Local path | Not provided | `req.file.path` |
| File remains after request | No | Usually, until deleted |
| Main concern | Memory use | Disk space and file cleanup |

## 5. Uploading multiple files

### Multiple files with the same field name

```js
app.post("/photos", upload.array("photos", 5), (req, res) => {
    console.log(req.files); // Array of uploaded file objects
    res.json({ count: req.files.length });
});
```

This accepts up to **5 files** submitted under the field name `photos`.

### Different file field names

```js
const uploadFields = upload.fields([
    { name: "avatar", maxCount: 1 },
    { name: "documents", maxCount: 3 }
]);

app.post("/profile", uploadFields, (req, res) => {
    console.log(req.files.avatar);
    console.log(req.files.documents);

    res.json({ message: "Files received" });
});
```

## 6. Important Multer methods

| Method | Use | Where files are available |
|---|---|---|
| `upload.single("image")` | One file | `req.file` |
| `upload.array("photos", 5)` | Multiple files from one field | `req.files` array |
| `upload.fields([...])` | Files from several named fields | `req.files` object |
| `upload.none()` | Text fields only, no files | `req.body` |
| `upload.any()` | Files from any field name | `req.files` array |

Use `upload.any()` carefully and only on routes where accepting arbitrary file fields is appropriate.

## 7. What information does `req.file` contain?

The exact fields depend on the storage engine. Common fields include:

```js
console.log(req.file.fieldname);    // Form field name
console.log(req.file.originalname); // Original filename
console.log(req.file.mimetype);    // Client-provided MIME type
console.log(req.file.size);         // Size in bytes
```

Storage-specific fields:

- Memory storage: `req.file.buffer`
- Disk storage: `req.file.destination`, `req.file.filename`, `req.file.path`

`req.file` may be `undefined` if no file was uploaded, so check before accessing its properties.

## 8. Limit file size and validate file types

Always put limits on uploads. Never trust a file's name or MIME type alone; client-provided metadata can be spoofed.

```js
const upload = multer({
    storage,
    limits: {
        fileSize: 5 * 1024 * 1024, // 5 MB
        files: 5
    },
    fileFilter: (req, file, cb) => {
        const allowedTypes = [
            "image/jpeg",
            "image/png",
            "image/webp"
        ];

        if (allowedTypes.includes(file.mimetype)) {
            return cb(null, true);
        }

        return cb(new Error("Only JPEG, PNG, and WebP images are allowed"));
    }
});
```

This example assumes `storage` was defined earlier. `fileFilter` checks the declared MIME type as a first check, not as proof that the file's content is safe. For stronger validation, inspect the actual file content and consider malware scanning where appropriate.

## 9. Handle upload errors

Multer errors can be handled with Express error middleware. For route-specific handling, you can also use a callback:

```js
app.post("/upload", (req, res, next) => {
    upload.single("image")(req, res, (err) => {
        if (err) {
            return next(err);
        }

        return res.json({ message: "Upload successful" });
    });
});
```

A centralized Express error handler can format the error response:

```js
app.use((err, req, res, next) => {
    if (res.headersSent) {
        return next(err);
    }

    if (err instanceof multer.MulterError) {
        return res.status(400).json({
            success: false,
            message: "File upload error",
            code: err.code
        });
    }

    return res.status(500).json({
        success: false,
        message: "Internal Server Error"
    });
});
```

In a real application, distinguish known validation errors from unexpected server failures and log details privately. Register this middleware after routes.

## 10. Multer with Cloudinary (common MERN workflow)

A common workflow is:

1. React sends a file using `FormData`.
2. Express receives it through Multer.
3. Multer stores it in memory or temporarily on disk.
4. The backend uploads it to Cloudinary.
5. The backend stores the returned Cloudinary URL in MongoDB.

For small image uploads, memory storage can be convenient because the file buffer can be passed to an upload helper. For large files, choose limits and a storage/streaming strategy carefully.

Do not store uploaded files permanently on a server's local disk unless your hosting environment and cleanup strategy support that design.

## 11. Common mistakes

- Forgetting `multipart/form-data` in an HTML form.
- Using a frontend field name that doesn't match `upload.single("...")`.
- Accessing `req.file` when no file was sent.
- Assuming default Multer storage saves files to disk.
- Accepting unlimited file sizes or too many files.
- Trusting `file.mimetype` as complete security validation.
- Exposing uploaded files publicly without considering access control.
- Forgetting to clean up temporary files after cloud upload.
- Using `upload.any()` unnecessarily.

## 12. Quick revision

- Multer handles `multipart/form-data` uploads in Express.
- `upload.single("image")` puts one file in `req.file`.
- `upload.array("photos", 5)` puts multiple files in `req.files`.
- Default storage keeps the file in memory as a `Buffer`.
- `diskStorage` saves the file to the server filesystem.
- Use upload size/count limits and validate files carefully.
- Use error middleware to return consistent upload error responses.
- For full options and updates, read the [official Multer npm page](https://www.npmjs.com/package/multer).
