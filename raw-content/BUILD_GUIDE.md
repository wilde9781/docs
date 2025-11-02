# Fullstack Docker Application - Complete Build Guide

This guide walks you through building a fullstack application from scratch with Express backend, React frontend, and Docker containerization.

---

## Table of Contents

1. [Backend Setup (Express.js)](#1-backend-setup-expressjs)
2. [Frontend Setup (Vite + React)](#2-frontend-setup-vite--react)
3. [Understanding CORS and Vite Proxy](#3-understanding-cors-and-vite-proxy)
4. [Fixing CORS on Backend](#4-fixing-cors-on-backend)
5. [Dockerizing the Backend](#5-dockerizing-the-backend)
6. [Dockerizing the Frontend](#6-dockerizing-the-frontend)
7. [Docker Compose Setup](#7-docker-compose-setup)

---

## 1. Backend Setup (Express.js)

### Step 1: Initialize Node.js Project

Create a new directory for the backend:

```bash
mkdir server
cd server
npm init -y
```

### Step 2: Install Dependencies

Install Express and CORS:

```bash
npm install express cors
```

### Step 3: Configure package.json

Update `package.json` to use ES modules:

```json
{
  "name": "server",
  "version": "1.0.0",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^5.1.0"
  }
}
```

**Key Points:**
- `"type": "module"` enables ES6 import/export syntax
- `"start"` script runs the server

### Step 4: Create Express Server

Create `index.js`:

```javascript
import express from "express";
import cors from "cors";

const app = express();

// Middleware
app.use(express.json());
app.use(cors());

// API Routes
app.get("/api/message", (req, res) => {
  res.json({ message: "Hello from chaicode" });
});

// Start Server
const PORT = 4000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

### Step 5: Test the Backend

Run the server:

```bash
npm start
```

Test the API:

```bash
curl http://localhost:4000/api/message
```

Expected response:
```json
{"message":"Hello from chaicode"}
```

---

## 2. Frontend Setup (Vite + React)

### Step 1: Initialize Vite Project

Create a new directory and initialize Vite with React:

```bash
cd ..
mkdir client
cd client
npm create vite@latest . -- --template react
npm install
```

### Step 2: Install Dependencies

The Vite React template includes:
- React
- React DOM
- Vite
- ESLint (optional)

### Step 3: Create API-Fetching Component

Update `src/App.jsx`:

```javascript
import { useState, useEffect } from "react";
import "./App.css";

function App() {
  const [message, setMessage] = useState("");

  useEffect(() => {
    fetch("http://localhost:4000/api/message")
      .then((res) => res.json())
      .then((data) => setMessage(data.message))
      .catch((err) => {
        console.error("Error fetching message:", err);
        setMessage("Failed to load message");
      });
  }, []);

  return (
    <>
      <h1>ChaiCode frontend</h1>
      <p>{message}</p>
    </>
  );
}

export default App;
```

### Step 4: Run Development Server

```bash
npm run dev
```

The app will run on `http://localhost:5173` (default Vite port).

**Problem Encountered:**
At this point, you'll likely see a CORS error in the browser console because:
- Frontend runs on `http://localhost:5173`
- Backend runs on `http://localhost:4000`
- Different origins trigger CORS policy

---

## 3. Understanding CORS and Vite Proxy

### What is CORS?

**Cross-Origin Resource Sharing (CORS)** is a browser security feature that blocks requests between different origins (different protocol, domain, or port).

**Example:**
- Frontend: `http://localhost:5173`
- Backend: `http://localhost:4000`
- These are **different origins** (different ports)

### Solution Options

#### Option A: Vite Proxy (Development Only)

Vite can proxy API requests during development, making them appear to come from the same origin.

#### Option B: Backend CORS Configuration (Production)

Configure the backend to allow requests from specific origins.

### Vite Proxy Setup

Update `vite.config.js`:

```javascript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      "/api": {
        target: "http://localhost:4000",
        changeOrigin: true,
        secure: false,
      },
    },
  },
});
```

**How it works:**
- Requests to `/api/*` from the frontend are proxied to `http://localhost:4000`
- Browser sees requests as same-origin (`localhost:5173`)
- No CORS issues during development

### Update Frontend Code (With Proxy)

With proxy enabled, update `App.jsx`:

```javascript
// Use relative path - Vite proxy handles the rest
fetch("/api/message")
  .then((res) => res.json())
  .then((data) => setMessage(data.message));
```

**Advantages:**
- ✅ No CORS issues in development
- ✅ Simple relative URLs
- ✅ Works seamlessly with Vite dev server

**Limitations:**
- ❌ Only works during development (`npm run dev`)
- ❌ Not available in production build
- ❌ Backend CORS still needed for production

---

## 4. Fixing CORS on Backend

### Why Backend CORS is Necessary

Even with Vite proxy for development, you need backend CORS configuration for:
1. **Production builds** (static files served separately)
2. **Direct API calls** from browsers
3. **Different deployment scenarios**

### Step 1: Basic CORS Setup

Update `server/index.js`:

```javascript
import express from "express";
import cors from "cors";

const app = express();

// Basic CORS - allows all origins (NOT recommended for production)
app.use(cors());

app.get("/api/message", (req, res) => {
  res.json({ message: "Hello from chaicode" });
});

app.listen(4000, () => {
  console.log("Server is running on port 4000");
});
```

### Step 2: Configure Specific Origins

For better security, specify allowed origins:

```javascript
import express from "express";
import cors from "cors";

const app = express();

// CORS Configuration
app.use(
  cors({
    origin: [
      "http://localhost:5173", // Vite dev server (default port)
      "http://localhost:5174", // Alternative Vite port
      "http://localhost:3000", // Production build port
      // Add production URLs when deployed
      // "http://yourdomain.com",
    ],
    credentials: true, // Allow cookies/auth headers
    methods: ["GET", "POST", "PUT", "DELETE", "OPTIONS"],
    allowedHeaders: ["Content-Type", "Authorization"],
  })
);

app.get("/api/message", (req, res) => {
  res.json({ message: "Hello from chaicode" });
});

app.listen(4000, () => {
  console.log("Server is running on port 4000");
});
```

### Step 3: Dynamic CORS with Environment Variables

For flexibility across environments:

```javascript
import express from "express";
import cors from "cors";

const app = express();

// Get allowed origins from environment or use defaults
const allowedOrigins = process.env.ALLOWED_ORIGINS
  ? process.env.ALLOWED_ORIGINS.split(",")
  : [
      "http://localhost:5173",
      "http://localhost:5174",
      "http://localhost:3000",
    ];

app.use(
  cors({
    origin: allowedOrigins,
    credentials: true,
    methods: ["GET", "POST", "PUT", "DELETE", "OPTIONS"],
    allowedHeaders: ["Content-Type", "Authorization"],
  })
);

app.get("/api/message", (req, res) => {
  res.json({ message: "Hello from chaicode" });
});

app.listen(4000, () => {
  console.log("Server is running on port 4000");
});
```

**Key CORS Options:**
- `origin`: Array of allowed origins
- `credentials`: Allow cookies/authentication headers
- `methods`: HTTP methods allowed
- `allowedHeaders`: Headers the client can send

---

## 5. Dockerizing the Backend

### Step 1: Create Backend Dockerfile

Create `server/Dockerfile`:

```dockerfile
FROM node:22-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy application code
COPY . .

# Expose port
EXPOSE 4000

# Start server
CMD ["npm", "start"]
```

**Explanation:**
- `FROM node:22-alpine`: Lightweight Node.js image
- `WORKDIR /app`: Set working directory
- Copy `package*.json` first for better Docker layer caching
- `EXPOSE 4000`: Document the port (doesn't actually publish it)
- `CMD`: Command to run when container starts

### Step 2: Important: Bind to 0.0.0.0

Update `server/index.js` to bind to all interfaces:

```javascript
// IMPORTANT: Bind to 0.0.0.0 for Docker
app.listen(4000, "0.0.0.0", () => {
  console.log("Server is running on port 4000");
});
```

**Why 0.0.0.0?**
- Default `localhost` binding only accepts connections from within the container
- `0.0.0.0` accepts connections from outside the container
- Essential for Docker port mapping

### Step 3: Create .dockerignore (Optional but Recommended)

Create `server/.dockerignore`:

```
node_modules
npm-debug.log
.env
.git
.gitignore
README.md
```

This prevents unnecessary files from being copied into the Docker image.

### Step 4: Build and Test Backend Docker Image

```bash
cd server
docker build -t express-server .
docker run -p 4000:4000 express-server
```

Test:
```bash
curl http://localhost:4000/api/message
```

---

## 6. Dockerizing the Frontend

### Step 1: Create Frontend Dockerfile (Multi-stage Build)

Create `client/Dockerfile`:

```dockerfile
# Build stage
FROM node:22-alpine AS build

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci || npm install

# Copy source code
COPY . .

# Build the application
RUN npm run build

# Production stage
FROM node:22-alpine

WORKDIR /app

# Install serve (static file server)
RUN npm i -g serve

# Copy built files from build stage
COPY --from=build /app/dist ./dist

# Expose port
EXPOSE 3000

# Start server
CMD ["serve", "-s", "dist", "-l", "3000"]
```

**Explanation:**
- **Multi-stage build**: Reduces final image size
- **Build stage**: Compiles React app
- **Production stage**: Serves static files with `serve`
- `serve -s dist -l 3000`: Serve `dist` folder on port 3000

### Step 2: Create .dockerignore for Frontend

Create `client/.dockerignore`:

```
node_modules
dist
npm-debug.log
.env
.git
.gitignore
README.md
```

### Step 3: Build and Test Frontend Docker Image

```bash
cd client
docker build -t react-client .
docker run -p 5174:3000 react-client
```

**Note:** Map host port `5174` to container port `3000`

Test: Open `http://localhost:5174` in browser

### Step 4: Update Frontend for Production

For production, update `src/App.jsx` to use environment variables:

```javascript
import { useState, useEffect } from "react";
import "./App.css";

function App() {
  const [message, setMessage] = useState("");

  useEffect(() => {
    // Use environment variable or fallback
    const apiUrl = import.meta.env.VITE_API_URL || "http://localhost:4000";
    
    fetch(`${apiUrl}/api/message`)
      .then((res) => res.json())
      .then((data) => setMessage(data.message))
      .catch((err) => {
        console.error("Error fetching message:", err);
        setMessage("Failed to load message");
      });
  }, []);

  return (
    <>
      <h1>ChaiCode frontend</h1>
      <p>{message}</p>
    </>
  );
}
```

Create `.env.production`:

```
VITE_API_URL=http://localhost:4000
```

---

## 7. Docker Compose Setup

### Step 1: Create docker-compose.yml

Create `docker-compose.yml` in the project root:

```yaml
version: "3.9"

services:
  server:
    build: ./server
    container_name: express-server
    ports:
      - "4000:4000"
    networks:
      - fullstack-net
    restart: unless-stopped

  client:
    build: ./client
    container_name: react-client
    ports:
      - "5174:3000"
    networks:
      - fullstack-net
    depends_on:
      - server
    restart: unless-stopped

networks:
  fullstack-net:
    driver: bridge
```

### Step 2: Understanding Docker Compose Configuration

#### Services

**Server Service:**
- `build: ./server`: Build context for server Dockerfile
- `container_name`: Custom container name
- `ports: "4000:4000"`: Map host port 4000 to container port 4000
- `networks`: Assign to custom network
- `restart`: Auto-restart policy

**Client Service:**
- `build: ./client`: Build context for client Dockerfile
- `ports: "5174:3000"`: Map host port 5174 to container port 3000
- `depends_on`: Wait for server to start first
- `networks`: Same network as server for communication

#### Networks

- `fullstack-net`: Custom bridge network
- Allows containers to communicate by service name
- Provides isolation from other Docker networks

### Step 3: Important Networking Concepts

#### Container-to-Container Communication

Within Docker network, containers can communicate using service names:

```javascript
// From within a container on the same network:
fetch("http://server:4000/api/message") // Works!
```

#### Browser-to-Container Communication

Browsers **cannot** use Docker service names. They must use:

- `localhost` (when accessing from host machine)
- Public IP or domain (when accessing remotely)

```javascript
// From browser - MUST use localhost or public IP
fetch("http://localhost:4000/api/message") // ✅ Works
fetch("http://server:4000/api/message")    // ❌ Browser can't resolve "server"
```

### Step 4: Run with Docker Compose

```bash
# Build and start all services
docker-compose up --build

# Run in detached mode
docker-compose up -d --build

# View logs
docker-compose logs -f

# Stop services
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

### Step 5: Update CORS for Production Deployment

When deploying to a server with a public IP, update backend CORS:

```javascript
const allowedOrigins = [
  "http://localhost:5174",
  "http://localhost:3000",
  "http://localhost:5173",
  "http://YOUR_PUBLIC_IP:5174", // Add your production IP
  // Or use environment variable:
  // ...(process.env.ALLOWED_ORIGINS?.split(",") || []),
];
```

### Step 6: Production Considerations

#### Environment Variables

Create `.env` for Docker Compose (optional):

```env
ALLOWED_ORIGINS=http://localhost:5174,http://YOUR_PUBLIC_IP:5174
```

Update `docker-compose.yml` to use env vars:

```yaml
services:
  server:
    build: ./server
    environment:
      - ALLOWED_ORIGINS=${ALLOWED_ORIGINS}
    # ... rest of config
```

#### Health Checks

Add health checks to ensure services are ready:

```yaml
services:
  server:
    # ... other config
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:4000/api/message"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s

  client:
    # ... other config
    depends_on:
      server:
        condition: service_healthy
```

---

## Complete File Structure

```
fullstack-docker/
├── server/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── package.json
│   ├── package-lock.json
│   └── index.js
├── client/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── package.json
│   ├── package-lock.json
│   ├── vite.config.js
│   ├── index.html
│   └── src/
│       ├── App.jsx
│       └── main.jsx
└── docker-compose.yml
```

---

## Common Issues and Solutions

### Issue 1: CORS Error in Browser

**Problem:** Browser blocks API requests from different origin.

**Solution:** 
- Configure CORS on backend with correct origins
- Ensure production IP/domain is in allowed origins list

### Issue 2: Can't Access Container from Browser

**Problem:** Container running but can't access on `localhost`.

**Solutions:**
- Ensure Express binds to `0.0.0.0` (not `localhost`)
- Check port mappings in docker-compose.yml
- Verify firewall settings

### Issue 3: Container-to-Container Communication Not Working

**Problem:** Services can't communicate using service names.

**Solution:**
- Ensure both services are on the same network
- Use service names (not `localhost`) within containers
- Check network configuration in docker-compose.yml

### Issue 4: Frontend Can't Connect to Backend

**Problem:** Frontend build can't reach backend API.

**Solutions:**
- Use environment variables for API URL
- Update CORS to include production origins
- Verify backend is accessible from browser (not just containers)

---

## Quick Reference Commands

```bash
# Backend development
cd server
npm install
npm start

# Frontend development
cd client
npm install
npm run dev

# Docker backend
cd server
docker build -t express-server .
docker run -p 4000:4000 express-server

# Docker frontend
cd client
docker build -t react-client .
docker run -p 5174:3000 react-client

# Docker Compose
docker-compose up --build
docker-compose down
docker-compose logs -f
docker-compose ps
```



## Summary

This guide covered:

1. Express backend setup with CORS
2. React frontend with Vite
3. Understanding CORS and Vite proxy
4. Proper CORS configuration
5. Dockerizing backend
6. Dockerizing frontend (multi-stage build)
7. Docker Compose orchestration

Key Takeaways:
- CORS is essential for browser-based API calls
- Vite proxy helps in development but production needs backend CORS
- Docker requires binding to `0.0.0.0` for external access
- Service names work for container-to-container communication
- Browsers need `localhost` or public IPs, not Docker service names

---

** Happy Coding over a chai! **

