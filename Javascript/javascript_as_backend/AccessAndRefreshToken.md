# Access Token and Refresh Token Workflow

This document explains how **Access Tokens** and **Refresh Tokens** work, focusing on their purpose, lifecycle, and implementation in web applications.

## 1. What is an Access Token?

An **Access Token** is a short-lived credential that grants a client application access to protected resources (e.g., user data) on a server.

### Key Features:
- **Short lifespan** (e.g., 15 minutes – 1 hour)
- Used to authenticate requests to the API
- Cannot be reused once expired

### Example Flow:
1. User logs in and the server issues an **Access Token**.
2. The client uses this token to access a protected API endpoint.
3. When the **Access Token** expires, a new one is requested using the **Refresh Token**.

## 2. What is a Refresh Token?

A **Refresh Token** is a long-lived credential used to obtain new **Access Tokens** without requiring the user to log in again.

### Key Features:
- **Long lifespan** (e.g., 7 days – 30 days)
- Stored securely (e.g., HTTP-only cookies or encrypted storage)
- Can only be used to request new **Access Tokens**

### Example Flow:
1. The server issues both an **Access Token** and a **Refresh Token** during login.
2. When the **Access Token** expires, the client sends the **Refresh Token** to the server.
3. The server verifies the **Refresh Token** and issues a new **Access Token**.

## 3. Token Expiration and Security

### Access Token Expiration:
- Typically expires in **minutes or hours**.
- If expired, a **Refresh Token** is used to obtain a new one.

### Refresh Token Expiration:
- Expires in **days or weeks**.
- Can also be invalidated if the user logs out or changes their password.

## 4. Code Example: Auto Refresh Tokens in JavaScript

This example shows how to automatically refresh **Access Tokens** when they expire.

```javascript
// Function to fetch data with automatic token refresh
async function fetchWithToken(url, options = {}) {
  try {
    const accessToken = localStorage.getItem("access_token");
    options.headers = {
      ...options.headers,
      Authorization: `Bearer ${accessToken}`,
    };

    const response = await fetch(url, options);

    // If access token is expired, refresh it
    if (response.status === 401) {
      await refreshAccessToken();
      return fetchWithToken(url, options); // Retry the request
    }

    return response.json();
  } catch (error) {
    console.error("Error fetching data:", error);
    throw error;
  }
}

// Function to refresh the access token
async function refreshAccessToken() {
  const refreshToken = localStorage.getItem("refresh_token");

  if (!refreshToken) {
    throw new Error("Refresh token not available. Please log in again.");
  }

  try {
    const response = await fetch("https://your-auth-server.com/token", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        grant_type: "refresh_token",
        refresh_token: refreshToken,
      }),
    });

    if (!response.ok) {
      throw new Error("Failed to refresh access token");
    }

    const data = await response.json();

    // Store the new tokens
    localStorage.setItem("access_token", data.access_token);
    if (data.refresh_token) {
      localStorage.setItem("refresh_token", data.refresh_token);
    }
  } catch (error) {
    console.error("Error refreshing token:", error);
    throw error;
  }
}

// Example Usage
(async () => {
  try {
    const data = await fetchWithToken("https://api.example.com/protected");
    console.log("Data:", data);
  } catch (error) {
    console.error("Failed to fetch protected resource:", error);
  }
})();
```

## 5. Best Practices

- **Secure Storage**: Store **Access Tokens** in memory and **Refresh Tokens** in HTTP-only cookies.
- **Token Rotation**: Issue a new **Refresh Token** on each use to prevent replay attacks.
- **Expiration Policy**: Implement absolute and sliding expiration policies.
- **Revoke Tokens**: Invalidate tokens if the user logs out or changes credentials.

This ensures a secure and efficient authentication process using **Access Tokens** and **Refresh Tokens**.
