## What is package.json?
- A file that stores project information, dependencies, and scripts in a **Node.js** project.

### Key Sections in package.json:
- **name:** Project name.
- **version:** Project version.
- **main:** Entry point (e.g., `index.js`).
- **dependencies:** External packages used by the project.
- **scripts:** Custom commands for automation.

### When to Create package.json:
- When using **external packages** (e.g., Express.js).
- To automate tasks with **scripts**.
- For **collaboration** and **deployment**.

### How to Create package.json:
```bash
npm init -y
```

### Example package.json:
```json
{
  "name": "node-app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

### Useful npm Commands:
- **Install a package:**
    ```bash
    npm install <package-name>
    ```
- **Run a script:**
    ```bash
    npm run <script-name>
    ```
- **Install all dependencies:**
    ```bash
    npm install

### If you are using import than add this line in package.json
```package.json
{
  "name": "node-app",
  "type":"module", // Added
   ...... rest of the code
}
```