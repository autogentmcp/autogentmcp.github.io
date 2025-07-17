# MCP Demo Applications

A comprehensive collection of example applications demonstrating real-world usage of the Autogent MCP ecosystem. These demos showcase best practices, integration patterns, and production-ready implementations across different scenarios.

## 🎯 Overview

The demo applications provide hands-on examples of how to build and integrate MCP-enabled services. Each demo is designed to be:

- **📚 Educational**: Clear, well-documented code with extensive comments
- **🔧 Practical**: Real-world scenarios you might encounter in production
- **⚡ Production-Ready**: Follows best practices for error handling, logging, and performance
- **🧪 Testable**: Includes unit tests and integration tests
- **🔄 Extensible**: Easy to modify and extend for your specific needs

## 🏗️ Architecture Overview

```mermaid
graph TB
    subgraph "Demo Applications"
        A[User Service<br/>Port: 8080]
        B[Order Service<br/>Port: 8081]
        C[Weather Service<br/>Port: 8082]
    end
    
    subgraph "MCP Infrastructure"
        D[MCP Registry<br/>Port: 8000]
        E[Autogent MCP Server<br/>Port: 8001]
    end
    
    subgraph "External Services"
        F[Ollama LLM]
        G[Vault Systems]
    end
    
    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    E --> G
    
    style A fill:#e3f2fd
    style B fill:#f3e5f5
    style C fill:#e8f5e8
    style D fill:#fff3e0
    style E fill:#fce4ec
```

## 📋 Available Demos

### ☕ Java Spring Boot Demos

#### 👤 User Service
**Port: 8080** | **App Key**: `app_5srfoiyfoew1q0zfi4354sj`

A complete user management service demonstrating:
- User CRUD operations
- Input validation and error handling
- RESTful API design
- MCP annotation usage

**Features:**
- Create new users
- Retrieve user information
- Update user profiles
- Delete users
- Search functionality

**Endpoints:**
```java
@AutogentTool(uri = "/users", description = "Create a new user")
@PostMapping
public User createUser(@RequestParam int id, @RequestParam String name, 
                      @RequestParam String email, @RequestParam int age)

@AutogentTool(uri = "/users/{id}", description = "Get user by ID")
@GetMapping("/{id}")
public User getUser(@PathVariable int id)
```

#### 📦 Order Service
**Port: 8081** | **App Key**: `app_x29iirgfycp9e92iqkb04`

An order tracking service showcasing:
- Order status management
- State transitions
- Business logic implementation
- Integration patterns

**Features:**
- Track order status
- Update order information
- Order history
- Status notifications

**Endpoints:**
```java
@AutogentTool(uri = "/orders/{orderId}/status", description = "Get order status")
@GetMapping("/{orderId}/status")
public String getOrderStatus(@PathVariable String orderId)

@AutogentTool(uri = "/orders/{orderId}/status", description = "Update order status")
@PostMapping("/{orderId}/status")
public String updateOrderStatus(@PathVariable String orderId, @RequestParam String status)
```

#### 🌤️ Weather Service
**Port: 8082** | **App Key**: `app_v79c5bmqv2x54akb3gog`

A weather information service demonstrating:
- External API integration patterns
- Data transformation
- Caching strategies
- Error resilience

**Features:**
- Current weather information
- Multi-city support
- Weather forecasting
- Data caching

**Endpoints:**
```java
@AutogentTool(uri = "/weather/{city}", description = "Get current weather by city")
@GetMapping("/{city}")
public String getWeather(@PathVariable String city)
```

### 🐍 Python FastAPI Demos (Coming Soon)

#### 📊 Analytics Service
A data analytics service demonstrating:
- Data processing pipelines
- Real-time analytics
- Chart generation
- Export capabilities

#### 🔍 Search Service
A search service showcasing:
- Full-text search
- Elasticsearch integration
- Faceted search
- Search analytics

### 🟢 Node.js Express Demos (Coming Soon)

#### 📁 File Service
A file management service demonstrating:
- File upload/download
- Metadata management
- Permission handling
- Cloud storage integration

