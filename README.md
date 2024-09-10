# Tridecco Game

## Overview

`Tridecco` is an online multiplayer board game based on the 13-sided "Einstein" shape discovered in 2023. Players aim to get rid of all their pieces by forming hexagons using Einstein shapes of two different colors. Forming a hexagon grants the player an additional turn, while they try to block their opponents from doing the same.

This project consists of two major components:

1. **Tridecco Game Server**: The backend server that manages game state, player connections, and game logic.
2. **Tridecco Client (Web)**: A frontend web application providing user interface rendering, dynamic content loading, and interaction handling for gameplay.

## Features

- **Real-time Multiplayer**: Real-time gameplay using Socket.IO for bidirectional communication between clients and server.
- **Game Queueing**: Players can queue up to join a game with other players. The server will automatically match players based on their queue order.
- **Game Room Management**: Players can create, join, and leave game rooms.
- **User Authentication**: Users can create accounts, authenticate, and 2FA for secure login.
- **Leaderboard**: Track player rankings and statistics.
- **Game History**: View past game results, including player scores, moves, and game duration.
- **Responsive Design**: Mobile-friendly UI for cross-platform compatibility.

## Table of Contents

- [Quick Start](#quick-start)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [HTTPS Configuration](#https-configuration)
- [API Documentation](API_DOCUMENTATION.md)

## Quick Start

### Prerequisites

Ensure you have the following installed on your machine:

- Node.js (v14.x or later)
- MongoDB (for game data storage)
- Nginx (for reverse proxy)

### Installation

1. Clone the repositories for the game server and client:

```bash
git clone https://github.com/tridecco/game-server.git
git clone https://github.com/tridecco/game-client-web.git
```

2. Configure the server:

- Navigate to the `game-server` directory and install dependencies:

```bash
cd game-server
npm install
```

- Navigate to the `game-server/config.js` file and adjust the values for:
  - `host`: The hostname or IP address of the server. (For example, `localhost`)
  - `port`: The port number for the server. (For example, `5000`)
  - `https`: HTTPS settings (enabled/disabled). (For example, `false`)
  - `mongo.uri`: MongoDB connection URI.
  - `session.secret`: Secret key for session management.
  - `auth.email2FA.transporter`: Email transporter settings for 2FA.

> **Note**: Use example values if you are unsure about the configuration settings.

3. Configure the client:

- Navigate to the `game-client-web` directory, install dependencies, and build the CSS:

```bash
cd game-client-web
npm install
npm run build:css
```

- Navigate to the `game-client-web/config.js` file and adjust the values for:
  - `host`: The hostname or IP address of the server. (For example, `localhost`)
  - `port`: The port number for the server. (For example, `3000`)
  - `https`: HTTPS settings (enabled/disabled).

> **Note**: Use example values if you are unsure about the configuration settings.

- Navigate to the `game-client-web/public/js/app.js` file and go to the bottom of the file to adjust the value for `serverURL` and `socketURL` to match the server configuration:

```javascript
// Create a new App object (global variable)
const app = new App("/api", "/");
```

> **Note**: The first parameter is the API endpoint, and the second parameter is the socket endpoint. (The example values are for reverse proxy configuration. Use example values if you are unsure about the configuration settings.)

4. Start the server:

- Navigate to the `game-server` directory and start the server:

```bash
npm start
```

- The server should now be running on the specified host and port.

5. Start the client:

- Navigate to the `game-client-web` directory and start the client:

```bash
npm start
```

- The client should now be running on the specified host and port.

6. Set up Nginx:

- Configure Nginx to serve the client and proxy requests to the server. Here is an example configuration:

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name yourdomain.com;

    # Serve the game client
    location / {
        proxy_pass http://localhost:3000;  # Port where the game-client-web is running
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # API requests go to the backend server
    location /api {
        proxy_pass http://localhost:5000;  # Port where the game-server is running
        rewrite ^/api/?(.*)$ /$1 break;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Handle WebSocket connections
    location /socket.io/ {
        proxy_pass http://localhost:5000;  # WebSocket for game-server
        proxy_http_version 1.1;  # Ensure WebSocket compatibility
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

> **Note**: Replace `yourdomain.com` with your actual domain name. Use `localhost` if you are running the server locally. If you are using example values for the server configuration, use the corresponding port numbers.

7. Access the game:

- Open a web browser and navigate to the domain or IP address where the client is hosted.

### HTTPS Configuration

To enable HTTPS, you need to obtain an SSL certificate and configure Nginx to use it or configure the server directly. Here is an example configuration for Nginx:

```nginx
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name yourdomain.com;

    ssl_certificate /path/to/fullchain.pem;
    ssl_certificate_key /path/to/privkey.pem;

    # Redirect HTTP to HTTPS
    if ($scheme != "https") {
        return 301 https://$host$request_uri;
    }

    # Serve the game client
    location / {
        proxy_pass http://localhost:3000;  # Port where the game-client-web is running
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # API requests go to the backend server
    location /api {
        proxy_pass http://localhost:5000;  # Port where the game-server is running
        rewrite ^/api/?(.*)$ /$1 break;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Handle WebSocket connections
    location /socket.io/ {
        proxy_pass http://localhost:5000;  # WebSocket for game-server
        proxy_http_version 1.1;  # Ensure WebSocket compatibility
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

> **Note**: Replace `yourdomain.com` with your actual domain name. Use `localhost` if you are running the server locally. Replace `/path/to/fullchain.pem` and `/path/to/privkey.pem` with the actual paths to your SSL certificate files.
