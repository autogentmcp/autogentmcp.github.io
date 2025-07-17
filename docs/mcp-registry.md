# MCP Registry Server

A high-performance, enterprise-grade Model Context Protocol registry server for managing applications, environments, and endpoints. Built with FastAPI and PostgreSQL with Prisma ORM.

## 🚀 Features

- **🏗️ Application Management**: Complete lifecycle management of applications and metadata
- **🔗 Endpoint Registry**: Comprehensive API endpoint documentation with detailed metadata
- **🌍 Multi-Environment Support**: Development, staging, production environment isolation
- **🛡️ API Key Authentication**: Secure access control with bcrypt hashing
- **📊 Health Monitoring**: Automatic health checks with configurable thresholds
- **📈 Audit Logging**: Complete audit trails for all registry operations
- **⚡ High Performance**: Optimized for production workloads with PostgreSQL

## 🏗️ Architecture

```mermaid
graph TB
    subgraph "Client Applications"
        A1[Spring Boot App]
        A2[FastAPI App]
        A3[Express App]
    end
    
    subgraph "MCP Registry Server"
        B[FastAPI Application]
        C[PostgreSQL Database]
        D[Prisma ORM]
        E[Authentication Layer]
        F[Health Check System]
    end
    
    subgraph "External Services"
        G[Application Health Endpoints]
    end
    
    A1 --> B
    A2 --> B
    A3 --> B
    B --> D
    D --> C
    B --> E
    B --> F
    F --> G
    
    style B fill:#e1f5fe
    style C fill:#f3e5f5
    style E fill:#fff3e0
    style F fill:#e8f5e8
```

## 🔧 Installation

### Prerequisites
- Python 3.9+
- PostgreSQL 12+
- Node.js 16+ (for Prisma CLI)

### Step 1: Clone and Setup

```bash
git clone https://github.com/autogentmcp/mcp-registry.git
cd mcp-registry

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Step 2: Database Setup

```bash
# Install Prisma CLI
npm install -g prisma

# Generate Prisma client
prisma generate

# Setup database (creates tables)
prisma db push
```

### Step 3: Configure Environment

```bash
# Copy environment template
cp .env.example .env

# Edit .env file
nano .env
```

**Required Environment Variables:**
```env
# Database
DATABASE_URL=postgresql://username:password@localhost:5432/mcp_registry

# Security
SECRET_KEY=your-super-secret-key-here
REGISTRY_ADMIN_KEY=your-admin-api-key-here

# Server
PORT=8000
HOST=0.0.0.0

# Health Check Configuration
HEALTH_CHECK_TIMEOUT=10
HEALTH_CHECK_INTERVAL=300
MAX_CONSECUTIVE_FAILURES=3
MAX_CONSECUTIVE_SUCCESSES=2
```

### Step 4: Start the Server

```bash
# Using the provided runner
python run_server.py

# Or directly with uvicorn
uvicorn src.mcp_registry.server:app --reload --host 0.0.0.0 --port 8000
```

## 📚 API Documentation

Once running, visit `http://localhost:8000/docs` for the interactive Swagger UI documentation.

## 🛡️ Authentication

The registry uses API key authentication with three levels of access:

### Headers Required
- **`X-API-Key`**: The API key for the application
- **`X-App-Key`**: The application key identifier
- **`X-Admin-Key`**: Admin key for administrative endpoints (admin only)
- **`X-Environment`**: Environment name (defaults to "production")

### Authentication Flow
1. Client sends API key in `X-API-Key` header
2. Registry looks up API keys for the application/environment
3. System uses bcrypt to verify the provided key against stored hashes
4. If valid, request is authenticated and authorized

## 📋 Core API Endpoints

### Application Management

#### Update Application
```http
PUT /applications/{app_key}
Content-Type: application/json
X-API-Key: your-api-key
X-App-Key: your-app-key

{
  "name": "My Application",
  "description": "Application description",
  "healthCheckUrl": "https://myapp.com/health"
}
```

#### Get Application
```http
GET /applications/{app_key}
X-Admin-Key: your-admin-key
```

### Endpoint Management