#### 🔔 Notification Service
A notification service showcasing:
- Multi-channel notifications
- Template management
- Delivery tracking
- Webhook integration

## 🚀 Quick Start

### Prerequisites

1. **Java 11+** (for Java demos)
2. **Maven 3.6+** (for Java demos)
3. **MCP Registry Server** running on port 8000
4. **Autogent MCP Server** running on port 8001

### Step 1: Clone the Repository

```bash
git clone https://github.com/autogentmcp/mcp-demo-apps.git
cd mcp-demo-apps
```

### Step 2: Start the Infrastructure

```bash
# Start MCP Registry (in another terminal)
cd ../mcp-registry
python run_server.py

# Start Autogent MCP Server (in another terminal)
cd ../autogentmcp_server
uvicorn app.main:app --reload --port 8001
```

### Step 3: Run the Java Demos

```bash
cd java-demo

# Start User Service
cd user-service
mvn spring-boot:run

# Start Order Service (in another terminal)
cd order-service
mvn spring-boot:run

# Start Weather Service (in another terminal)
cd weather-service
mvn spring-boot:run
```

### Step 4: Test the Integration

```bash
# Test direct service calls
curl "http://localhost:8080/users/123"
curl "http://localhost:8081/orders/ORD-456/status"
curl "http://localhost:8082/weather/london"

# Test via MCP orchestration
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Get user 123", "session_id": "test-session"}'
```

## 📁 Project Structure

```
mcp-demo-apps/
├── java-demo/
│   ├── user-service/
│   │   ├── src/
│   │   │   ├── main/
│   │   │   │   ├── java/
│   │   │   │   │   └── com/autogentmcp/user/
│   │   │   │   │       ├── UserServiceApplication.java
│   │   │   │   │       ├── UserController.java
│   │   │   │   │       └── User.java
│   │   │   │   └── resources/
│   │   │   │       └── application.properties
│   │   │   └── test/
│   │   └── pom.xml
│   ├── order-service/
│   │   ├── src/
│   │   │   ├── main/
│   │   │   │   ├── java/
│   │   │   │   │   └── com/autogentmcp/order/
│   │   │   │   │       ├── OrderServiceApplication.java
│   │   │   │   │       └── OrderController.java
│   │   │   │   └── resources/
│   │   │   │       └── application.properties
│   │   │   └── test/
│   │   └── pom.xml
│   └── weather-service/
│       ├── src/
│       │   ├── main/
│       │   │   ├── java/
│       │   │   │   └── com/autogentmcp/weather/
│       │   │   │       ├── WeatherServiceApplication.java
│       │   │   │       └── WeatherController.java
│       │   │   └── resources/
│       │   │       └── application.properties
│       │   └── test/
│       └── pom.xml
├── python-demo/              # Coming soon
├── node-demo/                # Coming soon
├── docker-compose.yml        # Full stack deployment
├── README.md
└── CONTRIBUTING.md
```

## 🔧 Configuration

### Application Properties

Each service uses standard Spring Boot configuration:

```properties
# Server configuration
server.port=8080
server.servlet.context-path=/

# MCP Configuration
autogentmcp.registry-url=http://localhost:8000
autogentmcp.api-key=your-api-key-here
autogentmcp.app-healthcheck-endpoint=/health
autogentmcp.environment=development

# Logging
logging.level.com.autogentmcp=DEBUG
logging.level.org.springframework.web=INFO
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss} - %msg%n
```

### Environment-Specific Configuration

```yaml
# application-dev.yml
autogentmcp:
  registry-url: http://localhost:8000
  environment: development
  api-key: dev-api-key

# application-prod.yml
autogentmcp:
  registry-url: https://registry.production.com
  environment: production
  api-key: ${MCP_API_KEY}
```

## 🧪 Testing

### Unit Tests

Each service includes comprehensive unit tests:

```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    void shouldCreateUser() throws Exception {
        mockMvc.perform(post("/users")
                .param("id", "123")
                .param("name", "John Doe")
                .param("email", "john@example.com")
                .param("age", "30"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.id").value(123))
                .andExpect(jsonPath("$.name").value("John Doe"));
    }
    
    @Test
    void shouldGetUser() throws Exception {
        mockMvc.perform(get("/users/123"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.id").value(123));
    }
}
```

