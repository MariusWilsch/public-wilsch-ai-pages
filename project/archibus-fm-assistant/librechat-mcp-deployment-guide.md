---
publish: true
---

# LibreChat + MCP Server Deployment Guide

[[client-archibus]]

Operational deployment guide for the Bruce BEM FM Assistant (LibreChat + MCP Server).

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Bruce BEM Platform                           │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Rain (Auth System) delivers tokens via iframe URL params   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              │                                      │
│              userToken + refreshToken                               │
│                              ▼                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                    LibreChat (port 3080)                    │   │
│  │  - Captures tokens from URL (App.jsx)                       │   │
│  │  - Stores in sessionStorage                                 │   │
│  │  - Passes to MCP via headers (librechat.yaml)               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              │                                      │
│           X-Rain-User-Token + X-Rain-Refresh-Token headers          │
│                              ▼                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                    MCP Server (port 8000)                   │   │
│  │  - Extracts Rain tokens from headers                        │   │
│  │  - Uses tokens for Bruce BEM API authentication             │   │
│  │  - Provides: search_assets, create_work_request             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              │                                      │
│                       Authenticated API calls                       │
│                              ▼                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                     Bruce BEM API                           │   │
│  │  - services.bruceplatform.com (BEM API)                     │   │
│  │  - userauth.bruceplatform.com (User Auth)                   │   │
│  └─────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

## Prerequisites

### Software Requirements

- Docker & Docker Compose
- Git
- Node.js 18+ (for MCP Inspector, optional)

### Network Requirements

- Port 3080: LibreChat web interface
- Port 8000: MCP server endpoint
- External Docker network: `archibus_full-stack_archibus_network`

## Environment Variables

### MCP Server (.env in project root)

| Variable | Description | Example |
|----------|-------------|---------|
| `USER_AUTH_URL` | Bruce BEM user auth API | `https://userauth.bruceplatform.com/api/` |
| `BEM_API_URL` | Bruce BEM services API | `https://services.bruceplatform.com/api/` |
| `USER_API_CLIENT_ID` | Client ID for user auth | `djfheytif4857gheydh` |
| `CLIENT_SECRET` | Client secret for user auth | `[provided by Bruce BEM]` |
| `OAUTH_CLIENT_ID` | OAuth client ID (bearer token) | `VGnlis5dJ9nl0Z8Kut8EedsvulcoyYpK` |
| `OAUTH_URL` | OAuth token endpoint | `https://dev-aaoqaqk2g1r6y1co.eu.auth0.com/oauth/token` |
| `GRANT_TYPE` | OAuth grant type | `client_credentials` |
| `AUDIENCE` | OAuth audience | `https://dev-aaoqaqk2g1r6y1co.eu.auth0.com/api/v2/` |

**Note:** `USERNAME` and `PASSWORD` are legacy - not used with Rain token auth.

### LibreChat (.env in services/librechat/)

| Variable | Description | Example |
|----------|-------------|---------|
| `HOST` | Bind address | `localhost` |
| `PORT` | LibreChat port | `3080` |
| `MONGO_URI` | MongoDB connection | `mongodb://127.0.0.1:27017/LibreChat` |
| `DOMAIN_CLIENT` | Client domain | `http://localhost:3080` |
| `DOMAIN_SERVER` | Server domain | `http://localhost:3080` |
| `MEILI_MASTER_KEY` | MeiliSearch key | `[generate random string]` |
| `ANTHROPIC_API_KEY` | Anthropic API key | `sk-ant-api03-...` |
| `OPENROUTER_KEY` | OpenRouter API key (optional) | `sk-or-v1-...` |

## Deployment Steps

### Step 1: Clone Repository

```bash
git clone https://github.com/veloxforce/ARCHIBUS__archibus-poc.git
cd ARCHIBUS__archibus-poc
```

### Step 2: Create Docker Network

```bash
docker network create archibus_full-stack_archibus_network
```

### Step 3: Configure Environment Variables

**MCP Server:**
```bash
cp .env.example .env
# Edit .env with Bruce BEM API credentials
```

**LibreChat:**
```bash
cp services/librechat/.env.example services/librechat/.env
# Edit with API keys and MongoDB settings
```

### Step 4: Deploy MCP Server

```bash
docker compose up -d --build archibus_fastmcp
```

Verify:
```bash
docker ps | grep archibus_fastmcp
docker logs archibus_fastmcp --tail 20
```

Expected output:
```
INFO     Starting MCP server 'bruce-bem-tools' with transport 'http' on http://0.0.0.0:8000/mcp
INFO     Uvicorn running on http://0.0.0.0:8000
```

### Step 5: Deploy LibreChat

```bash
cd services/librechat
docker compose -f docker-compose.dev.yml up -d --build
```

Verify:
```bash
docker ps | grep LibreChat
```

### Step 6: Verify MCP Connection

Check LibreChat can reach MCP:
```bash
curl -X POST https://archibus-mcp.wilsch-deployment.com/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Rain Token Flow Verification

### Expected Behavior

1. **Without Rain tokens:** MCP rejects with error
   ```
   ERROR:src.api:[BRUCE_BEM] Missing X-Rain-User-Token header - authentication rejected
   ```

2. **With Rain tokens (via Bruce BEM iframe):** MCP authenticates and executes tools

### Testing via Bruce BEM

1. Open Bruce BEM application: `https://dev-sp.bruceplatform.com`
2. Navigate to the LibreChat iframe
3. Send a message like "Search for assets with pump"
4. Check MCP logs for successful authentication:
   ```
   INFO:[BRUCE_BEM] Using Rain user token: abc123...
   ```

## Makefile Commands

```bash
# MCP Server
docker compose up -d --build archibus_fastmcp  # Build and start
docker compose restart archibus_fastmcp        # Restart
docker logs archibus_fastmcp --tail 50         # View logs

# LibreChat (custom build)
cd services/librechat
docker compose -f docker-compose.dev.yml up -d --build  # Build and start
docker compose -f docker-compose.dev.yml restart api    # Restart LibreChat
docker compose -f docker-compose.dev.yml logs -f api    # View logs
```

## Troubleshooting

### MCP Server Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| `Missing X-Rain-User-Token header` | Accessing without Bruce BEM iframe | Access via Bruce BEM platform |
| Container unhealthy | Health check timing | Wait 60s, check logs |
| Connection refused | Network not created | Run `docker network create ...` |

### LibreChat Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| `invalid x-api-key` | Anthropic API key invalid | Update `.env` with valid key |
| MCP tools not appearing | Model not selected | Select Claude model in UI |
| Auth error | No Rain tokens | Access via Bruce BEM iframe |

## Key URLs

| Service | URL | Purpose |
|---------|-----|---------|
| LibreChat | `https://archibus-chat.wilsch-deployment.com` | Chat interface |
| MCP Server | `https://archibus-mcp.wilsch-deployment.com/mcp` | MCP endpoint |
| Bruce BEM (Dev) | `https://dev-sp.bruceplatform.com` | Main application |

## Security Notes

1. **Rain tokens are mandatory** - MCP server rejects requests without valid tokens
2. **No credential fallback** - Hardcoded credentials removed (PR #7/PR #9)
3. **Token refresh** - MCP handles automatic token refresh on 401 responses
4. **Session isolation** - Each user's tokens are cached separately

---

*Last updated: 2026-01-09*
*Related: [PR #9](https://github.com/veloxforce/ARCHIBUS__archibus-poc/pull/9), [Issue #7](https://github.com/DaveX2001/deliverable-tracking/issues/7)*
