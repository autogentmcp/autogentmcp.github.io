# MCP Portal - Management Interface

The **MCP Portal** is the central management interface for the entire Autogent MCP ecosystem. It's a professional Next.js web application that provides comprehensive application management, environment configuration, API key generation, and enterprise-grade security provider integration.

## 🎯 Overview

The Portal serves as the **starting point** for all MCP ecosystem operations. Before you can use any other component in the ecosystem, you need to:

1. **Create applications** through the Portal
2. **Configure environments** (development, staging, production)
3. **Generate API keys** for authentication
4. **Set up security providers** for credential management
5. **Manage application secrets** securely

## 🚀 Key Features

### 📱 Application Management
- **Create, view, edit, and delete** MCP applications
- **Application metadata** management (name, description, endpoints)
- **Multi-environment support** with isolated configurations
- **Application lifecycle** management

### 🔐 Security & Authentication
- **JWT-based authentication** with role-based access control
- **API key generation** with configurable expiration
- **11 Authentication methods** support for applications
- **Enterprise security provider** integration

### 🛡️ Security Provider Integration
Support for **5 major security providers**:
- **HashiCorp Vault** - Industry standard secret management
- **Azure Key Vault** - Microsoft Azure's secret management service
- **Akeyless** - Cloud-native secret management platform
- **Google Cloud Secret Manager** - Google Cloud's secret management service
- **AWS Secrets Manager** - Amazon Web Services secret management

### 🎨 User Interface
- **Professional, responsive design** with dark mode support
- **Admin dashboard** with comprehensive controls
- **Real-time updates** and notifications
- **Intuitive navigation** and user experience

## 🏗️ Architecture

```mermaid
graph TB
    subgraph "MCP Portal (Next.js)"
        A[Web Interface]
        B[API Routes]
        C[Authentication]
        D[Security Provider Service]
    end
    
    subgraph "Database"
        E[SQLite/PostgreSQL<br/>Prisma ORM]
    end
    
    subgraph "Security Providers"
        F[HashiCorp Vault]
        G[Azure Key Vault]
        H[Akeyless]
        I[GCP Secret Manager]
        J[AWS Secrets Manager]
    end
    
    subgraph "MCP Ecosystem"
        K[MCP Registry Server]
        L[Autogent MCP Server]
        M[Client Applications]
    end
    
    A --> B
    B --> C
    B --> D
    B --> E
    D --> F
    D --> G
    D --> H
    D --> I
    D --> J
    B --> K
    K --> L
    L --> M
    
    style A fill:#e1f5fe
    style D fill:#fff3e0
    style E fill:#f3e5f5
```

## 🛠️ Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/autogentmcp/portal.git
cd portal
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Environment Configuration

Create your environment file:

```bash
cp .env.example .env
```

Basic configuration in `.env`:

```env
# Database
DATABASE_URL="file:./dev.db"

# JWT Authentication
JWT_SECRET="your-super-secret-jwt-key-here"

# Application
NODE_ENV="development"
PORT=3000

# Security Provider (choose one)
SECURITY_PROVIDER="hashicorp_vault"
```

### 4. Choose Your Security Provider

Select and configure one of the supported security providers:

#### HashiCorp Vault Configuration

```env
SECURITY_PROVIDER="hashicorp_vault"
VAULT_URL="https://vault.example.com"
VAULT_TOKEN="hvs.XXXXXXXXXXXX"
VAULT_NAMESPACE="admin"
VAULT_PATH="secret/data/mcp"
VAULT_MOUNT="kv"
```

**Setup Steps:**
```bash
# Install and start Vault (development)
vault server -dev

# Configure Vault
export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN="your-vault-token"

# Enable KV secrets engine
vault secrets enable -version=2 kv

# Create MCP policy
vault policy write mcp-policy - <<EOF
path "secret/data/mcp/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}
EOF
```

#### Azure Key Vault Configuration

