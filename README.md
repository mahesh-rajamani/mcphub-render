# MCPHub - One-Click Deploy to Render

Deploy MCPHub to Render.com with a single click! MCPHub is the complete platform for Model Context Protocol (MCP) development - design, test, deploy, and manage MCP servers with ease.

## 🚀 Quick Deploy

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/mahesh-rajamani/mcphub-render)

Click the button above to deploy MCPHub to your Render account.

## 📋 What Gets Deployed

When you deploy MCPHub, you get:

- **MCP Studio** - Visual interface for creating and managing MCP configurations
- **MCP Bridge** - Runtime server that hosts multiple MCP servers from JSON configurations
- **Python Engine** - Python-based execution engine for custom tools
- **Complete Platform** - All-in-one Docker image with frontend, backend, and Python engine

## 🔐 Finding Your Credentials

After deployment completes, you'll need to find your auto-generated password to access MCPHub.

### Step 1: Navigate to Your Service

1. Go to your [Render Dashboard](https://dashboard.render.com/)
2. Click on your newly deployed service (named `mcphub-all`)

### Step 2: View Logs

1. In the service page, click on **"Logs"** in the left sidebar
2. Look for the startup logs from the container

### Step 3: Find Your Password

Look for a log entry that shows:

```
=================================================
MCPHub Authentication Credentials
=================================================
   Username: admin
   Password: [your-auto-generated-password]
=================================================
IMPORTANT: Save these credentials immediately!
=================================================
```

**Important:** The password is auto-generated during deployment. Make sure to save it immediately as it will only be shown once in the logs.

### Alternative: Check Environment Variables

You can also find your password in the environment variables:

1. In your service page, click on **"Environment"** in the left sidebar
2. Look for the `MCPHUB_AUTH_PASSWORD` variable
3. Click the eye icon to reveal the password

## 🌐 Accessing MCPHub

Once deployed, your MCPHub instance will be available at:

```
https://your-service-name.onrender.com
```

### First-Time Login

1. Navigate to your MCPHub URL
2. When prompted for authentication:
   - **Username:** `admin`
   - **Password:** [your-password-from-logs]
3. After login, you'll see the **Welcome Wizard** that will guide you through:
   - Backend URL configuration (auto-detected)
   - Tenant ID setup (auto-generated UUID)
   - Optional AI model configuration

## 🔑 Getting Your API Authentication Token

To use MCPHub's REST APIs programmatically, you need to create a Base64-encoded authentication token.

### Creating Your Auth Token

The API uses HTTP Basic Authentication. You need to encode your credentials in the format `username:password` as Base64.

**Using Command Line (Mac/Linux):**

```bash
echo -n "admin:your-password" | base64
```

**Using Command Line (Windows PowerShell):**

```powershell
[Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("admin:your-password"))
```

**Example:**
If your password is `abc123def456`, run:
```bash
echo -n "admin:abc123def456" | base64
```

This will output something like: `YWRtaW46YWJjMTIzZGVmNDU2`

### Using the Token in API Calls

Once you have your Base64-encoded token, use it in the `Authorization` header:

**Using curl:**

```bash
curl -H "Authorization: Basic YWRtaW46YWJjMTIzZGVmNDU2" \
     https://your-service-name.onrender.com/api/endpoint
```

**Using Python:**

```python
import requests

headers = {
    'Authorization': 'Basic YWRtaW46YWJjMTIzZGVmNDU2'
}

response = requests.get('https://your-service-name.onrender.com/api/endpoint', headers=headers)
```

**Using JavaScript/Node.js:**

```javascript
const headers = {
    'Authorization': 'Basic YWRtaW46YWJjMTIzZGVmNDU2'
};

fetch('https://your-service-name.onrender.com/api/endpoint', { headers })
    .then(response => response.json())
    .then(data => console.log(data));
```

### Common API Endpoints

- **Admin API**: `https://your-service-name.onrender.com/admin/*`
- **MCP Configuration API**: `https://your-service-name.onrender.com/api/*`
- **Health Check** (no auth required): `https://your-service-name.onrender.com/health`
- **OpenAPI Spec**: `https://your-service-name.onrender.com/q/openapi`
- **Swagger UI**: `https://your-service-name.onrender.com/swagger-ui`

All endpoints except `/health` require the Authorization header with your Basic auth token.

## 🔧 Configuration

### Environment Variables

The following environment variables are pre-configured in `render.yaml`:

| Variable | Default Value | Description |
|----------|---------------|-------------|
| `MCPHUB_AUTH_USERNAME` | `admin` | Basic auth username |
| `MCPHUB_AUTH_PASSWORD` | Auto-generated | Basic auth password (generated on first deploy) |
| `PORT` | `3000` | External port for the service |
| `QUARKUS_HTTP_PORT` | `8080` | Internal backend port |
| `MCP_STORAGE_PROVIDER` | `memory` | Storage provider (memory for free tier) |
| `MCP_ENVIRONMENT` | `production` | Environment mode |
| `LOG_LEVEL` | `info` | Logging level |

### Customizing Your Deployment

You can customize your deployment by:

1. **Forking this repository**
2. **Modifying `render.yaml`** with your preferred settings
3. **Deploying from your fork** instead of this repository

## 📚 Features

### Visual MCP Design
- Create MCP configurations through an intuitive UI
- Support for REST, gRPC, GraphQL, and SOAP protocols
- Real-time validation and testing

### Authentication Support
- JWT authentication with auto-refresh
- OAuth 2.0 (client credentials and authorization code flows)
- Custom OAuth2 providers (Keycloak, Auth0, AWS Cognito, etc.)
- Static headers and header forwarding

### AI-Powered Features
- Multi-model support (OpenAI, Anthropic, Groq, custom providers)
- Automated description generation
- Smart tool selection and improvement suggestions

### Role-Based Access Control
- Create and manage roles with specific MCP access
- Export/import role configurations
- Deploy roles to production

### Testing & Development
- Live tool introspection from deployed MCP servers
- Multi-query testing with versioning
- Fine-tuning dataset export for OpenAI

## 💡 Tips

### Saving Your Tenant ID

The Welcome Wizard will generate a UUID for your Tenant ID. Make sure to:
1. Copy the Tenant ID when shown
2. Save it in a secure location
3. You'll need this ID to access your MCP configurations if you redeploy

### Upgrading MCPHub

To upgrade to the latest version:

1. Go to your service in Render dashboard
2. Click **"Manual Deploy"** → **"Deploy latest commit"**
3. Or update the image tag in `render.yaml` and push changes

### Using Persistent Storage

The default deployment uses in-memory storage (free tier). For production:

1. Upgrade to a paid Render plan
2. Update `MCP_STORAGE_PROVIDER` to use a database
3. Add database connection environment variables

## 🏗️ Architecture

MCPHub uses a multi-service architecture:

```
┌─────────────────────────────────────┐
│         nginx (Port 3000)           │
│  - Frontend serving at /designer    │
│  - Backend proxy                    │
│  - Basic Authentication             │
└─────────────────────────────────────┘
           │
           ├──> Frontend (Next.js)
           │    - MCP Studio UI
           │    - Visual configuration
           │
           ├──> Backend (Quarkus:8080)
           │    - MCP Bridge runtime
           │    - REST/GraphQL APIs
           │    - Multi-tenant support
           │
           └──> Python Engine (Port 8082)
                - Custom tool execution
                - Python-based MCP tools
```

## 📝 License

MCPHub is licensed under the MIT License. See the main repository for details.

## 🤝 Contributing

Contributions are welcome! Please visit the main repository to contribute:
- [github.com/mahesh-rajamani/mcphub](https://github.com/mahesh-rajamani/mcphub)

---

**Main Repository**: [github.com/mahesh-rajamani/mcphub](https://github.com/mahesh-rajamani/mcphub)
