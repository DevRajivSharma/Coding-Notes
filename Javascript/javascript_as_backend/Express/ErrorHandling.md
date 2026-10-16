# Express.js Error Handling (Beginner-Friendly Notes)

## 1. What is error handling?

Error handling means dealing with failures in a controlled way instead of letting them produce confusing responses or crash parts of your application.

In an Express API, good error handling helps you:
- Return the correct HTTP status code.
- Send a consistent JSON response.
- Log useful debugging information on the server.
- Avoid exposing internal details to clients.
- Keep error-handling code out of every controller.

## 2. The three main pieces

Think of error handling as three separate responsibilities:

1. **`Error` / `ApiError`** — represents an error.
2. **`throw` or `next(err)`** — sends the error into the error-handling flow.
3. **Error-handling middleware** — decides what response to send.

An error class does **not** automatically catch errors.

## 3. JavaScript's built-in `Error`

```js
const err = new Error("Something went wrong");

console.log(err.message); // "Something went wrong"
console.log(err.stack);   // Where the error was created/thrown (when available)
```

Creating an error object does not throw it:

```js
const err = new Error("Something went wrong"); // No exception yet
```

To raise the error:

```js
throw new Error("Something went wrong");
```

Common JavaScript error types include `TypeError`, `ReferenceError`, `SyntaxError`, and `RangeError`. These normally inherit from `Error`.

## 4. Create a custom API error class

A custom class lets us attach HTTP-related information to an error.

```js
class ApiError extends Error {
    constructor(
        statusCode,
        message,
        errorCode = "API_ERROR",
        errors = [],
        data = null
    ) {
        super(message);

        this.name = "ApiError";
        this.statusCode = statusCode;
        this.errorCode = errorCode;
        this.errors = errors;
        this.data = data;
        this.isOperational = true;

        Error.captureStackTrace?.(this, this.constructor);
    }
}

export default ApiError;
```

What the properties mean:

| Property | Purpose |
|---|---|
| `statusCode` | HTTP status, such as `400`, `404`, or `409` |
| `message` | Human-readable explanation |
| `errorCode` | Stable application code, such as `USER_NOT_FOUND` |
| `errors` | Optional validation details |
| `data` | Optional extra response data |
| `isOperational` | Marks an expected application/business error |

`isOperational` is only a flag we set ourselves. JavaScript and Express do not automatically interpret it.

## 5. Throw an `ApiError` in a controller

```js
import ApiError from "../utils/ApiError.js";

const getUser = async (req, res) => {
    const user = await User.findById(req.params.id);

    if (!user) {
        throw new ApiError(
            404,
            "User not found",
            "USER_NOT_FOUND"
        );
    }

    return res.status(200).json({
        success: true,
        data: user
    });
};
```

This example assumes **Express 5**, which forwards rejected Promises from route handlers to error middleware automatically.

When the user is missing:
1. The controller throws `ApiError`.
2. The async handler's Promise rejects.
3. Express 5 forwards the error.
4. The centralized error middleware creates the HTTP response.

You do not need to manually create an `ApiError` for every failure. Unexpected errors, such as a database operation rejecting, can also reach the error middleware and be handled as server errors.

## 6. Centralized error-handling middleware

Error middleware has **four parameters**: `(err, req, res, next)`.

```js
app.use((err, req, res, next) => {
    // If the response has already started, delegate to Express.
    if (res.headersSent) {
        return next(err);
    }

    const isApiError = err instanceof ApiError;

    const statusCode =
        isApiError &&
        Number.isInteger(err.statusCode) &&
        err.statusCode >= 400 &&
        err.statusCode <= 599
            ? err.statusCode
            : 500;

    // Log full details on the server, not in the client response.
    logger.error({
        message: err.message,
        stack: err.stack,
        requestId: req.id
    });

    return res.status(statusCode).json({
        success: false,
        error: {
            code: isApiError
                ? err.errorCode
                : "INTERNAL_SERVER_ERROR",
            message: isApiError
                ? err.message
                : "Internal Server Error",
            errors: isApiError ? err.errors : []
        }
    });
});
```

### How does this middleware run?

Express does not run error middleware for every request. It runs when an error is passed into the error-handling pipeline.

Examples:
- A synchronous `throw` inside a route.
- `next(err)` called by middleware or a route.
- A rejected Promise returned by a route handler in Express 5.

If no custom error middleware is registered, Express uses its built-in default error handler.

### Why is there no URL in `app.use()`?

