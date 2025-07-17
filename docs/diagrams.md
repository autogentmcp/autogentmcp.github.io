# Ecosystem Architecture Diagrams

This page provides comprehensive architectural diagrams and visual representations of the Autogent MCP ecosystem.

## 🏗️ High-Level Architecture

### Complete Ecosystem Overview

```mermaid
graph TB
    subgraph "External Users"
        A[Developers]
        B[API Clients]
        C[Admin Users]
    end
    
    subgraph "Management Layer"
        D[MCP Portal<br/>Next.js Web Interface]
    end
    
    subgraph "Client Applications"
        E1[Spring Boot App<br/>+mcp-core-java]
        E2[FastAPI App<br/>+mcp-core-python]
        E3[Express App<br/>+mcp-core-node]
        E4[Custom Applications]
    end
    
    subgraph "Core Infrastructure"
        F[MCP Registry Server<br/>PostgreSQL + FastAPI]
        G[Autogent MCP Server<br/>LLM Router + Orchestrator]
    end
    
    subgraph "Security Layer"
        H[Vault Integration<br/>HashiCorp/Azure/AWS/GCP/Akeyless]
        I[Authentication<br/>JWT/OAuth2/API Keys]
    end
    
    subgraph "Data Layer"
        J[PostgreSQL<br/>Application Registry]
        K[Redis<br/>Session Cache]
        L[File Storage<br/>Logs & Metrics]
    end
    
    subgraph "Demo Applications"
        M1[User Service Demo]
        M2[Order Service Demo]
        M3[Weather Service Demo]
    end
    
    A --> D
    B --> G
    C --> D
    D --> F
    D --> H
    D --> I
    E1 --> F
    E2 --> F
    E3 --> F
    E4 --> F
    F --> G
    F --> J
    G --> H
    G --> K
    F --> M1
    F --> M2
    F --> M3
    
    style D fill:#e8f5e8
    style F fill:#e1f5fe
    style G fill:#f3e5f5
    style H fill:#fff3e0
    style J fill:#e8eaf6
```

## 🔄 Application Lifecycle

### From Portal to Production

```mermaid
graph LR
    subgraph "1. Portal Management"
        A[Create Application]
        B[Configure Environment]
        C[Generate API Keys]
        D[Set Security Policies]
    end
    
    subgraph "2. Development"
        E[Integrate SDK]
        F[Register Endpoints]
        G[Test Locally]
    end
    
    subgraph "3. Registry"
        H[Auto-Register]
        I[Health Checks]
        J[Endpoint Discovery]
    end
    
    subgraph "4. Production"
        K[Load Balancing]
        L[Monitoring]
        M[Scaling]
    end
    
    A --> B --> C --> D
    D --> E --> F --> G
    G --> H --> I --> J
    J --> K --> L --> M
    
    style A fill:#e8f5e8
    style E fill:#e1f5fe
    style H fill:#f3e5f5
    style K fill:#fff3e0
```

## 🔐 Security Architecture

### Authentication & Authorization Flow

```mermaid
graph TB
    subgraph "Client Applications"
        A[Java App]
        B[Python App]
        C[Node.js App]
    end
    
    subgraph "Security Layer"
        D[API Gateway]
        E[JWT Validation]
        F[Rate Limiting]
        G[IP Whitelisting]
    end
    
    subgraph "Vault Integration"
        H[HashiCorp Vault]
        I[Azure Key Vault]
        J[AWS Secrets Manager]
        K[GCP Secret Manager]
        L[Akeyless]
    end
    
    subgraph "Authentication Methods"
        M[API Key]
        N[Bearer Token]
        O[OAuth2]
        P[Basic Auth]
        Q[Custom Auth]
    end
    
    subgraph "Core Services"
        R[MCP Registry]
        S[Autogent Server]
    end
    
    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    F --> G
    G --> R
    G --> S
    
    R --> H
    R --> I
    S --> J
    S --> K
    S --> L
    
    E --> M
    E --> N
    E --> O
    E --> P
    E --> Q
    
    style D fill:#fff3e0
    style E fill:#e8f5e8
    style R fill:#e1f5fe
    style S fill:#f3e5f5
```

## 📊 Data Flow Diagrams

