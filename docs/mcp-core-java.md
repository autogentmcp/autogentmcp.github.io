# MCP Core Java SDK

A high-performance Java SDK for Model Context Protocol (MCP) registry integration, designed for seamless Spring Boot integration with annotation-driven endpoint registration.

## 🚀 Features

- **☕ Java 8+ Compatible**: Works with Java 8 through Java 21
- **🍃 Spring Boot Integration**: Seamless integration with Spring Boot 2.x and 3.x
- **📝 Annotation-Driven**: Simple `@EnableAutogentMcp` and `@AutogentTool` annotations
- **🔄 Auto-Registration**: Automatic application and endpoint registration on startup
- **📊 Batch Operations**: Efficient batch endpoint registration for performance
- **🔍 Smart Discovery**: Automatic HTTP method and parameter detection
- **🛡️ Error Handling**: Robust error handling and logging
- **⚡ Production Ready**: Optimized for enterprise production environments

## 🏗️ Architecture

```mermaid
graph TB
    subgraph "Your Spring Boot Application"
        A[@SpringBootApplication]
        B[@EnableAutogentMcp]
        C[@RestController]
        D[@AutogentTool]
    end
    
    subgraph "MCP Core Java SDK"
        E[AutogentMcpAutoConfiguration]
        F[RegistryClient]
        G[EndpointCollector]
        H[BeanPostProcessor]
    end
    
    subgraph "MCP Registry Server"
        I[Application Registration]
        J[Endpoint Registration]
        K[Health Monitoring]
    end
    
    A --> B
    C --> D
    B --> E
    E --> F
    E --> G
    E --> H
    F --> I
    F --> J
    F --> K
    
    style E fill:#e1f5fe
    style F fill:#f3e5f5
    style G fill:#fff3e0
```

## 🔧 Installation

### Maven
Add to your `pom.xml`:

```xml
<dependency>
    <groupId>com.autogentmcp</groupId>
    <artifactId>mcp-core-java</artifactId>
    <version>0.0.2</version>
</dependency>
```

### Gradle
Add to your `build.gradle`:

```gradle
dependencies {
    implementation 'com.autogentmcp:mcp-core-java:0.0.2'
}
```

## ⚙️ Configuration

### Application Properties
Add to your `application.properties`:

```properties
# MCP Registry URL (required)
autogentmcp.registry-url=http://localhost:8000

# MCP API Key (required)
autogentmcp.api-key=your-api-key-here

# Application health check endpoint (required)
autogentmcp.app-healthcheck-endpoint=/actuator/health

# Environment (optional, default: production)
autogentmcp.environment=production
```

### YAML Configuration
Or in `application.yml`:

```yaml
autogentmcp:
  registry-url: http://localhost:8000
  api-key: your-api-key-here
  app-healthcheck-endpoint: /actuator/health
  environment: production
```

## 🎯 Quick Start

### 1. Enable MCP in Your Application

