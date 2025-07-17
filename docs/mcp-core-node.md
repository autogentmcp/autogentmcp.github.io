# MCP Core Node.js SDK

**Coming Soon** - Node.js/TypeScript SDK for Express.js, Fastify, and Koa integration.

## 🎯 Overview

The MCP Core Node.js SDK will provide seamless integration between Node.js/TypeScript web frameworks and the Autogent MCP ecosystem. Built with modern JavaScript features including async/await, TypeScript support, and automatic API documentation generation.

## 🚀 Planned Features

### 🟢 Framework Support
- **Express.js**: The most popular Node.js web framework
- **Fastify**: High-performance alternative to Express
- **Koa**: Next-generation web framework by the Express team
- **NestJS**: Angular-inspired framework for Node.js

### 🎨 Modern JavaScript Features
- **TypeScript First**: Full TypeScript support with strict type checking
- **Async/Await**: Native async support for all operations
- **ES Modules**: Modern module system support
- **Decorators**: Experimental decorator support for NestJS integration

### 🔧 Developer Experience
- **Middleware-based**: Simple middleware integration for all frameworks
- **Auto-discovery**: Automatic route introspection and metadata generation
- **Hot Reload**: Development server integration with auto-reload
- **IDE Support**: Full IntelliSense and autocomplete support

## 📋 Roadmap

### Phase 1: Core SDK (Coming Q1 2025)
- [ ] Basic MCP client implementation
- [ ] Express.js integration
- [ ] Endpoint registration and discovery
- [ ] Authentication support

### Phase 2: Extended Framework Support (Coming Q2 2025)
- [ ] Fastify integration
- [ ] Koa integration
- [ ] NestJS integration
- [ ] Performance optimizations

### Phase 3: Advanced Features (Coming Q3 2025)
- [ ] OpenAPI generation
- [ ] Custom serialization
- [ ] Plugin system
- [ ] Microservice support

## 🔮 Planned API Design

### Express.js Integration

```typescript
import express from 'express';
import { MCPApp, mcpTool } from 'mcp-core-node';

const app = express();
const mcpApp = new MCPApp({
  app,
  key: 'my-node-app',
  description: 'My Express MCP Application',
  registryUrl: 'http://localhost:8000'
});

interface UserRequest {
  name: string;
  age: number;
}

interface UserResponse {
  message: string;
  timestamp: string;
}

// Using decorator approach
@mcpTool({
  description: 'Create a new user',
  tags: ['users']
})
app.post('/users', (req: express.Request, res: express.Response) => {
  const user: UserRequest = req.body;
  const response: UserResponse = {
    message: `User ${user.name} created successfully`,
    timestamp: new Date().toISOString()
  };
  res.json(response);
});

// Using middleware approach
app.get('/users/:id', 
  mcpApp.tool({
    description: 'Get user by ID',
    tags: ['users']
  }),
  (req: express.Request, res: express.Response) => {
    const userId = req.params.id;
    const response: UserResponse = {
      message: `User ${userId} retrieved`,
      timestamp: new Date().toISOString()
    };
    res.json(response);
  }
);
```

### Fastify Integration

```typescript
import fastify from 'fastify';
import { MCPApp, mcpTool } from 'mcp-core-node';

const server = fastify({ logger: true });
const mcpApp = new MCPApp({
  app: server,
  key: 'my-fastify-app',
  description: 'My Fastify MCP Application',
  registryUrl: 'http://localhost:8000'
});

// Schema definition
const userSchema = {
  body: {
    type: 'object',
    properties: {
      name: { type: 'string' },
      age: { type: 'number' }
    },
    required: ['name', 'age']
  }
};

// Route with MCP integration
server.post('/users', {
  schema: userSchema,
  preHandler: mcpApp.tool({
    description: 'Create a new user',
    tags: ['users']
  })
}, async (request, reply) => {
  const user = request.body as { name: string; age: number };
  return {
    message: `User ${user.name} created successfully`,
    timestamp: new Date().toISOString()
  };
});

server.get('/users/:id', {
  preHandler: mcpApp.tool({
    description: 'Get user by ID',
    tags: ['users']
  })
}, async (request, reply) => {
  const { id } = request.params as { id: string };
  return {
    message: `User ${id} retrieved`,
    timestamp: new Date().toISOString()
  };
});
```

### Koa Integration

```typescript
import Koa from 'koa';
import Router from 'koa-router';
import { MCPApp, mcpTool } from 'mcp-core-node';

const app = new Koa();
const router = new Router();
const mcpApp = new MCPApp({
  app,
  key: 'my-koa-app',
  description: 'My Koa MCP Application',
  registryUrl: 'http://localhost:8000'
});

// Middleware for MCP integration
router.post('/users',
  mcpApp.tool({
    description: 'Create a new user',
    tags: ['users']
  }),
  async (ctx) => {
    const user = ctx.request.body;
    ctx.body = {
      message: `User ${user.name} created successfully`,
      timestamp: new Date().toISOString()
    };
  }
);

router.get('/users/:id',
  mcpApp.tool({
    description: 'Get user by ID',
    tags: ['users']
  }),
  async (ctx) => {
    const userId = ctx.params.id;
    ctx.body = {
      message: `User ${userId} retrieved`,
      timestamp: new Date().toISOString()
    };
  }
);

app.use(router.routes());
```

### NestJS Integration