### Request Processing Flow

```mermaid
sequenceDiagram
    participant C as Client App
    participant R as Registry
    participant A as Autogent Server
    participant V as Vault
    participant T as Target Service
    
    C->>R: Register endpoints
    R->>R: Store metadata
    
    C->>A: Send query
    A->>R: Get available tools
    R->>A: Return tool metadata
    
    A->>V: Get credentials
    V->>A: Return secrets
    
    A->>A: Process with LLM
    A->>T: Call selected tool
    T->>A: Return result
    
    A->>C: Return response
```

### Health Monitoring Flow

```mermaid
graph LR
    subgraph "Services"
        A[Service A]
        B[Service B]
        C[Service C]
    end
    
    subgraph "Registry"
        D[Health Checker]
        E[Status Store]
        F[Alert Manager]
    end
    
    subgraph "Monitoring"
        G[Prometheus]
        H[Grafana]
        I[Alerts]
    end
    
    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    F --> I
    E --> G
    G --> H
    
    style D fill:#e8f5e8
    style E fill:#e1f5fe
    style G fill:#f3e5f5
```

## 🌐 Network Architecture

### Production Deployment

```mermaid
graph TB
    subgraph "Internet"
        A[Users]
        B[API Clients]
    end
    
    subgraph "Load Balancer"
        C[nginx/HAProxy]
        D[SSL Termination]
    end
    
    subgraph "Application Tier"
        E[Portal Instance 1]
        F[Portal Instance 2]
        G[Registry Instance 1]
        H[Registry Instance 2]
        I[Autogent Instance 1]
        J[Autogent Instance 2]
    end
    
    subgraph "Database Tier"
        K[PostgreSQL Primary]
        L[PostgreSQL Replica]
        M[Redis Cluster]
    end
    
    subgraph "Security Tier"
        N[Vault Cluster]
        O[Secrets Manager]
    end
    
    A --> C
    B --> C
    C --> D
    D --> E
    D --> F
    D --> G
    D --> H
    D --> I
    D --> J
    
    E --> K
    F --> K
    G --> K
    H --> K
    I --> K
    J --> K
    K --> L
    
    E --> M
    F --> M
    I --> M
    J --> M
    
    G --> N
    H --> N
    I --> N
    J --> N
    N --> O
    
    style C fill:#fff3e0
    style K fill:#e8eaf6
    style N fill:#e8f5e8
```

## 🔧 Component Interactions

### SDK Integration Patterns

```mermaid
graph TB
    subgraph "Java SDK"
        A[Spring Boot Auto-Config]
        B[@EnableAutogentMcp]
        C[@AutogentTool]
        D[Endpoint Registration]
    end
    
    subgraph "Python SDK (Future)"
        E[FastAPI Integration]
        F[Flask Integration]
        G[Decorator Pattern]
        H[Async Support]
    end
    
    subgraph "Node.js SDK (Future)"
        I[Express Middleware]
        J[TypeScript Support]
        K[Route Discovery]
        L[OpenAPI Generation]
    end
    
    subgraph "Registry Server"
        M[Application Manager]
        N[Endpoint Store]
        O[Health Monitor]
    end
    
    A --> D
    B --> D
    C --> D
    D --> M
    
    E --> G
    F --> G
    G --> H
    H --> M
    
    I --> K
    J --> K
    K --> L
    L --> M
    
    M --> N
    N --> O
    
    style A fill:#e8f5e8
    style E fill:#e1f5fe
    style I fill:#f3e5f5
    style M fill:#fff3e0
```

## 🎯 Service Discovery

### Intelligent Tool Selection

```mermaid
graph TB
    subgraph "Query Processing"
        A[User Query]
        B[Intent Analysis]
        C[Context Extraction]
    end
    
    subgraph "Tool Discovery"
        D[Registry Query]
        E[Capability Matching]
        F[Health Check]
        G[Load Assessment]
    end
    
    subgraph "LLM Processing"
        H[Tool Selection]
        I[Parameter Mapping]
        J[Execution Planning]
    end
    
    subgraph "Execution"
        K[Tool Invocation]
        L[Result Processing]
        M[Response Generation]
    end
    
    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    I --> J
    J --> K
    K --> L
    L --> M
    
    style B fill:#e8f5e8
    style E fill:#e1f5fe
    style H fill:#f3e5f5
    style K fill:#fff3e0
```