```java
import com.autogentmcp.registry.EnableAutogentMcp;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@EnableAutogentMcp(
    key = "my-awesome-app", 
    description = "My Awesome Spring Boot Application"
)
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

### 2. Register Your Endpoints

```java
import com.autogentmcp.registry.AutogentTool;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @AutogentTool(
        name = "Create User",
        uri = "/api/users",
        description = "Creates a new user in the system",
        method = "POST",
        isPublic = false
    )
    @PostMapping
    public User createUser(@RequestBody CreateUserRequest request) {
        // Your implementation
        return userService.createUser(request);
    }
    
    @AutogentTool(
        name = "Get User",
        uri = "/api/users/{id}",
        description = "Retrieves a user by their ID",
        method = "GET",
        isPublic = true
    )
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        // Your implementation
        return userService.getUser(id);
    }
    
    @AutogentTool(
        name = "Search Users",
        uri = "/api/users/search",
        description = "Searches for users based on criteria",
        method = "GET"
    )
    @GetMapping("/search")
    public List<User> searchUsers(
        @RequestParam(required = false) String name,
        @RequestParam(required = false) String email,
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size
    ) {
        // Your implementation
        return userService.searchUsers(name, email, page, size);
    }
}
```

### 3. Run Your Application

```bash
mvn spring-boot:run
```

That's it! Your endpoints will be automatically registered with the MCP Registry on startup.

## 📚 Detailed Usage

### @EnableAutogentMcp Annotation

The `@EnableAutogentMcp` annotation enables MCP integration and must be placed on your main application class.

```java
@EnableAutogentMcp(
    key = "unique-app-key",          // Required: Unique application identifier
    description = "App description"   // Optional: Application description
)
```

**Parameters:**
- `key`: Unique identifier for your application in the registry
- `description`: Human-readable description of your application

### @AutogentTool Annotation

The `@AutogentTool` annotation registers methods as MCP tools/endpoints.

```java
@AutogentTool(
    name = "Tool Name",                    // Optional: Tool name (defaults to method name)
    uri = "/api/endpoint/{param}",         // Required: Endpoint URI with parameters
    description = "Tool description",      // Optional: Tool description
    method = "GET",                        // Optional: HTTP method (auto-detected if not specified)
    isPublic = true,                       // Optional: Public accessibility (default: false)
    contentType = "application/json",      // Optional: Content type (default: application/json)
    pathParams = "{\"id\":\"Long\"}",      // Optional: Path parameters (auto-detected)
    queryParams = "{\"page\":\"Integer\"}", // Optional: Query parameters (auto-detected)
    requestBody = "{\"name\":\"String\"}"  // Optional: Request body schema (auto-detected)
)
```

### Automatic Parameter Detection

The SDK automatically detects parameters from your method signatures and Spring annotations:

```java
@AutogentTool(uri = "/api/users/{id}")
@GetMapping("/{id}")
public User getUser(
    @PathVariable Long id,                    // Detected as path parameter
    @RequestParam(required = false) String include,  // Detected as query parameter
    @RequestHeader("X-Client-Version") String version // Detected as header
) {
    // Implementation
}
```

**Generated metadata:**
```json
{
  "name": "getUser",
  "path": "/api/users/{id}",
  "method": "GET",
  "pathParams": {
    "id": {
      "type": "Long",
      "description": "User ID"
    }
  },
  "queryParams": {
    "include": {
      "type": "String",
      "description": "Fields to include"
    }
  }
}
```

### Request Body Detection

For POST/PUT endpoints, the SDK automatically detects request body schemas:

```java
@AutogentTool(
    uri = "/api/users",
    description = "Creates a new user"
)
@PostMapping
public User createUser(@RequestBody CreateUserRequest request) {
    // Implementation
}
```

**Auto-generated request body schema:**
```json
{
  "requestBody": {
    "type": "object",
    "properties": {
      "name": {"type": "String"},
      "email": {"type": "String"},
      "age": {"type": "Integer"}
    }
  }
}
```

### Custom Content Types

Support for different content types:

```java
@AutogentTool(
    uri = "/api/users/upload",
    description = "Upload user profile picture",
    contentType = "multipart/form-data"
)
@PostMapping(value = "/upload", consumes = "multipart/form-data")
public ResponseEntity<String> uploadProfilePicture(
    @RequestParam("file") MultipartFile file,
    @RequestParam("userId") Long userId
) {
    // Implementation
}
```

## 🔧 Advanced Configuration

### Custom Registry Client

For advanced scenarios, you can provide your own `RegistryClient`:

```java
@Configuration
public class McpConfiguration {
    
    @Bean
    @Primary
    public RegistryClient customRegistryClient() {
        return new RegistryClient("http://localhost:8000", "api-key") {
            @Override
            protected CloseableHttpResponse executePost(HttpPost post) throws IOException {
                // Custom HTTP handling
                return super.executePost(post);
            }
        };
    }
}
```

### Health Check Customization

Customize the health check endpoint:

```java
@RestController
public class HealthController {
    
    @GetMapping("/health")
    public ResponseEntity<Map<String, Object>> health() {
        Map<String, Object> health = new HashMap<>();
        health.put("status", "UP");
        health.put("timestamp", Instant.now());
        health.put("version", "1.0.0");
        
        return ResponseEntity.ok(health);
    }
}
```

### Error Handling

The SDK includes comprehensive error handling:

```java
@ControllerAdvice
public class McpErrorHandler {
    
