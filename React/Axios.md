# Using Axios in React

## What is Axios?
- A popular HTTP client for making API requests
- Promise-based HTTP client for browser and Node.js
- Provides a simple and elegant API for HTTP requests
- Automatic transforms for JSON data
- Built-in error handling and request/response interceptors

## Installation

Using npm:
```bash
npm install axios
```

Using yarn:
```bash
yarn add axios
```

## Basic Usage

### Making GET Requests

```javascript:r:\CodingNotes\React\examples\AxiosGet.jsx
import axios from 'axios';
import { useState, useEffect } from 'react';

function UserList() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        // Basic GET request
        axios.get('https://api.example.com/users')
            .then(response => {
                setUsers(response.data);
                setLoading(false);
            })
            .catch(error => {
                setError(error.message);
                setLoading(false);
            });
    }, []);

    // Using async/await
    const fetchUsers = async () => {
        try {
            const response = await axios.get('https://api.example.com/users');
            setUsers(response.data);
        } catch (error) {
            setError(error.message);
        } finally {
            setLoading(false);
        }
    };

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;

    return (
        <ul>
            {users.map(user => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}
```

### Making POST Requests

```javascript:r:\CodingNotes\React\examples\AxiosPost.jsx
import axios from 'axios';
import { useState } from 'react';

function CreateUser() {
    const [formData, setFormData] = useState({
        name: '',
        email: ''
    });
    const [status, setStatus] = useState('');

    const handleSubmit = async (e) => {
        e.preventDefault();
        try {
            const response = await axios.post('https://api.example.com/users', formData);
            setStatus('User created successfully!');
            console.log('Response:', response.data);
        } catch (error) {
            setStatus(`Error: ${error.message}`);
        }
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                value={formData.name}
                onChange={(e) => setFormData({...formData, name: e.target.value})}
                placeholder="Name"
            />
            <input
                type="email"
                value={formData.email}
                onChange={(e) => setFormData({...formData, email: e.target.value})}
                placeholder="Email"
            />
            <button type="submit">Create User</button>
            {status && <p>{status}</p>}
        </form>
    );
}
```

## Advanced Usage

### Creating an Axios Instance

```javascript:r:\CodingNotes\React\examples\axiosConfig.js
import axios from 'axios';

// Create an instance with custom config
const api = axios.create({
    baseURL: 'https://api.example.com',
    timeout: 5000,
    headers: {
        'Content-Type': 'application/json',
        // Add any default headers
    }
});

// Add request interceptor
api.interceptors.request.use(
    config => {
        // Add token to headers
        const token = localStorage.getItem('token');
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
    },
    error => {
        return Promise.reject(error);
    }
);

// Add response interceptor
api.interceptors.response.use(
    response => response,
    error => {
        if (error.response?.status === 401) {
            // Handle unauthorized access
            localStorage.removeItem('token');
            window.location.href = '/login';
        }
        return Promise.reject(error);
    }
);

export default api;
```

### Using Custom Axios Instance

```javascript:r:\CodingNotes\React\examples\AxiosCustomInstance.jsx
import { useState, useEffect } from 'react';
import api from './axiosConfig';

function Dashboard() {
    const [data, setData] = useState(null);

    useEffect(() => {
        const fetchData = async () => {
            try {
                // Using custom instance
                const response = await api.get('/dashboard-data');
                setData(response.data);
            } catch (error) {
                console.error('Error fetching dashboard data:', error);
            }
        };

        fetchData();
    }, []);

    return (
        <div>
            {/* Render dashboard data */}
        </div>
    );
}
```

### Handling File Uploads

```javascript:r:\CodingNotes\React\examples\AxiosFileUpload.jsx
import axios from 'axios';
import { useState } from 'react';

function FileUpload() {
    const [file, setFile] = useState(null);
    const [progress, setProgress] = useState(0);

    const handleUpload = async () => {
        if (!file) return;

        const formData = new FormData();
        formData.append('file', file);

        try {
            await axios.post('https://api.example.com/upload', formData, {
                headers: {
                    'Content-Type': 'multipart/form-data'
                },
                onUploadProgress: (progressEvent) => {
                    const percentCompleted = Math.round(
                        (progressEvent.loaded * 100) / progressEvent.total
                    );
                    setProgress(percentCompleted);
                }
            });
            alert('File uploaded successfully!');
        } catch (error) {
            console.error('Upload failed:', error);
        }
    };

    return (
        <div>
            <input
                type="file"
                onChange={(e) => setFile(e.target.files[0])}
            />
            <button onClick={handleUpload}>Upload</button>
            {progress > 0 && <div>Upload Progress: {progress}%</div>}
        </div>
    );
}
```

### Concurrent Requests

```javascript:r:\CodingNotes\React\examples\AxiosConcurrent.jsx
import axios from 'axios';
import { useState, useEffect } from 'react';

function DataAggregator() {
    const [aggregatedData, setAggregatedData] = useState(null);

    useEffect(() => {
        const fetchAllData = async () => {
            try {
                const [usersResponse, postsResponse] = await axios.all([
                    axios.get('https://api.example.com/users'),
                    axios.get('https://api.example.com/posts')
                ]);

                setAggregatedData({
                    users: usersResponse.data,
                    posts: postsResponse.data
                });
            } catch (error) {
                console.error('Error fetching data:', error);
            }
        };

        fetchAllData();
    }, []);

    return (
        <div>
            {/* Render aggregated data */}
        </div>
    );
}
```

## Error Handling Best Practices

```javascript:r:\CodingNotes\React\examples\AxiosErrorHandling.jsx
import axios from 'axios';

async function makeApiRequest() {
    try {
        const response = await axios.get('https://api.example.com/data');
        return response.data;
    } catch (error) {
        if (error.response) {
            // Server responded with error status
            console.error('Server Error:', error.response.status);
            console.error('Error Data:', error.response.data);
        } else if (error.request) {
            // Request made but no response
            console.error('Network Error:', error.request);
        } else {
            // Error in request configuration
            console.error('Error:', error.message);
        }
        throw error; // Re-throw for component handling
    }
}
```

## Common Axios Configurations

```javascript:r:\CodingNotes\React\examples\AxiosConfigurations.js
import axios from 'axios';

// Global axios defaults
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = 'AUTH_TOKEN';
axios.defaults.headers.post['Content-Type'] = 'application/json';

// Request timeout
axios.defaults.timeout = 5000;

// Custom instance defaults
const instance = axios.create({
    baseURL: 'https://api.another-example.com',
    timeout: 3000,
    headers: {'X-Custom-Header': 'value'}
});

export default instance;
```

Remember to handle errors appropriately and implement proper loading states in your components when making API requests.