```env
SECURITY_PROVIDER="azure_keyvault"
AZURE_KEYVAULT_URL="https://myvault.vault.azure.net/"
AZURE_TENANT_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
AZURE_CLIENT_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
AZURE_CLIENT_SECRET="your-client-secret"
```

**Setup Steps:**
```bash
# Create KeyVault
az keyvault create --name "mcp-keyvault" --resource-group "mcp-rg" --location "East US"

# Create service principal
az ad sp create-for-rbac --name "mcp-sp" --role "Key Vault Secrets Officer"

# Set access policies
az keyvault set-policy --name "mcp-keyvault" --spn {client-id} --secret-permissions get set list delete
```

#### Akeyless Configuration

```env
SECURITY_PROVIDER="akeyless"
AKEYLESS_URL="https://api.akeyless.io"
AKEYLESS_ACCESS_ID="p-xxxxxxxx"
AKEYLESS_ACCESS_KEY="your-access-key"
AKEYLESS_PATH="/mcp/secrets"
```

#### Google Cloud Secret Manager Configuration

```env
SECURITY_PROVIDER="gcp_secret_manager"
GCP_PROJECT_ID="your-project-id"
GCP_SERVICE_ACCOUNT_KEY_PATH="/path/to/service-account.json"
GCP_SECRET_PATH_PREFIX="mcp-secrets"
```

#### AWS Secrets Manager Configuration

```env
SECURITY_PROVIDER="aws_secrets_manager"
AWS_REGION="us-east-1"
AWS_ACCESS_KEY_ID="your-access-key-id"
AWS_SECRET_ACCESS_KEY="your-secret-access-key"
AWS_SECRETS_PATH_PREFIX="mcp/secrets"
```

### 5. Database Setup

```bash
# Generate Prisma client
npx prisma generate

# Run database migrations
npx prisma migrate dev

# (Optional) Seed with sample data
node create-sample-data.js
```

### 6. Start the Portal

```bash
# Development mode
npm run dev

# Production mode
npm run build
npm start
```

The Portal will be available at `http://localhost:3000`

## 🎮 Usage Guide

### 1. Initial Setup

1. **Access the Portal** at `http://localhost:3000`
2. **Create an admin account** during first run
3. **Configure your security provider** in the settings
4. **Test the connection** to ensure proper setup

### 2. Application Management

#### Creating a New Application

1. **Navigate to Applications** → **Create New**
2. **Fill in application details**:
   - Application Name
   - Description
   - Base URL
   - Environment (dev/staging/prod)
3. **Configure security settings**:
   - Authentication method
   - Rate limiting
   - Custom headers
4. **Generate API keys** for the application
5. **Save and deploy** the configuration

#### Environment Configuration

For each application, you can configure multiple environments:

- **Development**: Local development and testing
- **Staging**: Pre-production testing
- **Production**: Live production environment

Each environment has:
- **Separate API keys**
- **Independent security settings**
- **Isolated secret storage**
- **Different rate limits**

### 3. Security Configuration

#### Authentication Methods

Configure authentication for your applications:

1. **API Key**: Header-based or query parameter
2. **Bearer Token**: JWT or OAuth2 tokens
3. **Basic Auth**: Username/password
4. **OAuth2**: Full OAuth2 flow
5. **Custom**: Custom authentication schemes

#### Secret Management

1. **Navigate to** Application → **Secrets**
2. **Add secrets** for external API calls:
   - API keys
   - Bearer tokens
   - Custom headers
   - OAuth credentials
3. **Secrets are stored** in your configured security provider
4. **Test connections** to ensure proper setup

### 4. API Key Management

1. **Generate API keys** for each application/environment
2. **Set expiration dates** for security
3. **Monitor key usage** and statistics
4. **Rotate keys** when needed
5. **Revoke compromised keys** immediately

## 🔌 API Endpoints

### Application Management

```bash
# List all applications
GET /api/applications

# Create new application
POST /api/applications
{
  "name": "My App",
  "description": "Application description",
  "baseUrl": "https://api.example.com",
  "environment": "development"
}

# Get application details
GET /api/applications/{id}

# Update application
PUT /api/applications/{id}

# Delete application
DELETE /api/applications/{id}
```

