# MCP Core Python SDK

**Coming Soon** - Python SDK for FastAPI, Flask, and Django integration.

## 🎯 Overview

The MCP Core Python SDK will provide seamless integration between Python web frameworks and the Autogent MCP ecosystem. Built with modern Python features including type hints, async/await, and Pydantic validation.

## 🚀 Planned Features

### 🐍 Framework Support
- **FastAPI**: Native async support with automatic OpenAPI generation
- **Flask**: Lightweight integration with decorator-based registration
- **Django**: Full Django integration with middleware support
- **Starlette**: Low-level ASGI framework support

### 🎨 Modern Python Features
- **Type Hints**: Full type safety with mypy compatibility
- **Async/Await**: Native async support for high-performance applications
- **Pydantic**: Request/response validation with automatic serialization
- **Dataclasses**: Support for Python dataclasses and attrs

### 🔧 Developer Experience
- **Decorator-based**: Simple `@mcp_tool` decorator for endpoint registration
- **Auto-discovery**: Automatic route introspection and metadata generation
- **Hot Reload**: Development server integration with auto-reload
- **IDE Support**: Full IntelliSense and autocomplete support

## 📋 Roadmap

### Phase 1: Core SDK (Coming Q1 2025)
- [ ] Basic MCP client implementation
- [ ] FastAPI integration
- [ ] Endpoint registration and discovery
- [ ] Authentication support

### Phase 2: Extended Framework Support (Coming Q2 2025)
- [ ] Flask integration
- [ ] Django integration
- [ ] Starlette integration
- [ ] Async/await optimization

### Phase 3: Advanced Features (Coming Q3 2025)
- [ ] Middleware support
- [ ] Custom serialization
- [ ] Plugin system
- [ ] Performance optimizations

## 🔮 Planned API Design

### FastAPI Integration

```python
from fastapi import FastAPI
from mcp_core_python import MCPApp, mcp_tool
from pydantic import BaseModel

# Initialize FastAPI app with MCP integration
app = FastAPI()
mcp_app = MCPApp(
    app=app,
    key="my-python-app",
    description="My FastAPI MCP Application",
    registry_url="http://localhost:8000"
)

class UserRequest(BaseModel):
    name: str
    age: int

class UserResponse(BaseModel):
    message: str
    timestamp: str

@app.post("/users")
@mcp_tool(
    description="Create a new user",
    tags=["users"]
)
async def create_user(user: UserRequest) -> UserResponse:
    return UserResponse(
        message=f"User {user.name} created successfully",
        timestamp=datetime.now().isoformat()
    )

@app.get("/users/{user_id}")
@mcp_tool(
    description="Get user by ID",
    tags=["users"]
)
async def get_user(user_id: int) -> UserResponse:
    return UserResponse(
        message=f"User {user_id} retrieved",
        timestamp=datetime.now().isoformat()
    )
```

### Flask Integration

```python
from flask import Flask, jsonify, request
from mcp_core_python import MCPApp, mcp_tool

app = Flask(__name__)
mcp_app = MCPApp(
    app=app,
    key="my-flask-app",
    description="My Flask MCP Application",
    registry_url="http://localhost:8000"
)

@app.route("/users", methods=["POST"])
@mcp_tool(
    description="Create a new user",
    request_schema={
        "type": "object",
        "properties": {
            "name": {"type": "string"},
            "age": {"type": "integer"}
        }
    }
)
def create_user():
    data = request.get_json()
    return jsonify({
        "message": f"User {data['name']} created successfully",
        "timestamp": datetime.now().isoformat()
    })

@app.route("/users/<int:user_id>")
@mcp_tool(
    description="Get user by ID",
    tags=["users"]
)
def get_user(user_id):
    return jsonify({
        "message": f"User {user_id} retrieved",
        "timestamp": datetime.now().isoformat()
    })
```

### Django Integration