#### Register Endpoints (Batch)
```http
POST /register/endpoints
Content-Type: application/json
X-API-Key: your-api-key
X-App-Key: your-app-key
X-Environment: production

{
  "app_key": "your-app-key",
  "environment": "production",
  "endpoints": [
    {
      "name": "Get User",
      "path": "/api/users/{userId}",
      "method": "GET",
      "description": "Get user by ID",
      "isPublic": false,
      "authType": "API_KEY",
      "pathParams": {
        "userId": {
          "type": "string",
          "description": "User ID"
        }
      },
      "queryParams": {
        "include": {
          "type": "string",
          "description": "Fields to include"
        }
      },
      "responseBody": {
        "type": "object",
        "properties": {
          "id": {"type": "string"},
          "name": {"type": "string"}
        }
      }
    }
  ]
}
```

#### List Endpoints
```http
GET /endpoints?app_key=your-app-key&environment=production
```

### Administrative Endpoints

#### List All Applications
```http
GET /applications
X-Admin-Key: your-admin-key
```

#### List Applications with Endpoints
```http
GET /applications/with-endpoints?environment=production
X-Admin-Key: your-admin-key
```

## 🏥 Health Monitoring

The registry includes a sophisticated health monitoring system that automatically checks the health of registered applications.

### Features
- **Automatic Health Checks**: Periodic HTTP requests to application health endpoints
- **Environment-Specific**: Separate health monitoring for each environment
- **Configurable Thresholds**: Set failure/success counts before status changes
- **Health History**: Complete audit trail of health check results
- **Manual Triggers**: API endpoints to manually trigger health checks

### Health Status States
- **ACTIVE**: Application is healthy and responding
- **INACTIVE**: Application failed consecutive health checks
- **UNKNOWN**: Initial state or health check errors

### Configuration
```env
# Health check timeout (seconds)
HEALTH_CHECK_TIMEOUT=10

# Interval between health checks (seconds)
HEALTH_CHECK_INTERVAL=300

# Consecutive failures before marking inactive
MAX_CONSECUTIVE_FAILURES=3

# Consecutive successes before marking active
MAX_CONSECUTIVE_SUCCESSES=2
```

### Health Check APIs

#### Get Application Health
```http
GET /health-check/applications/{app_id}
X-Admin-Key: your-admin-key
```

#### Get Health Logs
```http
GET /health-check/applications/{app_id}/logs
X-Admin-Key: your-admin-key
```

#### Manual Health Check
```http
POST /health-check/applications/{app_id}/check
X-Admin-Key: your-admin-key
```

## 📊 Database Schema

### Core Tables

#### Applications
- `id`: Unique identifier
- `name`: Application name
- `description`: Application description
- `appKey`: Unique application key
- `healthCheckUrl`: Health check endpoint URL
- `healthStatus`: Current health status
- `lastHealthCheckAt`: Last health check timestamp
- `consecutiveFailures`: Count of consecutive failures
- `consecutiveSuccesses`: Count of consecutive successes

#### Environments
- `id`: Unique identifier
- `name`: Environment name (e.g., "production")
- `baseDomain`: Base domain for the environment
- `healthStatus`: Environment-specific health status
- `applicationId`: Foreign key to Applications table

#### Endpoints
- `id`: Unique identifier
- `name`: Endpoint name
- `path`: URL path with parameters
- `method`: HTTP method
- `description`: Endpoint description
- `isPublic`: Public accessibility flag
- `authType`: Authentication type required
- `pathParams`: Path parameter schema (JSON)
- `queryParams`: Query parameter schema (JSON)
- `requestBody`: Request body schema (JSON)
- `responseBody`: Response body schema (JSON)
- `environmentId`: Foreign key to Environments table

#### API Keys
- `id`: Unique identifier
- `hashedKey`: Bcrypt-hashed API key
- `isActive`: Active status
- `expiresAt`: Expiration timestamp
- `environmentId`: Foreign key to Environments table

## 🛠️ Development

