# Production Deployment Guide

This guide covers deploying the complete Autogent MCP ecosystem to production environments with enterprise-grade security, monitoring, and scalability.

## 🏗️ Architecture Overview

```mermaid
graph TB
    subgraph "External Users"
        A[Web Browser]
        B[API Clients]
    end
    
    subgraph "Load Balancer"
        C[nginx/HAProxy]
    end
    
    subgraph "Application Layer"
        D[MCP Portal<br/>Next.js]
        E[MCP Registry<br/>FastAPI]
        F[Autogent Server<br/>FastAPI]
        G[Client Apps<br/>Java/Python/Node]
    end
    
    subgraph "Database Layer"
        H[PostgreSQL<br/>Primary]
        I[PostgreSQL<br/>Replica]
    end
    
    subgraph "Security Layer"
        J[HashiCorp Vault<br/>HA Cluster]
        K[Redis<br/>Session Store]
    end
    
    subgraph "Monitoring"
        L[Prometheus]
        M[Grafana]
        N[Jaeger]
    end
    
    A --> C
    B --> C
    C --> D
    C --> E
    C --> F
    D --> H
    E --> H
    F --> H
    H --> I
    D --> J
    E --> J
    F --> J
    G --> E
    D --> K
    E --> K
    F --> K
    
    L --> D
    L --> E
    L --> F
    M --> L
    N --> D
    N --> E
    N --> F
    
    style D fill:#e8f5e8
    style E fill:#e1f5fe
    style F fill:#f3e5f5
    style J fill:#fff3e0
```

## 🚀 Deployment Steps

### 1. Infrastructure Setup

#### 1.1 Server Requirements

**Minimum Production Setup**:
```
Portal Server:     2 CPU, 4GB RAM, 20GB SSD
Registry Server:   2 CPU, 4GB RAM, 20GB SSD
Autogent Server:   4 CPU, 8GB RAM, 40GB SSD
Database Server:   4 CPU, 8GB RAM, 100GB SSD
Vault Server:      2 CPU, 4GB RAM, 20GB SSD
```

**Recommended Production Setup**:
```
Portal Server:     4 CPU, 8GB RAM, 50GB SSD
Registry Server:   4 CPU, 8GB RAM, 50GB SSD
Autogent Server:   8 CPU, 16GB RAM, 100GB SSD
Database Server:   8 CPU, 16GB RAM, 500GB SSD
Vault Server:      4 CPU, 8GB RAM, 50GB SSD
```

#### 1.2 Network Configuration

```bash
# Security Groups / Firewall Rules
# Portal (Internal only)
Portal:     3000/tcp (internal)

# Registry (Public API)
Registry:   8000/tcp (public)

# Autogent Server (Public API)
Autogent:   8001/tcp (public)

# Database (Internal only)
Database:   5432/tcp (internal)

# Vault (Internal only)
Vault:      8200/tcp (internal)

# Load Balancer
LB:         80/tcp, 443/tcp (public)
```

### 2. Database Setup

#### 2.1 PostgreSQL Installation

```bash
# Install PostgreSQL 15
sudo apt update
sudo apt install postgresql-15 postgresql-contrib

# Create database and user
sudo -u postgres psql
CREATE DATABASE mcp_registry;
CREATE USER mcp_user WITH ENCRYPTED PASSWORD 'your_secure_password';
GRANT ALL PRIVILEGES ON DATABASE mcp_registry TO mcp_user;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO mcp_user;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO mcp_user;
```

#### 2.2 Database Configuration

```bash
# Edit postgresql.conf
sudo nano /etc/postgresql/15/main/postgresql.conf

# Key settings for production
max_connections = 200
shared_buffers = 256MB
effective_cache_size = 1GB
maintenance_work_mem = 64MB
checkpoint_completion_target = 0.9
wal_buffers = 16MB
default_statistics_target = 100
```

#### 2.3 Database Backup Strategy

```bash
# Create backup script
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backup/postgresql"
mkdir -p $BACKUP_DIR

pg_dump -h localhost -U mcp_user -d mcp_registry > $BACKUP_DIR/mcp_registry_$DATE.sql

# Keep only last 7 days
find $BACKUP_DIR -name "*.sql" -mtime +7 -delete
```

### 3. HashiCorp Vault Setup

#### 3.1 Vault Installation

```bash
# Install Vault
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update && sudo apt install vault

# Create vault configuration
sudo mkdir -p /etc/vault.d
sudo tee /etc/vault.d/vault.hcl <<EOF
storage "file" {
  path = "/opt/vault/data"
}

listener "tcp" {
  address     = "0.0.0.0:8200"
  tls_disable = 0
  tls_cert_file = "/etc/vault.d/vault.crt"
  tls_key_file = "/etc/vault.d/vault.key"
}

api_addr = "https://127.0.0.1:8200"
cluster_addr = "https://127.0.0.1:8201"
ui = true
EOF
```

