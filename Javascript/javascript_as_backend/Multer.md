# Handling File Uploads with Multer in Node.js/Express

This document explains how to use **Multer**, a Node.js middleware, for handling `multipart/form-data`, which is primarily used for uploading files.

## What is Multer?
- **Multer** is middleware specifically designed to handle `multipart/form-data` requests in Express applications.
- It parses incoming request bodies containing files and makes them accessible via the `req.file` or `req.files` objects.
- It does **not** process any non-`multipart/form-data` requests.

## Why Use Multer?
- Simplifies the process of extracting uploaded files from HTTP requests.
- Provides options for controlling where files are stored (disk, memory).
- Allows filtering files based on type (e.g., only allow images).
- Enables setting limits on file size, number of files, etc.

## 1. Installation

Install Multer using npm or yarn:

Using npm:
```bash
npm install multer
```

Using yarn:
```bash
yarn add multer
```

## 2. Configuring Multer Storage

This example shows how to configure Multer's `diskStorage` engine to control the destination directory and filename for uploaded files. This configuration is often placed in a separate file (e.g., `middleware/multer.middleware.js`) and exported.

```javascript:r%3A%5CCodingNotes%5CJavascript%5Cjavascript_as_backend%5Cmiddleware%5Cmulter.middleware.js
import multer from "multer";
import path from "path"; // Recommended for joining paths

// Configure disk storage engine
const storage = multer.diskStorage({
    // Define the destination directory for uploaded files
    destination: function (req, file, cb) {
        // 'cb' is a callback function (error, destinationPath)
        // We specify './public/temp' as the directory. Ensure this directory exists.
        cb(null, './public/temp'); // Relative path from where the script is run
    },
    // Define how filenames should be generated
    filename: function (req, file, cb) {
        // 'cb' is a callback function (error, filename)
        // We prepend a timestamp to the original filename to make it unique
        // Using path.extname(file.originalname) is safer for getting the extension
        const uniquePrefix = Date.now();
        const finalFilename = `${uniquePrefix}-${file.originalname}`;
        cb(null, finalFilename);
    },
});

// Create the Multer instance with the configured storage
const upload = multer({
    storage: storage,
    // You can add limits and fileFilter here as well
    // limits: { fileSize: 1024 * 1024 * 5 }, // Example: 5MB limit
    // fileFilter: ...
});

// Export the configured Multer instance
export default upload;
```

Explanation:

- multer.diskStorage({...}) : Creates a storage engine that saves files to the local disk.
- destination function : Determines the folder where the uploaded file will be saved. It receives the request ( req ), file details ( file ), and a callback ( cb ). You call cb(null, 'path/to/directory') to specify the destination. It's crucial that this directory exists beforehand, or you handle its creation.
- filename function : Determines the name of the file within the destination folder. It also receives req , file , and cb . You call cb(null, 'your-chosen-filename') to set the name. Prepending a timestamp or using a UUID is common practice to avoid filename collisions.
- multer({ storage: storage }) : Creates the Multer middleware instance, configured to use the diskStorage engine defined above.
- export default upload : Makes the configured upload instance available for import in other files (like your route definitions).
## 3. Using the Multer Middleware in Routes
Import the configured upload instance into your Express route files and use its methods as middleware before your route handler.

### Handling a Single File (upload.single())
Use upload.single('fieldName') when you expect only one file associated with the HTML form field named fieldName . The file details will be available in req.file .

```javascript
import express from 'express';
import upload from '../middleware/multer.middleware.js'; // Import the configured Multer instance
// ... other imports like controllers ...

const router = express.Router();

// Example route for uploading a profile picture
// 'avatar' must match the 'name' attribute of the <input type="file"> tag in the form
router.post('/upload-avatar', upload.single('avatar'), (req, res) => {
    if (!req.file) {
        return res.status(400).json({ message: 'No avatar file uploaded.' });
    }

    console.log('Uploaded file details:', req.file);
    // Process the file (e.g., save path to database)
    const filePath = req.file.path; // Path where the file was saved by Multer

    res.status(200).json({
        message: 'Avatar uploaded successfully!',
        filePath: filePath,
        filename: req.file.filename
    });
});

// ... other routes ...

export default router;
 ```


### Handling Multiple Files - Same Field (upload.array())
Use upload.array('fieldName', maxCount) when you expect multiple files uploaded under the same field name (e.g., using <input type="file" name="fieldName" multiple> ). The file details will be available in req.files as an array.

```javascript
import express from 'express';
import upload from '../middleware/multer.middleware.js';
// ... other imports ...

const router = express.Router();

// Example route for uploading multiple gallery images
// 'galleryImages' must match the 'name' attribute of the multiple file input
// Allows up to 10 files in this example
router.post('/upload-gallery', upload.array('galleryImages', 10), (req, res) => {
    if (!req.files || req.files.length === 0) {
        return res.status(400).json({ message: 'No gallery images uploaded.' });
    }

    console.log('Uploaded files details:', req.files);
    const filePaths = req.files.map(file => file.path);

    res.status(200).json({
        message: `${req.files.length} gallery images uploaded successfully!`,
        filePaths: filePaths
    });
});

// ... other routes ...

export default router;
 ```


### Handling Multiple Files - Different Fields (upload.fields())
Use upload.fields([{ name: 'field1', maxCount: 1 }, { name: 'field2', maxCount: 5 }]) when you expect files from different named fields. req.files will be an object where keys are field names, and values are arrays of file objects.

```javascript
import express from 'express';
import upload from '../middleware/multer.middleware.js';
// ... other imports ...

const router = express.Router();

// Example route for uploading a product image and a specification PDF
router.post('/add-product', upload.fields([
    { name: 'productImage', maxCount: 1 }, // Expect one file named 'productImage'
    { name: 'specSheet', maxCount: 1 }     // Expect one file named 'specSheet'
]), (req, res) => {

    console.log('Uploaded files object:', req.files);
    // req.files will look like:
    // {
    //   productImage: [ { file details for productImage } ],
    //   specSheet: [ { file details for specSheet } ]
    // }

    const productImage = req.files['productImage'] ? req.files['productImage'][0] : null;
    const specSheet = req.files['specSheet'] ? req.files['specSheet'][0] : null;

    if (!productImage) {
        return res.status(400).json({ message: 'Product image is required.' });
    }
    // Optionally check for specSheet or other files

    res.status(200).json({
        message: 'Product files uploaded successfully!',
        imagePath: productImage.path,
        specPath: specSheet ? specSheet.path : 'Not provided'
    });
});

// ... other routes ...

export default router;
 ```


### Handling Only Text Fields (upload.none())
Use upload.none() if the form is multipart/form-data but contains only text fields (no files). Multer will parse the text fields and make them available in req.body .

```javascript
import express from 'express';
import upload from '../middleware/multer.middleware.js';
// ... other imports ...

const router = express.Router();

// Example route for a multipart form with only text data
router.post('/text-only-form', upload.none(), (req, res) => {
    console.log('Received text data:', req.body);
    // Access fields like req.body.fieldName

    res.status(200).json({
        message: 'Text data received successfully!',
        data: req.body
    });
});

// ... other routes ...

export default router;
 ```


Remember to create the ./public/temp directory (or whichever directory you specify in destination ) before running the application, or handle its creation within your code. Also, implement proper error handling for production environments.