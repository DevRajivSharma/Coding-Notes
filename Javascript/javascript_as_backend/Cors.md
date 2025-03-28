### Cross Orgin Resource Sharing [Cors]
- Since we are running frontend and backend in different url/port we will get Cors error

### Proxy
- To fix Cors error we used  **proxy** differently based on how app is created
    1. Create react app
        - Add a proxy field to your package.json, for example:
        ```package.json
          "proxy": "http://localhost:4000",
        ```

    2. Vite
        - Add server field to your vite.config.js
        ```vite.config.js
        export default defineConfig({
              server: {
                    '/api':'http://jsonplaceholder.typicode.com',
              },
            })
        ```