```python
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
from mcp_core_python import MCPApp, mcp_tool
import json

# In settings.py
INSTALLED_APPS = [
    # ... other apps
    'mcp_core_python',
]

MCP_CONFIG = {
    'key': 'my-django-app',
    'description': 'My Django MCP Application',
    'registry_url': 'http://localhost:8000'
}

# In views.py
@csrf_exempt
@mcp_tool(
    description="Create a new user",
    methods=["POST"],
    tags=["users"]
)
def create_user(request):
    data = json.loads(request.body)
    return JsonResponse({
        "message": f"User {data['name']} created successfully",
        "timestamp": datetime.now().isoformat()
    })

@mcp_tool(
    description="Get user by ID",
    methods=["GET"],
    tags=["users"]
)
def get_user(request, user_id):
    return JsonResponse({
        "message": f"User {user_id} retrieved",
        "timestamp": datetime.now().isoformat()
    })
```

## 🔧 Configuration

### Environment Variables

```env
# MCP Registry Configuration
MCP_REGISTRY_URL=http://localhost:8000
MCP_APP_KEY=my-python-app
MCP_APP_DESCRIPTION=My Python MCP Application

# Authentication
MCP_API_KEY=your-api-key-here
MCP_AUTH_TYPE=api_key

# Performance
MCP_CACHE_TTL=300
MCP_BATCH_SIZE=10
MCP_TIMEOUT=30
```

### Configuration Object

```python
from mcp_core_python import MCPConfig

config = MCPConfig(
    registry_url="http://localhost:8000",
    app_key="my-python-app",
    description="My Python MCP Application",
    api_key="your-api-key-here",
    auth_type="api_key",
    cache_ttl=300,
    batch_size=10,
    timeout=30,
    environment="production"
)
```

## 🧪 Testing Support

### Unit Testing

```python
import pytest
from mcp_core_python.testing import MCPTestClient

@pytest.fixture
def mcp_client():
    return MCPTestClient(app, registry_url="http://localhost:8000")

def test_create_user(mcp_client):
    response = mcp_client.post("/users", json={"name": "John", "age": 30})
    assert response.status_code == 200
    assert "User John created successfully" in response.json()["message"]

def test_get_user(mcp_client):
    response = mcp_client.get("/users/1")
    assert response.status_code == 200
    assert "User 1 retrieved" in response.json()["message"]
```

### Integration Testing

```python
import pytest
from mcp_core_python.testing import MCPIntegrationTest

class TestMCPIntegration(MCPIntegrationTest):
    def test_registration(self):
        # Test automatic registration with MCP registry
        assert self.is_registered("my-python-app")
        
    def test_health_check(self):
        # Test health check endpoint
        response = self.client.get("/health")
        assert response.status_code == 200
        
    def test_endpoint_discovery(self):
        # Test endpoint discovery
        endpoints = self.get_registered_endpoints()
        assert len(endpoints) > 0
        assert any(ep["uri"] == "/users" for ep in endpoints)
```

## 📦 Installation (When Available)

```bash
# Install from PyPI
pip install mcp-core-python

# Install with specific framework support
pip install mcp-core-python[fastapi]
pip install mcp-core-python[flask]
pip install mcp-core-python[django]
pip install mcp-core-python[all]

# Install from source
git clone https://github.com/autogentmcp/mcp-core-python.git
cd mcp-core-python
pip install -e .
```

## 🤝 Contributing

We welcome contributions to the Python SDK! Here's how you can help:

### 1. Development Setup

```bash
# Clone the repository
git clone https://github.com/autogentmcp/mcp-core-python.git
cd mcp-core-python

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install development dependencies
pip install -e .[dev]

# Run tests
pytest

# Run linting
flake8 src/
black src/
mypy src/
```

### 2. Areas for Contribution

- **Framework Integrations**: Add support for new Python frameworks
- **Testing**: Improve test coverage and add integration tests
- **Documentation**: Write guides and examples
- **Performance**: Optimize for high-throughput applications
- **Type Safety**: Improve type hints and mypy compatibility

### 3. Development Guidelines

- Follow PEP 8 style guidelines
- Add type hints for all public APIs
- Write comprehensive tests
- Update documentation for new features
- Ensure backward compatibility

## 📞 Support

While the SDK is in development, you can:

- **Star the repository** to show interest
- **Create issues** for feature requests
- **Join discussions** about the SDK design
- **Follow progress** on the roadmap

---

**Status**: 🚧 **In Development** - Expected release Q1 2025

For updates on the Python SDK development, follow the [mcp-core-python repository](https://github.com/autogentmcp/mcp-core-python) and join our community discussions.