```typescript
import { Controller, Post, Get, Body, Param } from '@nestjs/common';
import { MCPTool } from 'mcp-core-node';

@Controller('users')
export class UsersController {
  @Post()
  @MCPTool({
    description: 'Create a new user',
    tags: ['users']
  })
  async createUser(@Body() user: { name: string; age: number }) {
    return {
      message: `User ${user.name} created successfully`,
      timestamp: new Date().toISOString()
    };
  }

  @Get(':id')
  @MCPTool({
    description: 'Get user by ID',
    tags: ['users']
  })
  async getUser(@Param('id') id: string) {
    return {
      message: `User ${id} retrieved`,
      timestamp: new Date().toISOString()
    };
  }
}

// Module configuration
import { Module } from '@nestjs/common';
import { MCPModule } from 'mcp-core-node';

@Module({
  imports: [
    MCPModule.register({
      key: 'my-nest-app',
      description: 'My NestJS MCP Application',
      registryUrl: 'http://localhost:8000'
    })
  ],
  controllers: [UsersController]
})
export class AppModule {}
```

## 🔧 Configuration

### Environment Variables

```env
# MCP Registry Configuration
MCP_REGISTRY_URL=http://localhost:8000
MCP_APP_KEY=my-node-app
MCP_APP_DESCRIPTION=My Node.js MCP Application

# Authentication
MCP_API_KEY=your-api-key-here
MCP_AUTH_TYPE=api_key

# Performance
MCP_CACHE_TTL=300
MCP_BATCH_SIZE=10
MCP_TIMEOUT=30000
```

### Configuration Object

```typescript
import { MCPConfig } from 'mcp-core-node';

const config: MCPConfig = {
  registryUrl: 'http://localhost:8000',
  appKey: 'my-node-app',
  description: 'My Node.js MCP Application',
  apiKey: 'your-api-key-here',
  authType: 'api_key',
  cacheTtl: 300,
  batchSize: 10,
  timeout: 30000,
  environment: 'production'
};
```

## 🧪 Testing Support

### Unit Testing (Jest)

```typescript
import request from 'supertest';
import { MCPTestClient } from 'mcp-core-node/testing';
import app from '../src/app';

describe('User API', () => {
  let mcpClient: MCPTestClient;

  beforeAll(async () => {
    mcpClient = new MCPTestClient(app, {
      registryUrl: 'http://localhost:8000'
    });
    await mcpClient.initialize();
  });

  afterAll(async () => {
    await mcpClient.cleanup();
  });

  test('should create a user', async () => {
    const response = await request(app)
      .post('/users')
      .send({ name: 'John', age: 30 })
      .expect(200);

    expect(response.body.message).toContain('User John created successfully');
  });

  test('should get a user', async () => {
    const response = await request(app)
      .get('/users/1')
      .expect(200);

    expect(response.body.message).toContain('User 1 retrieved');
  });
});
```

### Integration Testing

```typescript
import { MCPIntegrationTest } from 'mcp-core-node/testing';

describe('MCP Integration', () => {
  let integration: MCPIntegrationTest;

  beforeAll(async () => {
    integration = new MCPIntegrationTest({
      registryUrl: 'http://localhost:8000'
    });
    await integration.setup();
  });

  afterAll(async () => {
    await integration.teardown();
  });

  test('should register with MCP registry', async () => {
    const isRegistered = await integration.isRegistered('my-node-app');
    expect(isRegistered).toBe(true);
  });

  test('should discover endpoints', async () => {
    const endpoints = await integration.getRegisteredEndpoints();
    expect(endpoints).toHaveLength(2);
    expect(endpoints.some(ep => ep.uri === '/users')).toBe(true);
  });
});
```

## 📦 Installation (When Available)

```bash
# Install from npm
npm install mcp-core-node

# Install with TypeScript support
npm install mcp-core-node typescript @types/node

# Install with specific framework support
npm install mcp-core-node express
npm install mcp-core-node fastify
npm install mcp-core-node koa
npm install mcp-core-node @nestjs/core

# Install from source
git clone https://github.com/autogentmcp/mcp-core-node.git
cd mcp-core-node
npm install
npm run build
```

## 🤝 Contributing

We welcome contributions to the Node.js SDK! Here's how you can help:

### 1. Development Setup

```bash
# Clone the repository
git clone https://github.com/autogentmcp/mcp-core-node.git
cd mcp-core-node

# Install dependencies
npm install

# Run tests
npm test

# Run linting
npm run lint

# Build the project
npm run build
```

### 2. Areas for Contribution

- **Framework Integrations**: Add support for new Node.js frameworks
- **Testing**: Improve test coverage and add integration tests
- **Documentation**: Write guides and examples
- **Performance**: Optimize for high-throughput applications
- **TypeScript**: Improve type definitions and type safety

### 3. Development Guidelines

- Follow TypeScript strict mode
- Add comprehensive tests for new features
- Update documentation for new features
- Ensure backward compatibility
- Follow semantic versioning

### 4. Project Structure

```
mcp-core-node/
├── src/
│   ├── core/           # Core MCP functionality
│   ├── frameworks/     # Framework integrations
│   ├── testing/        # Testing utilities
│   └── types/          # TypeScript type definitions
├── tests/
│   ├── unit/           # Unit tests
│   ├── integration/    # Integration tests
│   └── fixtures/       # Test fixtures
├── docs/               # Documentation
└── examples/           # Example applications
```

## 📞 Support

While the SDK is in development, you can:

- **Star the repository** to show interest
- **Create issues** for feature requests
- **Join discussions** about the SDK design
- **Follow progress** on the roadmap

---

**Status**: 🚧 **In Development** - Expected release Q1 2025

For updates on the Node.js SDK development, follow the [mcp-core-node repository](https://github.com/autogentmcp/mcp-core-node) and join our community discussions.
