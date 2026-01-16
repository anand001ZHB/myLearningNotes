# YAML for DevOps and CI/CD Pipelines

Complete guide covering YAML concepts essential for GitHub Actions, Kubernetes, Docker, Azure Pipelines, and CI/CD deployments.

## Table of Contents
1. [YAML Theory](#yaml-theory)
2. [YAML Fundamentals](#yaml-fundamentals)
3. [Core YAML Concepts](#core-yaml-concepts)
4. [GitHub Actions YAML](#github-actions-yaml)
5. [Kubernetes YAML](#kubernetes-yaml)
6. [Docker Compose YAML](#docker-compose-yaml)
7. [Azure Pipelines YAML](#azure-pipelines-yaml)
8. [Deployment Shell Commands](#deployment-shell-commands)
9. [Real-World Examples](#real-world-examples)
10. [Best Practices](#best-practices)

---

## YAML Theory

### WHAT is YAML?

YAML is a **data serialization language** that converts complex data structures (objects, arrays, key-value pairs) into a text format that both humans and machines can understand. Think of it as a way to describe configuration, data, and instructions in a plain-text format.

**Key characteristics:**
- **Plain text format** - Readable by humans, editable in any text editor
- **Hierarchical structure** - Uses indentation to show relationships (parent-child)
- **Minimal syntax** - Fewer special characters compared to JSON or XML
- **Language-independent** - Works with Python, Java, Node.js, Go, etc.

**Example analogy:**
```
Without YAML (nested function calls):
  server_config(host="localhost", port=8080, 
    database=db_config(name="mydb", user="admin"))

With YAML (readable format):
  server:
    host: localhost
    port: 8080
    database:
      name: mydb
      user: admin
```

### WHY Use YAML for DevOps?

**Problem:** Configuration management for complex systems is difficult

**Before YAML (XML/JSON):**
```xml
<!-- Too verbose, hard to read -->
<server>
  <host>localhost</host>
  <port>8080</port>
  <database>
    <name>mydb</name>
  </database>
</server>
```

**After YAML (Clean and readable):**
```yaml
server:
  host: localhost
  port: 8080
  database:
    name: mydb
```

**Advantages:**
- **Readability** - Minimal syntax, whitespace-based structure (like Python)
- **Version control friendly** - Small, text-based diffs are easy to review
- **Multi-tool support** - Same format works for Docker, Kubernetes, GitHub, Azure, Terraform
- **Reduced errors** - Clear structure prevents misconfigurations
- **Comments support** - Unlike JSON, you can document your configurations
- **Reusability** - Anchors and aliases reduce duplication

### HOW Does YAML Work?

**Step 1: YAML Parser reads the file**
```yaml
application:
  name: myapp
  version: "1.0"
```

**Step 2: Parser converts to data structure**
```
{
  "application": {
    "name": "myapp",
    "version": "1.0"
  }
}
```

**Step 3: Application uses the data**
```
- Docker reads it to configure containers
- Kubernetes uses it to manage pods
- GitHub Actions uses it to define workflows
- Terraform uses it to create infrastructure
```

**Key parsing rules:**
1. **Indentation matters** - 2 spaces per level (not tabs)
2. **Key-value syntax** - `key: value` (colon + space required)
3. **Lists use hyphens** - `- item` at the start of a line
4. **Quotes for special chars** - Use `"value"` if contains `:`, `#`, etc.
5. **Type inference** - `true` = boolean, `123` = number, `"123"` = string

### WHEN to Use YAML vs Other Formats

| Scenario | Format | Reason |
|----------|--------|--------|
| Configuration files | YAML ✅ | Human-readable, supports comments |
| REST API responses | JSON ✅ | Compact, native JavaScript support |
| Infrastructure as Code | YAML ✅ | Readable structure for complex deployments |
| Data exchange | JSON ✅ | Lightweight, minimal overhead |
| Markup documents | XML ✅ | Structured metadata, namespaces |
| CI/CD pipelines | YAML ✅ | Standard across all platforms |
| Kubernetes manifests | YAML ✅ | Only officially supported format |
| Docker Compose | YAML ✅ | Default and recommended format |

### YAML in the DevOps Ecosystem

```
┌─────────────────────────────────────┐
│    YAML Configuration Files         │
└─────────────────────────────────────┘
              │
    ┌─────────┴─────────┬────────────────┬──────────────┐
    │                   │                │              │
    v                   v                v              v
GitHub Actions      Kubernetes        Docker        Azure
(CI/CD Pipeline)    (Orchestration)   Compose       Pipelines
                                      (Containers)  (CI/CD)
```

**Example flow:**
1. Developer writes `docker-compose.yml` to define services locally
2. Same developer writes `.github/workflows/deploy.yml` for CI/CD
3. Deploys to Kubernetes with `deployment.yaml`
4. All three use YAML format - consistency across tools!

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
# Line 1: name - Workflow name displayed in GitHub Actions UI
# WHY: Multiple workflows can exist; name identifies this one in the UI
# Appears in: "Actions" tab → workflow name
name: CI/CD Pipeline

# Line 5: on - Workflow trigger events
# WHY: Defines WHEN this workflow should run
# Can combine multiple triggers (runs on ANY matching condition)
on:
  # Line 8: push trigger - Run when code is pushed
  push:
    # Line 9: branches - Only run when pushing to these branches
    # WHY: Don't run workflow on every branch (e.g., skip feature branches)
    # Uses fnmatch pattern matching (glob syntax)
    branches: [ main, develop ]
    
    # Line 12: paths - Only run if these paths changed
    # WHY: Skip pipeline if only docs changed (don't rebuild for README.md changes)
    paths:
      - 'src/**'              # Any file under src/ directory
      - 'package.json'        # Dependency file (if changed, need to rebuild)
  
  # Line 16: pull_request trigger - Run when PR is opened/updated
  pull_request:
    # Line 17: branches - Only for PRs targeting these branches
    # WHY: Don't test PRs targeting old release branches
    branches: [ main ]
  
  # Line 20: Manual trigger - Run from GitHub UI
  # WHY: Allows manual deployments (not on push/PR)
  # Use case: Deploy to production without merging PR
  workflow_dispatch:
  
  # Line 24: Schedule trigger - Run on schedule (cron)
  # WHY: Run nightly tests, cleanup jobs, scheduled deployments
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM UTC (GitHub runs in UTC)
                         # Format: minute hour day month day-of-week
                         # Reference: https://crontab.guru

# Line 29: env - Environment variables for all jobs
# WHY: Centralize configuration used across all jobs
# Available to: all jobs, all steps
env:
  NODE_VERSION: '18.x'    # Version for setup-node action
  REGISTRY: ghcr.io       # Container registry for Docker images

# Line 33: jobs - Workflow jobs (parallel or sequential units of work)
# WHY: Break down work into manageable pieces
# Each job runs on separate runner (VM), can run in parallel
jobs:
  # Line 35: build - Job name (used in output, dependencies, etc.)
  build:
    # Line 36: runs-on - Which machine/environment to run this job on
    # Options:
    #   ubuntu-latest = Linux VM (recommended, fastest)
    #   windows-latest = Windows VM (for .NET, PowerShell)
    #   macos-latest = macOS VM (for Swift, Objective-C)
    # WHY: Different runners for different OS requirements
    runs-on: ubuntu-latest
    
    # Line 43: steps - Individual tasks within this job
    # Executed sequentially in order
    # If a step fails, subsequent steps don't run (unless continue-on-error)
    steps:
      # Line 46: uses - Run a pre-built Action (reusable workflow component)
      # Format: owner/repo@version (installed from GitHub Marketplace)
      # actions/checkout = official action to download your repository code
      # WHY: Actions encapsulate complex logic; reuse across workflows
      - uses: actions/checkout@v4
      
      # Line 50: Setup Node.js with caching
      # WHY: Speeds up subsequent builds by caching dependencies
      - uses: actions/setup-node@v4
        with:                           # Input parameters for the action
          node-version: ${{ env.NODE_VERSION }}  # Variable reference
          cache: 'npm'                  # Enable npm cache
      
      # Line 55: run - Execute shell command
      # Runs in: bash (on Linux/macOS), cmd (on Windows)
      - name: Install dependencies      # Step name (appears in logs)
        run: npm install                # npm ci better for CI/CD (cleaner installs)
      
      # Line 59: Build the project
      - name: Build application
        run: npm run build               # Runs script from package.json
      
      # Line 62: Run tests
      - name: Run tests
        run: npm test                    # Unit tests, assertion checks
```

**Workflow Execution Flow:**
```
1. Push to main branch
   ↓
2. GitHub detects trigger (push to main)
   ↓
3. Allocates runner (VM) for the workflow
   ↓
4. Clones your repository (actions/checkout)
   ↓
5. Runs each step in sequence:
   - Setup Node.js
   - Install dependencies
   - Build application
   - Run tests
   ↓
6. If any step fails, stop and notify (workflow failed)
   ↓
7. If all steps pass, mark workflow as success
   ↓
8. Logs and artifacts available in GitHub UI
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
# Line 1: API version - Specifies which Kubernetes API version to use
# apps/v1 = stable API for applications (deployments, statefulsets, daemonsets)
# WHY: Different API versions have different features and stability guarantees
apiVersion: apps/v1

# Line 4: Kind - Type of Kubernetes resource to create
# Deployment = manages pods with rolling updates, self-healing, scaling
# Other options: Service, ConfigMap, Secret, Pod, StatefulSet, DaemonSet, Job
# WHY: Kubernetes needs to know what type of resource you're defining
kind: Deployment

# Line 7: Metadata section - Information about this resource (name, labels, namespace)
metadata:
  # Line 8: name - Unique identifier for this deployment within a namespace
  # MUST be DNS-1123 compliant (lowercase, hyphens, alphanumeric)
  # Used by: kubectl commands, service discovery, logs
  name: my-app
  
  # Line 11: namespace - Logical cluster partition for multi-tenant isolation
  # WHY: Namespaces help organize resources (dev, staging, production)
  # Default namespace is "default" if not specified
  namespace: production
  
  # Line 14: labels - Key-value pairs for resource organization and selection
  # WHY: Labels are used to group resources, query with selectors, manage deployments
  labels:
    app: my-app            # Standard label: which app does this belong to?
    environment: prod      # Custom label: environment identifier
    version: v1.0.0        # Custom label: version for rolling updates

# Line 19: spec - Desired state specification (what Kubernetes should create and maintain)
spec:
  # Line 21: replicas - Number of pod copies to run
  # WHY: Provides redundancy, load distribution, and high availability
  # If a pod crashes, Kubernetes automatically creates a replacement
  # Can be changed without redeploying: kubectl scale deployment/my-app --replicas=5
  replicas: 3
  
  # Line 25: selector - How to identify pods managed by this deployment
  # WHY: Deployment needs to know which pods to manage and update
  # IMPORTANT: Must match labels in template.metadata.labels (otherwise pods are orphaned)
  selector:
    matchLabels:
      app: my-app          # Selector filter: only manage pods labeled app=my-app
  
  # Line 30: template - Blueprint for creating pods
  # WHY: Defines what each pod should look like (image, ports, env vars, resources)
  template:
    metadata:
      # Line 33: labels for pods created from this template
      # MUST match selector.matchLabels so deployment can manage them
      # WHY: Service will use these labels to route traffic to pods
      labels:
        app: my-app        # Must match selector above!
    
    spec:
      # Line 38: containers - List of containers to run in each pod
      # WHY: A pod can have multiple containers (main app + sidecar), but usually just one
      containers:
      # Line 40: Start of first container definition (list item with hyphen)
      - name: app-container           # Container name (used in logs, debugging)
        
        # Line 42: image - Docker image to use for this container
        # Format: [registry]/[image]:[tag]
        # myapp:1.0.0 = use local/default registry, image "myapp", tag "1.0.0"
        # WHY: Specifies exact version to prevent "works on my machine" issues
        image: myapp:1.0.0
        
        # Line 47: imagePullPolicy - When to pull the image from registry
        # Options:
        #   Always = always pull (for CI/CD, ensures latest from registry)
        #   IfNotPresent = only pull if not cached locally (default, faster)
        #   Never = only use cached images, fail if not present
        # WHY: Controls whether to check registry for newer versions
        imagePullPolicy: IfNotPresent
        
        # Line 53: ports - Which ports this container listens on
        # WHY: Kubernetes needs to know what ports to expose and monitor
        ports:
        - containerPort: 8080         # Port inside the container (where app listens)
          name: http                  # Name for this port (used in services)
        
        # Line 57: env - Environment variables to pass to the container
        # WHY: Configure app behavior without changing code or image
        env:
        # Line 59: Direct environment variable (hardcoded value)
        - name: DATABASE_URL
          value: "postgresql://db:5432/mydb"
        
        # Line 62: Environment variable from ConfigMap (read from config file)
        # WHY: Separate configuration from image, allow updates without rebuilding
        - name: APP_CONFIG
          valueFrom:
            configMapKeyRef:          # Read from ConfigMap resource
              name: app-config        # ConfigMap resource name
              key: settings           # Which key in the ConfigMap
        
        # Line 69: Environment variable from Secret (read from encrypted secret)
        # WHY: Keep sensitive data (passwords, tokens) encrypted and separate
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:             # Read from Secret resource
              name: db-secret         # Secret resource name
              key: password           # Which key in the Secret
        
        # Line 76: resources - CPU and memory constraints
        # WHY: 
        #   Prevent pods from consuming all cluster resources
        #   Allow Kubernetes to schedule pods efficiently
        #   Requests = what pod needs to run | Limits = maximum allowed
        resources:
          # Line 79: requests - Minimum resources needed to schedule this pod
          # WHY: Kubernetes scheduler reserves these resources for the pod
          # If not enough resources, pod stays in "Pending" state
          requests:
            memory: "256Mi"           # Minimum 256 Megabytes of RAM
            cpu: "250m"               # Minimum 0.25 CPU cores (1000m = 1 core)
          
          # Line 84: limits - Maximum resources pod can use
          # WHY: Prevent pod from using too much, killing other pods
          # If pod exceeds limits, it gets throttled (CPU) or killed (memory)
          limits:
            memory: "512Mi"           # Maximum 512 Megabytes of RAM
            cpu: "500m"               # Maximum 0.5 CPU cores
        
        # Line 89: livenessProbe - Is the container still running?
        # WHY: Detects zombie processes (app crashed but container still running)
        # ACTION: If fails, Kubernetes restarts the pod
        # Use case: App hangs but doesn't crash, needs restart
        livenessProbe:
          httpGet:                    # Check by making HTTP GET request
            path: /health            # Endpoint that returns 200 if healthy
            port: 8080               # Container port to check
          initialDelaySeconds: 30     # Wait 30 seconds before first check (startup time)
          periodSeconds: 10           # Check every 10 seconds
          timeoutSeconds: 5           # If no response in 5 seconds, consider failed
          failureThreshold: 3         # Restart after 3 consecutive failures
        
        # Line 105: readinessProbe - Is the container ready to handle traffic?
        # WHY: Pod might be healthy but not ready (loading data, warming up)
        # ACTION: If fails, remove pod from service load balancer (no new traffic)
        # Use case: Don't route requests until pod is fully initialized
        readinessProbe:
          httpGet:                    # Check by making HTTP GET request
            path: /ready              # Endpoint that returns 200 if ready
            port: 8080                # Container port to check
          initialDelaySeconds: 10     # Wait 10 seconds before first check
          periodSeconds: 5            # Check every 5 seconds (more frequent than liveness)
          failureThreshold: 3         # Remove from service after 3 consecutive failures
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
# Line 1: Compose file format version
# Version history:
#   3.8 = Latest stable (recommended)
#   3.7, 3.6, etc. = Older versions with fewer features
# WHY: Docker adds new features and syntax, version ensures backward compatibility
version: '3.8'

# Line 5: services - Dictionary of containers to run
# WHY: Compose orchestrates multiple containers as a single application
services:
  # Line 7: web - Service name (used in docker-compose commands and internal networking)
  # Access from other services via hostname "web" or "web:8080"
  web:
    # Line 9: build - Instructions to build image from Dockerfile
    # WHY: Allows customization of image during build (no need to push to registry)
    # Used in development; for production use pre-built "image" instead
    build:
      context: ./web           # Directory containing Dockerfile (build context)
                               # Sent to Docker daemon for building
      dockerfile: Dockerfile   # Dockerfile name (default is "Dockerfile")
    
    # Line 15: image - Pre-built Docker image to use
    # WHY: Much faster than building; use for production images from registries
    # Format: [registry]/[image]:[tag]
    # nginx:latest = Docker Hub, nginx image, latest tag
    # Note: Can use BOTH build and image; image becomes the name for built image
    image: nginx:latest
    
    # Line 20: container_name - Custom container name in Docker daemon
    # WHY: Makes it easier to identify container (instead of auto-generated names)
    # Default: [project-name]_[service-name]_1
    # Note: Set only if running single instance (can't scale with container_name)
    container_name: web-app
    
    # Line 24: restart - Restart policy if container exits
    # Options:
    #   no = don't automatically restart (default)
    #   always = always restart (even after Docker daemon restart)
    #   unless-stopped = restart unless manually stopped
    #   on-failure = restart only if exit code indicates failure
    # WHY: Ensures service keeps running in production
    restart: unless-stopped
    
    # Line 31: ports - Expose container ports to host/external network
    # Format: "host_port:container_port" or "127.0.0.1:host_port:container_port"
    # WHY: Container network is isolated; ports must be explicitly exposed
    ports:
      - "80:8080"             # Host port 80 → Container port 8080 (web traffic)
      - "443:8443"            # Host port 443 → Container port 8443 (HTTPS traffic)
    
    # Line 36: environment - Environment variables passed to container
    # WHY: Configure app behavior without changing code; different per environment
    # Available in container as process environment variables
    environment:
      - NODE_ENV=production   # Node.js mode (affects behavior and logging)
      - DATABASE_URL=postgres://db:5432/mydb  # Database connection string
                              # "db" = service name (Docker network DNS resolution)
    
    # Line 42: env_file - Load environment variables from file
    # WHY: Avoid hardcoding sensitive values in docker-compose.yml
    # .env.production file contains KEY=VALUE pairs, loaded at startup
    env_file:
      - .env.production       # Path to .env file (relative to docker-compose.yml)
    
    # Line 46: volumes - Mount directories/files from host or named volumes
    # WHY: 
    #   Persist data between container restarts
    #   Share code for development (hot reload)
    #   Share config files
    volumes:
      - ./app:/app            # Bind mount: host ./app → container /app (read-write)
                              # WHY: Share source code for development
      - app-data:/data        # Named volume: managed by Docker, persistent
                              # WHY: Persist application data
    
    # Line 54: depends_on - Service dependencies (wait for other services)
    # WHY: Ensure dependent services start before this one
    # Can wait for container to start or reach healthy state
    depends_on:
      db:
        condition: service_healthy  # Only continue when db is healthy (per its healthcheck)
    
    # Line 59: healthcheck - Periodic check to determine container health
    # WHY: docker-compose depends_on can wait for healthy status
    # Also used by monitoring/orchestration to restart unhealthy containers
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
                              # Run this command; if exit 0 = healthy
      interval: 30s           # Check every 30 seconds
      timeout: 10s            # Give command 10 seconds to complete; if longer = failed
      retries: 3              # After 3 failed checks, mark container unhealthy
    
    # Line 68: networks - Connect container to Docker networks
    # WHY: Controls network visibility and communication
    # Service can communicate with others on same network via service name (DNS)
    networks:
      - frontend              # Custom network for frontend services
      - backend               # Custom network for backend services (separate from frontend)
  
  # Line 73: db - Database service
  db:
    # Pre-built PostgreSQL image (no build needed)
    image: postgres:15-alpine
    
    # Container name for easy identification
    container_name: postgres-db
    
    # PostgreSQL-specific environment variables
    # WHY: PostgreSQL image uses these to initialize database
    environment:
      POSTGRES_DB: mydb       # Initial database name to create
      POSTGRES_USER: admin    # Default user
      POSTGRES_PASSWORD: secret  # Default password
                              # NOTE: Should come from .env file or secrets!
    
    # Persistent data storage
    volumes:
      - postgres-data:/var/lib/postgresql/data
                              # Named volume: persists database files
                              # WHY: Without this, data lost when container stops
    
    # Health check specific to PostgreSQL
    # WHY: Tells Docker when database is ready to accept connections
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
                              # PostgreSQL utility to check if accepting connections
      interval: 10s           # Check every 10 seconds (more frequent than web app)
      timeout: 5s             # Database should respond very quickly
      retries: 5              # More retries for startup time

# Line 104: volumes (top-level) - Define named volumes used by services
# WHY: Volumes are Docker's way to manage persistent storage
# Alternative to bind mounts: more portable, better performance, easier backup
volumes:
  postgres-data:              # Volume name (referenced in service volumes)
                              # Docker creates/manages this automatically
  app-data:                   # Another named volume for app data

# Line 109: networks (top-level) - Define custom networks
# WHY: Services on different networks can't communicate (security isolation)
# Example: frontend services can't directly access backend databases
networks:
  frontend:                   # Network for user-facing services
    driver: bridge            # Bridge network (local, single-host)
  backend:                    # Network for internal services
    driver: bridge            # Bridge network (isolated from frontend)
```

**Key Docker Compose Workflow:**
```
1. docker-compose up -d
   ↓
2. Create networks (frontend, backend)
   ↓
3. Create volumes (postgres-data, app-data)
   ↓
4. Start db service
   ↓
5. Wait for db healthcheck to pass
   ↓
6. Start web service
   ↓
7. Web connects to db via network (hostname "db")
   ↓
8. Both services running, application operational
```
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
# Line 1: trigger - When to automatically run this pipeline
# WHY: Automatically run on push/PR without manual trigger
trigger:
  # Line 3: branches - Run on pushes to these branches
  # WHY: Don't run on every branch; only important ones
  branches:
    include:
      - main        # Run on main branch
      - develop     # Run on develop branch
    exclude:
      - releases/*  # Don't run on releases/* branches
  
  # Line 10: paths - Only run if these paths changed
  # WHY: Skip expensive builds if only docs changed
  paths:
    include:
      - src/        # Include source code changes
      - tests/      # Include test file changes
    exclude:
      - docs/       # Exclude documentation changes
      - README.md   # Exclude README updates

# Line 17: pr - When to run on pull requests
# WHY: Validate code before merge
pr:
  # Line 19: branches - Only run PR validation for PRs targeting these branches
  # WHY: Don't validate PRs to old branches
  branches:
    include:
      - main        # Require checks before merging to main

# Line 24: variables - Global variables accessible in all stages/jobs
# WHY: Centralize configuration; define once, use everywhere
# Three types: hardcoded, variable groups (from library), dynamic (from expressions)
variables:
  buildConfiguration: 'Release'  # Build configuration (Release vs Debug)
  nodeVersion: '18.x'            # Node.js version for build
  imageName: 'myapp'             # Docker image name for builds

# Line 29: stages - Logical grouping of jobs
# WHY: Organize pipeline into phases (Build → Test → Deploy)
# Runs SEQUENTIALLY by default (wait for previous stage to complete)
stages:
  # Line 32: Stage 1 - Build and Test
  - stage: Build
    displayName: 'Build Stage'    # User-friendly name
    
    # Line 35: jobs - Work units within stage
    jobs:
      # Line 37: Job 1 - Build application
      - job: BuildJob
        displayName: 'Build Application'
        
        # Line 40: pool - Which machine to run job on
        # WHY: Different pools have different OS/tools available
        pool:
          vmImage: 'ubuntu-latest'  # Use latest Ubuntu Linux VM (recommended)
                                    # Other options: windows-latest, macos-latest
        
        # Line 44: steps - Sequential tasks within job
        steps:
          # Step 1: Setup Node.js
          # WHY: Need Node.js available to run npm commands
          - task: NodeTool@0          # Azure task identifier
            displayName: 'Use Node.js $(nodeVersion)'
            inputs:                   # Task configuration
              versionSpec: '$(nodeVersion)'  # Install Node.js 18.x
          
          # Step 2: Install dependencies
          # WHY: npm install fetches all required packages from npm registry
          - task: Npm@1               # Azure's npm task (wrapper around npm)
            displayName: 'npm install'
            inputs:
              command: 'install'      # Run npm install command
          
          # Step 3: Build application
          # WHY: Compile TypeScript → JavaScript, bundle code, etc.
          - task: Npm@1
            displayName: 'npm run build'
            inputs:
              command: 'custom'       # Run custom npm script
              customCommand: 'run build'  # Runs "npm run build" from package.json
          
          # Step 4: Publish build artifacts
          # WHY: Make build output available to other stages/jobs
          - task: PublishBuildArtifacts@1
            displayName: 'Publish Artifacts'
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)'
                                      # Built output directory
              artifactName: 'drop'    # Artifact name for reference
  
  # Line 66: Stage 2 - Deploy
  - stage: Deploy
    displayName: 'Deploy Stage'
    # Line 69: dependsOn - Wait for Build stage to complete
    # WHY: Can't deploy if build fails
    dependsOn: Build
    # Line 71: condition - Only run if Build succeeded
    # WHY: Skip deploy if tests/build failed
    condition: succeeded()
    
    jobs:
      # Line 74: deployment - Special job type for deployments
      # WHY: Deployment jobs have additional features:
      #   - environment selection
      #   - approval gates
      #   - deployment strategies (rolling, canary, etc.)
      - deployment: DeployApp
        displayName: 'Deploy to Production'
        
        # Line 80: environment - Target deployment environment
        # WHY: Azure can track deployment history, apply approval rules, etc.
        # Environments defined in Azure DevOps project settings
        environment:
          name: 'Production'          # Environment name
          resourceType: 'Kubernetes'  # Resource type (for validation/approval)
        
        # Line 85: strategy - How to deploy (rolling, canary, blue-green, etc.)
        strategy:
          # Line 87: rolling - Update pods incrementally
          # WHY: Prevents downtime, easy rollback if issues
          rolling:
            maxParallel: 2            # Update maximum 2 pods at a time
          
          # Alternative: canary deployment
          canary:
            increments: [10, 20, 50, 100]  # % rollout increments
                                      # Deploy to 10% → 20% → 50% → 100%
                                      # Allows validation before full rollout
        
        # Line 95: steps - Deployment tasks
        steps:
          # Download artifact from Build stage
          - download: current
            artifact: drop            # Download 'drop' artifact published earlier
          
          # Deploy using kubectl
          - task: Kubernetes@1        # Azure Kubernetes task
            displayName: 'Deploy to Kubernetes'
            inputs:
              connectionType: 'Kubernetes Service Connection'
                                      # Connection to Kubernetes cluster
              kubernetesServiceEndpoint: 'k8s-prod'
                                      # Named connection in Azure DevOps
              namespace: 'production'  # Kubernetes namespace
              command: 'apply'        # kubectl apply (create/update resources)
              arguments: '-f deployment.yaml'  # Apply this YAML file
```

**Azure Pipelines Execution Flow:**
```
1. Push to main branch
   ↓
2. Azure Pipelines detects trigger
   ↓
3. STAGE 1: Build
   - Allocate ubuntu-latest VM
   - Setup Node.js
   - Run npm install
   - Run npm run build
   - Publish artifacts
   ↓
4. Wait for Build stage to complete
   ↓
5. STAGE 2: Deploy
   - Check condition: succeeded() ✓
   - Allocate VM for deployment
   - Download artifacts from Build
   - Deploy to Kubernetes with rolling strategy
   ↓
6. Pipeline complete, logs and status available
```

**Key Differences: GitHub Actions vs Azure Pipelines:**
```
GitHub Actions                          | Azure Pipelines
─────────────────────────────────────────────────────────────
- Workflows in .github/workflows/       | - Pipeline in azure-pipelines.yml
- Uses "jobs" (parallel)                | - Uses "stages" (sequential) + jobs
- Triggers: on                          | - Triggers: trigger, pr
- Steps use "uses" and "run"            | - Steps use "task" (built-in) or scripts
- Uses marketplace actions              | - Uses Azure DevOps tasks
- GitHub-hosted or self-hosted          | - Microsoft-hosted or self-hosted
- Free for public repos                 | - Free tier available
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

