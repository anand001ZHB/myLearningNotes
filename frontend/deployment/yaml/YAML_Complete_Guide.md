# Complete YAML Guide: Everything You Need to Know

## Table of Contents
1. [Introduction to YAML](#introduction-to-yaml)
2. [Basic Syntax](#basic-syntax)
3. [Data Types](#data-types)
4. [Collections](#collections)
5. [Advanced Features](#advanced-features)
6. [Common Use Cases](#common-use-cases)
7. [Best Practices](#best-practices)
8. [Examples](#examples)
9. [Tools and Resources](#tools-and-resources)

---

## Introduction to YAML

### What is YAML?

**YAML** stands for **"YAML Ain't Markup Language"** (recursive acronym). It's a human-readable data serialization standard that can be used in conjunction with all programming languages and is often used for writing configuration files.

### Key Characteristics

- **Human-readable**: Easy to read and write
- **Language-independent**: Works with any programming language
- **Data-focused**: Designed for data, not documents
- **Whitespace-sensitive**: Indentation matters (like Python)
- **Case-sensitive**: `Name` and `name` are different

### Common File Extensions

- `.yaml` (preferred)
- `.yml` (commonly used, especially in CI/CD)

---

## Basic Syntax

### Indentation

YAML uses **spaces** (not tabs) for indentation. Standard indentation is **2 spaces** per level.

```yaml
# Correct (2 spaces)
person:
  name: John
  age: 30

# Incorrect (tabs or inconsistent spacing)
person:
    name: John  # Wrong - 4 spaces
  age: 30       # Wrong - 2 spaces (inconsistent)
```

### Comments

Comments start with `#` and continue to the end of the line.

```yaml
# This is a comment
name: John  # Inline comment
# Multi-line comments require # on each line
```

### Key-Value Pairs

Basic structure uses `key: value` format.

```yaml
name: John Doe
age: 30
email: john@example.com
```

### Strings

Strings can be written in multiple ways:

```yaml
# Simple string (no quotes needed if unambiguous)
name: John Doe

# Quoted strings (useful for special characters)
name: "John O'Doe"
description: "He said \"Hello\""

# Single quotes (preserves everything literally)
name: 'John O''Doe'  # Single quote escaped as ''

# Multiline strings
description: |
  This is a multiline string.
  It preserves line breaks.
  All lines are included.

# Folded strings (converts line breaks to spaces)
description: >
  This is a folded string.
  Line breaks become spaces.
  Good for long paragraphs.
```

### Boolean Values

```yaml
# True values
isActive: true
enabled: True
yes: YES
on: On

# False values
isActive: false
disabled: False
no: NO
off: Off

# Null/empty
value: null
empty: ~
nothing: Null
```

### Numbers

```yaml
# Integers
age: 30
count: -5
zero: 0

# Floats
price: 29.99
temperature: -10.5
pi: 3.14159

# Scientific notation
large: 1.2e+10
small: 3.4e-5

# Octal (base 8)
octal: 0o755

# Hexadecimal (base 16)
hex: 0xFF

# Infinity and NaN
infinity: .inf
negative_infinity: -.inf
not_a_number: .nan
```

---

## Data Types

### Explicit Type Tags

YAML supports explicit type specification:

```yaml
# String
name: !!str John

# Integer
age: !!int 30

# Float
price: !!float 29.99

# Boolean
isActive: !!bool true

# Null
value: !!null null

# Timestamp
date: !!timestamp 2024-01-15T10:30:00Z

# Binary data
image: !!binary |
  R0lGODlhDAAMAIQAAP//9/X
  17unp5WZmZgAAAOfn515eXv
  Pz7Y6OjuDg4J+fn5OTk6enp
  56enmleECcgggoBADs=
```

### Date and Time

```yaml
# ISO 8601 format
date1: 2024-01-15
datetime1: 2024-01-15T10:30:00
datetime2: 2024-01-15T10:30:00Z
datetime3: 2024-01-15T10:30:00+05:30

# Timestamp
timestamp: !!timestamp 2024-01-15 10:30:00
```

---

## Collections

### Lists/Arrays

```yaml
# Simple list (hyphen syntax)
fruits:
  - apple
  - banana
  - orange

# Inline list
fruits: [apple, banana, orange]

# List of objects
people:
  - name: John
    age: 30
  - name: Jane
    age: 25

# Mixed types
items: [apple, 42, true, null]
```

### Dictionaries/Maps

```yaml
# Simple dictionary
person:
  name: John
  age: 30
  city: New York

# Inline dictionary
person: {name: John, age: 30, city: New York}

# Nested dictionaries
company:
  name: Acme Corp
  address:
    street: 123 Main St
    city: New York
    zip: 10001
  employees:
    - name: John
      role: Developer
    - name: Jane
      role: Designer
```

### Complex Nested Structures

```yaml
# Combining lists and dictionaries
server:
  name: production
  ports:
    - 80
    - 443
  environment:
    database:
      host: db.example.com
      port: 5432
      name: mydb
    cache:
      enabled: true
      ttl: 3600
  services:
    - name: api
      version: 1.0.0
      replicas: 3
    - name: worker
      version: 2.1.0
      replicas: 2
```

---

## Advanced Features

### Anchors and Aliases

Reuse data without duplication:

```yaml
# Define anchor with &
defaults: &defaults
  adapter: postgres
  host: localhost
  port: 5432

# Reference anchor with *
development:
  <<: *defaults  # Merge anchor
  database: dev_db

production:
  <<: *defaults
  database: prod_db
  host: prod.example.com

# Simple alias
name: &name John Doe
person1: *name
person2: *name
```

### Multi-Document Files

Separate multiple documents with `---`:

```yaml
---
# Document 1
name: John
age: 30
---
# Document 2
name: Jane
age: 25
---
# Document 3
name: Bob
age: 35
```

### Tags and Custom Types

```yaml
# Custom tag
!myTag value

# Example with custom types
!!python/object:__main__.Person
name: John
age: 30

# Example with custom constructor
!CustomType
  field1: value1
  field2: value2
```

### Flow vs Block Style

```yaml
# Block style (default, more readable)
person:
  name: John
  age: 30
  hobbies:
    - reading
    - coding

# Flow style (compact, JSON-like)
person: {name: John, age: 30, hobbies: [reading, coding]}

# Mixed style
person:
  name: John
  tags: [developer, python, yaml]
```

### Escaping Special Characters

```yaml
# Colon in value
description: "Time: 10:30 AM"

# Hash in value
comment: "Version #1.0"

# Pipe and greater-than
text: "Use | for multiline, > for folded"

# Special characters
special: "Line 1\nLine 2\tTabbed"
```

---

## Common Use Cases

### Configuration Files

```yaml
# application.yaml (Spring Boot example)
spring:
  application:
    name: myapp
  datasource:
    url: jdbc:postgresql://localhost/mydb
    username: admin
    password: secret
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

server:
  port: 8080
  servlet:
    context-path: /api

logging:
  level:
    root: INFO
    com.example: DEBUG
```

### Docker Compose

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=example.com
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

### CI/CD Pipelines

#### GitHub Actions

GitHub Actions uses YAML files (`.github/workflows/*.yml`) to define CI/CD pipelines. Key concepts:

- **Workflows**: Defined in `.github/workflows/` directory
- **Events**: Triggers like `push`, `pull_request`, `schedule`, `workflow_dispatch`
- **Jobs**: Units of work that run on runners (can run in parallel or sequentially)
- **Steps**: Individual tasks within a job
- **Actions**: Reusable units of work (from marketplace or custom)
- **Runners**: Virtual machines that execute jobs (GitHub-hosted or self-hosted)
- **Secrets**: Encrypted variables for sensitive data
- **Environments**: Deployment targets with protection rules

**Key GitHub Actions YAML Syntax:**

```yaml
# Workflow triggers
on:
  push:
    branches: [main]
    paths:
      - 'src/**'
      - 'package.json'
  pull_request:
    types: [opened, synchronize, reopened]
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight
  workflow_dispatch:  # Manual trigger
    inputs:
      environment:
        type: choice
        options: [staging, production]

# Matrix strategy for multiple configurations
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest, macos-latest]
    node-version: [16.x, 18.x, 20.x]

# Job dependencies
jobs:
  build:
    runs-on: ubuntu-latest
  test:
    needs: build  # Wait for build to complete
  deploy:
    needs: [build, test]  # Wait for both

# Conditional execution
if: github.ref == 'refs/heads/main'
if: success() || failure()
if: contains(github.event.head_commit.message, 'deploy')

# Environment protection
environment:
  name: production
  url: https://example.com
```

See detailed examples in the [Real-World Examples](#real-world-examples) section below.

#### Azure Pipelines

Azure DevOps uses YAML files (`azure-pipelines.yml`) for CI/CD. Key concepts:

- **Stages**: High-level divisions (Build, Test, Deploy)
- **Jobs**: Units of work within stages
- **Tasks**: Individual operations (build, test, deploy)
- **Variables**: Configuration values (can be scoped)
- **Environments**: Deployment targets with approvals
- **Service Connections**: External service authentication

**Key Azure Pipelines YAML Syntax:**

```yaml
# Multi-stage pipeline
stages:
  - stage: Build
    jobs:
      - job: BuildJob
        steps: [...]

# Deployment jobs with strategies
deployment: DeployApp
  strategy:
    runOnce:
      deploy: [...]
    canary:
      increments: [10, 20, 30, 100]
    rolling:
      maxParallel: 2

# Variable groups and secrets
variables:
  - group: 'Production-Secrets'
  - name: buildConfiguration
    value: 'Release'

# Conditions and dependencies
condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
dependsOn: Build
```

See detailed examples in the [Real-World Examples](#real-world-examples) section below.

### Kubernetes Manifests

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app:1.0.0
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
---
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: LoadBalancer
```

### Ansible Playbooks

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: yes
  vars:
    http_port: 80
    max_clients: 200
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Configure nginx
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: restart nginx

    - name: Start nginx
      service:
        name: nginx
        state: started
        enabled: yes

  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

---

## Best Practices

### 1. Consistent Indentation

```yaml
# Good: Consistent 2-space indentation
server:
  name: production
  config:
    port: 8080
    timeout: 30

# Bad: Mixed indentation
server:
    name: production
  config:
      port: 8080
    timeout: 30
```

### 2. Use Meaningful Keys

```yaml
# Good: Descriptive keys
database_connection_timeout: 30
max_retry_attempts: 3

# Bad: Abbreviated or unclear
db_to: 30
mra: 3
```

### 3. Organize Related Data

```yaml
# Good: Logical grouping
application:
  name: myapp
  version: 1.0.0
database:
  host: localhost
  port: 5432
logging:
  level: INFO
  file: app.log

# Bad: Flat structure
application_name: myapp
application_version: 1.0.0
database_host: localhost
database_port: 5432
logging_level: INFO
logging_file: app.log
```

### 4. Use Anchors for Repeated Data

```yaml
# Good: Reuse with anchors
defaults: &defaults
  timeout: 30
  retries: 3

service1:
  <<: *defaults
  name: api

service2:
  <<: *defaults
  name: worker

# Bad: Duplication
service1:
  timeout: 30
  retries: 3
  name: api

service2:
  timeout: 30
  retries: 3
  name: worker
```

### 5. Quote Strings When Necessary

```yaml
# Good: Quote special values
version: "1.0.0"
description: "Version #1 with special chars: yes"

# Good: No quotes for simple strings
name: John
age: 30

# Bad: Unquoted special characters
version: 1.0.0  # Could be interpreted as number
description: Version #1  # Comment starts here!
```

### 6. Validate Your YAML

Always validate YAML files before using them:
- Use online validators
- Use IDE plugins
- Use command-line tools like `yamllint`

### 7. Document Complex Structures

```yaml
# Good: Comments explain complex logic
# This configuration uses a round-robin load balancing strategy
# with health checks every 30 seconds
load_balancer:
  strategy: round-robin
  health_check:
    interval: 30  # seconds
    timeout: 5    # seconds
    retries: 3
```

---

## Real-World Examples

### Example 1: Kubernetes Deployment

Complete Kubernetes manifest with Deployment, Service, ConfigMap, and Secret:

```yaml
# k8s-deployment.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    environment: production
    team: backend
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: production
data:
  app.properties: |
    database.host=postgres-service
    database.port=5432
    cache.enabled=true
    cache.ttl=3600
    logging.level=INFO
  nginx.conf: |
    server {
      listen 80;
      server_name example.com;
      location / {
        proxy_pass http://localhost:8080;
      }
    }
---
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
  namespace: production
type: Opaque
stringData:
  database-password: "super-secret-password-123"
  api-key: "sk_live_abc123xyz789"
  jwt-secret: "my-jwt-secret-key-2024"
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: production
  labels:
    app: web-app
    version: v1.0.0
    environment: production
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
        version: v1.0.0
    spec:
      containers:
      - name: web-app
        image: myregistry/web-app:1.0.0
        imagePullPolicy: Always
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
        - name: metrics
          containerPort: 9090
          protocol: TCP
        env:
        - name: DATABASE_HOST
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.host
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: database-password
        - name: ENVIRONMENT
          value: "production"
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 3
        volumeMounts:
        - name: config-volume
          mountPath: /etc/config
          readOnly: true
      volumes:
      - name: config-volume
        configMap:
          name: app-config
      restartPolicy: Always
      imagePullSecrets:
      - name: registry-credentials
---
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
  namespace: production
  labels:
    app: web-app
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 8080
    protocol: TCP
  - name: metrics
    port: 9090
    targetPort: 9090
    protocol: TCP
  selector:
    app: web-app
  sessionAffinity: ClientIP
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 10800
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-app-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-app
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 30
      - type: Pods
        value: 2
        periodSeconds: 30
      selectPolicy: Max
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-app-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/rate-limit: "100"
spec:
  tls:
  - hosts:
    - api.example.com
    - www.example.com
    secretName: web-app-tls
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-app-service
            port:
              number: 80
  - host: www.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-app-service
            port:
              number: 80
```

### Example 2: Docker Compose - Multi-Service Application

Complete Docker Compose setup with web app, database, cache, and monitoring:

```yaml
# docker-compose.yml
version: '3.8'

services:
  # Web Application
  web:
    build:
      context: ./web
      dockerfile: Dockerfile
      args:
        NODE_ENV: production
    image: myapp/web:latest
    container_name: web-app
    restart: unless-stopped
    ports:
      - "3000:3000"
      - "3001:3001"  # Admin port
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://admin:${DB_PASSWORD}@db:5432/mydb
      - REDIS_URL=redis://redis:6379
      - SESSION_SECRET=${SESSION_SECRET}
      - API_KEY=${API_KEY}
    env_file:
      - .env.production
    volumes:
      - ./web/uploads:/app/uploads
      - ./web/logs:/app/logs
      - web-data:/app/data
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - frontend
      - backend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 1G
        reservations:
          cpus: '0.5'
          memory: 512M
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.web.rule=Host(`app.example.com`)"
      - "traefik.http.routers.web.entrypoints=websecure"
      - "traefik.http.routers.web.tls.certresolver=letsencrypt"

  # PostgreSQL Database
  db:
    image: postgres:15-alpine
    container_name: postgres-db
    restart: unless-stopped
    environment:
      - POSTGRES_DB=mydb
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=${DB_PASSWORD}
      - POSTGRES_INITDB_ARGS=--encoding=UTF8 --locale=en_US.UTF-8
      - PGDATA=/var/lib/postgresql/data/pgdata
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./db/init:/docker-entrypoint-initdb.d
      - ./db/backups:/backups
    ports:
      - "5432:5432"
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5
    deploy:
      resources:
        limits:
          cpus: '2.0'
          memory: 2G
        reservations:
          cpus: '1.0'
          memory: 1G
    command:
      - postgres
      - -c
      - max_connections=200
      - -c
      - shared_buffers=512MB
      - -c
      - effective_cache_size=1536MB

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: redis-cache
    restart: unless-stopped
    command:
      - redis-server
      - --appendonly
      - "yes"
      - --requirepass
      - "${REDIS_PASSWORD}"
      - --maxmemory
      - "512mb"
      - --maxmemory-policy
      - "allkeys-lru"
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - backend
    healthcheck:
      test: ["CMD", "redis-cli", "--raw", "incr", "ping"]
      interval: 10s
      timeout: 3s
      retries: 5

  # Nginx Reverse Proxy
  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
      - nginx-logs:/var/log/nginx
    depends_on:
      - web
    networks:
      - frontend
    labels:
      - "com.example.service=nginx"

  # Monitoring - Prometheus
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    volumes:
      - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus-data:/prometheus
    ports:
      - "9090:9090"
    networks:
      - monitoring
    depends_on:
      - web

  # Monitoring - Grafana
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_PASSWORD}
      - GF_USERS_ALLOW_SIGN_UP=false
    volumes:
      - grafana-data:/var/lib/grafana
      - ./monitoring/grafana/dashboards:/etc/grafana/provisioning/dashboards
      - ./monitoring/grafana/datasources:/etc/grafana/provisioning/datasources
    ports:
      - "3001:3000"
    networks:
      - monitoring
    depends_on:
      - prometheus

  # Background Worker
  worker:
    build:
      context: ./worker
      dockerfile: Dockerfile
    image: myapp/worker:latest
    container_name: worker-app
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://admin:${DB_PASSWORD}@db:5432/mydb
      - REDIS_URL=redis://redis:6379
      - QUEUE_NAME=jobs
    env_file:
      - .env.production
    volumes:
      - ./worker/logs:/app/logs
    depends_on:
      - db
      - redis
    networks:
      - backend
    deploy:
      replicas: 2
      resources:
        limits:
          cpus: '0.5'
          memory: 512M

networks:
  frontend:
    driver: bridge
    name: app-frontend
  backend:
    driver: bridge
    name: app-backend
  monitoring:
    driver: bridge
    name: app-monitoring

volumes:
  postgres-data:
    driver: local
    name: postgres-data
  redis-data:
    driver: local
    name: redis-data
  web-data:
    driver: local
    name: web-data
  prometheus-data:
    driver: local
    name: prometheus-data
  grafana-data:
    driver: local
    name: grafana-data
  nginx-logs:
    driver: local
    name: nginx-logs
```

### Example 3: GitHub Actions - Complete CI/CD Pipeline

Comprehensive GitHub Actions workflow with testing, building, security scanning, and deployment:

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
      - develop
      - 'release/**'
    tags:
      - 'v*.*.*'
  pull_request:
    branches:
      - main
      - develop
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        type: choice
        options:
          - staging
          - production
      version:
        description: 'Version to deploy'
        required: false
        type: string
  schedule:
    # Run tests every day at 2 AM UTC
    - cron: '0 2 * * *'

env:
  NODE_VERSION: '18.x'
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  # Lint and Format Check
  lint:
    name: Code Quality
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run ESLint
        run: npm run lint

      - name: Check code formatting
        run: npm run format:check

      - name: Run Prettier
        run: npx prettier --check "**/*.{js,ts,json,md}"

  # Unit Tests
  test:
    name: Unit Tests
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: ['16.x', '18.x', '20.x']
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit
        env:
          CI: true

      - name: Upload coverage reports
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          flags: unittests
          name: codecov-umbrella

  # Integration Tests
  integration-test:
    name: Integration Tests
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
      redis:
        image: redis:7-alpine
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 6379:6379
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/testdb
          REDIS_URL: redis://localhost:6379

  # Security Scanning
  security:
    name: Security Scan
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run npm audit
        run: npm audit --audit-level=moderate
        continue-on-error: true

      - name: Run Snyk security scan
        uses: snyk/actions/node@master
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'

  # Build Docker Image
  build:
    name: Build Docker Image
    runs-on: ubuntu-latest
    needs: [lint, test]
    outputs:
      image-tag: ${{ steps.meta.outputs.tags }}
      image-digest: ${{ steps.build.outputs.digest }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=ref,event=pr
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=sha,prefix={{branch}}-
            type=raw,value=latest,enable={{is_default_branch}}

      - name: Build and push Docker image
        id: build
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=registry,ref=${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:buildcache
          cache-to: type=registry,ref=${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:buildcache,mode=max

  # Deploy to Staging
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: [build, integration-test]
    if: github.ref == 'refs/heads/develop' || github.event_name == 'workflow_dispatch'
    environment:
      name: staging
      url: https://staging.example.com
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Deploy to ECS
        run: |
          aws ecs update-service \
            --cluster staging-cluster \
            --service web-app \
            --force-new-deployment \
            --region us-east-1

      - name: Run smoke tests
        run: |
          npm run test:smoke -- --env=staging

      - name: Notify Slack
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Deployed to staging'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
        if: always()

  # Deploy to Production
  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: [build, security]
    if: |
      github.ref == 'refs/heads/main' ||
      startsWith(github.ref, 'refs/tags/v') ||
      (github.event_name == 'workflow_dispatch' && github.event.inputs.environment == 'production')
    environment:
      name: production
      url: https://example.com
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Deploy to ECS (Blue-Green)
        run: |
          aws ecs update-service \
            --cluster production-cluster \
            --service web-app \
            --force-new-deployment \
            --region us-east-1

      - name: Wait for deployment
        run: |
          aws ecs wait services-stable \
            --cluster production-cluster \
            --services web-app \
            --region us-east-1

      - name: Run production smoke tests
        run: |
          npm run test:smoke -- --env=production

      - name: Create GitHub Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          body: |
            Production deployment completed successfully
            - Image: ${{ needs.build.outputs.image-tag }}
            - Commit: ${{ github.sha }}
          draft: false
          prerelease: false

      - name: Notify Teams
        uses: aliencube/microsoft-teams-actions@v0.8.0
        with:
          webhook_uri: ${{ secrets.TEAMS_WEBHOOK }}
          title: 'Production Deployment'
          summary: 'Deployment successful'
          theme-color: '#00FF00'

  # Cleanup
  cleanup:
    name: Cleanup
    runs-on: ubuntu-latest
    needs: [deploy-production]
    if: always()
    steps:
      - name: Delete old container images
        uses: actions/delete-package-versions@v4
        with:
          package-name: ${{ env.IMAGE_NAME }}
          package-type: 'container'
          min-versions-to-keep: 10
          delete-only-untagged-versions: true
```

### Example 4: Azure Pipelines - Multi-Stage CI/CD

Complete Azure DevOps pipeline with build, test, and deployment stages:

```yaml
# azure-pipelines.yml
trigger:
  branches:
    include:
      - main
      - develop
      - releases/*
  paths:
    exclude:
      - README.md
      - docs/*

pr:
  branches:
    include:
      - main
      - develop

variables:
  - group: 'Production-Secrets'
  - name: buildConfiguration
    value: 'Release'
  - name: dockerRegistryServiceConnection
    value: 'DockerHub-Connection'
  - name: imageRepository
    value: 'myapp/web'
  - name: containerRegistry
    value: 'docker.io'
  - name: dockerfilePath
    value: '$(Build.SourcesDirectory)/Dockerfile'
  - name: tag
    value: '$(Build.BuildId)'
  - name: vmImageName
    value: 'ubuntu-latest'

stages:
  # Stage 1: Build and Test
  - stage: Build
    displayName: 'Build and Test Stage'
    jobs:
      - job: Build
        displayName: 'Build Application'
        pool:
          vmImage: $(vmImageName)
        steps:
          - task: NodeTool@0
            displayName: 'Use Node.js 18.x'
            inputs:
              versionSpec: '18.x'

          - task: Npm@1
            displayName: 'npm install'
            inputs:
              command: 'install'
              workingDir: '$(Build.SourcesDirectory)'

          - task: Npm@1
            displayName: 'npm run build'
            inputs:
              command: 'custom'
              workingDir: '$(Build.SourcesDirectory)'
              customCommand: 'run build'

          - task: PublishBuildArtifacts@1
            displayName: 'Publish build artifacts'
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)'
              artifactName: 'drop'

      - job: UnitTests
        displayName: 'Run Unit Tests'
        pool:
          vmImage: $(vmImageName)
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '18.x'

          - task: Npm@1
            displayName: 'Install dependencies'
            inputs:
              command: 'install'

          - task: Npm@1
            displayName: 'Run unit tests'
            inputs:
              command: 'custom'
              customCommand: 'run test:unit'

          - task: PublishTestResults@2
            displayName: 'Publish test results'
            inputs:
              testResultsFormat: 'JUnit'
              testResultsFiles: '**/test-results.xml'
              failTaskOnFailedTests: true

          - task: PublishCodeCoverageResults@1
            displayName: 'Publish code coverage'
            inputs:
              codeCoverageTool: 'Cobertura'
              summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'

      - job: IntegrationTests
        displayName: 'Run Integration Tests'
        pool:
          vmImage: $(vmImageName)
        services:
          postgres:
            image: postgres:15
            env:
              POSTGRES_USER: test
              POSTGRES_PASSWORD: test
              POSTGRES_DB: testdb
            ports:
              - 5432:5432
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '18.x'

          - task: Npm@1
            displayName: 'Install dependencies'
            inputs:
              command: 'install'

          - task: Npm@1
            displayName: 'Run integration tests'
            inputs:
              command: 'custom'
              customCommand: 'run test:integration'
            env:
              DATABASE_URL: 'postgresql://test:test@localhost:5432/testdb'

  # Stage 2: Security and Quality
  - stage: Security
    displayName: 'Security and Quality Checks'
    dependsOn: Build
    jobs:
      - job: SecurityScan
        displayName: 'Security Scanning'
        pool:
          vmImage: $(vmImageName)
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '18.x'

          - task: Npm@1
            displayName: 'npm audit'
            inputs:
              command: 'custom'
              customCommand: 'audit --audit-level=moderate'
            continueOnError: true

          - task: SnykSecurityScan@1
            displayName: 'Snyk Security Scan'
            inputs:
              serviceConnectionEndpoint: 'Snyk-Connection'
              testType: 'app'
              severityThreshold: 'high'
            continueOnError: true

          - task: SonarCloudPrepare@1
            displayName: 'Prepare SonarCloud analysis'
            inputs:
              SonarCloud: 'SonarCloud-Connection'
              organization: 'my-org'
              scannerMode: 'CLI'
              configMode: 'manual'
              cliProjectKey: 'myapp'
              cliProjectName: 'My Application'

          - task: SonarCloudAnalyze@1
            displayName: 'Run SonarCloud analysis'

          - task: SonarCloudPublish@1
            displayName: 'Publish SonarCloud results'

      - job: CodeQuality
        displayName: 'Code Quality Checks'
        pool:
          vmImage: $(vmImageName)
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '18.x'

          - task: Npm@1
            displayName: 'Run ESLint'
            inputs:
              command: 'custom'
              customCommand: 'run lint'

          - task: Npm@1
            displayName: 'Check code formatting'
            inputs:
              command: 'custom'
              customCommand: 'run format:check'

  # Stage 3: Build Docker Image
  - stage: BuildDocker
    displayName: 'Build and Push Docker Image'
    dependsOn: 
      - Build
      - Security
    condition: succeeded()
    jobs:
      - job: DockerBuild
        displayName: 'Build Docker Image'
        pool:
          vmImage: $(vmImageName)
        steps:
          - task: Docker@2
            displayName: 'Build and push Docker image'
            inputs:
              containerRegistry: $(dockerRegistryServiceConnection)
              repository: $(imageRepository)
              command: 'buildAndPush'
              Dockerfile: $(dockerfilePath)
              tags: |
                $(tag)
                latest
                $(Build.SourceBranchName)

          - task: Docker@2
            displayName: 'Scan Docker image for vulnerabilities'
            inputs:
              containerRegistry: $(dockerRegistryServiceConnection)
              repository: $(imageRepository)
              command: 'scan'
              tags: $(tag)

  # Stage 4: Deploy to Staging
  - stage: DeployStaging
    displayName: 'Deploy to Staging Environment'
    dependsOn: BuildDocker
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/develop'))
    jobs:
      - deployment: DeployStaging
        displayName: 'Deploy to Staging'
        environment:
          name: 'Staging'
          resourceType: 'Kubernetes'
          resourceName: 'staging-cluster'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: Kubernetes@1
                  displayName: 'Deploy to Kubernetes'
                  inputs:
                    connectionType: 'Kubernetes Service Connection'
                    kubernetesServiceEndpoint: 'Staging-K8s-Connection'
                    namespace: 'staging'
                    command: 'apply'
                    arguments: '-f k8s/staging-deployment.yaml'
                    secretType: 'dockerRegistry'
                    containerRegistryType: 'Azure Container Registry'

                - task: Kubernetes@1
                  displayName: 'Wait for deployment'
                  inputs:
                    connectionType: 'Kubernetes Service Connection'
                    kubernetesServiceEndpoint: 'Staging-K8s-Connection'
                    namespace: 'staging'
                    command: 'rollout'
                    arguments: 'status deployment/web-app'

                - task: PowerShell@2
                  displayName: 'Run smoke tests'
                  inputs:
                    targetType: 'inline'
                    script: |
                      npm run test:smoke -- --env=staging

                - task: Slack@1
                  displayName: 'Notify Slack'
                  inputs:
                    channel: '#deployments'
                    message: 'Deployed to staging: $(Build.BuildNumber)'
                  condition: always()

  # Stage 5: Deploy to Production
  - stage: DeployProduction
    displayName: 'Deploy to Production Environment'
    dependsOn: BuildDocker
    condition: and(succeeded(), or(eq(variables['Build.SourceBranch'], 'refs/heads/main'), startsWith(variables['Build.SourceBranch'], 'refs/heads/releases/')))
    jobs:
      - deployment: DeployProduction
        displayName: 'Deploy to Production'
        environment:
          name: 'Production'
          resourceType: 'Kubernetes'
          resourceName: 'production-cluster'
        strategy:
          canary:
            increments: [10, 20, 30, 100]
            deploy:
              steps:
                - task: Kubernetes@1
                  displayName: 'Deploy canary'
                  inputs:
                    connectionType: 'Kubernetes Service Connection'
                    kubernetesServiceEndpoint: 'Production-K8s-Connection'
                    namespace: 'production'
                    command: 'apply'
                    arguments: '-f k8s/production-canary.yaml'
                    secretType: 'dockerRegistry'

                - task: PowerShell@2
                  displayName: 'Run canary tests'
                  inputs:
                    targetType: 'inline'
                    script: |
                      npm run test:canary -- --env=production

                - task: ManualValidation@0
                  displayName: 'Manual approval for production'
                  inputs:
                    notifyUsers: 'devops-team@example.com'
                    instructions: 'Please review the canary deployment and approve to continue'
                    onTimeout: 'reject'

                - task: Kubernetes@1
                  displayName: 'Promote canary to production'
                  inputs:
                    connectionType: 'Kubernetes Service Connection'
                    kubernetesServiceEndpoint: 'Production-K8s-Connection'
                    namespace: 'production'
                    command: 'apply'
                    arguments: '-f k8s/production-full.yaml'

                - task: Kubernetes@1
                  displayName: 'Wait for production deployment'
                  inputs:
                    connectionType: 'Kubernetes Service Connection'
                    kubernetesServiceEndpoint: 'Production-K8s-Connection'
                    namespace: 'production'
                    command: 'rollout'
                    arguments: 'status deployment/web-app'

                - task: PowerShell@2
                  displayName: 'Run production smoke tests'
                  inputs:
                    targetType: 'inline'
                    script: |
                      npm run test:smoke -- --env=production

                - task: EmailReport@1
                  displayName: 'Send deployment email'
                  inputs:
                    to: 'team@example.com'
                    subject: 'Production Deployment: $(Build.BuildNumber)'
                    body: 'Deployment completed successfully'

  # Stage 6: Post-Deployment
  - stage: PostDeployment
    displayName: 'Post-Deployment Tasks'
    dependsOn: 
      - DeployStaging
      - DeployProduction
    condition: succeeded()
    jobs:
      - job: Cleanup
        displayName: 'Cleanup and Notifications'
        pool:
          vmImage: $(vmImageName)
        steps:
          - task: PowerShell@2
            displayName: 'Cleanup old artifacts'
            inputs:
              targetType: 'inline'
              script: |
                # Cleanup logic here
                Write-Host "Cleaning up old artifacts"

          - task: PublishTestResults@2
            displayName: 'Publish final test results'
            condition: always()
```

## Examples

### Example 1: Application Configuration

```yaml
# config.yaml
application:
  name: "My Awesome App"
  version: "2.1.0"
  environment: production

server:
  host: "0.0.0.0"
  port: 8080
  ssl:
    enabled: true
    certificate: "/path/to/cert.pem"
    key: "/path/to/key.pem"

database:
  primary:
    host: "db-primary.example.com"
    port: 5432
    name: "mydb"
    user: "admin"
    pool:
      min: 5
      max: 20
      timeout: 30
  replica:
    host: "db-replica.example.com"
    port: 5432
    name: "mydb"
    read_only: true

features:
  enabled:
    - authentication
    - caching
    - analytics
  disabled:
    - beta_features
    - experimental_api

logging:
  level: INFO
  format: json
  outputs:
    - type: file
      path: "/var/log/app.log"
      rotation: daily
    - type: console
      colored: true
```

### Example 2: API Specification

```yaml
# api.yaml
openapi: 3.0.0
info:
  title: User Management API
  version: 1.0.0
  description: API for managing users

servers:
  - url: https://api.example.com/v1
    description: Production server
  - url: https://staging-api.example.com/v1
    description: Staging server

paths:
  /users:
    get:
      summary: List users
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            default: 1
        - name: limit
          in: query
          schema:
            type: integer
            default: 10
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
                  total:
                    type: integer

    post:
      summary: Create user
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserInput'
      responses:
        '201':
          description: User created

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
          format: email
        created_at:
          type: string
          format: date-time

    UserInput:
      type: object
      required:
        - name
        - email
      properties:
        name:
          type: string
        email:
          type: string
          format: email
```

### Example 3: Environment-Specific Configs

```yaml
# Using anchors for common config
common: &common
  app_name: "MyApp"
  api_version: "v1"
  timeout: 30
  retries: 3

development:
  <<: *common
  environment: development
  debug: true
  database:
    host: localhost
    port: 5432
    name: myapp_dev
  logging:
    level: DEBUG
    console: true

staging:
  <<: *common
  environment: staging
  debug: false
  database:
    host: staging-db.example.com
    port: 5432
    name: myapp_staging
  logging:
    level: INFO
    console: false

production:
  <<: *common
  environment: production
  debug: false
  database:
    host: prod-db.example.com
    port: 5432
    name: myapp_prod
  logging:
    level: WARNING
    console: false
```

---

## Tools and Resources

### Online Validators

- **YAML Lint**: https://www.yamllint.com/
- **Online YAML Parser**: https://yaml-online-parser.appspot.com/
- **YAML Validator**: https://codebeautify.org/yaml-validator

### Command-Line Tools

```bash
# yamllint (Python)
pip install yamllint
yamllint file.yaml

# yq (YAML processor, like jq for JSON)
# macOS: brew install yq
# Linux: wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64
yq eval '.key' file.yaml

# yaml2json / json2yaml
npm install -g yamljs
yaml2json file.yaml
```

### IDE Support

- **VS Code**: YAML extension by Red Hat
- **IntelliJ IDEA**: Built-in YAML support
- **Sublime Text**: YAML syntax highlighting
- **Vim**: vim-yaml plugin

### Libraries by Language

- **Python**: `PyYAML`, `ruamel.yaml`
- **JavaScript/Node.js**: `js-yaml`, `yaml`
- **Java**: `SnakeYAML`, `Jackson YAML`
- **Ruby**: `psych` (built-in)
- **Go**: `gopkg.in/yaml.v3`
- **C#**: `YamlDotNet`

### Learning Resources

- **Official YAML Spec**: https://yaml.org/spec/
- **YAML Tutorial**: https://learnxinyminutes.com/docs/yaml/
- **YAML Wikipedia**: https://en.wikipedia.org/wiki/YAML

---

## Common Pitfalls and How to Avoid Them

### 1. Tabs vs Spaces

```yaml
# ❌ Wrong: Using tabs
person:
	name: John  # Tab character
	age: 30

# ✅ Correct: Using spaces
person:
  name: John  # 2 spaces
  age: 30
```

### 2. Inconsistent Indentation

```yaml
# ❌ Wrong: Mixed indentation
server:
  name: production
    port: 8080  # Wrong: 4 spaces after 2 spaces

# ✅ Correct: Consistent indentation
server:
  name: production
  port: 8080
```

### 3. Forgetting Quotes for Special Values

```yaml
# ❌ Wrong: Version interpreted as number
version: 1.0.0  # Becomes 1.0 (float)

# ✅ Correct: Quoted string
version: "1.0.0"

# ❌ Wrong: Comment starts early
description: Version #1  # Only "Version " is the value

# ✅ Correct: Quoted string
description: "Version #1"
```

### 4. Boolean Confusion

```yaml
# These are all strings, not booleans:
enabled: "true"   # String
enabled: "yes"    # String
enabled: "on"     # String

# These are booleans:
enabled: true     # Boolean
enabled: True     # Boolean (case-insensitive)
enabled: yes       # Boolean (YAML 1.1)
```

### 5. List vs Dictionary Confusion

```yaml
# List (array)
items:
  - apple
  - banana

# Dictionary (object)
items:
  apple: fruit
  banana: fruit

# Mixed (list of dictionaries)
items:
  - name: apple
    type: fruit
  - name: banana
    type: fruit
```

---

## YAML vs JSON vs XML

### Comparison

| Feature | YAML | JSON | XML |
|---------|------|------|-----|
| Human-readable | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| Verbosity | Low | Medium | High |
| Comments | ✅ Yes | ❌ No | ✅ Yes |
| Data types | Rich | Basic | Text-based |
| Learning curve | Easy | Easy | Medium |
| Use case | Configs, Docs | APIs, Data | Documents, Configs |

### When to Use YAML

✅ **Use YAML for:**
- Configuration files
- CI/CD pipelines
- Infrastructure as Code (Ansible, Kubernetes)
- Documentation
- Data that humans need to read/edit

❌ **Don't use YAML for:**
- High-performance APIs (use JSON)
- Very large files (use JSON or binary)
- When you need strict schema validation (consider JSON Schema)

---

## Conclusion

YAML is a powerful, human-readable data serialization format that's essential for modern development workflows. Whether you're writing configuration files, defining CI/CD pipelines, or managing infrastructure, YAML provides a clean and intuitive way to represent structured data.

### Key Takeaways

1. **Indentation matters** - Use spaces, not tabs
2. **Quotes are optional** - But use them for special characters
3. **Anchors reduce duplication** - Use `&` and `*` for reusable data
4. **Comments are your friend** - Document complex configurations
5. **Validate your YAML** - Always check syntax before deploying

### Next Steps

- Practice writing YAML files for your projects
- Explore YAML in popular tools (Docker Compose, Kubernetes, Ansible)
- Learn about YAML schema validation
- Experiment with advanced features like anchors and custom tags

---

**Remember**: The best way to learn YAML is by using it. Start with simple configuration files and gradually work your way up to more complex structures!