### Project Structure
```
mcp-registry/
├── src/
│   └── mcp_registry/
│       ├── server.py              # FastAPI application
│       ├── models.py              # Pydantic models
│       ├── database.py            # Database connection
│       ├── auth.py                # Authentication logic
│       ├── endpoint_registration.py # Endpoint registration
│       └── config.py              # Configuration
├── prisma/
│   └── schema.prisma              # Database schema
├── examples/
│   ├── curl/                      # cURL examples
│   └── curl_examples.md           # Documentation examples
├── docs/                          # Additional documentation
├── requirements.txt               # Python dependencies
└── run_server.py                  # Server runner
```

### Running Tests
```bash
# Install test dependencies
pip install pytest pytest-asyncio

# Run tests
pytest tests/
```

### Database Migrations
```bash
# Generate migration after schema changes
prisma migrate dev --name describe_changes

# Apply migrations to production
prisma migrate deploy
```

## 🔄 Migration from Redis

If you're migrating from a Redis-based registry:

### Key Changes
1. **Batch Endpoint Registration**: New `/register/endpoints` endpoint for efficient bulk operations
2. **Environment-Specific API Keys**: API keys are now tied to specific environments
3. **Enhanced Authentication**: Bcrypt hashing for API keys
4. **Structured Data**: Proper relational data model vs key-value storage

### Migration Steps
1. Export data from Redis using provided migration script
2. Set up PostgreSQL database
3. Run migration script to import data
4. Update client applications to use new API format
5. Update authentication headers to include environment

## 🚀 Production Deployment

### Docker Deployment
```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
RUN prisma generate

EXPOSE 8000
CMD ["python", "run_server.py"]
```

### Environment Configuration
```env
# Production settings
DATABASE_URL=postgresql://user:pass@prod-db:5432/mcp_registry
SECRET_KEY=production-secret-key
REGISTRY_ADMIN_KEY=production-admin-key

# Security
CORS_ORIGINS=https://yourdomain.com,https://api.yourdomain.com
ALLOWED_HOSTS=yourdomain.com,api.yourdomain.com

# Performance
WORKERS=4
MAX_CONNECTIONS=20
```

### Health Monitoring
Monitor these endpoints for production health:
- `GET /health` - Overall system health
- `GET /health-check/applications/{app_id}` - Individual application health
- Database connection status
- API response times

## 🔍 Troubleshooting

### Common Issues

#### Database Connection Errors
```bash
# Check PostgreSQL is running
pg_isready -h localhost -p 5432

# Verify database exists
psql -h localhost -p 5432 -d mcp_registry -c "SELECT 1"

# Check connection string
echo $DATABASE_URL
```

#### Authentication Failures
```bash
# Verify API key exists in database
psql -d mcp_registry -c "SELECT * FROM \"ApiKey\" WHERE \"environmentId\" = 'your-env-id'"

# Check bcrypt hash
python -c "import bcrypt; print(bcrypt.checkpw(b'your-key', b'stored-hash'))"
```

#### Health Check Issues
```bash
# Check health check configuration
curl -H "X-Admin-Key: your-admin-key" http://localhost:8000/health-check/applications/app-id

# Manual health check
curl -X POST -H "X-Admin-Key: your-admin-key" http://localhost:8000/health-check/applications/app-id/check
```

## 📈 Performance Tuning

### Database Optimization
```sql
-- Add indexes for common queries
CREATE INDEX IF NOT EXISTS idx_applications_app_key ON "Application"("appKey");
CREATE INDEX IF NOT EXISTS idx_endpoints_environment ON "Endpoint"("environmentId");
CREATE INDEX IF NOT EXISTS idx_api_keys_environment ON "ApiKey"("environmentId");
```

### Caching Strategy
- Use Redis for session caching
- Implement application-level caching for frequently accessed data
- Enable PostgreSQL query caching

### Connection Pooling
```python
# Configure connection pool
DATABASE_URL=postgresql://user:pass@host:5432/db?pool_size=20&max_overflow=30
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass: `pytest`
6. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/autogentmcp/mcp-registry/blob/main/LICENSE) file for details.

**Note**: LICENSE file will be added to the repository soon. See the [Contributing Guide](contributing.md#license) for full license details.

---

**Next Steps**: Explore the [Autogent MCP Server](autogentmcp_server.md) for intelligent orchestration or check out our [Java SDK](mcp-core-java.md) for easy integration.
