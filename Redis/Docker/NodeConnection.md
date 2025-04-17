# Connecting Node.js to Redis

This document explains how to connect a Node.js application to a Redis server, typically one running in a Docker container as set up in previous documentation.

## Why Connect Node.js to Redis?
- **Caching**: Store frequently accessed data in Redis to reduce database load and improve application speed.
- **Session Management**: Use Redis to store user session data, enabling stateless application servers.
- **Real-time Features**: Implement features like leaderboards, rate limiting, and pub/sub messaging.
- **Job Queues**: Manage background tasks and job processing.

## 1. Prerequisites
- **Node.js and npm/yarn**: Ensure Node.js and a package manager are installed.
- **Running Redis Instance**: Have a Redis server running (e.g., using Docker as described in `RedisSetup.md`). Make sure the Redis port (default 6379) is accessible from your Node.js application.

## 2. Install the Redis Client Library

Choose **one** of the following popular Redis client libraries for Node.js and install it using npm or yarn. The examples in this document primarily use the official `redis` library, but `ioredis` is another excellent option (see comparison below).

**Option 1: Install `redis` (Official Library)**

Using npm:
```bash
npm install redis
```

Using yarn:
```bash
yarn add redis
```
Option 2: Install ioredis

Using npm:

```bash
npm install ioredis
 ```

Using yarn:

```bash
yarn add ioredis
 ```

## 3. Basic Connection Example

Here's a simple Node.js script demonstrating how to connect to Redis, set a key, get a key, and handle events.

```javascript:r:\CodingNotes\Redis\Docker\connect-redis.js
// Import the redis library
import { createClient } from 'ioredis';
// or import { createClient } from 'redis';

// Create a Redis client
// By default, it connects to redis://127.0.0.1:6379
const client = createClient();

// Handle connection errors
client.on('error', (err) => {
    console.error('Redis Client Error:', err);
});

// Asynchronous function to run Redis commands
async function runRedisExample() {
    try {
        // Connect to the Redis server
        await client.connect();
        console.log('Connected to Redis successfully!');

        // Set a key
        const setResult = await client.set('mykey', 'Hello from Node.js!');
        console.log('SET result:', setResult); // Output: OK

        // Get the key
        const getResult = await client.get('mykey');
        console.log('GET result:', getResult); // Output: Hello from Node.js!

    } catch (err) {
        console.error('Error during Redis operation:', err);
    } finally {
        // Disconnect the client when done
        await client.disconnect();
        console.log('Disconnected from Redis.');
    }
}

// Run the example
runRedisExample();
```

**To run this script:**
1.  Save the code as `connect-redis.js` (or similar) in your project directory (`r:\CodingNotes\Redis\Docker\`).
2.  Make sure your Redis container (`my-redis-container` or `my-redis-stack`) is running.
3.  Execute the script using Node.js:
    ```bash
    node r:\CodingNotes\Redis\Docker\connect-redis.js
    ```
    *(Note: You might need to configure Node.js to handle ES Modules (`import`) or use `require` syntax instead)*

## 4. Connection Options

You can customize the connection by passing a configuration object or URL to `createClient`:

```javascript:r:\CodingNotes\Redis\Docker\connect-options.js
import { createClient } from 'redis';

// Option 1: Using a URL string
// const client = createClient({ url: 'redis://username:password@host:port/db_number' });

// Option 2: Using a configuration object
const clientWithOptions = createClient({
    socket: {
        host: '127.0.0.1', // Or your Docker container's IP if different
        port: 6379
    }
    // password: 'your_redis_password', // Uncomment if Redis requires authentication
    // database: 0 // Specify DB number if needed
});

clientWithOptions.on('error', err => console.error('Redis Client Error:', err));

async function connectWithOptions() {
    try {
        await clientWithOptions.connect();
        console.log('Connected with custom options!');
        // Perform operations...
        await clientWithOptions.set('anotherkey', 'value with options');
        console.log(await clientWithOptions.get('anotherkey'));
    } finally {
        await clientWithOptions.disconnect();
    }
}

connectWithOptions();
```

## 5. Common Redis Commands in Node.js

The `redis` library maps Redis commands to asynchronous client methods:

- `SET key value`: `await client.set('user:1', 'Alice');`
- `GET key`: `const name = await client.get('user:1');`
- `INCR key`: `const count = await client.incr('counter');`
- `EXPIRE key seconds`: `await client.expire('session:xyz', 3600);` // Expires in 1 hour
- `DEL key`: `await client.del('mykey');`
- `HSET hash field value`: `await client.hSet('user:profile', 'name', 'Bob');`
- `HGETALL hash`: `const profile = await client.hGetAll('user:profile');`

ñRefer to the [official redis library documentation](https://github.com/redis/node-redis) for a full list of commands and advanced usage.

## `redis` vs `ioredis` Libraries

When choosing a Redis client library for Node.js, the two most popular options are `redis` (the official library, formerly `node-redis`) and `ioredis`. Here's a comparison:

| Feature             | `redis` (v4+)                                  | `ioredis`                                        |
| :------------------ | :--------------------------------------------- | :----------------------------------------------- |
| **API Style**       | Primarily `async/await` (Promise-based)        | Promises, Callbacks, `async/await`               |
| **Performance**     | Generally very good, significantly improved in v4 | Often cited as having slightly higher performance |
| **Cluster Support** | Supported                                      | Robust built-in support                          |
| **Sentinel Support**| Supported                                      | Robust built-in support                          |
| **Lua Scripting**   | Supported                                      | Enhanced support, easier API                     |
| **Offline Queue**   | Configurable                                   | Built-in, configurable                           |
| **Pipelining**      | Supported (`.multi()`/`.exec()`)               | Supported (`.pipeline()`)                        |
| **Pub/Sub**         | Supported                                      | Supported                                        |
| **Streams Support** | Supported                                      | Supported                                        |
| **Maintainer**      | Official Redis Team                            | Community (actively maintained)                  |
| **Ease of Use**     | Modern API, generally straightforward          | Slightly more features, potentially steeper curve |

**Key Differences Explained:**

1.  **API:**
    *   `redis` (v4+): Modernized its API heavily towards `async/await`, making it feel very integrated with modern JavaScript.
    *   `ioredis`: Offers more flexibility with support for callbacks, Promises, and `async/await`, which might be preferable for older codebases or specific patterns.

2.  **Performance:**
    *   Both libraries are highly performant. `ioredis` has historically been benchmarked as slightly faster in some scenarios, but `redis` v4 closed much of this gap. For most applications, the difference is negligible.

3.  **Features (Cluster/Sentinel):**
    *   `ioredis` has long been favored for its robust, built-in support for Redis Cluster and Sentinel configurations, often requiring less manual setup than `redis`. However, `redis` v4 has improved its support significantly.

4.  **Offline Queueing:**
    *   Both libraries queue commands if the connection is temporarily lost, but `ioredis`'s handling is often considered more seamless out-of-the-box.

**Which one to choose?**

*   **For new projects using modern JavaScript (`async/await`):** The official `redis` library (v4+) is an excellent choice due to its clean API and official backing.
*   **For complex setups (Cluster/Sentinel) or maximum performance:** `ioredis` is a very strong contender, known for its robustness and speed in these areas.
*   **If you need callback support or prefer its API:** `ioredis` provides more flexibility in how you interact with it.

Ultimately, both are excellent, well-maintained libraries. You can build robust applications with either. Consider trying small examples with both to see which API style you prefer.