### Integration Tests

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@TestPropertySource(properties = {
    "autogentmcp.registry-url=http://localhost:8000",
    "autogentmcp.api-key=test-key"
})
class McpIntegrationTest {
    
    @Autowired
    private TestRestTemplate restTemplate;
    
    @MockBean
    private RegistryClient registryClient;
    
    @Test
    void shouldRegisterWithMcp() {
        // Verify MCP registration
        verify(registryClient).registerEndpointsBatch(
            eq("app_5srfoiyfoew1q0zfi4354sj"),
            eq("development"),
            argThat(endpoints -> endpoints.size() > 0)
        );
    }
}
```

### End-to-End Tests

```java
@SpringBootTest
@DirtiesContext
class EndToEndTest {
    
    @Test
    void shouldCompleteUserWorkflow() throws Exception {
        // Create user
        User user = createUser("John Doe", "john@example.com");
        assertThat(user.getId()).isNotNull();
        
        // Retrieve user
        User retrieved = getUser(user.getId());
        assertThat(retrieved.getName()).isEqualTo("John Doe");
        
        // Update user
        User updated = updateUser(user.getId(), "John Smith");
        assertThat(updated.getName()).isEqualTo("John Smith");
        
        // Delete user
        deleteUser(user.getId());
        assertThat(getUser(user.getId())).isNull();
    }
}
```

## 🔄 Deployment

### Docker Compose Deployment

```yaml
version: '3.8'
services:
  mcp-registry:
    build: ../mcp-registry
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/mcp_registry
    depends_on:
      - postgres
  
  autogent-mcp-server:
    build: ../autogentmcp_server
    ports:
      - "8001:8001"
    environment:
      - REGISTRY_URL=http://mcp-registry:8000
      - OLLAMA_BASE_URL=http://ollama:11434
    depends_on:
      - mcp-registry
      - ollama
  
  user-service:
    build: ./java-demo/user-service
    ports:
      - "8080:8080"
    environment:
      - AUTOGENTMCP_REGISTRY_URL=http://mcp-registry:8000
      - AUTOGENTMCP_API_KEY=demo-api-key
    depends_on:
      - mcp-registry
  
  order-service:
    build: ./java-demo/order-service
    ports:
      - "8081:8081"
    environment:
      - AUTOGENTMCP_REGISTRY_URL=http://mcp-registry:8000
      - AUTOGENTMCP_API_KEY=demo-api-key
    depends_on:
      - mcp-registry
  
  weather-service:
    build: ./java-demo/weather-service
    ports:
      - "8082:8082"
    environment:
      - AUTOGENTMCP_REGISTRY_URL=http://mcp-registry:8000
      - AUTOGENTMCP_API_KEY=demo-api-key
    depends_on:
      - mcp-registry
  
  postgres:
    image: postgres:13
    environment:
      - POSTGRES_DB=mcp_registry
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - "5432:5432"
  
  ollama:
    image: ollama/ollama
    ports:
      - "11434:11434"
```

Start the full stack:

```bash
docker-compose up -d
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
      - name: user-service
        image: user-service:latest
        ports:
        - containerPort: 8080
        env:
        - name: AUTOGENTMCP_REGISTRY_URL
          value: "http://mcp-registry:8000"
        - name: AUTOGENTMCP_API_KEY
          valueFrom:
            secretKeyRef:
              name: mcp-secrets
              key: api-key
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
```

## 📊 Monitoring and Observability

### Health Checks

Each service includes comprehensive health checks:

```java
@Component
public class CustomHealthIndicator implements HealthIndicator {
    
