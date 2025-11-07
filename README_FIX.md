# Fix for ECONNREFUSED Error

## Problem
The server was failing to start with the error:
```
Error: connect ECONNREFUSED 194.182.64.209:3000
```

## Root Cause
The application was trying to connect to `http://194.182.64.209:3000` during startup before the server had started listening on that port. This caused a connection refused error.

## Solution
Changed the `address` field in `data.js` from the external IP address (`http://194.182.64.209:3000`) to localhost (`http://127.0.0.1:3000`).

### Changes Made:
1. Modified `data.js`:
   - Old: `address: "http://194.182.64.209:3000"`
   - New: `address: "http://127.0.0.1:3000"`

2. Added `.gitignore` to exclude `node_modules/` and `package-lock.json`

## Why This Works
- When the server starts, it binds to all network interfaces (0.0.0.0:3000 or similar)
- Internal health checks and self-connections should use `127.0.0.1` (localhost) instead of external IPs
- This ensures the server can connect to itself immediately after starting, regardless of the external IP address
- External clients can still connect using the server's public IP (`194.182.64.209:3000`)

## Testing
To test the server:
```bash
npm install
npm start
```

Note: The server requires access to Telegram API (`api.telegram.org`). If you see `ENOTFOUND api.telegram.org` errors, ensure your network allows outbound HTTPS connections to Telegram's servers.

## Deployment
When deploying this server on a machine with IP `194.182.64.209`:
1. The server will bind to port 3000 on all interfaces
2. Internal operations use `127.0.0.1:3000` for self-communication
3. External clients connect using `http://194.182.64.209:3000`
