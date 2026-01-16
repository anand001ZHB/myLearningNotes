# YAML Interview Questions & Answers

Complete interview preparation guide with real-world scenarios and tricky questions.

## Table of Contents
1. [Basic YAML Concepts](#basic-yaml-concepts)
2. [YAML Syntax & Structure](#yaml-syntax--structure)
3. [Advanced YAML Features](#advanced-yaml-features)
4. [Kubernetes YAML](#kubernetes-yaml)
5. [Docker Compose YAML](#docker-compose-yaml)
6. [GitHub Actions YAML](#github-actions-yaml)
7. [Azure Pipelines YAML](#azure-pipelines-yaml)
8. [Problem-Solving & Debugging](#problem-solving--debugging)
9. [Real-World Scenarios](#real-world-scenarios)
10. [Tricky Questions](#tricky-questions)

---

## Basic YAML Concepts

### Q1: What is YAML and why is it used in DevOps?

**Answer:**
YAML stands for **"YAML Ain't Markup Language"** and is a human-readable data serialization format. 

**Why used in DevOps:**
- **Readability**: Minimal syntax, clean structure (similar to Python)
- **Version Control**: Plain text format, easy to diff and review
- **Multi-tool Support**: Standard format for Docker, Kubernetes, GitHub, Azure, Terraform
- **Comments Support**: Unlike JSON, can document configurations
- **Minimal Overhead**: No excessive tags or special characters

**Example:**
```yaml
# YAML - Clean and readable
server:
  host: localhost
  port: 8080
  database:
    name: mydb
```

**vs JSON - More verbose:**
```json
{
  "server": {
    "host": "localhost",
    "port": 8080,
    "database": {
      "name": "mydb"
    }
  }
}
```

---

### Q2: What are the key characteristics of YAML?

**Answer:**
1. **Whitespace-sensitive** - Indentation matters (like Python)
2. **Language-independent** - Works with any programming language
3. **Human-readable** - Minimal special syntax
4. **Case-sensitive** - `Name` and `name` are different
5. **Type-inferred** - `true` = boolean, `123` = number, `"123"` = string
6. **Supports comments** - Can document configurations inline
7. **Supports multiple data types** - Strings, numbers, booleans, lists, dictionaries
8. **No markup overhead** - No closing tags like XML

---

### Q3: What is YAML's relationship with JSON?

**Answer:**
YAML is a **superset of JSON**, meaning:
- All valid JSON is also valid YAML
- YAML is easier to read but less compact
- Both serialize to the same data structures

**Example:**
```yaml
# This YAML
users:
  - name: John
    age: 30
  - name: Jane
    age: 25
```

**Is equivalent to this JSON:**
```json
{
  "users": [
    {"name": "John", "age": 30},
    {"name": "Jane", "age": 25}
  ]
}
```

---

## YAML Syntax & Structure

### Q4: Explain YAML indentation rules with examples.

**Answer:**
**Rules:**
- Use **2 spaces per indentation level** (not tabs)
- Indentation must be consistent throughout the file
- Indentation determines parent-child relationships

**Example:**
```yaml
# Correct - 2 spaces per level
parent:
  child1: value1
  child2:
    grandchild: value2

# Wrong - 4 spaces
parent:
    child1: value1
    child2:
        grandchild: value2

# Wrong - Inconsistent spacing
parent:
  child1: value1
    child2: value2  # Inconsistent!
```

**Error Result:** YAML parser will throw indentation error.

---

### Q5: What are the differences between lists and dictionaries in YAML?

**Answer:**

**Lists (Arrays):**
```yaml
# List format - hyphen + space
fruits:
  - apple
  - banana
  - orange

# Inline format
fruits: [apple, banana, orange]

# List of objects
users:
  - name: John
    age: 30
  - name: Jane
    age: 25
```

**Dictionaries (Maps):**
```yaml
# Dictionary format - key: value
person:
  name: John
  age: 30
  email: john@example.com

# Inline format
person: {name: John, age: 30, email: john@example.com}
```

**Key Difference:**
- **Lists** use hyphen `-` for items
- **Dictionaries** use `key: value` pairs
- Lists are **ordered**, dictionaries use **keys**

---

### Q6: When should you use quotes in YAML?

**Answer:**
**Use quotes when:**
1. Value contains special characters (`:`, `#`, `@`, `&`, `*`, etc.)
2. Value starts with numbers but should be string (version numbers)
3. Value contains leading/trailing spaces
4. Value is `true`, `false`, `yes`, `no`, `null` but you want it as string

**Examples:**
```yaml
# Must quote - contains colon
message: "Error: Connection failed"

# Must quote - contains hash
version: "1.0.0"  # Without quotes, might be parsed as float

# Must quote - starts with number but is string
version: "1.0.0"
phone: "9876543210"

# Don't quote - simple strings
name: John
host: localhost

# Special: quote to force string type
enabled: "true"    # String "true"
enabled: true      # Boolean true
```

---

### Q7: Explain YAML data types.

**Answer:**

```yaml
# String
name: John Doe
email: "john@example.com"

# Integer
count: 42
port: 8080
negative: -5

# Float
price: 29.99
temperature: -10.5

# Boolean
enabled: true         # or True, yes, on
disabled: false       # or False, no, off

# Null
value: null           # or ~
empty:

# List
items:
  - item1
  - item2

# Dictionary
person:
  name: John
  age: 30

# Date (ISO 8601)
created: 2024-01-15T10:30:00Z
```

**Type Inference:**
```yaml
"123"     # String (quotes)
123       # Integer (no quotes)
"123.45"  # String
123.45    # Float
true      # Boolean
"true"    # String
null      # Null
```

---

## Advanced YAML Features

### Q8: What are anchors and aliases in YAML? Why use them?

**Answer:**
**Anchors `&`** and **Aliases `*`** allow reusing data without duplication.

**Without anchors (repetition):**
```yaml
development:
  database:
    host: localhost
    port: 5432
    timeout: 30
    retries: 3

production:
  database:
    host: prod.example.com
    port: 5432
    timeout: 30    # Duplicated!
    retries: 3     # Duplicated!
```

**With anchors and aliases:**
```yaml
# Define anchor with &
defaults: &default_settings
  timeout: 30
  retries: 3

# Use alias with *
development:
  database:
    host: localhost
    port: 5432
    <<: *default_settings    # Merge anchor

production:
  database:
    host: prod.example.com
    port: 5432
    <<: *default_settings    # Reuse same settings
    timeout: 60              # Override if needed
```

**Benefits:**
- Avoid duplication (DRY principle)
- Easier to maintain
- Less error-prone
- Single source of truth

---

### Q9: Explain `<<` (merge key) in YAML.

**Answer:**
The **merge key `<<`** copies all key-value pairs from an alias into the current mapping.

**Example:**
```yaml
defaults: &defaults
  adapter: postgres
  host: localhost
  port: 5432

development:
  <<: *defaults
  database: development_db
  # Results in:
  # adapter: postgres
  # host: localhost
  # port: 5432
  # database: development_db

production:
  <<: *defaults
  database: production_db
  host: prod.example.com  # Override specific value
  # Results in:
  # adapter: postgres
  # host: prod.example.com (overridden)
  # port: 5432
  # database: production_db
```

**Key Points:**
- `<<:` copies all keys from anchor
- Can override specific keys after merge
- Multiple merges possible: `<<: [*defaults, *security]`

---

### Q10: What are multi-line strings in YAML? Explain `|` and `>`.

**Answer:**

**Literal block `|`** - Preserves newlines exactly:
```yaml
description: |
  This is a multi-line string.
  Each line is preserved.
  Newlines are included.

# Result:
# "This is a multi-line string.\nEach line is preserved.\nNewlines are included.\n"
```

**Folded block `>`** - Converts newlines to spaces:
```yaml
summary: >
  This is a long string
  that spans multiple lines
  but should be folded into one line.

# Result:
# "This is a long string that spans multiple lines but should be folded into one line.\n"
```

**Use Cases:**
- `|` - Configuration files, scripts, error messages (preserve formatting)
- `>` - Documentation, long descriptions (save space)

**Comparison:**
```yaml
# Using |
script: |
  #!/bin/bash
  echo "Hello"
  exit 0

# Using >
description: >
  This is a very long description that spans
  multiple lines but doesn't need special
  formatting and can be folded into one line.
```

---

## Kubernetes YAML

### Q11: What does `apiVersion: apps/v1` mean in Kubernetes?

**Answer:**
`apiVersion` specifies which **Kubernetes API version** to use for that resource type.

**Common API versions:**
```yaml
# Stable APIs
apiVersion: apps/v1          # Deployments, StatefulSets, DaemonSets
apiVersion: v1               # Pods, Services, ConfigMaps, Secrets, Namespaces
apiVersion: batch/v1         # Jobs, CronJobs
apiVersion: autoscaling/v2   # HorizontalPodAutoscaler
apiVersion: networking.k8s.io/v1  # Ingress, NetworkPolicy
apiVersion: rbac.authorization.k8s.io/v1  # Roles, RoleBindings
```

**Why different versions:**
- Different API versions have different features
- `apps/v1` is stable for deployments
- `v1` is stable for core resources
- Versions indicate maturity level

**Error Example:**
```yaml
apiVersion: v1
kind: Deployment  # Wrong! Deployment uses apps/v1, not v1
```

---

### Q12: Explain the difference between `requests` and `limits` in Kubernetes resource specifications.

**Answer:**

**Requests** - Minimum resources guaranteed:
```yaml
resources:
  requests:
    memory: "256Mi"   # Pod needs at least 256MB
    cpu: "250m"       # Pod needs at least 0.25 CPU cores
```
- Kubernetes **reserves** these resources for the pod
- Scheduler uses requests to decide where to place pod
- If cluster doesn't have enough, pod stays **Pending**
- Pod can use more if available

**Limits** - Maximum resources allowed:
```yaml
resources:
  limits:
    memory: "512Mi"   # Pod can't use more than 512MB
    cpu: "500m"       # Pod can't use more than 0.5 CPU cores
```
- Prevents pod from consuming all cluster resources
- **CPU**: Pod gets throttled if exceeds limit
- **Memory**: Pod gets killed (OOMKilled) if exceeds limit
- Other pods won't be starved

**Complete Example:**
```yaml
resources:
  requests:
    memory: "256Mi"   # Minimum (reserved)
    cpu: "250m"
  limits:
    memory: "512Mi"   # Maximum (enforced)
    cpu: "500m"
```

**Comparison:**
| Aspect | Requests | Limits |
|--------|----------|--------|
| Purpose | Reserve resources | Cap resource usage |
| Guarantee | Guaranteed availability | Prevents hogging |
| CPU Exceeded | Pod shares remaining CPU | Pod gets throttled |
| Memory Exceeded | OK if available | Pod killed (OOMKilled) |
| Effect | Scheduling decision | Runtime enforcement |

---

### Q13: What's the difference between `livenessProbe` and `readinessProbe`?

**Answer:**

**livenessProbe** - Is the container still running?
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30    # Wait 30s before first check
  periodSeconds: 10          # Check every 10 seconds
  failureThreshold: 3        # Restart after 3 failures
```
- **Purpose**: Detect stuck/zombie processes
- **Action on Failure**: **Restart the pod**
- **Use Case**: App hangs but doesn't crash, needs restart
- **Example**: Application stuck in infinite loop

**readinessProbe** - Is container ready for traffic?
```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 10    # Wait 10s before first check
  periodSeconds: 5           # Check more frequently (every 5s)
  failureThreshold: 3        # Remove from service after 3 failures
```
- **Purpose**: Check if container is initialized
- **Action on Failure**: **Remove from load balancer** (no new traffic)
- **Use Case**: Pod is running but still initializing
- **Example**: Loading data from database, warming up cache

**Comparison:**
```yaml
Liveness:                          Readiness:
- Is app alive?                    - Is app ready to serve?
- Detects dead processes           - Detects initialization issues
- Restarts pod on failure          - Removes from service on failure
- Less frequent checks             - More frequent checks
- Longer initial delay             - Shorter initial delay
- Example: App hangs               - Example: Loading cache
```

**Complete Example:**
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10
  failureThreshold: 3

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
  failureThreshold: 3
```

---

### Q14: How does pod selector work in Kubernetes deployments?

**Answer:**
**Selector** is the mechanism that connects Deployments to Pods.

**Example:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  # Selector: "Find pods with app=web-app label"
  selector:
    matchLabels:
      app: web-app        # Must match pod labels!
  
  template:
    metadata:
      labels:
        app: web-app      # Pod gets this label
        version: v1.0.0   # Additional labels
    spec:
      containers:
      - name: web
        image: nginx:latest
```

**How It Works:**
1. Deployment looks for pods with label `app=web-app`
2. Any pod with matching label is managed by this Deployment
3. New pods created from template inherit the labels
4. If you remove label, pod becomes unmanaged (orphaned)

**Critical Rule:**
```yaml
selector.matchLabels MUST MATCH template.metadata.labels
```

**Common Mistake:**
```yaml
selector:
  matchLabels:
    app: web-app

template:
  metadata:
    labels:
      app: different-name  # ❌ Doesn't match! Pods become orphaned!
```

---

### Q15: What is a namespace in Kubernetes and why use it?

**Answer:**
**Namespace** is a logical partition of a Kubernetes cluster for isolation and organization.

**Use Cases:**
```yaml
# Development environment
---
apiVersion: v1
kind: Namespace
metadata:
  name: development

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
  namespace: development  # In development namespace
spec:
  replicas: 1
  # ...

# Production environment
---
apiVersion: v1
kind: Namespace
metadata:
  name: production

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
  namespace: production   # In production namespace
spec:
  replicas: 3
  # ...
```

**Benefits:**
1. **Isolation** - Separate dev/staging/production
2. **Resource Quotas** - Limit namespace usage
3. **RBAC** - Different permissions per namespace
4. **Name Reuse** - Same app name in different namespaces
5. **Organization** - Group related resources

**Service Discovery:**
```yaml
# Within same namespace
service_name: my-service

# Across namespaces
service_name.namespace_name.svc.cluster.local: my-service.production.svc.cluster.local
```

---

## Docker Compose YAML

### Q16: What's the difference between `docker run` and `docker-compose up`?

**Answer:**

**`docker run`** - Single container:
```bash
docker run -d \
  -p 8080:8080 \
  -e DATABASE_URL=postgres://db:5432/mydb \
  -v app-data:/data \
  --name myapp \
  myapp:1.0.0
```

**`docker-compose up`** - Multiple containers:
```yaml
version: '3.8'
services:
  app:
    image: myapp:1.0.0
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgres://db:5432/mydb
    volumes:
      - app-data:/data
  
  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=mydb
```

**Comparison:**
| Aspect | docker run | docker-compose |
|--------|-----------|-----------------|
| Containers | Single | Multiple |
| Networking | Manual linking | Automatic DNS |
| Dependencies | Manual ordering | depends_on |
| Configuration | Command line args | YAML file |
| Persistence | Separate commands | Single file |
| Scalability | Limited | Scale easily |
| Use Case | Quick testing | Development/production |

---

### Q17: Explain `depends_on` in Docker Compose.

**Answer:**

**Simple dependency** - Wait for service to start:
```yaml
services:
  web:
    image: myapp:latest
    depends_on:
      - db           # Wait for db to start

  db:
    image: postgres:15
```
- Waits for `db` container to **start**
- Doesn't check if `db` is **ready**
- Can cause issues if db startup is slow

**Better: Conditional dependency** - Wait for healthcheck:
```yaml
services:
  web:
    image: myapp:latest
    depends_on:
      db:
        condition: service_healthy  # Wait for health check to pass

  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
      timeout: 5s
      retries: 5
```

**Real Scenario:**
```yaml
version: '3.8'
services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://admin:pass@db:5432/mydb
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: pass
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 40s
```

---

### Q18: What are volumes in Docker Compose? Explain bind mounts vs named volumes.

**Answer:**

**Bind Mounts** - Mount host directory:
```yaml
volumes:
  - ./app:/app            # Host directory : Container path
  - ./config:/etc/config:ro  # Read-only mount
```
- Maps host directory to container
- Changes on host visible in container immediately
- Good for **development** (hot reload)
- Path must exist on host
- Not portable across machines

**Named Volumes** - Docker managed:
```yaml
volumes:
  - app-data:/data        # Volume name : Container path

volumes:
  app-data:               # Define named volume
```
- Docker creates and manages volume
- Persists data when container stops
- Good for **production** (database storage)
- Portable across machines
- Better performance than bind mounts

**Comparison Table:**
```yaml
# Bind Mount - Development
volumes:
  - ./src:/app/src         # Source code for hot reload
  - ./config:/config       # Config files

# Named Volume - Production
volumes:
  - db-data:/var/lib/postgresql/data  # Persist database
  - cache:/app/cache                   # Persist cache
```

**Real Example:**
```yaml
version: '3.8'
services:
  web:
    build: .
    volumes:
      - ./src:/app/src            # Bind mount for development
      - app-cache:/app/.cache     # Named volume for cache

  db:
    image: postgres:15
    volumes:
      - postgres-data:/var/lib/postgresql/data  # Named volume for data

volumes:
  app-cache:
  postgres-data:
```

---

## GitHub Actions YAML

### Q19: Explain GitHub Actions workflow triggers (`on`).

**Answer:**

**Push trigger** - Run on code push:
```yaml
on:
  push:
    branches: [main, develop]        # Only these branches
    paths:                           # Only if these paths changed
      - 'src/**'
      - 'package.json'
    tags: ['v*']                     # Run on version tags
```

**Pull request trigger** - Run on PR:
```yaml
on:
  pull_request:
    branches: [main]                 # Only PRs targeting main
    types: [opened, synchronize]     # When opened or updated
```

**Schedule trigger** - Run on cron schedule:
```yaml
on:
  schedule:
    - cron: '0 2 * * *'              # Daily at 2 AM UTC
    - cron: '0 */6 * * *'            # Every 6 hours
```

**Manual trigger** - Run from GitHub UI:
```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        type: choice
        options: [staging, production]
```

**Release trigger** - Run on release:
```yaml
on:
  release:
    types: [published, created]
```

**Complete Example:**
```yaml
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 2 * * *'
  workflow_dispatch:
```

---

### Q20: What's the difference between `uses` and `run` in GitHub Actions steps?

**Answer:**

**`uses`** - Run pre-built action:
```yaml
steps:
  - uses: actions/checkout@v4
    # Runs action from marketplace or repository
  
  - uses: actions/setup-node@v4
    with:
      node-version: '18.x'
    # Passes input parameters to action
```
- Runs **pre-built action** from GitHub Marketplace
- Easier, less code
- Reusable across workflows
- Examples: checkout, setup-node, build-push-action

**`run`** - Execute shell command:
```yaml
steps:
  - run: npm install
    # Runs bash command
  
  - run: npm test
    shell: bash        # Explicit shell (bash, pwsh, cmd)
```
- Runs arbitrary **shell commands**
- More control and flexibility
- Custom scripts and logic
- Examples: npm commands, bash scripts

**Comparison:**
```yaml
# Using action (simpler)
- uses: actions/setup-node@v4
  with:
    node-version: '18.x'

# vs shell command (more control)
- run: |
    curl https://nodejs.org/dist/v18.0.0/node-v18.0.0-linux-x64.tar.xz | tar xJ
    export PATH=$PWD/node-v18.0.0-linux-x64/bin:$PATH
```

**When to use each:**
- **`uses`** - Common tasks (checkout, setup, publish)
- **`run`** - Custom logic, scripting, complex operations

---

### Q21: Explain GitHub Actions secrets and variables.

**Answer:**

**Secrets** - Encrypted sensitive data:
```yaml
steps:
  - name: Deploy
    run: ./deploy.sh
    env:
      API_KEY: ${{ secrets.API_KEY }}        # From Secrets
      DATABASE_PASSWORD: ${{ secrets.DB_PASS }}
```
- Encrypted and secure
- Not visible in logs or output
- Set in repository settings
- Used for: API keys, passwords, tokens

**Variables** - Non-sensitive configuration:
```yaml
env:
  ENVIRONMENT: production              # Global variable

steps:
  - name: Deploy
    run: ./deploy.sh
    env:
      DEPLOY_ENV: ${{ vars.DEPLOYMENT_ENV }}  # From Variables
      NODE_VERSION: '18.x'
```
- Visible in logs and output
- Non-sensitive data only
- Can be set globally or per-step
- Used for: Versions, URLs, configuration

**Difference:**
```yaml
# Secrets (encrypted)
${{ secrets.SECRET_NAME }}              # Created in repo settings

# Variables (plain text)
${{ vars.VARIABLE_NAME }}               # Created in repo settings

# Environment (hardcoded)
env:
  MY_VAR: value                         # Hardcoded in workflow
```

**Best Practices:**
```yaml
# ✅ Good
env:
  NODE_VERSION: '18.x'                  # Version - use variable
  API_URL: 'https://api.example.com'   # URL - use variable

# ✅ Good
env:
  DATABASE_PASSWORD: ${{ secrets.DB_PASS }}  # Password - use secret
  API_TOKEN: ${{ secrets.API_TOKEN }}       # Token - use secret

# ❌ Bad
env:
  API_KEY: 'sk_live_abc123xyz789'      # Hardcoded secret!
```

---

## Azure Pipelines YAML

### Q22: Explain the difference between stages, jobs, and tasks in Azure Pipelines.

**Answer:**

**Stages** - Logical phases (sequential):
```yaml
stages:
  - stage: Build
    displayName: 'Build Stage'
    jobs: [...]
  
  - stage: Test
    displayName: 'Test Stage'
    dependsOn: Build
    jobs: [...]
  
  - stage: Deploy
    displayName: 'Deploy Stage'
    dependsOn: Test
    jobs: [...]
```
- Run **sequentially** (one after another)
- Can depend on previous stages
- Organize pipeline logically
- Example: Build → Test → Deploy

**Jobs** - Work units (parallel within stage):
```yaml
- stage: Build
  jobs:
    - job: BuildWindows
      pool: windows-latest
      steps: [...]
    
    - job: BuildLinux
      pool: ubuntu-latest
      steps: [...]
```
- Run **in parallel** within stage
- Can run on different machines
- Depend on other jobs
- Example: Build on Windows and Linux simultaneously

**Tasks** - Individual operations:
```yaml
- stage: Build
  jobs:
    - job: Build
      steps:
        - task: NodeTool@0
          inputs:
            versionSpec: '18.x'
        
        - task: Npm@1
          inputs:
            command: 'install'
        
        - task: PublishBuildArtifacts@1
          inputs:
            pathToPublish: 'dist/'
```
- Built-in Azure operations
- Run **sequentially** within a job
- Examples: NodeTool, Npm, Docker, Deploy

**Hierarchy:**
```
Pipeline
├── Stage 1 (Sequential)
│   ├── Job A (Parallel)
│   ├── Job B (Parallel)
│   │   ├── Task 1 (Sequential)
│   │   ├── Task 2 (Sequential)
│   │   └── Task 3 (Sequential)
│   └── Job C (Parallel)
│
└── Stage 2 (Sequential, depends on Stage 1)
```

---

### Q23: Explain deployment strategies in Azure Pipelines (rolling vs canary).

**Answer:**

**Rolling Deployment** - Update gradually:
```yaml
strategy:
  rolling:
    maxParallel: 2          # Update max 2 pods at a time
  
  deploy:
    steps:
      - task: Kubernetes@1
        inputs:
          command: 'apply'
          arguments: '-f deployment.yaml'
```
- Replaces pods incrementally
- Example: 3 pods → update pod 1 → update pod 2 → update pod 3
- **Advantages**: No downtime, easy rollback, conservative
- **Disadvantages**: Slower deployment, testing complexity
- **Use Case**: Production deployments

**Canary Deployment** - Deploy to percentage:
```yaml
strategy:
  canary:
    increments: [10, 20, 50, 100]  # Deploy to 10% → 20% → 50% → 100%
  
  deploy:
    steps:
      - task: Kubernetes@1
        inputs:
          command: 'apply'
          arguments: '-f deployment.yaml'
```
- Deploy to small percentage first
- Validate before full rollout
- Example: Deploy to 10% users → Check metrics → Expand to 20%
- **Advantages**: Early detection of issues, fast rollback, zero downtime
- **Disadvantages**: Complex setup, multiple versions running
- **Use Case**: Critical production deployments, new features

**Comparison:**
```yaml
Rolling:            Canary:
10%  20%  30%      10%  ↓  20%  ↓  50%  ↓  100%
20%  30%  40%      Check metrics before expanding
30%  40%  50%      Rollback if issues found
100%               Green light after validation
```

---

## Problem-Solving & Debugging

### Q24: Your YAML file fails with "Unexpected indentation error". How would you debug it?

**Answer:**
**Common causes and solutions:**

1. **Mixed tabs and spaces:**
```yaml
# ❌ Wrong - Line 2 has tab
service:
	name: web         # Tab character!
  port: 8080          # Spaces

# ✅ Fix - Use only spaces
service:
  name: web           # 2 spaces
  port: 8080          # 2 spaces
```

**Debug steps:**
```bash
# Show invisible characters
cat -A deployment.yaml
# Output: service:$ ($ = newline)
#         ^Iname: web$ (^I = tab character)

# Fix with sed (replace tabs with spaces)
sed 's/\t/  /g' deployment.yaml > deployment_fixed.yaml

# Or use editor to convert tabs to spaces
```

2. **Inconsistent indentation:**
```yaml
# ❌ Wrong - Inconsistent spacing
deployment:
  name: myapp
    replicas: 3      # Wrong - 4 spaces!
  template:
    spec:
      containers:
      - name: app    # Wrong - inconsistent
```

**Solution:** Use linter to catch errors:
```bash
# Install yamllint
pip install yamllint

# Validate YAML file
yamllint deployment.yaml

# Output: deployment.yaml
#         3:5    error   wrong indentation: expected 2 but found 4
```

3. **Missing indentation:**
```yaml
# ❌ Wrong - Missing indentation for nested values
deployment:
name: myapp           # Wrong - should be indented!
replicas: 3

# ✅ Correct
deployment:
  name: myapp
  replicas: 3
```

---

### Q25: Your Kubernetes pod is stuck in "Pending" state. What could be wrong in the YAML?

**Answer:**
**Common causes:**

1. **Insufficient resources (requests too high):**
```yaml
# ❌ Problem - Requests more than available
resources:
  requests:
    memory: "100Gi"   # Cluster only has 50Gi total!
    cpu: "50"         # Cluster only has 10 cores!

# ✅ Solution - Realistic requests
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
```

**Debug:**
```bash
kubectl describe pod pod-name
# Output shows: "Insufficient memory", "Insufficient cpu"
```

2. **Wrong image:**
```yaml
# ❌ Problem - Image doesn't exist
image: myapp:1.0.0     # This image hasn't been pushed to registry!

# ✅ Solution - Use available image
image: nginx:latest    # Or build and push first
```

3. **No available nodes:**
```yaml
# ❌ Problem - Taints prevent scheduling
nodeSelector:
  disktype: ssd       # No node has this label!

# ✅ Solution - Check available labels
```

**Debug steps:**
```bash
# Check pod status and events
kubectl describe pod pod-name

# Check node resources
kubectl top nodes

# Check available images on nodes
kubectl get nodes
kubectl describe node node-name

# Check events
kubectl get events --sort-by='.lastTimestamp'
```

---

### Q26: Your Docker Compose services won't communicate. What's wrong?

**Answer:**
**Common issues:**

1. **Wrong hostname in connection string:**
```yaml
# ❌ Wrong - Using localhost
environment:
  DATABASE_URL: postgresql://localhost:5432/mydb  # Won't work!

# ✅ Correct - Use service name
environment:
  DATABASE_URL: postgresql://db:5432/mydb  # Service name: db
```

2. **Different networks:**
```yaml
# ❌ Wrong - Services on different networks
services:
  web:
    networks:
      - frontend      # Can't reach db on backend!
  
  db:
    networks:
      - backend       # web can't reach here

# ✅ Correct - Same network
services:
  web:
    networks:
      - app-network
  
  db:
    networks:
      - app-network

networks:
  app-network:
```

3. **Port binding issue:**
```yaml
# ❌ Wrong - Port not exposed
services:
  db:
    image: postgres
    # No ports defined - web can't connect!

# ✅ Correct
services:
  db:
    image: postgres
    ports:
      - "5432:5432"   # Or expose via network
```

**Debug:**
```bash
# Check connectivity from web container
docker-compose exec web bash
ping db                    # Should work
curl http://db:5432       # Test connection

# Check logs
docker-compose logs -f db
docker-compose logs -f web

# Inspect network
docker network inspect <network_name>
```

---

## Real-World Scenarios

### Q27: Design a complete YAML for a Node.js app with PostgreSQL in Docker Compose.

**Answer:**
```yaml
version: '3.8'

services:
  # Node.js Application
  web:
    build:
      context: .
      dockerfile: Dockerfile
    image: myapp:latest
    container_name: myapp-web
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://app_user:app_pass@db:5432/mydb
      - REDIS_URL=redis://redis:6379
      - SESSION_SECRET=${SESSION_SECRET}
      - API_KEY=${API_KEY}
    env_file:
      - .env.production
    volumes:
      - ./src:/app/src          # For development hot reload
      - app-logs:/app/logs      # Persistent logs
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  # PostgreSQL Database
  db:
    image: postgres:15-alpine
    container_name: myapp-db
    restart: unless-stopped
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD: app_pass
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app_user -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: myapp-redis
    restart: unless-stopped
    volumes:
      - redis-data:/data
    ports:
      - "6379:6379"
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 3

  # Nginx Reverse Proxy (Optional)
  nginx:
    image: nginx:alpine
    container_name: myapp-nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - web
    networks:
      - app-network

volumes:
  postgres-data:
    driver: local
  redis-data:
    driver: local
  app-logs:
    driver: local

networks:
  app-network:
    driver: bridge
```

---

### Q28: Design a Kubernetes deployment for a production Node.js application.

**Answer:**
```yaml
---
# Namespace
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    environment: production

---
# ConfigMap - Non-sensitive configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: production
data:
  NODE_ENV: "production"
  LOG_LEVEL: "info"
  PORT: "3000"
  CACHE_ENABLED: "true"
  CACHE_TTL: "3600"

---
# Secret - Sensitive data
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
  namespace: production
type: Opaque
stringData:
  DATABASE_URL: postgresql://user:password@postgres:5432/mydb
  REDIS_URL: redis://redis:6379
  SESSION_SECRET: your-session-secret-here
  API_KEY: your-api-key-here
  JWT_SECRET: your-jwt-secret

---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: production
  labels:
    app: myapp
    version: v1.0.0
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
        version: v1.0.0
    spec:
      containers:
      - name: app
        image: myregistry/myapp:1.0.0
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3000
          name: http
          protocol: TCP
        
        # Environment variables
        envFrom:
        - configMapRef:
            name: app-config
        - secretRef:
            name: app-secrets
        
        # Resource constraints
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        
        # Liveness probe
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        
        # Readiness probe
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 2
        
        # Security context
        securityContext:
          runAsNonRoot: true
          runAsUser: 1000
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
        
        # Volumes
        volumeMounts:
        - name: tmp
          mountPath: /tmp
        - name: logs
          mountPath: /app/logs
      
      volumes:
      - name: tmp
        emptyDir: {}
      - name: logs
        emptyDir: {}

---
# Service
apiVersion: v1
kind: Service
metadata:
  name: myapp
  namespace: production
  labels:
    app: myapp
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
    protocol: TCP
    name: http

---
# HorizontalPodAutoscaler
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
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
```

---

## Tricky Questions

### Q29: What happens if you reference a secret in a ConfigMap value?

**Answer:**
**You can't - ConfigMaps are visible, Secrets are not.**

```yaml
# ❌ Wrong - Exposing secret in ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: config
data:
  database_password: "my_secret_password"  # Visible to anyone!

# ✅ Correct - Use Secret for passwords
apiVersion: v1
kind: Secret
metadata:
  name: secrets
type: Opaque
stringData:
  database_password: "my_secret_password"
```

**Why it matters:**
- ConfigMaps are **not encrypted** - visible in etcd
- Secrets are **base64 encoded** (should be at-rest encrypted)
- ConfigMaps for: versions, URLs, feature flags
- Secrets for: passwords, tokens, API keys

---

### Q30: Can you have the same service name in different namespaces?

**Answer:**
**Yes! Service names are unique within namespace, not globally.**

```yaml
# production namespace
---
apiVersion: v1
kind: Namespace
metadata:
  name: production

---
apiVersion: v1
kind: Service
metadata:
  name: api          # Name: "api"
  namespace: production
spec:
  ports:
  - port: 80

# staging namespace
---
apiVersion: v1
kind: Namespace
metadata:
  name: staging

---
apiVersion: v1
kind: Service
metadata:
  name: api          # Same name: "api"
  namespace: staging
spec:
  ports:
  - port: 80
```

**Access across namespaces:**
```yaml
# Within same namespace
api:3000

# Across namespaces
api.production.svc.cluster.local:3000
api.staging.svc.cluster.local:3000
```

---

### Q31: What's the difference between `value` and `valueFrom` in Kubernetes env vars?

**Answer:**

**`value`** - Hardcoded string:
```yaml
env:
- name: APP_NAME
  value: "myapp"          # Direct value
- name: PORT
  value: "3000"
```

**`valueFrom`** - Reference from ConfigMap/Secret:
```yaml
env:
# From ConfigMap
- name: DATABASE_HOST
  valueFrom:
    configMapKeyRef:
      name: app-config    # ConfigMap name
      key: db_host        # Key in ConfigMap

# From Secret
- name: DATABASE_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret     # Secret name
      key: password       # Key in Secret

# From pod metadata
- name: POD_NAME
  valueFrom:
    fieldRef:
      fieldPath: metadata.name

# From container resources
- name: MEMORY_LIMIT
  valueFrom:
    resourceFieldRef:
      containerName: app
      resource: limits.memory
```

**When to use:**
- **`value`**: Static configuration, non-sensitive data
- **`valueFrom`**: Dynamic configuration, sensitive data, metadata

---

### Q32: You have a YAML with anchors that works locally but fails in production. Why?

**Answer:**
**Anchors are file-scoped - they don't work across multiple files.**

```yaml
# defaults.yaml
defaults: &defaults
  timeout: 30
  retries: 3

# deployment.yaml - ❌ Fails!
deployment:
  <<: *defaults     # Error: anchor 'defaults' not found!
```

**Solutions:**

1. **Combine into single file:**
```yaml
# combined.yaml
defaults: &defaults
  timeout: 30
  retries: 3

deployment:
  <<: *defaults     # ✅ Works - same file
```

2. **Use Kustomize overlays (for Kubernetes):**
```yaml
# base/defaults.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: defaults
data:
  timeout: "30"
  retries: "3"

# overlays/production/kustomization.yaml
patchesStrategicMerge:
- deployment.yaml
```

3. **Use template variables (for CI/CD):**
```yaml
# template.yaml
deployment:
  timeout: ${TIMEOUT:-30}
  retries: ${RETRIES:-3}
```

---

### Q33: Your GitHub Actions workflow uses `secrets.DB_PASSWORD` but it's empty. What could be wrong?

**Answer:**
**Secret exists but isn't accessible to this workflow.**

**Possible causes:**

1. **Secret doesn't exist:**
```bash
# Check: Repository Settings → Secrets
# Add secret: DB_PASSWORD = value
```

2. **Secret has wrong scope:**
```yaml
# ❌ Organization secret not accessible to repo
${{ secrets.ORG_SECRET }}

# ✅ Repository secret
${{ secrets.DB_PASSWORD }}
```

3. **Wrong reference format:**
```yaml
# ❌ Wrong
${{ secrets.db-password }}  # Secrets are uppercase!

# ✅ Correct
${{ secrets.DB_PASSWORD }}
```

4. **Workflow permissions issue:**
```yaml
# ❌ Workflow can't access secrets
permissions:
  contents: read    # Doesn't include secrets

# ✅ Explicit permission
permissions:
  contents: read
  secrets: write
```

**Debug:**
```yaml
- name: Check secret exists
  run: |
    if [ -z "${{ secrets.DB_PASSWORD }}" ]; then
      echo "Secret is empty!"
      exit 1
    fi
```

---

### Q34: Explain what happens when you scale a Kubernetes deployment while pods are starting up.

**Answer:**
**Kubernetes handles it gracefully:**

```yaml
# Initial deployment
replicas: 3

# Running pods: 0 (initializing)
# Pod 1: Starting
# Pod 2: Starting
# Pod 3: Starting
# Readiness probe: Failing (app not ready)

# While initializing, scale up to 5
replicas: 5

# New pods added:
# Pod 4: Starting
# Pod 5: Starting

# Once ready:
# Pod 1: Ready ✓
# Pod 2: Ready ✓
# Pod 3: Ready ✓
# Pod 4: Ready ✓
# Pod 5: Ready ✓
# Traffic routed to ready pods only
```

**Key behavior:**
1. Kubernetes creates additional pods immediately
2. Readiness probe filters traffic to ready pods only
3. Terminating pods: Graceful shutdown (30s default)
4. Unready pods: No traffic but still running

---

### Q35: Your Docker Compose healthcheck passes, but service still fails. Why?

**Answer:**
**Healthcheck only reports status - doesn't guarantee functionality.**

```yaml
services:
  web:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      # Returns 200 OK ✓
    depends_on:
      - db
      # But depends_on only waits for container start, not health!

  db:
    healthcheck:
      test: ["CMD-SHELL", "pg_isready"]
      # Returns "accepting connections" ✓
```

**The issue:**
- Healthcheck says "app is running"
- But app might not be **connected to database**
- Healthcheck doesn't test the entire stack

**Better healthcheck:**
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:3000/status"]
  # This endpoint checks:
  # - App is running
  # - Database connection OK
  # - Cache is responsive
  # - All dependencies are ready
```

---

## Summary

### Key Takeaways

✅ **YAML Theory**
- WHAT: Data serialization format
- WHY: Readability, version control friendly
- HOW: Parser converts to data structures
- WHEN: Configs, CI/CD, IaC

✅ **Common Patterns**
- Use 2-space indentation consistently
- Quote strings with special characters
- Use anchors for duplication
- Separate config from code

✅ **Kubernetes**
- apiVersion, kind, metadata, spec structure
- requests vs limits for resources
- livenessProbe vs readinessProbe
- Selectors connect deployments to pods

✅ **Docker Compose**
- depends_on waits for container start, use healthcheck for readiness
- Named volumes for production, bind mounts for development
- Networks isolate services for security

✅ **CI/CD**
- GitHub Actions: on, jobs, steps, uses vs run
- Azure Pipelines: stages, jobs, tasks hierarchy
- Both support triggers, secrets, variables, matrices

✅ **Debugging**
- Use linters (yamllint) to catch syntax errors
- Check events and logs for runtime issues
- Validate assumptions with kubectl describe

---

**Practice:** Try to implement scenarios from Q27-Q28 in your environment!