## 📈 Scaling Patterns

### Horizontal Scaling

```mermaid
graph TB
    subgraph "Load Balancer"
        A[nginx/HAProxy]
    end
    
    subgraph "Portal Tier"
        B[Portal 1]
        C[Portal 2]
        D[Portal N]
    end
    
    subgraph "Registry Tier"
        E[Registry 1]
        F[Registry 2]
        G[Registry N]
    end
    
    subgraph "Autogent Tier"
        H[Autogent 1]
        I[Autogent 2]
        J[Autogent N]
    end
    
    subgraph "Database Tier"
        K[Primary DB]
        L[Read Replica 1]
        M[Read Replica 2]
    end
    
    A --> B
    A --> C
    A --> D
    
    B --> E
    C --> F
    D --> G
    
    E --> H
    F --> I
    G --> J
    
    E --> K
    F --> L
    G --> M
    
    style A fill:#fff3e0
    style K fill:#e8eaf6
```

## 🔍 Monitoring & Observability

### Metrics Collection

```mermaid
graph TB
    subgraph "Applications"
        A[Portal Metrics]
        B[Registry Metrics]
        C[Autogent Metrics]
        D[Client App Metrics]
    end
    
    subgraph "Collection"
        E[Prometheus]
        F[Grafana]
        G[Jaeger Tracing]
        H[Log Aggregation]
    end
    
    subgraph "Alerting"
        I[Alert Manager]
        J[Notification Channels]
        K[Incident Response]
    end
    
    subgraph "Analytics"
        L[Performance Dashboard]
        M[Usage Analytics]
        N[Error Tracking]
    end
    
    A --> E
    B --> E
    C --> E
    D --> E
    
    E --> F
    E --> I
    G --> F
    H --> F
    
    I --> J
    J --> K
    
    F --> L
    F --> M
    F --> N
    
    style E fill:#e8f5e8
    style F fill:#e1f5fe
    style I fill:#f3e5f5
```

## 🚀 Deployment Patterns

### Multi-Environment Setup

```mermaid
graph TB
    subgraph "Development"
        A[Dev Portal]
        B[Dev Registry]
        C[Dev Autogent]
        D[Dev Database]
    end
    
    subgraph "Staging"
        E[Stage Portal]
        F[Stage Registry]
        G[Stage Autogent]
        H[Stage Database]
    end
    
    subgraph "Production"
        I[Prod Portal Cluster]
        J[Prod Registry Cluster]
        K[Prod Autogent Cluster]
        L[Prod Database Cluster]
    end
    
    subgraph "Shared Services"
        M[CI/CD Pipeline]
        N[Monitoring]
        O[Security Scanning]
    end
    
    A --> E
    B --> F
    C --> G
    D --> H
    
    E --> I
    F --> J
    G --> K
    H --> L
    
    M --> A
    M --> E
    M --> I
    
    N --> A
    N --> E
    N --> I
    
    O --> A
    O --> E
    O --> I
    
    style A fill:#e8f5e8
    style E fill:#e1f5fe
    style I fill:#f3e5f5
```

---

## 📚 Understanding the Diagrams

### Legend

- **Green nodes**: Management and UI components
- **Blue nodes**: Core infrastructure services
- **Purple nodes**: Orchestration and intelligence
- **Orange nodes**: Security and authentication
- **Light blue nodes**: Data storage and caching

### Best Practices

1. **Start with the Portal** for application management
2. **Use the Registry** for service discovery
3. **Leverage the Autogent Server** for intelligent orchestration
4. **Implement proper security** with vault integration
5. **Monitor everything** with comprehensive observability

### Next Steps

- **Portal Setup**: Begin with [MCP Portal](portal.md)
- **Registry Configuration**: Continue with [MCP Registry](mcp-registry.md)
- **SDK Integration**: Choose your [SDK](mcp-core-java.md)
- **Production Deployment**: Follow the [Deployment Guide](deployment.md)

---

*These diagrams represent the current and planned architecture of the Autogent MCP ecosystem. For implementation details, refer to the specific component documentation.*