#### 3.2 Vault Initialization

```bash
# Initialize Vault
vault operator init

# Unseal Vault (use 3 of 5 keys)
vault operator unseal <key1>
vault operator unseal <key2>
vault operator unseal <key3>

# Enable KV secrets engine
vault secrets enable -version=2 kv

# Create MCP policy
vault policy write mcp-policy - <<EOF
path "secret/data/mcp/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}
EOF

# Create service token
vault token create -policy=mcp-policy -ttl=8760h
```

### 4. MCP Portal Deployment

#### 4.1 Portal Setup

```bash
# Clone and build portal
git clone https://github.com/autogentmcp/portal.git
cd portal

# Install dependencies
npm ci --production

# Build application
npm run build

# Create production environment
sudo tee /etc/systemd/system/mcp-portal.service <<EOF
[Unit]
Description=MCP Portal
After=network.target

[Service]
Type=simple
User=mcp
WorkingDirectory=/opt/mcp/portal
ExecStart=/usr/bin/node server.js
Restart=always
RestartSec=3
Environment=NODE_ENV=production
Environment=PORT=3000
EnvironmentFile=/etc/mcp/portal.env

[Install]
WantedBy=multi-user.target
EOF
```

#### 4.2 Portal Configuration

```bash
# Create production environment file
sudo mkdir -p /etc/mcp
sudo tee /etc/mcp/portal.env <<EOF
# Database
DATABASE_URL="postgresql://mcp_user:your_secure_password@localhost:5432/mcp_registry"

# JWT Authentication
JWT_SECRET="your-super-secret-jwt-key-here-min-32-chars"

# Application
NODE_ENV="production"
PORT=3000

# Security Provider
SECURITY_PROVIDER="hashicorp_vault"
VAULT_URL="https://localhost:8200"
VAULT_TOKEN="your-vault-token-here"
VAULT_NAMESPACE="admin"
VAULT_PATH="secret/data/mcp"
VAULT_MOUNT="kv"

# SSL/TLS
SSL_CERT="/etc/ssl/certs/portal.crt"
SSL_KEY="/etc/ssl/private/portal.key"
EOF
```

### 5. MCP Registry Deployment

#### 5.1 Registry Setup

```bash
# Create virtual environment
python3 -m venv /opt/mcp/registry
cd /opt/mcp/registry
source bin/activate

# Clone and install
git clone https://github.com/autogentmcp/mcp-registry.git .
pip install -r requirements.txt

# Run database migrations
prisma generate
prisma db push

# Create systemd service
sudo tee /etc/systemd/system/mcp-registry.service <<EOF
[Unit]
Description=MCP Registry Server
After=network.target postgresql.service

[Service]
Type=simple
User=mcp
WorkingDirectory=/opt/mcp/registry
ExecStart=/opt/mcp/registry/bin/python run_server.py
Restart=always
RestartSec=3
Environment=PYTHONPATH=/opt/mcp/registry
EnvironmentFile=/etc/mcp/registry.env

[Install]
WantedBy=multi-user.target
EOF
```

#### 5.2 Registry Configuration

```bash
# Create production environment file
sudo tee /etc/mcp/registry.env <<EOF
# Database
DATABASE_URL="postgresql://mcp_user:your_secure_password@localhost:5432/mcp_registry"

# Server
HOST=0.0.0.0
PORT=8000
WORKERS=4

# Security
SECRET_KEY="your-secret-key-here"
ALLOWED_HOSTS=["your-domain.com", "api.your-domain.com"]

# Logging
LOG_LEVEL=INFO
LOG_FILE="/var/log/mcp/registry.log"

# Health Check
HEALTH_CHECK_INTERVAL=30
HEALTH_CHECK_TIMEOUT=10
HEALTH_CHECK_RETRIES=3
EOF
```

### 6. Autogent Server Deployment

#### 6.1 Autogent Server Setup

```bash
# Create virtual environment
python3 -m venv /opt/mcp/autogent
cd /opt/mcp/autogent
source bin/activate

# Clone and install
git clone https://github.com/autogentmcp/autogentmcp_server.git .
pip install -r requirements.txt

# Create systemd service
sudo tee /etc/systemd/system/mcp-autogent.service <<EOF
[Unit]
Description=Autogent MCP Server
After=network.target mcp-registry.service

[Service]
Type=simple
User=mcp
WorkingDirectory=/opt/mcp/autogent
ExecStart=/opt/mcp/autogent/bin/uvicorn app.main:app --host 0.0.0.0 --port 8001 --workers 4
Restart=always
RestartSec=3
Environment=PYTHONPATH=/opt/mcp/autogent
EnvironmentFile=/etc/mcp/autogent.env

[Install]
WantedBy=multi-user.target
EOF
```