    @ExceptionHandler(RegistryException.class)
    public ResponseEntity<ErrorResponse> handleRegistryException(RegistryException e) {
        log.error("Registry error: {}", e.getMessage(), e);
        return ResponseEntity.status(HttpStatus.SERVICE_UNAVAILABLE)
            .body(new ErrorResponse("Registry temporarily unavailable"));
    }
}
```

## 🏥 Health Monitoring

The SDK automatically registers your application's health endpoint with the registry for monitoring.

### Health Check Flow
1. SDK registers health endpoint during startup
2. Registry periodically checks the health endpoint
3. Registry updates application status based on health responses
4. Failed health checks trigger alerts and status updates

### Health Response Format
Your health endpoint should return:

```json
{
  "status": "UP",
  "components": {
    "database": {
      "status": "UP"
    },
    "external-api": {
      "status": "UP"
    }
  }
}
```

## 🔄 Lifecycle Management

### Application Startup
1. **Bean Processing**: SDK scans for `@AutogentTool` annotations
2. **Application Registration**: Registers application metadata with registry
3. **Endpoint Collection**: Collects all annotated endpoints
4. **Batch Registration**: Registers all endpoints in a single batch request
5. **Health Registration**: Registers health check endpoint

### Runtime Updates
- **Endpoint Updates**: Changes to endpoints require application restart
- **Health Monitoring**: Continuous health check monitoring
- **Error Recovery**: Automatic retry on registry communication failures

### Application Shutdown
- **Graceful Shutdown**: SDK handles graceful shutdown
- **Registry Notification**: Notifies registry of application shutdown
- **Resource Cleanup**: Cleans up connections and resources

## 🧪 Testing

### Unit Testing

```java
@ExtendWith(MockitoExtension.class)
class UserControllerTest {
    
    @Mock
    private UserService userService;
    
    @InjectMocks
    private UserController userController;
    
    @Test
    void shouldCreateUser() {
        // Test your endpoint logic
        CreateUserRequest request = new CreateUserRequest("John", "john@example.com");
        User expectedUser = new User(1L, "John", "john@example.com");
        
        when(userService.createUser(request)).thenReturn(expectedUser);
        
        User result = userController.createUser(request);
        
        assertEquals(expectedUser, result);
        verify(userService).createUser(request);
    }
}
```

### Integration Testing

```java
@SpringBootTest
@AutoConfigureMockMvc
class McpIntegrationTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private RegistryClient registryClient;
    
    @Test
    void shouldRegisterEndpoints() throws Exception {
        // Verify that endpoints are registered
        verify(registryClient).registerEndpointsBatch(
            eq("test-app"),
            eq("test"),
            argThat(endpoints -> endpoints.size() > 0)
        );
    }
}
```

### Mock Registry for Testing

```java
@TestConfiguration
public class TestMcpConfiguration {
    
    @Bean
    @Primary
    public RegistryClient mockRegistryClient() {
        return Mockito.mock(RegistryClient.class);
    }
}
```

## 🔍 Troubleshooting

### Common Issues

#### Registry Connection Failures
```java
// Check registry URL and API key
@Value("${autogentmcp.registry-url}")
private String registryUrl;

@Value("${autogentmcp.api-key}")
private String apiKey;

@PostConstruct
public void validateConfig() {
    if (registryUrl == null || apiKey == null) {
        throw new IllegalStateException("MCP configuration is missing");
    }
}
```

#### Endpoint Registration Failures
```java
// Enable debug logging
logging.level.com.autogentmcp=DEBUG

// Check endpoint collection
@Component
public class EndpointDebugger {
    
    @EventListener
    public void onApplicationReady(ApplicationReadyEvent event) {
        List<Map<String, Object>> endpoints = EndpointCollector.getAll();
        log.info("Collected {} endpoints for registration", endpoints.size());
        endpoints.forEach(ep -> log.info("Endpoint: {}", ep));
    }
}
```

#### Authentication Issues
```bash
# Verify API key in registry
curl -H "X-Admin-Key: admin-key" http://localhost:8000/applications/your-app-key

# Check API key format
curl -X POST http://localhost:8000/register/endpoints \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-api-key" \
  -H "X-App-Key: your-app-key" \
  -d '{"app_key": "your-app-key", "environment": "production", "endpoints": []}'
```

### Debug Logging

Enable comprehensive logging:

```properties
# Application properties
logging.level.com.autogentmcp=DEBUG
logging.level.org.apache.http=DEBUG