    @Override
    public Health health() {
        try {
            // Check database connectivity
            boolean dbHealth = checkDatabaseHealth();
            
            // Check external service connectivity
            boolean externalServiceHealth = checkExternalServices();
            
            if (dbHealth && externalServiceHealth) {
                return Health.up()
                    .withDetail("database", "UP")
                    .withDetail("external_services", "UP")
                    .withDetail("timestamp", Instant.now())
                    .build();
            } else {
                return Health.down()
                    .withDetail("database", dbHealth ? "UP" : "DOWN")
                    .withDetail("external_services", externalServiceHealth ? "UP" : "DOWN")
                    .build();
            }
        } catch (Exception e) {
            return Health.down()
                .withDetail("error", e.getMessage())
                .build();
        }
    }
}
```

### Metrics Collection

```java
@Component
public class ApplicationMetrics {
    
    private final Counter requestCounter;
    private final Timer requestTimer;
    private final Gauge activeConnections;
    
    public ApplicationMetrics(MeterRegistry meterRegistry) {
        this.requestCounter = Counter.builder("requests_total")
            .description("Total requests processed")
            .register(meterRegistry);
        
        this.requestTimer = Timer.builder("request_duration_seconds")
            .description("Request processing time")
            .register(meterRegistry);
        
        this.activeConnections = Gauge.builder("active_connections")
            .description("Number of active connections")
            .register(meterRegistry);
    }
    
    public void recordRequest() {
        requestCounter.increment();
    }
    
    public void recordRequestTime(Duration duration) {
        requestTimer.record(duration);
    }
}
```

### Logging Configuration

```properties
# Logging configuration
logging.level.com.autogentmcp=DEBUG
logging.level.org.springframework.web=INFO
logging.level.org.springframework.security=DEBUG

# Console output
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n

# File output
logging.file.name=logs/application.log
logging.file.max-size=10MB
logging.file.max-history=30
```

## 🎯 Demo Scenarios

### Scenario 1: User Management Workflow

```bash
# Create a user
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Create a new user with ID 123, name John Doe, email john@example.com, and age 30", "session_id": "user-demo"}'

# Get user information
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Get user 123", "session_id": "user-demo"}'

# Update user
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Update user 123 email to john.doe@example.com", "session_id": "user-demo"}'
```

### Scenario 2: Order Tracking

```bash
# Check order status
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "What is the status of order ORD-123?", "session_id": "order-demo"}'

# Update order status
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Update order ORD-123 status to shipped", "session_id": "order-demo"}'
```

### Scenario 3: Weather Information

```bash
# Get weather for a city
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "What is the weather like in London?", "session_id": "weather-demo"}'

# Get weather for multiple cities
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Compare weather in London, Paris, and New York", "session_id": "weather-demo"}'
```

### Scenario 4: Multi-Service Workflow

```bash
# Complex workflow combining multiple services
curl -X POST "http://localhost:8001/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Get user 123, check their recent orders, and provide weather for their location", "session_id": "complex-demo"}'
```

## 🔧 Customization

### Adding New Endpoints

1. **Create the endpoint method**:

```java
@AutogentTool(
    name = "Search Users",
    uri = "/users/search",
    description = "Search for users by name or email",
    method = "GET"
)
@GetMapping("/search")
public List<User> searchUsers(
    @RequestParam(required = false) String name,
    @RequestParam(required = false) String email
) {
    return userService.searchUsers(name, email);
}
```

2. **Add business logic**:

```java
@Service
public class UserService {
    
    public List<User> searchUsers(String name, String email) {
        return users.values().stream()
            .filter(user -> {
                boolean nameMatch = name == null || 
                    user.getName().toLowerCase().contains(name.toLowerCase());
                boolean emailMatch = email == null || 
                    user.getEmail().toLowerCase().contains(email.toLowerCase());
                return nameMatch && emailMatch;
            })
            .collect(Collectors.toList());
    }
}
```

3. **Add tests**:

```java
@Test
void shouldSearchUsers() throws Exception {
    // Create test users
    createUser(1, "John Doe", "john@example.com", 30);
    createUser(2, "Jane Smith", "jane@example.com", 25);
    
    // Search by name
    mockMvc.perform(get("/users/search")
            .param("name", "John"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$", hasSize(1)))
            .andExpect(jsonPath("$[0].name").value("John Doe"));
}
```

### Creating New Services

1. **Create a new Spring Boot application**:

```java
@EnableAutogentMcp(key = "my-new-service", description = "My New Service")
@SpringBootApplication
public class MyNewServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyNewServiceApplication.class, args);
    }
}
```

2. **Add controller with MCP annotations**:

```java
@RestController
@RequestMapping("/api/my-service")
public class MyController {
    