#### 6.2 Autogent Configuration

```bash
# Create production environment file
sudo tee /etc/mcp/autogent.env <<EOF
# Registry
REGISTRY_URL="http://localhost:8000"
REGISTRY_API_KEY="your-registry-api-key"

# LLM Configuration
OLLAMA_BASE_URL="http://localhost:11434"
OPENAI_API_KEY="your-openai-api-key"
DEFAULT_MODEL="llama3.1"

# Vault Integration
VAULT_URL="https://localhost:8200"
VAULT_TOKEN="your-vault-token"
VAULT_MOUNT="kv"
VAULT_PATH="secret/data/mcp"

# Performance
CACHE_TTL=300
MAX_CONCURRENT_REQUESTS=50
REQUEST_TIMEOUT=30

# Logging
LOG_LEVEL=INFO
LOG_FILE="/var/log/mcp/autogent.log"
EOF
```

### 7. Load Balancer Setup

#### 7.1 Nginx Configuration

```bash
# Install nginx
sudo apt install nginx

# Create configuration
sudo tee /etc/nginx/sites-available/mcp <<EOF
upstream mcp_portal {
    server localhost:3000;
}

upstream mcp_registry {
    server localhost:8000;
}

upstream mcp_autogent {
    server localhost:8001;
}

server {
    listen 80;
    server_name portal.your-domain.com;
    return 301 https://\$server_name\$request_uri;
}

server {
    listen 443 ssl http2;
    server_name portal.your-domain.com;
    
    ssl_certificate /etc/ssl/certs/portal.crt;
    ssl_certificate_key /etc/ssl/private/portal.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    
    location / {
        proxy_pass http://mcp_portal;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}

server {
    listen 80;
    server_name api.your-domain.com;
    return 301 https://\$server_name\$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.your-domain.com;
    
    ssl_certificate /etc/ssl/certs/api.crt;
    ssl_certificate_key /etc/ssl/private/api.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    
    location /registry/ {
        proxy_pass http://mcp_registry/;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
    
    location /autogent/ {
        proxy_pass http://mcp_autogent/;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOF

# Enable site
sudo ln -s /etc/nginx/sites-available/mcp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### 8. SSL Certificate Setup

#### 8.1 Let's Encrypt (Free SSL)

```bash
# Install certbot
sudo apt install certbot python3-certbot-nginx

# Generate certificates
sudo certbot --nginx -d portal.your-domain.com -d api.your-domain.com

# Auto-renewal
sudo crontab -e
# Add: 0 12 * * * /usr/bin/certbot renew --quiet
```

#### 8.2 Custom SSL Certificate

```bash
# Generate private key
sudo openssl genrsa -out /etc/ssl/private/portal.key 2048

# Generate certificate request
sudo openssl req -new -key /etc/ssl/private/portal.key -out /etc/ssl/certs/portal.csr

# Generate self-signed certificate (for testing)
sudo openssl x509 -req -days 365 -in /etc/ssl/certs/portal.csr -signkey /etc/ssl/private/portal.key -out /etc/ssl/certs/portal.crt
```

### 9. Monitoring Setup

#### 9.1 Prometheus Configuration

```bash
# Install Prometheus
sudo apt install prometheus

# Configure Prometheus
sudo tee /etc/prometheus/prometheus.yml <<EOF
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'mcp-portal'
    static_configs:
      - targets: ['localhost:3000']
    metrics_path: '/metrics'
    
  - job_name: 'mcp-registry'
    static_configs:
      - targets: ['localhost:8000']
    metrics_path: '/metrics'
    
  - job_name: 'mcp-autogent'
    static_configs:
      - targets: ['localhost:8001']
    metrics_path: '/metrics'
EOF

sudo systemctl restart prometheus
```

#### 9.2 Grafana Setup

```bash
# Install Grafana
sudo apt install grafana

# Configure Grafana
sudo systemctl enable grafana-server
sudo systemctl start grafana-server

# Access Grafana at http://localhost:3000
# Default credentials: admin/admin
```

### 10. Security Hardening

#### 10.1 System Security

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Configure firewall
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw --force enable

# Disable unnecessary services
sudo systemctl disable bluetooth
sudo systemctl disable cups

# Configure fail2ban
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

#### 10.2 Application Security

```bash
# Create dedicated user
sudo useradd -r -s /bin/false mcp
sudo mkdir -p /opt/mcp
sudo chown mcp:mcp /opt/mcp