# Specific logger configuration
logging.level.com.autogentmcp.registry.spring.AutogentMcpAutoConfiguration=TRACE
logging.level.com.autogentmcp.registry.RegistryClient=DEBUG
```

## 🚀 Production Best Practices

### Configuration Management
```properties
# Use environment-specific configuration
autogentmcp.registry-url=${MCP_REGISTRY_URL:http://localhost:8000}
autogentmcp.api-key=${MCP_API_KEY}
autogentmcp.environment=${MCP_ENVIRONMENT:production}
```

### Health Check Optimization
```java
@Component
public class OptimizedHealthIndicator implements HealthIndicator {
    
    @Override
    public Health health() {
        // Lightweight health check
        return Health.up()
            .withDetail("timestamp", Instant.now())
            .withDetail("version", getClass().getPackage().getImplementationVersion())
            .build();
    }
}
```

### Error Recovery
```java
@Component
public class RegistryErrorHandler {
    
    @Retryable(value = {RegistryException.class}, maxAttempts = 3)
    public void registerWithRetry() {
        // Registration logic with retry
    }
    
    @Recover
    public void recoverFromRegistryFailure(RegistryException ex) {
        log.error("Failed to register after retries: {}", ex.getMessage());
        // Fallback logic
    }
}
```

### Monitoring and Metrics
```java
@Component
public class McpMetrics {
    
    private final Counter registrationAttempts;
    private final Timer registrationTime;
    
    public McpMetrics(MeterRegistry meterRegistry) {
        this.registrationAttempts = Counter.builder("mcp.registration.attempts")
            .register(meterRegistry);
        this.registrationTime = Timer.builder("mcp.registration.time")
            .register(meterRegistry);
    }
    
    public void recordRegistrationAttempt() {
        registrationAttempts.increment();
    }
    
    public void recordRegistrationTime(Duration duration) {
        registrationTime.record(duration);
    }
}
```

## 📋 API Reference

### Core Classes

#### `@EnableAutogentMcp`
Enables MCP integration for the application.

**Attributes:**
- `key()`: Application key (required)
- `description()`: Application description (optional)

#### `@AutogentTool`
Registers a method as an MCP tool.

**Attributes:**
- `name()`: Tool name (optional, defaults to method name)
- `uri()`: Endpoint URI (required)
- `description()`: Tool description (optional)
- `method()`: HTTP method (optional, auto-detected)
- `isPublic()`: Public accessibility (optional, default: false)
- `contentType()`: Content type (optional, default: application/json)
- `pathParams()`: Path parameters JSON (optional, auto-detected)
- `queryParams()`: Query parameters JSON (optional, auto-detected)
- `requestBody()`: Request body schema JSON (optional, auto-detected)

#### `RegistryClient`
HTTP client for registry communication.

**Methods:**
- `registerEndpointsBatch(String appKey, String environment, List<Map<String, Object>> endpoints)`
- `updateApplication(String appKey, Map<String, Object> updateData)`

#### `EndpointCollector`
Collects and manages endpoint metadata.

**Methods:**
- `getAll()`: Get all collected endpoints
- `add(Map<String, Object> endpoint)`: Add endpoint
- `clear()`: Clear all endpoints

## 🔗 Integration Examples

### Spring Security Integration
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeRequests(authz -> authz
                .antMatchers("/actuator/health").permitAll()
                .antMatchers("/api/public/**").permitAll()
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2.jwt());
        
        return http.build();
    }
}
```

### Database Integration
```java
@AutogentTool(
    uri = "/api/users",
    description = "Get all users with pagination"
)
@GetMapping
@Transactional(readOnly = true)
public Page<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(required = false) String search
) {
    Pageable pageable = PageRequest.of(page, size);
    
    if (search != null && !search.isEmpty()) {
        return userRepository.findByNameContainingIgnoreCase(search, pageable);
    }
    
    return userRepository.findAll(pageable);
}
```

### Async Processing
```java
@AutogentTool(
    uri = "/api/users/batch",
    description = "Process users in batch"
)
@PostMapping("/batch")
@Async
public CompletableFuture<BatchResult> processBatch(@RequestBody List<User> users) {
    return CompletableFuture.supplyAsync(() -> {
        // Async processing logic
        return batchProcessor.process(users);
    });
}
```

## 📈 Performance Considerations

### Startup Performance
- **Lazy Initialization**: Use `@Lazy` for non-essential beans
- **Batch Operations**: All endpoints registered in single batch request
- **Async Registration**: Registration happens asynchronously after startup

### Runtime Performance
- **Connection Pooling**: HTTP client uses connection pooling
- **Caching**: Registry responses are cached when appropriate
- **Error Resilience**: Circuit breaker pattern for registry failures

### Memory Management
- **Endpoint Metadata**: Minimal memory footprint for metadata storage
- **Connection Management**: Proper connection lifecycle management
- **Resource Cleanup**: Automatic cleanup on application shutdown

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass: `mvn test`
6. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/autogentmcp/mcp-core-java/blob/main/LICENSE) file for details.

**Note**: LICENSE file will be added to the repository soon. See the [Contributing Guide](contributing.md#license) for full license details.

---

**Next Steps**: 
- Explore the [MCP Demo Apps](mcp-demo-apps.md) for working examples
- Set up the [MCP Registry Server](mcp-registry.md) for your environment
- Check out the [Autogent MCP Server](autogentmcp_server.md) for orchestration