```js
app.use(errorHandler);
```

Without a path, the middleware is mounted across the application. Since this is error middleware, it runs when Express is processing an error—not as a normal handler for every URL.

### Why should it be near the bottom?

Middleware runs in registration order. Register the error handler after your routes and after your 404 handler so errors from earlier middleware and routes can reach it.

## 7. Express 5 vs Express 4: async errors

### Express 5

Express 5 forwards rejected Promises returned by route handlers automatically:

```js
app.get("/users/:id", async (req, res) => {
    const user = await User.findById(req.params.id);

    if (!user) {
        throw new ApiError(404, "User not found", "USER_NOT_FOUND");
    }

    return res.json({ success: true, data: user });
});
```

### Express 4

In Express 4, use `try/catch` and `next(err)`, or a wrapper:

```js
const getUser = async (req, res, next) => {
    try {
        const user = await User.findById(req.params.id);

        if (!user) {
            throw new ApiError(404, "User not found", "USER_NOT_FOUND");
        }

        return res.json({ success: true, data: user });
    } catch (err) {
        return next(err);
    }
};

app.get("/users/:id", getUser);
```

## 8. What is `asyncHandler`?

A wrapper often used in Express 4 projects:

```js
const asyncHandler = (requestHandler) => {
    return (req, res, next) => {
        Promise.resolve(requestHandler(req, res, next))
            .catch(next);
    };
};

export { asyncHandler };
```

Usage:

```js
app.get("/users/:id", asyncHandler(async (req, res) => {
    const user = await User.findById(req.params.id);

    if (!user) {
        throw new ApiError(404, "User not found", "USER_NOT_FOUND");
    }

    return res.json({ success: true, data: user });
}));
```

The wrapper forwards rejected Promises to `next(err)`. In Express 5 it is generally optional for this purpose, but teams may retain it for consistency or compatibility.

It does **not** catch every error in the Node.js process. For example, errors in unrelated `setTimeout` callbacks, background jobs, or worker processes need their own handling.

## 9. Handle unknown routes (404)

An unmatched URL does not automatically become an `ApiError`. Add a 404 handler after your routes:

```js
app.use((req, res, next) => {
    next(new ApiError(
        404,
        `Route ${req.method} ${req.originalUrl} not found`,
        "ROUTE_NOT_FOUND"
    ));
});
```

Then register the centralized error middleware **after it**:

```js
// 1. Routes
app.use("/api/users", userRoutes);
app.use("/api/products", productRoutes);

// 2. Unknown routes
app.use((req, res, next) => {
    next(new ApiError(404, "Route not found", "ROUTE_NOT_FOUND"));
});

// 3. Error middleware
app.use(errorHandler);
```

## 10. Operational vs. unexpected errors

**Operational errors** are expected failures the application can respond to normally:
- User does not exist (`404`).
- Invalid request data (`400`).
- Duplicate email (`409`).
- Not authenticated (`401`).

**Unexpected errors** often indicate a bug or infrastructure failure:
- An unhandled programming mistake.
- An unexpected database failure.
- An invalid assumption in code.

A custom `isOperational` flag can help classify errors, but it must be set intentionally and used consistently. Do not trust arbitrary client-provided values or expose raw internal error messages.

## 11. Security and production tips

- Never return `err.stack` to clients in production.
- Log stack traces on the server, with appropriate access controls and redaction.
- Use stable error codes so frontend code can respond reliably.
- Validate status codes before using them in responses.
- Avoid sending a response twice.
- Handle malformed JSON and other known framework errors deliberately if you need specific response formats.
- Errors in background jobs, timers, and workers require separate handling.
- Do not assume that every error can safely be ignored. Some fatal process-level errors may require graceful shutdown and a process manager restart.

## 12. Quick mental model

```text
An error happens
      |
      v
throw / rejected Promise / next(err)
      |
      v
Express error-handling pipeline
      |
      v
Centralized error middleware
      |
      +--> Log details privately
      |
      +--> Choose safe HTTP status and error code
      |
      v
Consistent JSON response to client
```

## Interview summary

- `Error` represents an error; it does not catch it.
- `ApiError` adds application-specific fields like status and error code.
- `throw` raises an error.
- `next(err)` forwards an error to Express.
- Express 5 forwards rejected Promises returned by route handlers automatically.
- Error middleware has four parameters and should be registered after routes.
- A 404 handler catches unmatched routes and should be placed before the error middleware.
- Keep internal stack traces in protected logs, never in production API responses.