# Set file permissions
sudo chmod 750 /opt/mcp
sudo chmod 640 /etc/mcp/*.env

# Enable SELinux/AppArmor (if available)
sudo apt install apparmor-utils
sudo aa-enforce /etc/apparmor.d/*
```

### 11. Backup Strategy

#### 11.1 Database Backup

```bash
# Create backup script
sudo tee /usr/local/bin/backup-mcp.sh <<EOF
#!/bin/bash
DATE=\$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backup/mcp"
mkdir -p \$BACKUP_DIR

# Database backup
pg_dump -h localhost -U mcp_user -d mcp_registry > \$BACKUP_DIR/database_\$DATE.sql

# Application backup
tar -czf \$BACKUP_DIR/portal_\$DATE.tar.gz -C /opt/mcp portal
tar -czf \$BACKUP_DIR/registry_\$DATE.tar.gz -C /opt/mcp registry
tar -czf \$BACKUP_DIR/autogent_\$DATE.tar.gz -C /opt/mcp autogent

# Vault backup
vault operator raft snapshot save \$BACKUP_DIR/vault_\$DATE.snap

# Cleanup old backups (keep 30 days)
find \$BACKUP_DIR -name "*.sql" -mtime +30 -delete
find \$BACKUP_DIR -name "*.tar.gz" -mtime +30 -delete
find \$BACKUP_DIR -name "*.snap" -mtime +30 -delete
EOF

sudo chmod +x /usr/local/bin/backup-mcp.sh

# Schedule backup
sudo crontab -e
# Add: 0 2 * * * /usr/local/bin/backup-mcp.sh
```

### 12. Service Management

#### 12.1 Start All Services

```bash
# Enable and start services
sudo systemctl enable postgresql
sudo systemctl enable vault
sudo systemctl enable mcp-portal
sudo systemctl enable mcp-registry
sudo systemctl enable mcp-autogent
sudo systemctl enable nginx
sudo systemctl enable prometheus
sudo systemctl enable grafana-server

# Start services in order
sudo systemctl start postgresql
sudo systemctl start vault
sudo systemctl start mcp-registry
sudo systemctl start mcp-autogent
sudo systemctl start mcp-portal
sudo systemctl start nginx
sudo systemctl start prometheus
sudo systemctl start grafana-server
```

#### 12.2 Health Check Script

```bash
# Create health check script
sudo tee /usr/local/bin/health-check.sh <<EOF
#!/bin/bash

echo "=== MCP Ecosystem Health Check ==="
echo "Date: \$(date)"
echo

# Check services
services=("postgresql" "vault" "mcp-portal" "mcp-registry" "mcp-autogent" "nginx")
for service in "\${services[@]}"; do
    if systemctl is-active --quiet \$service; then
        echo "✓ \$service is running"
    else
        echo "✗ \$service is not running"
    fi
done

echo

# Check endpoints
endpoints=(
    "http://localhost:3000/health"
    "http://localhost:8000/health"
    "http://localhost:8001/health"
)

for endpoint in "\${endpoints[@]}"; do
    if curl -s -o /dev/null -w "%{http_code}" \$endpoint | grep -q "200"; then
        echo "✓ \$endpoint is responding"
    else
        echo "✗ \$endpoint is not responding"
    fi
done
EOF

sudo chmod +x /usr/local/bin/health-check.sh
```

## 🔄 Maintenance

### Regular Tasks

1. **Daily**:
   - Check service status
   - Review logs for errors
   - Monitor resource usage

2. **Weekly**:
   - Update system packages
   - Review security logs
   - Check backup integrity

3. **Monthly**:
   - Rotate certificates
   - Review access logs
   - Update dependencies

### Log Management

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/mcp <<EOF
/var/log/mcp/*.log {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    notifempty
    create 644 mcp mcp
    postrotate
        systemctl reload mcp-portal mcp-registry mcp-autogent
    endscript
}
EOF
```

### Scaling Considerations

1. **Horizontal Scaling**:
   - Add more application servers
   - Use load balancer health checks
   - Implement session affinity

2. **Database Scaling**:
   - Set up read replicas
   - Implement connection pooling
   - Consider database partitioning

3. **Vault Scaling**:
   - Configure Vault HA cluster
   - Use Consul for storage backend
   - Implement auto-unsealing

## 📞 Support

For production deployment support:
- Review the troubleshooting guides
- Check the monitoring dashboards
- Contact support for enterprise assistance

---

This deployment guide provides a comprehensive setup for production environments. Adjust configurations based on your specific requirements and infrastructure.