    @AutogentTool(
        uri = "/api/my-service/action",
        description = "Performs a specific action",
        method = "POST"
    )
    @PostMapping("/action")
    public ResponseEntity<String> performAction(@RequestBody ActionRequest request) {
        // Your implementation
        return ResponseEntity.ok("Action completed");
    }
}
```

3. **Configure application properties**:

```properties
server.port=8083
autogentmcp.registry-url=http://localhost:8000
autogentmcp.api-key=your-api-key
autogentmcp.app-healthcheck-endpoint=/health
autogentmcp.environment=development
```

## 🔍 Troubleshooting

### Common Issues

#### Service Registration Issues
```bash
# Check if service is registered
curl -H "X-Admin-Key: admin-key" http://localhost:8000/applications

# Check service endpoints
curl "http://localhost:8000/endpoints?app_key=your-app-key&environment=development"
```

#### MCP Annotation Issues
```java
// Ensure @EnableAutogentMcp is on main class
@EnableAutogentMcp(key = "unique-key", description = "Description")
@SpringBootApplication
public class Application { ... }

// Ensure @AutogentTool has required parameters
@AutogentTool(uri = "/endpoint", description = "Description")
```

#### Port Conflicts
```bash
# Check which ports are in use
netstat -tulpn | grep :8080
netstat -tulpn | grep :8081
netstat -tulpn | grep :8082

# Change port in application.properties
server.port=8083
```

#### API Key Issues
```bash
# Verify API key in registry
curl -H "X-Admin-Key: admin-key" http://localhost:8000/applications/your-app-key

# Check API key configuration
grep -r "api-key" src/main/resources/
```

### Debug Mode

Enable debug logging for troubleshooting:

```properties
# Application properties
logging.level.com.autogentmcp=DEBUG
logging.level.org.springframework.web=DEBUG
logging.level.org.apache.http=DEBUG

# Console output with more detail
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n
```

## 🎓 Learning Resources

### Tutorials
- [Getting Started with MCP](../getting-started.md)
- [Building Your First MCP Service](../tutorials/first-service.md)
- [Advanced MCP Patterns](../tutorials/advanced-patterns.md)

### Best Practices
- [Error Handling](../best-practices/error-handling.md)
- [Security Guidelines](../best-practices/security.md)
- [Performance Optimization](../best-practices/performance.md)

### API References
- [MCP Core Java SDK](mcp-core-java.md)
- [MCP Registry Server](mcp-registry.md)
- [Autogent MCP Server](autogentmcp_server.md)

## 🤝 Contributing

We welcome contributions to the demo applications! Here's how to get started:

### Adding New Demos

1. **Create a new service** following the existing patterns
2. **Add comprehensive tests** for all functionality
3. **Update documentation** with new endpoints and usage examples
4. **Add integration tests** to verify MCP registration
5. **Submit a pull request** with your changes

### Improving Existing Demos

1. **Identify areas for improvement** (performance, features, documentation)
2. **Create feature branch** with descriptive name
3. **Implement improvements** with tests
4. **Update documentation** as needed
5. **Submit pull request** with detailed description

### Reporting Issues

1. **Check existing issues** to avoid duplicates
2. **Create detailed bug report** with steps to reproduce
3. **Include environment information** (Java version, OS, etc.)
4. **Provide logs and error messages** if applicable

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/autogentmcp/mcp-demo-apps/blob/main/LICENSE) file for details.

**Note**: LICENSE file will be added to the repository soon. See the [Contributing Guide](contributing.md#license) for full license details.

---

**Next Steps**: 
- Try running the demos to see the MCP ecosystem in action
- Explore the [MCP Core Java SDK](mcp-core-java.md) for detailed integration guide
- Check out the [Autogent MCP Server](autogentmcp_server.md) for orchestration capabilities