### Secret Management

```bash
# Get application secrets
GET /api/applications/{id}/secrets

# Store application secrets
POST /api/applications/{id}/secrets
{
  "secrets": {
    "api_key": "your-api-key",
    "bearer_token": "your-bearer-token"
  }
}

# Update application secrets
PUT /api/applications/{id}/secrets
```

### Authentication

```bash
# User login
POST /api/auth/login
{
  "email": "user@example.com",
  "password": "password"
}

# User registration
POST /api/auth/register
{
  "email": "user@example.com",
  "password": "password",
  "name": "User Name"
}

# User logout
POST /api/auth/logout
```

## 🔗 Integration with MCP Ecosystem

The Portal integrates seamlessly with the rest of the MCP ecosystem:

### 1. Registry Server Integration

- **Automatic registration** of applications with the Registry
- **Health monitoring** setup and configuration
- **Endpoint discovery** and metadata management

### 2. Autogent MCP Server Integration

- **API key validation** for server requests
- **Secret retrieval** for external API calls
- **Authentication header** injection

### 3. Client SDK Integration

- **Application metadata** for SDK initialization
- **Environment-specific** configuration
- **Authentication credentials** for registry access

## 🔒 Security Best Practices

### 1. Environment Variables
- **Never store secrets** in the database
- **Use environment variables** for all sensitive configuration
- **Rotate secrets regularly** through your security provider

### 2. Access Control
- **Implement role-based access** control
- **Use least-privilege principles** for service accounts
- **Monitor access logs** and audit trails

### 3. Secret Management
- **Store secrets** only in your security provider
- **Use separate secrets** for different environments
- **Enable secret rotation** where possible

### 4. API Security
- **Use HTTPS** for all communications
- **Implement rate limiting** to prevent abuse
- **Validate all inputs** and sanitize outputs

## 🚀 Deployment

### Production Deployment

1. **Environment Setup**:
   ```bash
   NODE_ENV=production
   DATABASE_URL="postgresql://user:pass@host:5432/db"
   ```

2. **Security Configuration**:
   - Use production security provider
   - Enable SSL/TLS
   - Configure proper CORS

3. **Database Migration**:
   ```bash
   npx prisma migrate deploy
   ```

4. **Build and Start**:
   ```bash
   npm run build
   npm start
   ```

### Docker Deployment

```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

EXPOSE 3000
CMD ["npm", "start"]
```

## 🧪 Testing

### Unit Tests
```bash
npm test
```

### Integration Tests
```bash
npm run test:integration
```

### Security Provider Tests
```bash
# Test vault connection
node test-vault-connection.js

# Test authentication fields
node test-auth-field.js
```

## 📊 Monitoring & Analytics

### Application Metrics
- **API key usage** statistics
- **Request/response** tracking
- **Error rate** monitoring
- **Performance metrics**

### Security Monitoring
- **Failed authentication** attempts
- **Secret access** audit logs
- **Rate limiting** violations
- **Suspicious activity** detection

## 🤝 Contributing

1. **Fork the repository**
2. **Create a feature branch**
3. **Make your changes**
4. **Add tests** if applicable
5. **Submit a pull request**

### Adding New Security Providers

To add a new security provider:

1. **Update SecurityProviderService** in `src/lib/security-provider.ts`
2. **Add environment configuration** in `.env.example`
3. **Implement provider methods**: `getSecrets()`, `setSecrets()`, `testConnection()`
4. **Update documentation** with setup instructions

## 📞 Support

For support and questions:
- **GitHub Issues**: Create issues for bugs and feature requests
- **Documentation**: Comprehensive setup and usage guides
- **Community**: Join discussions and get help from the community

---

**Next Steps**: After setting up the Portal, proceed to [MCP Registry Server](mcp-registry.md) to complete your ecosystem setup.
