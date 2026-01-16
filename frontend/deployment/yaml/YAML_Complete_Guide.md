# YAML for DevOps and CI/CD Pipelines

Complete guide covering YAML concepts essential for GitHub Actions, Kubernetes, Docker, Azure Pipelines, and CI/CD deployments.

## Table of Contents
1. [YAML Fundamentals](#yaml-fundamentals)
2. [Core YAML Concepts](#core-yaml-concepts)
3. [GitHub Actions YAML](#github-actions-yaml)
4. [Kubernetes YAML](#kubernetes-yaml)
5. [Docker Compose YAML](#docker-compose-yaml)
6. [Azure Pipelines YAML](#azure-pipelines-yaml)
7. [Deployment Shell Commands](#deployment-shell-commands)
8. [Real-World Examples](#real-world-examples)
9. [Best Practices](#best-practices)

---

## YAML Fundamentals

### What is YAML?

**YAML** stands for **"YAML Ain't Markup Language"**. It's a human-readable data serialization format that's perfect for:
- Configuration files
- CI/CD pipeline definitions
- Infrastructure as Code (IaC)
- Container orchestration
- Deployment automation

### Why YAML for DevOps?

✅ **Easy to read and write** - Minimal syntax, whitespace matters (like Python)
✅ **Language-agnostic** - Works with any technology stack
✅ **Version control friendly** - Plain text format, easy to diff
✅ **Widely adopted** - Standard for Docker, Kubernetes, GitHub, Azure, and more

### Basic Syntax Rules

```yaml
# RULE 1: Key-value pairs use colon and space syntax
key: value

# RULE 2: Indentation uses spaces (NOT tabs), typically 2 spaces
parent:
  child: value
  another_child: value

# RULE 3: Comments start with # and extend to end of line
# This is a comment
value: 42  # This is also a comment

# RULE 4: Lists use hyphen-space prefix
items:
  - item1
  - item2
  - item3

# RULE 5: Strings don't need quotes unless they contain special characters
name: John Doe              # OK - no quotes needed
message: "Hello: World"     # Quotes needed because of colon
```

---

## Core YAML Concepts

### 1. Data Types

```yaml
# STRING - Text values
username: john_doe
email: "john@example.com"  # Quoted if contains special chars

# NUMBER - Integer or float
port: 8080                 # Integer
timeout: 30.5              # Float
replicas: 3

# BOOLEAN - True/false values
enabled: true              # Boolean true
debug: false               # Boolean false

# NULL - Empty/null values
value: null                # Null value
empty: ~                   # Alternative null syntax

# DATE - ISO 8601 format (rarely used in CI/CD)
created: 2024-01-15T10:30:00Z
```

### 2. Lists and Arrays

```yaml
# List format - each item on new line with hyphen
languages:
  - python
  - javascript
  - go

# Inline list format - compact notation
tools: [docker, kubernetes, jenkins]

# List of objects
services:
  - name: api
    port: 8080
  - name: db
    port: 5432
```

### 3. Dictionaries and Nested Objects

```yaml
# Simple dictionary
person:
  name: John
  age: 30
  email: john@example.com

# Nested dictionaries
deployment:
  production:
    environment: prod
    replicas: 3
    resources:
      memory: 2Gi
      cpu: "1"
  staging:
    environment: staging
    replicas: 1
    resources:
      memory: 512Mi
      cpu: "0.5"
```

### 4. Anchors and Aliases (Reusable Blocks)

Purpose: Avoid repeating the same configuration in multiple places.

```yaml
# Define a reusable block with &anchor_name
defaults: &default_settings
  timeout: 30
  retries: 3
  debug: false

# Use the anchor in multiple places with *anchor_name
development_env:
  <<: *default_settings          # Merge all keys from defaults
  database: dev_db

production_env:
  <<: *default_settings          # Reuse same defaults
  database: prod_db
  timeout: 60                    # Override timeout value
```

### 5. Multi-line Strings

```yaml
# Literal block (preserves newlines) - use |
description: |
  This is a multi-line
  string that preserves
  all line breaks exactly.

# Folded block (converts newlines to spaces) - use >
summary: >
  This is a multi-line string
  that will fold all
  newlines into spaces.

# Result: "This is a multi-line string that will fold all newlines into spaces."
```

---

## GitHub Actions YAML

**Location:** `.github/workflows/*.yml` files in your repository

### Basic Workflow Structure

```yaml
# Workflow name - appears in GitHub Actions tab
name: CI/CD Pipeline

# Triggers - when this workflow runs
on:
  push:
    # Runs when code is pushed to these branches
    branches: [ main, develop ]
    # Runs only when these paths change
    paths:
      - 'src/**'
      - 'package.json'
  
  pull_request:
    # Runs when PR is opened, updated, or reopened
    branches: [ main ]
  
  # Manual trigger from GitHub UI
  workflow_dispatch:
  
  # Run on schedule (cron format)
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM UTC

# Environment variables available to all jobs
env:
  NODE_VERSION: '18.x'
  REGISTRY: ghcr.io

# Jobs - units of work that run on runners
jobs:
  build:
    # Which machine to run on
    runs-on: ubuntu-latest
    
    # Steps - individual tasks
    steps:
      # ACTION: Check out your code
      - uses: actions/checkout@v4
      
      # ACTION: Setup Node.js environment
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'  # Cache node_modules for speed
      
      # RUN: Execute shell command
      - name: Install dependencies
        run: npm install
      
      # RUN: Build the project
      - name: Build application
        run: npm run build
      
      # RUN: Run tests
      - name: Run tests
        run: npm test
```

### Key GitHub Actions Concepts

#### 1. Workflow Triggers (on:)
```yaml
on:
  # Trigger on push to specific branches
  push:
    branches: [main, develop]
  
  # Trigger on pull requests
  pull_request:
    branches: [main]
  
  # Trigger on manual dispatch
  workflow_dispatch:
  
  # Trigger on schedule
  schedule:
    - cron: '0 2 * * *'
  
  # Trigger on release
  release:
    types: [published]
```

**Purpose:** Defines WHEN the workflow should execute

#### 2. Jobs (jobs:)
```yaml
jobs:
  # Job 1 - runs in parallel by default
  test:
    runs-on: ubuntu-latest
    steps: [...]
  
  # Job 2 - also runs in parallel
  build:
    runs-on: ubuntu-latest
    steps: [...]
  
  # Job 3 - depends on job 2, waits for it to complete
  deploy:
    needs: build  # Wait for 'build' job to finish
    runs-on: ubuntu-latest
    steps: [...]
```

**Purpose:** Define parallel or dependent work units

#### 3. Steps (steps:)
```yaml
steps:
  # Step 1: Use a pre-built action
  - uses: actions/checkout@v4
  
  # Step 2: Run a shell command
  - name: Install dependencies
    run: npm install
  
  # Step 3: Run with environment variables
  - name: Deploy
    run: ./deploy.sh
    env:
      DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
```

**Purpose:** Individual tasks within a job

#### 4. Conditional Execution (if:)
```yaml
steps:
  # Only run on main branch
  - name: Deploy to production
    if: github.ref == 'refs/heads/main'
    run: npm run deploy:prod
  
  # Only run on pull requests
  - name: Comment on PR
    if: github.event_name == 'pull_request'
    run: echo "Running on PR"
  
  # Only run if previous steps succeeded
  - name: Notify success
    if: success()
    run: echo "All checks passed"
  
  # Only run if previous steps failed
  - name: Notify failure
    if: failure()
    run: echo "Something failed"
```

**Purpose:** Control when steps run

#### 5. Secrets and Variables
```yaml
steps:
  # Access GitHub secret (stored securely)
  - name: Deploy with credentials
    run: ./deploy.sh
    env:
      # Repository secret
      API_KEY: ${{ secrets.API_KEY }}
      # GitHub variable (non-sensitive)
      ENVIRONMENT: ${{ vars.DEPLOYMENT_ENV }}
      # Context variable
      BRANCH: ${{ github.ref_name }}
```

**Purpose:** Securely pass sensitive data and configuration

#### 6. Matrix Strategy (for parallel testing across versions)
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        # Test across multiple Node versions
        node-version: ['16.x', '18.x', '20.x']
        # Test across multiple OS
        os: [ubuntu-latest, windows-latest, macos-latest]
    
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - run: npm test
```

**Purpose:** Test against multiple configurations simultaneously

#### 7. Artifacts and Caching
```yaml
steps:
  # Cache dependencies (for speed)
  - uses: actions/setup-node@v4
    with:
      cache: 'npm'  # Auto-cache node_modules
  
  # Save build output as artifact
  - name: Build
    run: npm run build
  
  - uses: actions/upload-artifact@v3
    with:
      name: build-output
      path: dist/
  
  # Download artifact in another job
  - uses: actions/download-artifact@v3
    with:
      name: build-output
      path: dist/
```

**Purpose:** Share files between jobs and speed up workflows

---

## Kubernetes YAML

**Location:** `.yaml` or `.yml` files applied with `kubectl apply -f`

### Basic Kubernetes Manifest Structure

```yaml
# API version - which Kubernetes API to use
apiVersion: apps/v1

# Kind - type of Kubernetes resource
kind: Deployment

# Metadata - information about this resource
metadata:
  name: my-app              # Name of the deployment
  namespace: production     # Namespace (logical cluster)
  labels:
    app: my-app            # Labels for organizing resources
    environment: prod
    version: v1.0.0

# Spec - desired state of the resource
spec:
  # Number of pod replicas
  replicas: 3
  
  # How to select pods managed by this deployment
  selector:
    matchLabels:
      app: my-app          # Must match labels in template
  
  # Template for pod creation
  template:
    metadata:
      labels:
        app: my-app        # Pods get these labels
    
    spec:
      # Container definitions
      containers:
      - name: app-container           # Container name
        image: myapp:1.0.0            # Docker image
        imagePullPolicy: IfNotPresent  # Only pull if not present
        
        ports:
        - containerPort: 8080         # Port to expose
          name: http
        
        # Environment variables in the container
        env:
        - name: DATABASE_URL
          value: "postgresql://db:5432/mydb"
        
        # Read from ConfigMap
        - name: APP_CONFIG
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: settings
        
        # Read from Secret (encrypted)
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        
        # Resource limits (prevent resource exhaustion)
        resources:
          requests:                    # Minimum required
            memory: "256Mi"
            cpu: "250m"
          limits:                      # Maximum allowed
            memory: "512Mi"
            cpu: "500m"
        
        # Health check - is the app running?
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        
        # Ready check - can it handle traffic?
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 3
```

### Core Kubernetes Resources

#### 1. Deployment (for stateless apps)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3                    # Run 3 instances
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: app
        image: myapp:1.0.0
        ports:
        - containerPort: 8080
```

**Purpose:** Deploy and manage stateless applications with rolling updates

#### 2. Service (expose apps to network)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  # ClusterIP (internal) | NodePort (external) | LoadBalancer (cloud)
  type: LoadBalancer
  
  # Which pods to expose
  selector:
    app: web-app
  
  # Port mapping
  ports:
  - port: 80                    # External port
    targetPort: 8080            # Container port
    protocol: TCP
```

**Purpose:** Network abstraction - expose pods to internal or external traffic

#### 3. ConfigMap (non-secret configuration)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: production
data:
  # Key-value pairs for configuration
  DATABASE_HOST: db.example.com
  DATABASE_PORT: "5432"
  LOG_LEVEL: INFO
  
  # Multi-line config files
  nginx.conf: |
    server {
      listen 80;
      location / {
        proxy_pass http://app:8080;
      }
    }
```

**Purpose:** Store non-sensitive configuration data

#### 4. Secret (sensitive data - encrypted)
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
  namespace: production
type: Opaque
stringData:
  # Will be base64 encoded in etcd
  password: "super-secret-password"
  api-key: "sk_live_123abc"
  connection-string: "postgresql://admin:pass@db:5432/db"
```

**Purpose:** Store sensitive data like passwords, API keys, tokens

#### 5. HorizontalPodAutoscaler (auto-scaling)
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-app
  
  minReplicas: 2              # Minimum pods
  maxReplicas: 10             # Maximum pods
  
  metrics:
  # Scale based on CPU usage
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70  # Scale when CPU > 70%
  
  # Scale based on memory usage
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80  # Scale when memory > 80%
```

**Purpose:** Automatically add/remove pods based on load

#### 6. Ingress (HTTP/HTTPS routing)
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
  ingressClassName: nginx
  
  # SSL/TLS certificate
  tls:
  - hosts:
    - api.example.com
    secretName: tls-secret
  
  # URL routing rules
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
  
  - host: admin.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: admin-app-service
            port:
              number: 8080
```

**Purpose:** HTTP/HTTPS routing and load balancing

---

## Docker Compose YAML

**Location:** `docker-compose.yml` file

### Basic Docker Compose Structure

```yaml
# Compose file version (3.8 is common)
version: '3.8'

# Services - containers to run
services:
  # Service 1: Web application
  web:
    # Build from Dockerfile
    build:
      context: ./web           # Build context directory
      dockerfile: Dockerfile   # Dockerfile name
    
    # OR use pre-built image
    image: nginx:latest
    
    # Container name
    container_name: web-app
    
    # Restart policy
    restart: unless-stopped    # Always restart unless stopped manually
    
    # Port mapping
    ports:
      - "80:8080"             # Host:Container
      - "443:8443"
    
    # Environment variables
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgres://db:5432/mydb
    
    # OR from .env file
    env_file:
      - .env.production
    
    # Volume mounting (persistent storage)
    volumes:
      - ./app:/app            # Bind mount
      - app-data:/data        # Named volume
    
    # Service dependencies
    depends_on:
      db:
        condition: service_healthy
    
    # Health check
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    
    # Network
    networks:
      - frontend
      - backend
  
  # Service 2: Database
  db:
    image: postgres:15-alpine
    container_name: postgres-db
    
    # Environment variables for postgres
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    
    # Persistent volume
    volumes:
      - postgres-data:/var/lib/postgresql/data
    
    # Health check (for depends_on)
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
      timeout: 5s
      retries: 5

# Named volumes
volumes:
  postgres-data:
  app-data:

# Networks
networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
```

### Key Docker Compose Fields

#### 1. Image vs Build
```yaml
# Use pre-built image
image: nginx:latest

# OR build from Dockerfile
build:
  context: .              # Directory containing Dockerfile
  dockerfile: Dockerfile  # Dockerfile name
  args:                   # Build arguments
    NODE_ENV: production
```

**Purpose:** Specify how to run containers

#### 2. Ports
```yaml
ports:
  - "80:8080"        # Host port 80 → Container port 8080
  - "443:8443"
  - "127.0.0.1:3000:3000"  # Only bind to localhost
```

**Purpose:** Expose container ports to host

#### 3. Volumes
```yaml
volumes:
  # Bind mount (host directory)
  - ./app:/app
  
  # Named volume (managed by Docker)
  - db-data:/var/lib/postgresql/data
  
  # Read-only
  - ./config:/config:ro
```

**Purpose:** Persistent storage and file sharing

#### 4. Environment Variables
```yaml
environment:
  - DATABASE_URL=postgres://db:5432/db
  - DEBUG=true

# OR from file
env_file:
  - .env
  - .env.production
```

**Purpose:** Configure application behavior

#### 5. Depends On
```yaml
depends_on:
  # Simple dependency - just wait for container start
  - db
  
  # OR with health check condition
  db:
    condition: service_healthy
```

**Purpose:** Control startup order

#### 6. Healthcheck
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
  interval: 30s          # Check every 30 seconds
  timeout: 10s           # Timeout after 10 seconds
  retries: 3             # Fail after 3 retries
  start_period: 40s      # Grace period on startup
```

**Purpose:** Monitor container health

---

## Azure Pipelines YAML

**Location:** `azure-pipelines.yml` file in repository root

### Basic Azure Pipelines Structure

```yaml
# Trigger rules
trigger:
  branches:
    include:
      - main
      - develop
    exclude:
      - releases/*
  paths:
    include:
      - src/
      - tests/
    exclude:
      - docs/

# Pull request trigger
pr:
  branches:
    include:
      - main

# Variables (configuration)
variables:
  buildConfiguration: 'Release'
  nodeVersion: '18.x'
  imageName: 'myapp'

# Stages - logical groups of jobs
stages:
  # Stage 1: Build and Test
  - stage: Build
    displayName: 'Build Stage'
    jobs:
      # Job 1: Build application
      - job: BuildJob
        displayName: 'Build Application'
        pool:
          vmImage: 'ubuntu-latest'  # Which agent/VM to use
        
        steps:
          # Setup Node.js
          - task: NodeTool@0
            displayName: 'Use Node.js $(nodeVersion)'
            inputs:
              versionSpec: '$(nodeVersion)'
          
          # Install dependencies
          - task: Npm@1
            displayName: 'npm install'
            inputs:
              command: 'install'
          
          # Run build
          - task: Npm@1
            displayName: 'npm run build'
            inputs:
              command: 'custom'
              customCommand: 'run build'
          
          # Publish build artifacts
          - task: PublishBuildArtifacts@1
            displayName: 'Publish Artifacts'
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)'
              artifactName: 'drop'
  
  # Stage 2: Deploy
  - stage: Deploy
    displayName: 'Deploy Stage'
    dependsOn: Build           # Wait for Build stage
    condition: succeeded()     # Only if Build succeeded
    
    jobs:
      - deployment: DeployApp
        displayName: 'Deploy to Production'
        environment:
          name: 'Production'
          resourceType: 'Kubernetes'
        
        strategy:
          # Rolling deployment strategy
          rolling:
            maxParallel: 2         # Update 2 pods at a time
          # OR canary deployment
          canary:
            increments: [10, 20, 50, 100]  # % rollout increments
        
        steps:
          # Download artifact from Build stage
          - download: current
            artifact: drop
          
          # Deploy using kubectl
          - task: Kubernetes@1
            displayName: 'Deploy to Kubernetes'
            inputs:
              connectionType: 'Kubernetes Service Connection'
              kubernetesServiceEndpoint: 'k8s-prod'
              namespace: 'production'
              command: 'apply'
              arguments: '-f deployment.yaml'
```

### Key Azure Pipelines Concepts

#### 1. Stages (sequential groups of jobs)
```yaml
stages:
  - stage: Build
    displayName: 'Build'
    jobs: [...]
  
  - stage: Test
    displayName: 'Test'
    dependsOn: Build      # Wait for Build to complete
    condition: succeeded()
    jobs: [...]
  
  - stage: Deploy
    displayName: 'Deploy'
    dependsOn: Test       # Wait for Test to complete
    jobs: [...]
```

**Purpose:** Group jobs logically and control dependencies

#### 2. Variables (configuration)
```yaml
variables:
  # Scalar variables
  buildConfiguration: 'Release'
  nodeVersion: '18.x'
  
  # Variable group (stored in library)
  - group: 'Production-Secrets'
  
  # Reference in scripts
  - name: imageName
    value: 'myapp'

steps:
  - script: echo $(nodeVersion)  # Access variable
```

**Purpose:** Store configuration values

#### 3. Jobs (units of work)
```yaml
jobs:
  # Sequential jobs (default)
  - job: Job1
    steps: [...]
  
  - job: Job2
    dependsOn: Job1
    steps: [...]
  
  # OR parallel jobs
  - job: UnitTests
    steps: [...]
  
  - job: IntegrationTests
    steps: [...]
```

**Purpose:** Define work units

#### 4. Tasks (built-in Azure Pipelines actions)
```yaml
steps:
  # NodeTool task
  - task: NodeTool@0
    inputs:
      versionSpec: '18.x'
  
  # Npm task
  - task: Npm@1
    inputs:
      command: 'install'
  
  # Docker task
  - task: Docker@2
    inputs:
      command: 'build'
      Dockerfile: 'Dockerfile'
  
  # Publish artifact task
  - task: PublishBuildArtifacts@1
    inputs:
      pathToPublish: 'dist/'
```

**Purpose:** Common CI/CD operations

#### 5. Deployment Jobs (for deployments)
```yaml
- deployment: DeploymentJob
  displayName: 'Deploy App'
  
  environment:
    name: 'Production'
    resourceType: 'Kubernetes'
  
  strategy:
    # Running strategy
    runOnce:
      deploy:
        steps: [...]
    
    # OR rolling update
    rolling:
      maxParallel: 2
      deploy:
        steps: [...]
    
    # OR canary deployment
    canary:
      increments: [10, 50, 100]
      deploy:
        steps: [...]
```

**Purpose:** Specialized job for deployments with strategies

---

## Deployment Shell Commands

Common shell commands used in deployment pipelines and DevOps workflows.

### Git Commands

```bash
# Clone repository
git clone https://github.com/user/repo.git
git clone --depth 1 https://github.com/user/repo.git  # Shallow clone (faster)

# Check status
git status
git log --oneline -5  # Last 5 commits

# Branching
git branch -a                    # List all branches
git checkout -b feature/new      # Create new branch
git switch main                  # Switch to main branch

# Staging and committing
git add .                        # Stage all changes
git commit -m "feat: new feature"
git push origin feature/new      # Push to remote

# Tags (for releases)
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin --tags
```

**Purpose:** Version control operations

### Docker Commands

```bash
# Build image
docker build -t myapp:1.0.0 .
docker build -t myapp:latest -f Dockerfile.prod .

# Run container
docker run -d -p 8080:8080 --name app myapp:1.0.0
docker run -e DATABASE_URL=postgres://db:5432/db myapp:1.0.0

# Container management
docker ps                        # List running containers
docker ps -a                     # List all containers
docker logs container-name       # View logs
docker logs -f container-name    # Follow logs
docker stop container-name
docker rm container-name

# Image management
docker images                    # List images
docker rmi myapp:1.0.0          # Remove image
docker tag myapp:1.0.0 myapp:latest
docker push myregistry/myapp:1.0.0

# Registry login
docker login ghcr.io             # GitHub Container Registry
docker login docker.io           # Docker Hub
docker login myregistry.azurecr.io  # Azure Container Registry

# Clean up (remove unused resources)
docker system prune -a           # Remove all unused images/containers
docker image prune                # Remove dangling images
```

**Purpose:** Container building, running, and management

### Docker Compose Commands

```bash
# Start services (in background)
docker-compose up -d

# Start services (foreground, see logs)
docker-compose up

# View logs
docker-compose logs -f           # Follow all service logs
docker-compose logs db           # View db service logs

# Stop services
docker-compose stop

# Stop and remove containers
docker-compose down

# Remove everything (including volumes)
docker-compose down -v

# Rebuild images
docker-compose build
docker-compose build --no-cache  # Rebuild without cache

# Execute command in running container
docker-compose exec web npm run build

# Scale service (run multiple instances)
docker-compose up -d --scale worker=3  # 3 worker instances
```

**Purpose:** Multi-container application management

### Kubernetes Commands

```bash
# Cluster information
kubectl cluster-info
kubectl get nodes                # List cluster nodes
kubectl config current-context   # Show current context
kubectl config use-context prod  # Switch context

# Deploy application
kubectl apply -f deployment.yaml
kubectl apply -f .               # Apply all YAML files in directory
kubectl apply -f deployment.yaml -n production  # Specific namespace

# View deployments
kubectl get deployments
kubectl get pods
kubectl get services
kubectl describe deployment my-app
kubectl describe pod pod-name

# Check pod logs
kubectl logs pod-name                    # Single pod logs
kubectl logs deployment/my-app          # All pod logs for deployment
kubectl logs pod-name --previous        # Logs from previous crashed pod
kubectl logs pod-name -f                # Follow logs

# Update deployment
kubectl set image deployment/my-app app=myapp:2.0.0  # Update image
kubectl rollout status deployment/my-app             # Check rollout progress
kubectl rollout undo deployment/my-app               # Rollback to previous version

# Execute commands in pod
kubectl exec -it pod-name -- bash      # Interactive shell
kubectl exec pod-name -- npm run test  # Run command

# Port forwarding (for debugging)
kubectl port-forward pod-name 8080:8080  # Forward port 8080

# Delete resources
kubectl delete pod pod-name
kubectl delete deployment my-app
kubectl delete all -l app=my-app     # Delete all with specific label

# ConfigMap and Secrets
kubectl create configmap app-config --from-literal=KEY=value
kubectl create secret generic db-secret --from-literal=password=secret

# Scaling
kubectl scale deployment my-app --replicas=5

# Health and status
kubectl get events              # Cluster events
kubectl describe node node-name # Node details
kubectl top nodes               # Node resource usage
kubectl top pods                # Pod resource usage
```

**Purpose:** Container orchestration and deployment

### Build and Package Management

```bash
# Node.js / npm
npm install                      # Install dependencies
npm ci                          # Clean install (for CI/CD)
npm run build                   # Build application
npm run test                    # Run tests
npm run lint                    # Run linter
npm publish                     # Publish package to npm

# Python / pip
pip install -r requirements.txt
pip install --upgrade package-name
pip freeze > requirements.txt   # Export dependencies

# Java / Maven
mvn clean                       # Clean build directory
mvn compile                     # Compile code
mvn test                        # Run tests
mvn package                     # Create JAR/WAR

# Java / Gradle
gradle build
gradle test
gradle clean
gradle publish
```

**Purpose:** Language-specific build commands

### Database and Migration Commands

```bash
# Database backups
pg_dump -U admin dbname > backup.sql           # PostgreSQL dump
mysqldump -u root -p dbname > backup.sql       # MySQL dump
mongodump --uri mongodb+srv://server/db        # MongoDB dump

# Database restore
psql -U admin dbname < backup.sql              # PostgreSQL restore
mysql -u root -p dbname < backup.sql           # MySQL restore

# Database migrations
./node_modules/.bin/sequelize-cli db:migrate   # Run migrations
./node_modules/.bin/sequelize-cli db:seed      # Run seeders

# Check database connection
psql -U admin -h localhost -d dbname -c "SELECT version();"
mysql -u root -p -h localhost -e "SELECT VERSION();"
```

**Purpose:** Database management and migration

### Health Checks and Monitoring

```bash
# HTTP health checks
curl -f http://localhost:8080/health           # Check endpoint
curl -I http://localhost:8080                  # Head request (headers only)
curl -s http://localhost:8080/health | jq     # Pretty print JSON

# Service checks
netstat -tlnp | grep 8080                      # Check port listening
lsof -i :8080                                  # List process on port
ps aux | grep node                             # Find process

# System monitoring
top                              # Real-time process monitoring
df -h                            # Disk usage
free -h                          # Memory usage
du -sh /path                     # Directory size
```

**Purpose:** Verify service health and system resources

### Deployment and Release Commands

```bash
# SSH deployment
ssh user@server.com "cd /app && git pull && npm install && npm run build"
scp -r dist/ user@server.com:/app/

# Helm (Kubernetes package manager)
helm repo add myrepo https://charts.example.com
helm install my-release myrepo/mychart
helm upgrade my-release myrepo/mychart
helm rollback my-release                # Rollback release

# Infrastructure as Code (Terraform)
terraform init                          # Initialize
terraform plan                          # Preview changes
terraform apply -auto-approve           # Apply changes
terraform destroy                       # Destroy infrastructure

# Cloud CLI (AWS)
aws s3 cp dist/ s3://my-bucket/ --recursive     # Upload to S3
aws ecs update-service --cluster my-cluster --service my-service --force-new-deployment

# Cloud CLI (Azure)
az webapp deployment source config-zip --resource-group rg --name app --src-path dist.zip
az acr build --registry myregistry --image myapp:latest .
```

**Purpose:** Deploy applications to production

---

## Real-World Examples

### Example 1: GitHub Actions - Node.js CI/CD

```yaml
# .github/workflows/deploy.yml
name: Build and Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18.x'
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build

  build:
    name: Build Docker Image
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/metadata-action@v5
        id: meta
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=sha,prefix={{branch}}-
      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}

  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Kubernetes
        run: |
          kubectl set image deployment/my-app \
            app=${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
```

### Example 2: Kubernetes - Complete Deployment

```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: production

---
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: production
data:
  NODE_ENV: production
  LOG_LEVEL: info

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: production
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
      - name: app
        image: myregistry/my-app:latest
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: app-config
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

---
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
  namespace: production
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
```

### Example 3: Docker Compose - Multi-Service Stack

```yaml
version: '3.8'

services:
  web:
    build:
      context: ./web
    image: myapp:latest
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgresql://admin:password@db:5432/mydb
    depends_on:
      db:
        condition: service_healthy
    networks:
      - backend

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: password
    volumes:
      - postgres-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
    networks:
      - backend

volumes:
  postgres-data:

networks:
  backend:
    driver: bridge
```

---

## Best Practices

### YAML-Specific Best Practices

```yaml
# ✅ DO: Use 2-space indentation consistently
deployment:
  name: my-app
  spec:
    replicas: 3

# ❌ DON'T: Mix tabs and spaces
deployment:
    name: my-app      # 4 spaces - wrong
  spec:               # 2 spaces - inconsistent

# ✅ DO: Quote strings with special characters
version: "1.0.0"
message: "Error: Connection failed"

# ❌ DON'T: Leave special characters unquoted
version: 1.0.0       # Might be interpreted as float
message: Error: Connection failed  # Comment conflict

# ✅ DO: Use anchors to avoid duplication
defaults: &defaults
  replicas: 3
  timeout: 30

dev:
  <<: *defaults
  env: development

prod:
  <<: *defaults
  env: production
```

### CI/CD Pipeline Best Practices

```yaml
# ✅ DO: Separate concerns into stages
stages:
  - Build
  - Test
  - Deploy

# ✅ DO: Use meaningful step names
- name: Install dependencies
  run: npm ci

# ✅ DO: Implement health checks
- name: Health check
  run: curl -f http://localhost:8080/health

# ✅ DO: Secure secrets properly
env:
  API_KEY: ${{ secrets.API_KEY }}

# ❌ DON'T: Commit secrets
env:
  API_KEY: "sk_live_abc123"
```

### Kubernetes Best Practices

```yaml
# ✅ DO: Always specify resource requests and limits
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
  limits:
    memory: "512Mi"
    cpu: "500m"

# ✅ DO: Use health checks (liveness and readiness probes)
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30

# ✅ DO: Use namespaces to isolate environments
metadata:
  namespace: production

# ✅ DO: Label resources for organization
labels:
  app: my-app
  environment: production
  version: v1.0.0

# ✅ DO: Use ConfigMaps for non-sensitive config
envFrom:
- configMapRef:
    name: app-config

# ✅ DO: Use Secrets for sensitive data
envFrom:
- secretRef:
    name: app-secrets

# ❌ DON'T: Hardcode credentials
env:
- name: DATABASE_PASSWORD
  value: "my-secret-password"
```

### Docker Compose Best Practices

```yaml
# ✅ DO: Always specify image versions
image: postgres:15-alpine

# ✅ DO: Include health checks
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
  interval: 30s

# ✅ DO: Use named volumes for persistent data
volumes:
  postgres-data:

# ✅ DO: Use networks to control communication
networks:
  - backend

# ✅ DO: Set resource limits
deploy:
  resources:
    limits:
      cpus: '1.0'
      memory: 1G

# ❌ DON'T: Use latest tags in production
image: myapp:latest
```

---

## Summary

### What You Now Know

✅ **YAML Fundamentals** - Syntax, data types, indentation rules
✅ **GitHub Actions** - Workflows, jobs, steps, secrets, matrix strategies
✅ **Kubernetes** - Deployments, Services, ConfigMaps, Secrets, scaling
✅ **Docker Compose** - Multi-container setup, networks, volumes
✅ **Azure Pipelines** - Stages, jobs, tasks, variables, deployment strategies
✅ **Deployment Commands** - Git, Docker, kubectl, Cloud CLIs
✅ **Real-World Examples** - Complete CI/CD pipelines for production use
✅ **Best Practices** - Security, performance, maintainability

### Next Steps

1. **Practice** - Write YAML for your projects
2. **Validate** - Use tools like `yamllint` and IDE plugins
3. **Automate** - Build CI/CD pipelines for your applications
4. **Deploy** - Use Kubernetes, Docker, and cloud platforms
5. **Monitor** - Set up health checks and logging

---

**Remember:** YAML is just data serialization. The power comes from using it with modern DevOps tools!

