# 2-Week Intensive Backend Engineering Course

**From Fundamentals to Advanced Architecture**

> This course covers all 20 backend concepts from [backend_concepts.md](backend_concepts.md), organized into a progressive 10-day learning plan (weekdays only). Each day includes theory, hands-on exercises, and a mini-project.

---

## Course Overview

| Week | Focus | Concepts Covered |
|------|-------|-----------------|
| Week 1 (Days 1-5) | Fundamentals & Core Engineering | Tier 1 (concepts 1-5) + Tier 2 (concepts 6-10) |
| Week 2 (Days 6-10) | Architecture, Scalability & Specialization | Tier 3 (concepts 11-15) + Tier 4 (concepts 16-20) |

**Daily Structure:**
- Morning (3h): Theory + guided examples
- Afternoon (3h): Hands-on exercises + mini-project
- Evening (1h): Review + reading for next day

**Prerequisites:**
- Comfortable with at least one programming language (Python, Node.js, Go, or Java)
- Basic terminal/command-line skills
- Git basics

**Recommended Stack for Exercises:**
- Language: Python (FastAPI) or Node.js (Express)
- Database: PostgreSQL + Redis
- Tools: Docker, Postman/curl, VS Code

---

## Week 1 — Fundamentals & Core Engineering

---

### Day 1: HTTP & REST APIs

> Reference: [backend_concepts.md — Concept #1](backend_concepts.md#1-http--rest-apis)

#### Theory

**1.1 How the Web Works**
- Client-server model
- TCP/IP basics — how data travels across networks
- DNS resolution — translating domain names to IP addresses
- HTTP as an application-layer protocol

**1.2 HTTP Deep Dive**
- Request structure: method, URL, headers, body
- Response structure: status line, headers, body
- HTTP methods and their semantics:

| Method | Purpose | Idempotent | Safe |
|--------|---------|------------|------|
| GET | Retrieve resource | Yes | Yes |
| POST | Create resource | No | No |
| PUT | Replace resource | Yes | No |
| PATCH | Partial update | No | No |
| DELETE | Remove resource | Yes | No |

- Status codes families:
  - `2xx` — Success (200 OK, 201 Created, 204 No Content)
  - `3xx` — Redirection (301 Moved, 304 Not Modified)
  - `4xx` — Client error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests)
  - `5xx` — Server error (500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable)

- Important headers:
  - `Content-Type` — what format the body is in (application/json, text/html)
  - `Authorization` — credentials for authentication
  - `Accept` — what formats the client can handle
  - `Cache-Control` — caching directives
  - `CORS headers` — Cross-Origin Resource Sharing

**1.3 REST Principles**
- **Statelessness** — each request contains all information needed; server stores no session state
- **Resource-oriented** — URLs represent resources, not actions
- **Uniform interface** — consistent URL patterns and HTTP method usage
- **HATEOAS** — Hypermedia as the Engine of Application State (links in responses)

REST URL conventions:
```
GET    /api/v1/users          → List users
POST   /api/v1/users          → Create user
GET    /api/v1/users/:id      → Get single user
PUT    /api/v1/users/:id      → Replace user
PATCH  /api/v1/users/:id      → Update user fields
DELETE /api/v1/users/:id      → Delete user
GET    /api/v1/users/:id/orders → List user's orders (nested resource)
```

**1.4 Idempotency**
- Why it matters: network retries, duplicate requests
- GET, PUT, DELETE are idempotent by design
- POST is NOT idempotent — use idempotency keys for critical operations
- Example: payment APIs use `Idempotency-Key` header to prevent double charges

#### Hands-On Exercises

1. **Build a REST API** — Create a simple task management API with CRUD endpoints using FastAPI or Express
2. **Test with curl/Postman** — Exercise all HTTP methods, inspect headers and status codes
3. **Implement proper error responses** — Return consistent JSON error format:
   ```json
   {
     "error": {
       "code": "RESOURCE_NOT_FOUND",
       "message": "Task with id 42 not found",
       "status": 404
     }
   }
   ```

#### Mini-Project
Build a **Bookstore API** with endpoints for books, authors, and reviews. Implement proper HTTP methods, status codes, and error handling.

---

### Day 2: Relational Databases (SQL)

> Reference: [backend_concepts.md — Concept #2](backend_concepts.md#2-databases--relational-sql)

#### Theory

**2.1 Relational Model Fundamentals**
- Tables, rows, columns — the building blocks
- Primary keys (natural vs surrogate)
- Foreign keys and referential integrity
- Data types: choosing the right type matters for performance and correctness

**2.2 SQL Essentials**
- CRUD operations:
  ```sql
  -- Create
  INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');

  -- Read
  SELECT name, email FROM users WHERE active = true ORDER BY name LIMIT 10;

  -- Update
  UPDATE users SET email = 'new@example.com' WHERE id = 1;

  -- Delete
  DELETE FROM users WHERE id = 1;
  ```

- Joins — the heart of relational databases:
  ```sql
  -- INNER JOIN: only matching rows from both tables
  SELECT u.name, o.total
  FROM users u
  INNER JOIN orders o ON u.id = o.user_id;

  -- LEFT JOIN: all rows from left table, matching from right
  SELECT u.name, COUNT(o.id) as order_count
  FROM users u
  LEFT JOIN orders o ON u.id = o.user_id
  GROUP BY u.name;
  ```

- Aggregations: COUNT, SUM, AVG, MIN, MAX + GROUP BY + HAVING

**2.3 Schema Design**
- Normalization forms (1NF, 2NF, 3NF) — eliminate redundancy
  - 1NF: atomic values, no repeating groups
  - 2NF: no partial dependencies on composite keys
  - 3NF: no transitive dependencies
- When to denormalize — read performance vs write complexity tradeoff
- Common patterns:
  - One-to-many: foreign key on the "many" side
  - Many-to-many: junction/bridge table
  - One-to-one: foreign key with UNIQUE constraint

**2.4 Indexing**
- What indexes do: trade write speed + storage for read speed
- B-Tree indexes — the default, good for range queries and equality
- Composite indexes — column order matters (leftmost prefix rule)
- Covering indexes — include all columns needed by a query
- When NOT to index: small tables, rarely-queried columns, high-write tables

**2.5 Transactions & ACID**
- **Atomicity** — all or nothing
- **Consistency** — database stays valid
- **Isolation** — concurrent transactions don't interfere
- **Durability** — committed data survives crashes

- Isolation levels:
  | Level | Dirty Read | Non-Repeatable Read | Phantom Read |
  |-------|-----------|-------------------|-------------|
  | Read Uncommitted | Yes | Yes | Yes |
  | Read Committed | No | Yes | Yes |
  | Repeatable Read | No | No | Yes |
  | Serializable | No | No | No |

**2.6 Query Optimization**
- `EXPLAIN ANALYZE` — understand query execution plans
- Common optimizations: add indexes, avoid SELECT *, use WHERE before JOIN, limit result sets
- N+1 query problem: fetching related data in a loop instead of a single JOIN

#### Hands-On Exercises

1. **Design a schema** — Create tables for an e-commerce system (users, products, orders, order_items, categories)
2. **Write complex queries** — JOINs, subqueries, CTEs, window functions
3. **Optimize queries** — Use EXPLAIN ANALYZE to identify slow queries and add appropriate indexes
4. **Transaction exercise** — Implement a money transfer between accounts with proper transaction handling

#### Mini-Project
Extend the Bookstore API from Day 1 with a PostgreSQL database. Implement proper schema, migrations, and optimized queries.

---

### Day 3: Authentication, Authorization & API Design

> Reference: [backend_concepts.md — Concepts #3 and #5](backend_concepts.md#3-authentication--authorization)

#### Theory

**3.1 Authentication — "Who are you?"**

- **Session-based authentication:**
  ```
  1. Client sends credentials (username/password)
  2. Server validates, creates session, stores in DB/Redis
  3. Server returns session ID in Set-Cookie header
  4. Client sends cookie with every subsequent request
  5. Server looks up session to identify user
  ```
  - Pros: easy to revoke, server controls state
  - Cons: requires server-side storage, harder to scale across servers

- **JWT (JSON Web Tokens):**
  ```
  Header: { "alg": "HS256", "typ": "JWT" }
  Payload: { "sub": "user123", "role": "admin", "exp": 1700000000 }
  Signature: HMAC-SHA256(base64(header) + "." + base64(payload), secret)
  ```
  - Pros: stateless, scalable, good for microservices
  - Cons: can't easily revoke, token size grows with claims
  - Best practices: short expiry (15 min), use refresh tokens, store in httpOnly cookies

- **OAuth2 flows:**
  - Authorization Code flow (web apps) — most secure
  - Client Credentials flow (service-to-service)
  - PKCE extension (mobile/SPA apps)

- **Password security:**
  - NEVER store plain text passwords
  - Use bcrypt or argon2 with proper salt rounds
  - Implement rate limiting on login endpoints

**3.2 Authorization — "What can you do?"**

- **RBAC (Role-Based Access Control):**
  ```
  User → Role → Permissions
  Example: user "Alice" → role "editor" → permissions ["read", "write", "publish"]
  ```

- **ABAC (Attribute-Based Access Control):**
  - Decisions based on user attributes, resource attributes, and environment
  - Example: "Allow if user.department == resource.department AND time.hour BETWEEN 9 AND 17"

- **Middleware pattern** for authorization:
  ```python
  @app.route("/admin/users")
  @require_auth
  @require_role("admin")
  def list_all_users():
      ...
  ```

**3.3 API Design Principles**

- **Versioning strategies:**
  - URL path: `/api/v1/users` (most common, explicit)
  - Header: `Accept: application/vnd.api.v1+json`
  - Query param: `/api/users?version=1`

- **Pagination:**
  - Offset-based: `?page=2&limit=20` — simple but slow for large datasets (requires counting)
  - Cursor-based: `?cursor=abc123&limit=20` — performant, stable for real-time data
    ```json
    {
      "data": [...],
      "pagination": {
        "next_cursor": "eyJpZCI6MTAwfQ==",
        "has_more": true
      }
    }
    ```

- **Rate limiting:**
  - Return `429 Too Many Requests` with `Retry-After` header
  - Use `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset` headers

- **OpenAPI / Swagger:**
  - Document all endpoints, request/response schemas, authentication
  - Auto-generate client SDKs and interactive documentation

#### Hands-On Exercises

1. **Implement JWT auth** — Login endpoint that returns access + refresh tokens, protected routes with middleware
2. **Build RBAC** — Create roles (admin, editor, viewer) and protect endpoints based on permissions
3. **Add pagination** — Implement both offset-based and cursor-based pagination for the books list
4. **Document with OpenAPI** — Add Swagger/OpenAPI docs to your API

#### Mini-Project
Add authentication, authorization, and pagination to the Bookstore API. Only admins can create/delete books, editors can update, anyone authenticated can read.

---

### Day 4: Caching & Security Fundamentals

> Reference: [backend_concepts.md — Concepts #4 and #10](backend_concepts.md#4-caching)

#### Theory

**4.1 Why Cache?**
- Reduce database load
- Improve response times (milliseconds vs seconds)
- Lower infrastructure costs
- The two hard problems: cache invalidation and naming things

**4.2 Caching Strategies**

| Strategy | Description | Best For |
|----------|-------------|----------|
| Cache-Aside (Lazy Loading) | App checks cache first, on miss reads from DB and populates cache | Read-heavy, tolerance for stale data |
| Write-Through | App writes to cache and DB simultaneously | Data consistency critical |
| Write-Behind (Write-Back) | App writes to cache, cache async writes to DB | High write throughput |
| Read-Through | Cache sits between app and DB, loads on miss automatically | Simplified app code |

- **Cache-Aside pattern** (most common):
  ```python
  def get_user(user_id):
      # 1. Check cache
      cached = redis.get(f"user:{user_id}")
      if cached:
          return json.loads(cached)

      # 2. Cache miss — read from DB
      user = db.query("SELECT * FROM users WHERE id = %s", user_id)

      # 3. Populate cache with TTL
      redis.setex(f"user:{user_id}", 3600, json.dumps(user))
      return user
  ```

**4.3 Cache Invalidation**
- **TTL (Time-To-Live):** simplest approach, set expiration time
- **Event-driven invalidation:** delete cache on write/update
- **Cache stampede prevention:** use locks or probabilistic early expiration
- **Versioned keys:** `user:123:v2` — change version to invalidate

**4.4 Redis as a Cache**
- In-memory key-value store — sub-millisecond reads
- Data structures: strings, hashes, lists, sets, sorted sets
- Use cases beyond caching: session storage, leaderboards, pub/sub, rate limiting
- Key Redis commands:
  ```
  SET key value EX 3600     # Set with 1h expiry
  GET key                    # Get value
  DEL key                    # Delete
  MGET key1 key2 key3        # Batch get
  INCR counter               # Atomic increment
  ```

**4.5 CDN Caching**
- Cache static assets (images, CSS, JS) at edge locations
- `Cache-Control` header directives: `max-age`, `no-cache`, `no-store`, `public`, `private`
- Cache busting with file hashes: `app.a1b2c3.js`

**4.6 Security Fundamentals**

- **OWASP Top 10** — the most critical web security risks:
  1. **Injection** (SQL, NoSQL, command) — always use parameterized queries
  2. **Broken Authentication** — secure session management, MFA
  3. **Sensitive Data Exposure** — encrypt at rest and in transit
  4. **XSS (Cross-Site Scripting)** — sanitize output, use CSP headers
  5. **CSRF (Cross-Site Request Forgery)** — use anti-CSRF tokens
  6. **IDOR (Insecure Direct Object Reference)** — always verify authorization

- **Input validation:**
  ```python
  # BAD — SQL injection vulnerability
  query = f"SELECT * FROM users WHERE name = '{user_input}'"

  # GOOD — parameterized query
  query = "SELECT * FROM users WHERE name = %s"
  cursor.execute(query, (user_input,))
  ```

- **Secrets management:**
  - Never hardcode secrets in source code
  - Use environment variables or secret managers (HashiCorp Vault, AWS Secrets Manager)
  - Rotate secrets regularly
  - Use `.gitignore` to exclude `.env` files

- **TLS/HTTPS:**
  - Encrypt all traffic in transit
  - HSTS (HTTP Strict Transport Security) header
  - Certificate management (Let's Encrypt for free certs)

- **Principle of least privilege:**
  - Database users should only have necessary permissions
  - API keys should be scoped to required resources
  - Service accounts should have minimal roles

#### Hands-On Exercises

1. **Add Redis caching** — Cache book listings and individual book lookups with TTL-based invalidation
2. **Implement cache invalidation** — When a book is updated/deleted, invalidate relevant cache entries
3. **Security audit** — Review your API for OWASP vulnerabilities, fix any issues
4. **Add input validation** — Use a validation library (Pydantic, Joi, Zod) to validate all request bodies
5. **Implement rate limiting** — Add rate limiting middleware using Redis

#### Mini-Project
Add Redis caching and comprehensive security hardening to the Bookstore API. Implement input validation, parameterized queries, rate limiting, and proper headers.

---

### Day 5: Async Processing, Message Queues & NoSQL

> Reference: [backend_concepts.md — Concepts #6 and #7](backend_concepts.md#6-asynchronous-processing--message-queues)

#### Theory

**5.1 Why Asynchronous Processing?**
- Some operations are too slow for synchronous request-response:
  - Sending emails/notifications
  - Generating reports or PDFs
  - Processing uploaded files (images, videos)
  - Calling slow third-party APIs
  - Data aggregation and analytics

- Pattern: accept request immediately, process in background, notify when done
  ```
  Client → API: "Generate my report"
  API → Queue: publish job {type: "report", userId: 123}
  API → Client: 202 Accepted {jobId: "abc", statusUrl: "/jobs/abc"}
  Worker picks up job → generates report → stores result
  Client polls statusUrl → eventually gets 200 with download link
  ```

**5.2 Message Queues**

- **RabbitMQ** — traditional message broker
  - AMQP protocol
  - Exchanges, queues, bindings
  - Good for: task distribution, RPC patterns
  - Delivery guarantees: at-most-once or at-least-once

- **Apache Kafka** — distributed event streaming platform
  - Topics, partitions, consumer groups
  - Durable, ordered, replayable message log
  - Good for: event streaming, log aggregation, real-time pipelines
  - High throughput (millions of messages/sec)

- **AWS SQS** — managed queue service
  - Standard (at-least-once, best-effort ordering) vs FIFO (exactly-once, ordered)
  - No infrastructure management
  - Good for: AWS-native architectures

**5.3 Job Queue Patterns**

- **Dead Letter Queue (DLQ):**
  - Failed messages go to a separate queue after N retries
  - Prevents poison messages from blocking the queue
  - Monitor DLQ for alerting

- **Retry strategies:**
  - Immediate retry → exponential backoff → DLQ
  - Example: retry at 1s, 5s, 30s, 5min, then DLQ

- **Idempotent consumers:**
  - Messages may be delivered more than once
  - Use unique message IDs to deduplicate
  - Make processing idempotent: processing same message twice = same result

**5.4 NoSQL Databases**

- **When to choose NoSQL over SQL:**
  | Choose SQL When | Choose NoSQL When |
  |----------------|------------------|
  | Complex relationships/joins needed | Flexible/evolving schema |
  | Strong consistency required | Horizontal scaling needed |
  | ACID transactions critical | High write throughput |
  | Structured, well-defined data | Document/key-value data model fits |

- **MongoDB (Document Store):**
  ```javascript
  // Flexible schema — documents in same collection can differ
  db.products.insertOne({
    name: "Laptop",
    specs: { ram: "16GB", cpu: "M2" },  // nested document
    tags: ["electronics", "portable"],    // arrays
    reviews: [{ user: "Alice", rating: 5 }]
  });

  // Rich query language
  db.products.find({ "specs.ram": "16GB", tags: "electronics" });
  ```

- **Redis (Key-Value Store):**
  - Beyond caching: sorted sets for leaderboards, pub/sub for real-time, streams for event processing
  - Atomic operations for counters, locks, rate limiting

- **CAP Theorem:**
  ```
  In a distributed system, you can only guarantee 2 of 3:
  - Consistency: every read gets the most recent write
  - Availability: every request gets a response
  - Partition Tolerance: system works despite network failures

  Since partitions ARE inevitable in distributed systems:
  → CP systems: sacrifice availability (e.g., MongoDB, HBase)
  → AP systems: sacrifice consistency (e.g., Cassandra, DynamoDB)
  ```

#### Hands-On Exercises

1. **Set up a message queue** — Use Redis (BullMQ) or RabbitMQ to process background jobs
2. **Build an email notification system** — When a new review is posted, queue an email notification
3. **Implement retry + DLQ** — Handle failed jobs with exponential backoff and dead letter queue
4. **MongoDB exercise** — Model a product catalog with nested attributes and array fields
5. **Compare queries** — Write the same query in SQL and MongoDB, compare approaches

#### Mini-Project
Add a background job system to the Bookstore API: when a new book is added, asynchronously generate a thumbnail, send notification emails to subscribers, and update search indexes. Use Redis or RabbitMQ as the message broker.

---

## Week 2 — Architecture, Scalability & Specialization

---

### Day 6: Containerization & CI/CD

> Reference: [backend_concepts.md — Concepts #8 and #15](backend_concepts.md#8-containerization--deployment)

#### Theory

**6.1 Docker Fundamentals**

- **Why containers?**
  - "Works on my machine" → works everywhere
  - Consistent environments: dev, staging, production
  - Isolation: each service has its own dependencies
  - Fast startup compared to VMs

- **Core concepts:**
  - **Image:** read-only template with application + dependencies (like a class)
  - **Container:** running instance of an image (like an object)
  - **Dockerfile:** recipe to build an image
  - **Volume:** persistent storage that survives container restarts
  - **Network:** communication between containers

- **Writing efficient Dockerfiles:**
  ```dockerfile
  # Use specific version tags, not 'latest'
  FROM python:3.12-slim

  # Set working directory
  WORKDIR /app

  # Copy dependency files first (layer caching optimization)
  COPY requirements.txt .
  RUN pip install --no-cache-dir -r requirements.txt

  # Copy application code (changes frequently — last layer)
  COPY . .

  # Run as non-root user (security best practice)
  RUN adduser --disabled-password appuser
  USER appuser

  EXPOSE 8000
  CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
  ```

- **Docker Compose** for local multi-service development:
  ```yaml
  version: "3.8"
  services:
    api:
      build: .
      ports:
        - "8000:8000"
      environment:
        - DATABASE_URL=postgresql://user:pass@db:5432/mydb
        - REDIS_URL=redis://cache:6379
      depends_on:
        - db
        - cache

    db:
      image: postgres:16
      volumes:
        - pgdata:/var/lib/postgresql/data
      environment:
        - POSTGRES_PASSWORD=pass

    cache:
      image: redis:7-alpine

  volumes:
    pgdata:
  ```

**6.2 Kubernetes Concepts (Overview)**
- **Pod:** smallest deployable unit (one or more containers)
- **Deployment:** manages pod replicas, rolling updates
- **Service:** stable network endpoint for pods
- **Ingress:** external access rules (HTTP routing)
- **ConfigMap/Secret:** externalized configuration

**6.3 CI/CD Pipelines**

- **Continuous Integration (CI):**
  - Run on every push/PR: lint, test, build
  - Fast feedback loop — catch bugs early
  - Example GitHub Actions workflow:
    ```yaml
    name: CI
    on: [push, pull_request]
    jobs:
      test:
        runs-on: ubuntu-latest
        services:
          postgres:
            image: postgres:16
            env:
              POSTGRES_PASSWORD: test
        steps:
          - uses: actions/checkout@v4
          - uses: actions/setup-python@v5
            with:
              python-version: "3.12"
          - run: pip install -r requirements.txt
          - run: pytest --cov=app tests/
    ```

- **Continuous Deployment (CD):**
  - **Blue/Green deployment:** run two identical environments, switch traffic
  - **Canary deployment:** route small % of traffic to new version, monitor, then roll out
  - **Rolling deployment:** gradually replace old pods with new ones

**6.4 Testing Strategies**

- **Testing pyramid:**
  ```
       /  E2E  \        ← Few, slow, expensive
      / Integration \    ← Medium amount
     /    Unit Tests  \  ← Many, fast, cheap
  ```

- **Unit tests:** test individual functions in isolation
- **Integration tests:** test components working together (API + DB)
- **E2E tests:** test full user workflows
- **Test doubles:** mocks (verify behavior), stubs (return fixed data), fakes (simplified implementations)

- **Database migration strategies:**
  - Forward-only migrations with version tracking
  - Zero-downtime: add column → deploy code that uses it → backfill → add constraint
  - Never drop columns in the same deploy that stops using them

#### Hands-On Exercises

1. **Dockerize the Bookstore API** — Write Dockerfile and docker-compose.yml with PostgreSQL and Redis
2. **Write tests** — Unit tests for business logic, integration tests for API endpoints with a real database
3. **Set up CI** — Create a GitHub Actions workflow that runs tests on every push
4. **Practice migrations** — Create and run database migrations, practice a zero-downtime column rename

#### Mini-Project
Containerize the entire Bookstore system with Docker Compose (API + PostgreSQL + Redis + worker). Add a CI pipeline with linting, testing, and Docker build steps.

---

### Day 7: Logging, Observability & Concurrency

> Reference: [backend_concepts.md — Concepts #9 and #11](backend_concepts.md#9-logging-monitoring--observability)

#### Theory

**7.1 Structured Logging**

- **Why structured logs?**
  - Unstructured: `"User 123 failed to login from 1.2.3.4"` — hard to parse
  - Structured: `{"event": "login_failed", "user_id": 123, "ip": "1.2.3.4", "timestamp": "2026-04-08T10:00:00Z"}` — easily searchable, filterable

- **Log levels:** DEBUG → INFO → WARNING → ERROR → CRITICAL
  - DEBUG: detailed diagnostic info (development only)
  - INFO: general operational events (request processed, job completed)
  - WARNING: unexpected but handled situations (deprecated API used, retry triggered)
  - ERROR: failures that need attention (database connection failed, external API timeout)
  - CRITICAL: system is down or unusable

- **What to log:**
  - Request ID (correlation ID) for tracing
  - User ID (for audit trails)
  - Duration of operations
  - Error details with stack traces
  - Business events (order placed, payment processed)

- **What NOT to log:**
  - Passwords, tokens, API keys
  - Credit card numbers, SSNs
  - Full request/response bodies (in production)

**7.2 Observability Stack**

- **The three pillars:**
  1. **Logs** — discrete events (ELK Stack, Loki)
  2. **Metrics** — numerical measurements over time (Prometheus + Grafana)
  3. **Traces** — request flow across services (OpenTelemetry + Jaeger)

- **Key metrics to track:**
  - RED metrics (for services): Rate, Errors, Duration
  - USE metrics (for resources): Utilization, Saturation, Errors
  - Business metrics: orders/minute, signup conversion, revenue

- **Alerting best practices:**
  - Alert on symptoms (high error rate), not causes (high CPU)
  - Avoid alert fatigue — only alert on actionable items
  - Include runbook links in alerts

**7.3 Concurrency & Multithreading**

- **Why concurrency matters:**
  - Handle multiple requests simultaneously
  - Utilize multi-core CPUs
  - Avoid blocking on I/O operations

- **Concurrency models:**

  | Model | Description | Used By |
  |-------|-------------|---------|
  | Multi-threading | OS threads, shared memory | Java, C#, Go |
  | Event Loop | Single thread, non-blocking I/O | Node.js, Nginx |
  | Async/Await | Cooperative multitasking with coroutines | Python asyncio, Rust tokio |
  | Actor Model | Isolated actors communicating via messages | Erlang, Akka |

- **Thread safety issues:**
  - **Race condition:** two threads read-modify-write same data simultaneously
    ```python
    # UNSAFE — race condition
    balance = get_balance(account_id)  # Thread A reads 100
    # Thread B also reads 100 here
    balance -= 50                       # Thread A: 50
    # Thread B: 50
    set_balance(account_id, balance)    # Both write 50, but should be 0!
    ```
  - **Deadlock:** Thread A holds lock 1, waits for lock 2. Thread B holds lock 2, waits for lock 1.

- **Solutions:**
  - **Mutex/Lock:** only one thread can access resource at a time
  - **Semaphore:** limit N concurrent accesses
  - **Atomic operations:** hardware-level read-modify-write
  - **Connection pools:** reuse database connections instead of creating new ones per request
    - Too few connections → requests queue up
    - Too many connections → database overloaded
    - Rule of thumb: pool size = (2 * CPU cores) + disk spindles

#### Hands-On Exercises

1. **Add structured logging** — Replace print statements with structured JSON logging using a proper library
2. **Add request tracing** — Generate and propagate request IDs across all log entries
3. **Set up Prometheus metrics** — Track request count, latency, and error rate
4. **Concurrency exercise** — Demonstrate a race condition, then fix it with proper locking
5. **Connection pool tuning** — Configure and test database connection pool settings

#### Mini-Project
Add comprehensive observability to the Bookstore API: structured logging with request IDs, Prometheus metrics endpoint, and request duration tracking. Set up a Docker Compose service with Grafana to visualize metrics.

---

### Day 8: Microservices & Database Scaling

> Reference: [backend_concepts.md — Concepts #12 and #13](backend_concepts.md#12-microservices-vs-monolith)

#### Theory

**8.1 Monolith vs Microservices**

| Aspect | Monolith | Microservices |
|--------|----------|---------------|
| Deployment | Single unit | Independent per service |
| Scaling | Scale everything together | Scale individual services |
| Complexity | Simple to build/deploy | Complex infrastructure |
| Data | Shared database | Database per service |
| Transactions | ACID transactions easy | Distributed transactions hard |
| Team scaling | Harder with many developers | Independent team ownership |
| Best for | Startups, small teams, MVPs | Large orgs, proven domain boundaries |

- **Start with a monolith.** Extract microservices only when you have clear domain boundaries and a team size that demands it.

**8.2 Microservice Communication**

- **Synchronous (request-response):**
  - REST APIs — simple, universal
  - gRPC — high performance, strong typing, streaming
  - Downsides: tight coupling, cascading failures

- **Asynchronous (event-driven):**
  - Message queues (RabbitMQ, Kafka)
  - Downsides: eventual consistency, harder to debug
  - Upsides: loose coupling, better resilience

- **API Gateway pattern:**
  ```
  Client → API Gateway → Service A
                       → Service B
                       → Service C
  ```
  - Single entry point for clients
  - Handles: routing, auth, rate limiting, load balancing
  - Tools: Kong, Envoy, AWS API Gateway, Nginx

- **Service discovery:**
  - How services find each other
  - DNS-based (Kubernetes Services)
  - Registry-based (Consul, Eureka)

**8.3 Distributed System Challenges**

- **Network is unreliable** — always handle timeouts and retries
- **Circuit breaker pattern:**
  ```
  Closed → (failures exceed threshold) → Open → (timeout) → Half-Open
    ↑                                                          |
    └──────── (success) ──────────────────────────────────────┘
  ```
  - Prevents cascading failures
  - Libraries: Hystrix (Java), resilience4j, Polly (.NET)

- **Eventual consistency:**
  - In distributed systems, strong consistency is expensive
  - Accept that reads may be slightly stale
  - Design UIs to handle eventual consistency gracefully

**8.4 Database Scaling Patterns**

- **Read replicas:**
  ```
  Writes → Primary DB
  Reads  → Replica 1, Replica 2, Replica 3
  ```
  - Scale read-heavy workloads
  - Replication lag: replicas may be slightly behind

- **Sharding (Horizontal Partitioning):**
  ```
  Shard key: user_id
  Users 1-1M     → Shard A
  Users 1M-2M    → Shard B
  Users 2M-3M    → Shard C
  ```
  - Scale write-heavy workloads
  - Challenges: cross-shard queries, rebalancing, shard key selection
  - Choose shard key wisely: even distribution, query locality

- **Connection pooling:**
  - PostgreSQL: PgBouncer (external) or built-in pools
  - MySQL: ProxySQL
  - Java: HikariCP (application-level)
  - Modes: session pooling, transaction pooling, statement pooling

- **Database per service (microservices):**
  - Each microservice owns its data
  - No direct cross-service database queries
  - Use APIs or events to share data between services
  - Saga pattern for distributed transactions

#### Hands-On Exercises

1. **Split a monolith** — Extract the "reviews" feature from the Bookstore into a separate service
2. **Service communication** — Make the book service call the reviews service via REST
3. **Add circuit breaker** — Implement circuit breaker pattern for inter-service calls
4. **Read replicas exercise** — Configure PostgreSQL replication and route reads to replica
5. **Connection pooling** — Set up PgBouncer and benchmark with/without pooling

#### Mini-Project
Refactor the Bookstore into two services: Book Service and Review Service. They communicate via REST APIs, with an API gateway routing requests. Each service has its own database.

---

### Day 9: Event-Driven Architecture & GraphQL

> Reference: [backend_concepts.md — Concepts #14 and #16](backend_concepts.md#14-event-driven-architecture)

#### Theory

**9.1 Event-Driven Architecture**

- **Events vs Commands:**
  - Event: "OrderPlaced" — something that happened (past tense, immutable)
  - Command: "PlaceOrder" — request to do something (imperative)
  - Events are facts. Commands are requests.

- **Pub/Sub pattern:**
  ```
  Order Service publishes: "OrderPlaced" event
  ├── Inventory Service subscribes → reduces stock
  ├── Email Service subscribes → sends confirmation
  ├── Analytics Service subscribes → records metrics
  └── Billing Service subscribes → initiates payment
  ```
  - Publishers don't know about subscribers (loose coupling)
  - Adding new consumers doesn't require changing publishers

- **Event Sourcing:**
  - Store events, not current state
  - Current state = replay all events from the beginning
  ```
  Event Log for Account #123:
  1. AccountCreated { balance: 0 }
  2. MoneyDeposited { amount: 100 }
  3. MoneyWithdrawn { amount: 30 }
  4. MoneyDeposited { amount: 50 }
  → Current state: balance = 120
  ```
  - Benefits: complete audit trail, temporal queries, debugging
  - Drawbacks: complexity, storage growth, eventual consistency

- **CQRS (Command Query Responsibility Segregation):**
  ```
  Commands (writes) → Write Model → Event Store
                                        ↓
  Queries (reads)  ← Read Model  ← Projection (materialized view)
  ```
  - Optimize read and write models independently
  - Write model: normalized, consistent
  - Read model: denormalized, optimized for query patterns

- **Outbox Pattern — reliable event publishing:**
  ```
  1. Within a single DB transaction:
     - Update domain state (e.g., insert order)
     - Insert event into outbox table
  2. Separate process polls outbox table
  3. Publishes events to message broker
  4. Marks outbox entries as published
  ```
  - Solves: dual-write problem (writing to DB and queue is not atomic)

- **Kafka as Event Backbone:**
  - Topics: named channels for events
  - Partitions: parallelism within a topic
  - Consumer groups: each group gets every message, members split partitions
  - Retention: events are kept for configured duration (or forever)
  - Compaction: keep only latest value per key

**9.2 GraphQL**

- **Why GraphQL?**
  - REST problem: over-fetching (get all fields) and under-fetching (need multiple requests)
  - GraphQL: client specifies exactly what data it needs

- **Schema Definition Language (SDL):**
  ```graphql
  type Book {
    id: ID!
    title: String!
    author: Author!
    reviews: [Review!]!
    averageRating: Float
  }

  type Author {
    id: ID!
    name: String!
    books: [Book!]!
  }

  type Query {
    book(id: ID!): Book
    books(limit: Int, cursor: String): BookConnection!
  }

  type Mutation {
    createBook(input: CreateBookInput!): Book!
    addReview(bookId: ID!, rating: Int!, comment: String): Review!
  }
  ```

- **Resolvers:**
  ```python
  @query.field("book")
  async def resolve_book(_, info, id):
      return await db.get_book(id)

  @book.field("author")
  async def resolve_book_author(book, info):
      return await db.get_author(book.author_id)

  @book.field("reviews")
  async def resolve_book_reviews(book, info):
      return await db.get_reviews(book.id)
  ```

- **N+1 Problem & DataLoader:**
  ```
  # N+1 problem: fetching 10 books, then 10 individual author queries
  # Solution: DataLoader batches and caches

  author_loader = DataLoader(batch_fn=load_authors_by_ids)

  @book.field("author")
  async def resolve_author(book, info):
      return await author_loader.load(book.author_id)
      # Batches: load_authors_by_ids([1, 2, 3, 5]) — single query!
  ```

- **When to use GraphQL vs REST:**
  | Choose REST | Choose GraphQL |
  |-------------|---------------|
  | Simple CRUD APIs | Complex data with many relationships |
  | Server-to-server | Client-driven data needs (mobile, SPA) |
  | Caching is critical (HTTP caching is easy) | Multiple client types with different needs |
  | Simple data model | Rapid frontend iteration |

#### Hands-On Exercises

1. **Implement Pub/Sub** — Use Redis Pub/Sub or Kafka to publish "BookCreated" events consumed by a notification service
2. **Build an Outbox** — Implement the outbox pattern to reliably publish events
3. **CQRS exercise** — Create a separate read model (denormalized view) updated by events
4. **Build a GraphQL API** — Create a GraphQL endpoint for the Bookstore with books, authors, and reviews
5. **Fix N+1** — Identify and fix N+1 queries using DataLoader

#### Mini-Project
Add event-driven features to the Bookstore: when a book is reviewed, publish an event that updates the book's average rating (CQRS read model) and notifies the author. Additionally, expose a GraphQL endpoint alongside the REST API.

---

### Day 10: gRPC, Search, Rate Limiting & Webhooks — Capstone

> Reference: [backend_concepts.md — Concepts #17, #18, #19, #20](backend_concepts.md#17-grpc--protocol-buffers)

#### Theory

**10.1 gRPC & Protocol Buffers**

- **What is gRPC?**
  - High-performance RPC framework by Google
  - Uses HTTP/2: multiplexing, header compression, streaming
  - Binary serialization with Protocol Buffers (Protobuf)

- **Protocol Buffer definition:**
  ```protobuf
  syntax = "proto3";

  service BookService {
    rpc GetBook (GetBookRequest) returns (Book);
    rpc ListBooks (ListBooksRequest) returns (stream Book);  // server streaming
    rpc CreateBook (CreateBookRequest) returns (Book);
  }

  message Book {
    string id = 1;
    string title = 2;
    string author = 3;
    float price = 4;
  }

  message GetBookRequest {
    string id = 1;
  }
  ```

- **gRPC streaming types:**
  | Type | Description | Use Case |
  |------|-------------|----------|
  | Unary | Single request → single response | Regular RPC calls |
  | Server streaming | Single request → stream of responses | Real-time feeds |
  | Client streaming | Stream of requests → single response | File upload |
  | Bidirectional | Stream ↔ stream | Chat, gaming |

- **When to use gRPC vs REST:**
  - gRPC: internal microservice communication, high throughput, streaming
  - REST: external/public APIs, browser clients, simplicity

**10.2 Search Engines**

- **Why not just SQL LIKE?**
  - `LIKE '%search%'` can't use indexes → full table scan
  - No relevance ranking
  - No fuzzy matching, stemming, synonyms

- **Elasticsearch / OpenSearch:**
  - Full-text search engine built on Apache Lucene
  - Inverted index: maps terms to documents (like a book index)
  ```
  "python"  → [doc1, doc3, doc7]
  "backend" → [doc1, doc2, doc5]
  "python AND backend" → [doc1]
  ```

- **Key concepts:**
  - **Index:** collection of documents (like a database table)
  - **Analyzer:** processes text during indexing (tokenization, lowercasing, stemming)
  - **Relevance scoring:** TF-IDF, BM25 — how well a document matches
  - **Aggregations:** analytics on search results (facets, histograms)

- **Indexing strategy:**
  - Keep search index in sync with database
  - Options: dual-write (risky), change data capture, event-driven updates
  - Reindex periodically for schema changes

**10.3 Rate Limiting Algorithms**

- **Token Bucket:**
  ```
  Bucket holds N tokens. Each request consumes 1 token.
  Tokens are added at rate R per second.
  If bucket is empty → reject request (429).

  Allows bursts up to bucket capacity.
  ```

- **Leaky Bucket:**
  ```
  Requests enter a queue (bucket). Processed at fixed rate.
  If queue is full → reject.

  Smooths out traffic — no bursts.
  ```

- **Sliding Window:**
  ```
  Track requests in a sliding time window (e.g., last 60 seconds).
  Count requests in window → allow/deny.

  More accurate than fixed windows, avoids boundary spikes.
  ```

- **Distributed rate limiting with Redis:**
  ```python
  def is_rate_limited(user_id: str, limit: int, window_seconds: int) -> bool:
      key = f"rate:{user_id}"
      current = redis.incr(key)
      if current == 1:
          redis.expire(key, window_seconds)
      return current > limit
  ```

**10.4 Webhooks**

- **What are webhooks?**
  - HTTP callbacks: your server sends POST requests to external URLs when events happen
  - "Don't call us, we'll call you" — push instead of poll

- **Implementing webhooks:**
  ```python
  # Webhook registration
  POST /api/webhooks
  {
    "url": "https://partner.com/hooks/orders",
    "events": ["order.created", "order.shipped"],
    "secret": "whsec_abc123"
  }

  # Webhook delivery
  POST https://partner.com/hooks/orders
  Headers:
    X-Webhook-Signature: sha256=<HMAC of body with secret>
    X-Webhook-ID: evt_123
    X-Webhook-Timestamp: 1700000000
  Body:
    { "event": "order.created", "data": { ... } }
  ```

- **Reliability patterns:**
  - **Retry with exponential backoff:** 1s, 5s, 30s, 5min, 30min, 2h
  - **Signature verification:** HMAC-SHA256 to verify authenticity
  - **Idempotency:** include event ID, receivers should deduplicate
  - **Timeout:** 5-30 second timeout per delivery attempt
  - **Dead letter / alert:** notify after all retries exhausted

#### Hands-On Exercises

1. **Build a gRPC service** — Create a gRPC book service with Protobuf definitions
2. **Set up Elasticsearch** — Index books and implement full-text search with relevance ranking
3. **Implement rate limiting** — Build token bucket rate limiter with Redis, apply to API
4. **Build webhook system** — Register webhooks, deliver events with signature verification and retries
5. **Compare search** — Benchmark SQL LIKE vs Elasticsearch for search queries

#### Capstone Project

Build the **complete Bookstore Platform** combining everything learned:

```
Architecture:
┌──────────┐     ┌───────────────┐     ┌──────────────┐
│  Client   │────▶│  API Gateway  │────▶│ Book Service │──▶ PostgreSQL
│ (GraphQL) │     │ (rate limit)  │     │   (REST)     │──▶ Redis Cache
└──────────┘     └───────────────┘     └──────┬───────┘
                                              │ events
                                        ┌─────▼───────┐
                                        │   Kafka /    │
                                        │  Redis PubSub│
                                        └──┬──────┬───┘
                                  ┌────────▼┐  ┌──▼────────┐
                                  │ Review   │  │ Search    │
                                  │ Service  │  │ Service   │
                                  │ (gRPC)   │  │ (Elastic) │
                                  └──────────┘  └───────────┘
```

**Requirements:**
1. REST + GraphQL + gRPC endpoints
2. PostgreSQL + Redis + Elasticsearch
3. JWT authentication with RBAC
4. Event-driven communication between services
5. Caching with invalidation
6. Structured logging and metrics
7. Rate limiting
8. Webhook notifications for new books
9. Dockerized with Docker Compose
10. CI pipeline with tests

---

## Daily Schedule Template

| Time | Activity | Duration |
|------|----------|----------|
| 09:00-09:30 | Review previous day, Q&A | 30 min |
| 09:30-12:00 | Theory + guided examples | 2.5 hr |
| 12:00-13:00 | Lunch break | 1 hr |
| 13:00-15:30 | Hands-on exercises | 2.5 hr |
| 15:30-15:45 | Break | 15 min |
| 15:45-17:00 | Mini-project work | 1.25 hr |
| 17:00-17:30 | Wrap-up, preview next day | 30 min |
| Evening | Optional reading + prep | 1 hr |

---

## Recommended Resources

### Books
- *Designing Data-Intensive Applications* by Martin Kleppmann — the definitive guide to backend architecture
- *Web Scalability for Startup Engineers* by Artur Ejsmont — practical scaling patterns
- *Clean Architecture* by Robert C. Martin — software design principles

### Online
- [PostgreSQL Documentation](https://www.postgresql.org/docs/) — best database docs in the industry
- [Redis University](https://university.redis.com/) — free Redis courses
- [Docker Getting Started](https://docs.docker.com/get-started/) — official Docker tutorial
- [The Twelve-Factor App](https://12factor.net/) — methodology for building SaaS apps
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) — web security essentials

### Practice
- Build real projects — the exercises in this course are starting points
- Contribute to open-source backend projects
- Deploy to a cloud provider (AWS Free Tier, GCP, Railway, Fly.io)

---

## Progress Checklist

### Week 1
- [ ] Day 1: HTTP & REST APIs — can build a complete REST API
- [ ] Day 2: SQL Databases — can design schemas, write complex queries, optimize performance
- [ ] Day 3: Auth & API Design — can implement JWT auth, RBAC, and well-designed APIs
- [ ] Day 4: Caching & Security — can implement Redis caching and secure an application
- [ ] Day 5: Async & NoSQL — can set up message queues and choose the right database

### Week 2
- [ ] Day 6: Docker & CI/CD — can containerize apps and set up CI pipelines
- [ ] Day 7: Observability & Concurrency — can implement structured logging, metrics, and handle concurrency
- [ ] Day 8: Microservices & DB Scaling — can design service boundaries and scale databases
- [ ] Day 9: Event-Driven & GraphQL — can implement event sourcing, CQRS, and GraphQL
- [ ] Day 10: gRPC, Search, Rate Limiting, Webhooks — can build the complete capstone project

---

---

## Appendix: Real-World Backend Engineering Issues & Solutions

> These are problems you **will** encounter in production. Each section describes the issue, why it happens, how to detect it, and how to fix or prevent it using sound backend engineering and architecture.

---

### Issue #1: Database Connection Exhaustion

**The Problem:**
Your application suddenly starts throwing "too many connections" or "connection pool exhausted" errors. Requests queue up, latency spikes, and the service appears down even though the database server itself is healthy.

**Why It Happens:**
- Connection leaks: code opens a connection but doesn't close it (missing `finally` block, unhandled exceptions)
- Long-running transactions holding connections open
- Sudden traffic spikes exceeding pool capacity
- Microservices each maintaining their own pools, collectively overwhelming the database
- ORM default settings are often too generous (e.g., pool of 100 per instance × 20 instances = 2000 connections)

**How to Detect:**
```sql
-- PostgreSQL: check active connections
SELECT count(*) FROM pg_stat_activity;
SELECT state, count(*) FROM pg_stat_activity GROUP BY state;

-- Check for idle-in-transaction connections (connection leaks)
SELECT pid, state, query, age(now(), xact_start) AS transaction_age
FROM pg_stat_activity
WHERE state = 'idle in transaction'
ORDER BY transaction_age DESC;
```

**How to Fix:**
1. **Use a connection pooler** like PgBouncer in transaction mode between your app and PostgreSQL
2. **Set pool limits appropriately:** a good starting formula is `pool_size = (2 × CPU_cores) + number_of_disks` per database instance
3. **Always use context managers** to guarantee connection release:
   ```python
   # GOOD — connection is always returned to the pool
   async with db.acquire() as conn:
       result = await conn.fetch("SELECT ...")
   
   # BAD — if an exception occurs, the connection leaks
   conn = await db.acquire()
   result = await conn.fetch("SELECT ...")
   await conn.release()
   ```
4. **Set connection timeouts:** both idle timeout (reclaim unused connections) and statement timeout (kill runaway queries)
5. **Monitor pool utilization** as a metric and alert when it exceeds 80%

---

### Issue #2: Slow Queries Creeping Into Production

**The Problem:**
A query that worked fine in development takes 30+ seconds in production. The table grew from 10K rows to 10M rows and no one noticed the missing index.

**Why It Happens:**
- Missing indexes on columns used in WHERE, JOIN, and ORDER BY
- Index bloat over time (especially with lots of UPDATE/DELETE)
- ORM-generated queries that look innocent but produce terrible execution plans
- Statistics getting stale, causing the query planner to choose bad plans
- N+1 query patterns hidden behind ORM lazy loading

**How to Detect:**
```sql
-- PostgreSQL: find slow queries
SELECT query, calls, mean_exec_time, total_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 20;

-- Find missing indexes (tables with lots of sequential scans)
SELECT relname, seq_scan, idx_scan,
       seq_scan - idx_scan AS too_many_seq_scans
FROM pg_stat_user_tables
WHERE seq_scan > idx_scan
ORDER BY too_many_seq_scans DESC;
```

**How to Fix:**
1. **Enable `pg_stat_statements`** — essential for production query monitoring
2. **Run EXPLAIN ANALYZE** on slow queries to understand the execution plan
3. **Add targeted indexes**, especially composite indexes matching your WHERE clause patterns
4. **Periodically REINDEX** and run `VACUUM ANALYZE` to update statistics
5. **Set `log_min_duration_statement`** (e.g., 200ms) to automatically log slow queries
6. **Use query monitoring tools** like pganalyze, Datadog, or New Relic to catch regressions early
7. **Review ORM queries** — log the actual SQL your ORM generates in development:
   ```python
   # SQLAlchemy: enable echo to see generated SQL
   engine = create_engine(url, echo=True)
   ```

---

### Issue #3: Thundering Herd / Cache Stampede

**The Problem:**
A popular cache key expires. Hundreds of concurrent requests all see a cache miss simultaneously and all hit the database at once. The database gets overwhelmed, causing cascading timeouts.

**Why It Happens:**
- High-traffic cache keys with fixed TTLs expire at the same instant
- Cold cache after a deployment or Redis restart
- Cache invalidation of a frequently accessed key

**How to Fix:**

1. **Locking / single-flight pattern:** only one request fetches from DB, others wait for the cache to be repopulated
   ```python
   def get_with_lock(key: str):
       value = redis.get(key)
       if value:
           return json.loads(value)
       
       lock_key = f"lock:{key}"
       if redis.set(lock_key, "1", nx=True, ex=10):  # acquire lock
           try:
               value = fetch_from_db(key)
               redis.setex(key, 3600, json.dumps(value))
               return value
           finally:
               redis.delete(lock_key)
       else:
           # Another request is fetching, wait and retry
           time.sleep(0.1)
           return get_with_lock(key)
   ```

2. **Probabilistic early expiration:** refresh the cache before it actually expires
   ```python
   # Each request has an increasing probability of refreshing as TTL approaches
   ttl = redis.ttl(key)
   if ttl < 60 and random.random() < (1 - ttl / 60):
       refresh_cache_in_background(key)
   ```

3. **Stale-while-revalidate:** serve stale data while refreshing in the background

4. **Cache warming:** pre-populate critical cache keys before deployments or after restarts

---

### Issue #4: Memory Leaks in Long-Running Services

**The Problem:**
Your service's memory usage grows steadily over hours or days until it gets OOM-killed. Restarting fixes it temporarily, but the pattern repeats.

**Why It Happens:**
- Unbounded in-memory caches or lists that grow without eviction
- Event listeners or callbacks registered but never cleaned up
- Large objects held in closures or global state
- Connection objects or file handles not being properly closed
- Circular references preventing garbage collection (language-specific)

**How to Detect:**
- Monitor RSS (Resident Set Size) over time — a steady upward trend is a leak
- Use language-specific profilers:
  ```python
  # Python: tracemalloc
  import tracemalloc
  tracemalloc.start()
  # ... run your code ...
  snapshot = tracemalloc.take_snapshot()
  for stat in snapshot.statistics('lineno')[:10]:
      print(stat)
  ```
  ```javascript
  // Node.js: take heap snapshots
  // --inspect flag + Chrome DevTools → Memory → Heap Snapshot
  node --inspect app.js
  ```

**How to Fix:**
1. **Bound all in-memory collections** — use LRU caches with max size, not plain dicts/maps
2. **Use weak references** for caches where appropriate
3. **Audit event listener registration** — ensure every `on()` has a corresponding `off()`
4. **Set memory limits** on containers so OOM kills happen early and visibly
5. **Profile in staging** with realistic traffic patterns, not just unit tests
6. **Use external caches (Redis)** instead of in-process caches for large datasets

---

### Issue #5: Cascading Failures Across Services

**The Problem:**
Service A depends on Service B. Service B becomes slow (not down, just slow). Service A's threads/connections are all blocked waiting for B. Service A becomes unresponsive. Now Service C, which depends on A, also fails. The whole system goes down.

**Why It Happens:**
- No timeouts on HTTP calls between services
- No circuit breakers to stop calling a degraded service
- Thread/connection pool exhaustion while waiting for slow upstream
- Retry storms: every service retries, amplifying load on the struggling service

**Architecture & Solution:**

1. **Set aggressive timeouts on all inter-service calls:**
   ```python
   # Set both connect and read timeouts
   response = httpx.get(
       "http://service-b/api/data",
       timeout=httpx.Timeout(connect=1.0, read=3.0)
   )
   ```

2. **Implement circuit breakers:**
   ```python
   # States: CLOSED → OPEN → HALF_OPEN → CLOSED
   class CircuitBreaker:
       def __init__(self, failure_threshold=5, recovery_timeout=30):
           self.failures = 0
           self.threshold = failure_threshold
           self.recovery_timeout = recovery_timeout
           self.state = "CLOSED"
           self.last_failure_time = None
       
       def call(self, func, *args, **kwargs):
           if self.state == "OPEN":
               if time.time() - self.last_failure_time > self.recovery_timeout:
                   self.state = "HALF_OPEN"
               else:
                   raise CircuitOpenError("Service unavailable")
           
           try:
               result = func(*args, **kwargs)
               if self.state == "HALF_OPEN":
                   self.state = "CLOSED"
                   self.failures = 0
               return result
           except Exception as e:
               self.failures += 1
               self.last_failure_time = time.time()
               if self.failures >= self.threshold:
                   self.state = "OPEN"
               raise
   ```

3. **Use bulkheads:** isolate resources so one slow dependency can't exhaust all capacity
   ```
   Thread Pool A (for Service B calls): max 20 threads
   Thread Pool B (for Service C calls): max 20 threads
   → If Service B is slow, only pool A is exhausted; pool B keeps working
   ```

4. **Implement graceful degradation:** return cached/default data when an upstream is unavailable
   ```python
   try:
       recommendations = recommendation_service.get(user_id, timeout=2.0)
   except (TimeoutError, CircuitOpenError):
       recommendations = get_popular_items()  # fallback to generic recommendations
   ```

5. **Add retry budgets** instead of naive retries: "max 20% of requests can be retries" to prevent retry storms

---

### Issue #6: Handling Distributed Transactions (Data Consistency Across Services)

**The Problem:**
In a monolith, you wrap everything in a database transaction. In microservices, an "order" operation involves: payment service charges the card, inventory service reserves stock, order service creates the order. If inventory fails after payment succeeds, the customer is charged but gets no product.

**Why It Happens:**
- Each microservice has its own database — no shared ACID transactions
- Two-phase commit (2PC) is slow, fragile, and doesn't scale
- Network failures can leave operations in a partially completed state

**Architecture & Solution:**

1. **Saga Pattern (Choreography):** each service listens for events and reacts, publishing compensating events on failure
   ```
   Order Service → publishes "OrderCreated"
   Payment Service → listens, charges card → publishes "PaymentCompleted"
   Inventory Service → listens, reserves stock → publishes "StockReserved"
   
   If Inventory fails:
   Inventory Service → publishes "StockReservationFailed"
   Payment Service → listens → refunds card → publishes "PaymentRefunded"
   Order Service → listens → marks order as failed
   ```

2. **Saga Pattern (Orchestration):** a central orchestrator coordinates the steps
   ```python
   class OrderSagaOrchestrator:
       async def execute(self, order):
           try:
               payment = await payment_service.charge(order.total)
               try:
                   await inventory_service.reserve(order.items)
               except ReservationFailed:
                   await payment_service.refund(payment.id)  # compensate
                   raise
               await order_service.confirm(order.id)
           except Exception:
               await order_service.fail(order.id)
               raise
   ```

3. **Outbox Pattern** for reliable event publishing (as covered in Day 9)

4. **Design for idempotency:** every step must be safe to retry
   - Payment: use idempotency keys so charging twice doesn't double-charge
   - Inventory: check if already reserved before reserving again

---

### Issue #7: Handling Time Zones and Timestamps Correctly

**The Problem:**
Users see wrong times. Scheduled jobs fire at unexpected hours. Reports show transactions on the wrong day. A customer in Tokyo and one in New York see different data for "today's orders."

**Why It Happens:**
- Storing local times without timezone information
- Server timezone differs between environments
- Converting timezones inconsistently across services
- Daylight Saving Time (DST) edge cases

**How to Fix:**

1. **Store everything in UTC in the database:**
   ```sql
   -- PostgreSQL: always use timestamptz (timestamp with time zone)
   CREATE TABLE events (
       id SERIAL PRIMARY KEY,
       name TEXT,
       event_time TIMESTAMPTZ DEFAULT NOW()  -- stored as UTC
   );
   ```

2. **Convert to the user's timezone only at the presentation layer:**
   ```python
   from datetime import datetime, timezone
   import pytz
   
   # Store in UTC
   utc_now = datetime.now(timezone.utc)
   
   # Display in user's timezone
   user_tz = pytz.timezone("Asia/Ho_Chi_Minh")
   local_time = utc_now.astimezone(user_tz)
   ```

3. **Use ISO 8601 format** in all APIs: `"2026-04-08T10:30:00Z"` (the Z means UTC)

4. **Never use server local time** — always use UTC explicitly:
   ```python
   # BAD
   datetime.now()
   
   # GOOD
   datetime.now(timezone.utc)
   ```

5. **Be careful with date boundaries:** "today" depends on the user's timezone. Always convert before filtering:
   ```sql
   -- Find today's orders for a user in Asia/Tokyo (+9)
   SELECT * FROM orders
   WHERE created_at >= '2026-04-08T00:00:00+09:00'
     AND created_at < '2026-04-09T00:00:00+09:00';
   ```

---

### Issue #8: Handling File Uploads at Scale

**The Problem:**
Users upload large files (images, videos, CSVs) through your API. The API server's memory spikes, request timeouts occur, and scaling becomes painful because each upload ties up a web server process for the entire duration.

**Why It Happens:**
- Loading the entire file into memory before processing
- Synchronous processing blocking the request handler
- Single-server storage that doesn't scale
- No size limits or validation

**Architecture & Solution:**

1. **Use pre-signed URLs for direct-to-storage uploads:**
   ```python
   # Step 1: Client requests an upload URL
   @app.post("/uploads/request")
   async def request_upload(file_name: str, content_type: str):
       key = f"uploads/{uuid4()}/{file_name}"
       url = s3_client.generate_presigned_url(
           "put_object",
           Params={"Bucket": BUCKET, "Key": key, "ContentType": content_type},
           ExpiresIn=3600
       )
       return {"upload_url": url, "key": key}
   
   # Step 2: Client uploads directly to S3 (bypasses your API server entirely)
   # Step 3: Client notifies your API that upload is complete
   @app.post("/uploads/complete")
   async def complete_upload(key: str):
       # Queue background processing (thumbnail, virus scan, etc.)
       await queue.enqueue("process_upload", {"key": key})
       return {"status": "processing"}
   ```

2. **Stream large files** instead of buffering in memory:
   ```python
   # Streaming upload processing (if you must handle the file)
   @app.post("/uploads")
   async def upload(file: UploadFile):
       async with aiofiles.open(f"/tmp/{file.filename}", "wb") as out:
           while chunk := await file.read(8192):  # 8KB chunks
               await out.write(chunk)
   ```

3. **Validate before processing:**
   - File size limits (reject oversized files early via `Content-Length` header)
   - File type validation (check magic bytes, not just extension)
   - Virus scanning before making files accessible

4. **Process asynchronously:** thumbnail generation, transcoding, and analysis should all happen in background workers

---

### Issue #9: API Versioning and Breaking Changes

**The Problem:**
You need to change an API response format, rename a field, or remove an endpoint. But external clients depend on the current contract. Deploying the change will break them.

**Why It Happens:**
- No versioning strategy from the start
- Tight coupling between API contract and internal data model
- Multiple consumers with different upgrade timelines

**How to Handle It:**

1. **Use URL-based versioning** (simplest and most explicit):
   ```
   /api/v1/users → original format
   /api/v2/users → new format
   ```

2. **Apply additive changes** whenever possible (non-breaking):
   - Adding new fields to responses: **safe** (existing clients ignore unknown fields)
   - Adding new optional query parameters: **safe**
   - Adding new endpoints: **safe**
   - Removing or renaming fields: **breaking**
   - Changing field types: **breaking**

3. **Deprecation workflow for breaking changes:**
   ```
   Phase 1: Add v2 endpoint alongside v1 (both work)
   Phase 2: Add Deprecation header to v1 responses
            Deprecation: true
            Sunset: Sat, 01 Nov 2026 00:00:00 GMT
   Phase 3: Monitor v1 usage, notify consumers
   Phase 4: After sunset date, return 410 Gone for v1
   ```

4. **Use an API gateway** to handle version routing and response transformation

5. **Contract testing** to catch breaking changes before deployment:
   ```
   Consumer-Driven Contract Tests (Pact):
   - Consumers define what they expect from your API
   - Your CI runs these contracts against your implementation
   - Breaking a contract fails the build
   ```

---

### Issue #10: Dealing with Deadlocks

**The Problem:**
Two concurrent requests try to update the same rows in different orders. Each holds a lock the other needs. Both wait indefinitely (or until the database kills one).

```
Transaction A: UPDATE accounts SET balance = balance - 100 WHERE id = 1;  -- locks row 1
Transaction B: UPDATE accounts SET balance = balance - 50 WHERE id = 2;   -- locks row 2
Transaction A: UPDATE accounts SET balance = balance + 100 WHERE id = 2;  -- waits for row 2 (held by B)
Transaction B: UPDATE accounts SET balance = balance + 50 WHERE id = 1;   -- waits for row 1 (held by A) → DEADLOCK
```

**How to Fix:**

1. **Always acquire locks in a consistent order:**
   ```python
   def transfer(from_id: int, to_id: int, amount: float):
       # Always lock the lower ID first
       first, second = sorted([from_id, to_id])
       with db.transaction():
           db.execute("SELECT * FROM accounts WHERE id = %s FOR UPDATE", first)
           db.execute("SELECT * FROM accounts WHERE id = %s FOR UPDATE", second)
           db.execute("UPDATE accounts SET balance = balance - %s WHERE id = %s", amount, from_id)
           db.execute("UPDATE accounts SET balance = balance + %s WHERE id = %s", amount, to_id)
   ```

2. **Keep transactions short:** do as little work as possible inside a transaction
3. **Use optimistic locking** for low-contention scenarios:
   ```sql
   -- Add a version column
   UPDATE products
   SET price = 29.99, version = version + 1
   WHERE id = 42 AND version = 3;  -- only succeeds if no one else changed it
   -- If 0 rows affected → someone else updated first → retry
   ```
4. **Set lock timeouts** so deadlocked transactions don't wait forever:
   ```sql
   SET lock_timeout = '5s';
   SET deadlock_timeout = '1s';
   ```

---

### Issue #11: Handling Backpressure in High-Throughput Systems

**The Problem:**
Your producer generates events faster than your consumer can process them. The queue grows unboundedly, memory runs out, and eventually the entire pipeline collapses.

**Why It Happens:**
- Consumer is slower than producer (I/O bound, CPU bound, or downstream dependency is slow)
- Traffic spikes that exceed consumer capacity
- Consumer failures causing messages to pile up

**Architecture & Solution:**

1. **Bounded queues with rejection:** set max queue size and reject/drop when full
   ```python
   # Redis Stream with max length
   redis.xadd("events", {"data": payload}, maxlen=100000)
   # Oldest entries are automatically trimmed
   ```

2. **Consumer scaling:** auto-scale consumers based on queue depth
   ```yaml
   # Kubernetes HPA based on custom metric (queue length)
   apiVersion: autoscaling/v2
   kind: HorizontalPodAutoscaler
   spec:
     metrics:
     - type: External
       external:
         metric:
           name: rabbitmq_queue_messages
         target:
           type: AverageValue
           averageValue: 100  # scale up when >100 messages per consumer
   ```

3. **Rate limiting at the source:** use token bucket to control ingest rate

4. **Load shedding:** deliberately drop low-priority work under pressure
   ```python
   async def handle_request(request):
       queue_depth = await get_queue_depth()
       if queue_depth > HIGH_WATERMARK:
           if request.priority == "low":
               return Response(status=503, body="Service busy, try later")
       await enqueue(request)
   ```

5. **Batch processing:** instead of processing one message at a time, batch them
   ```python
   # Instead of: INSERT INTO events VALUES (...) × 1000 times
   # Do: INSERT INTO events VALUES (...), (...), (...) ... × 1 time with 1000 rows
   ```

---

### Issue #12: Secret Rotation Without Downtime

**The Problem:**
A database password or API key needs to be rotated (due to leak, policy, or employee departure). Simply changing the secret breaks all running instances immediately.

**How to Handle It:**

1. **Dual-read pattern for secrets:**
   ```
   Step 1: Add the NEW secret alongside the old one (both are valid)
   Step 2: Deploy application update that uses the NEW secret
   Step 3: Verify all instances are using the new secret
   Step 4: Remove/invalidate the OLD secret
   ```

2. **For database passwords:**
   ```sql
   -- Step 1: Create a new user with the new password
   CREATE USER app_user_v2 WITH PASSWORD 'new_password';
   GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO app_user_v2;
   
   -- Step 2: Update application config to use app_user_v2
   -- Step 3: After all instances are updated, drop the old user
   DROP USER app_user_v1;
   ```

3. **For API keys and JWT signing keys:**
   ```python
   # Accept tokens signed with either key during rotation
   SIGNING_KEYS = [
       {"kid": "key-2026-04", "secret": "new_secret"},  # sign new tokens with this
       {"kid": "key-2026-01", "secret": "old_secret"},   # still verify old tokens
   ]
   
   def verify_token(token):
       header = decode_header(token)
       key = next(k for k in SIGNING_KEYS if k["kid"] == header["kid"])
       return jwt.decode(token, key["secret"])
   ```

4. **Use a secrets manager** (HashiCorp Vault, AWS Secrets Manager) that supports automatic rotation and dynamic credentials

---

### Issue #13: Data Migration on Large Tables Without Downtime

**The Problem:**
You need to add a NOT NULL column, change a column type, or restructure a table with 100M+ rows. A naive `ALTER TABLE` locks the table for minutes or hours, causing a full outage.

**How to Handle It:**

1. **Expand-and-contract pattern (zero-downtime column changes):**
   ```
   Phase 1 — Expand:
     ALTER TABLE users ADD COLUMN full_name TEXT;  -- nullable, no lock
     Deploy code that writes to BOTH name AND full_name
   
   Phase 2 — Migrate:
     Backfill in batches:
     UPDATE users SET full_name = name WHERE full_name IS NULL AND id BETWEEN 1 AND 10000;
     UPDATE users SET full_name = name WHERE full_name IS NULL AND id BETWEEN 10001 AND 20000;
     -- ... repeat in small batches to avoid long locks
   
   Phase 3 — Contract:
     Deploy code that reads only from full_name
     ALTER TABLE users ALTER COLUMN full_name SET NOT NULL;
     -- Later: ALTER TABLE users DROP COLUMN name;
   ```

2. **Batched backfills** to avoid locking the table:
   ```python
   BATCH_SIZE = 5000
   while True:
       rows_updated = db.execute("""
           UPDATE users
           SET full_name = first_name || ' ' || last_name
           WHERE full_name IS NULL
           LIMIT %s
       """, BATCH_SIZE)
       if rows_updated == 0:
           break
       time.sleep(0.5)  # give the database breathing room
   ```

3. **Use tools designed for online schema changes:**
   - PostgreSQL: `pg_repack` for table reorganization without locks
   - MySQL: `pt-online-schema-change` (Percona) or `gh-ost` (GitHub)
   - These create a shadow table, copy data in batches, then swap

4. **Never combine schema migration and code deployment** — deploy them separately:
   ```
   Deploy 1: Add new column (nullable)
   Deploy 2: Code writes to both old and new columns
   Deploy 3: Backfill data
   Deploy 4: Code reads from new column only
   Deploy 5: Drop old column
   ```

---

### Issue #14: Handling Poison Messages in Queues

**The Problem:**
A malformed or unprocessable message enters your queue. The consumer crashes, the message goes back to the queue, the consumer picks it up again, crashes again — infinite loop. The entire queue is stuck behind this one bad message.

**How to Fix:**

1. **Implement a retry counter with Dead Letter Queue (DLQ):**
   ```python
   async def process_message(message):
       retry_count = message.headers.get("x-retry-count", 0)
       try:
           await handle(message.body)
           await message.ack()
       except Exception as e:
           if retry_count >= MAX_RETRIES:
               await dead_letter_queue.publish(message, error=str(e))
               await message.ack()  # remove from main queue
               logger.error(f"Message sent to DLQ after {MAX_RETRIES} retries", 
                          message_id=message.id, error=str(e))
           else:
               await message.nack(
                   delay=exponential_backoff(retry_count),
                   headers={"x-retry-count": retry_count + 1}
               )
   ```

2. **Validate messages before processing:**
   ```python
   async def handle(body):
       try:
           event = EventSchema.parse(body)  # validate schema first
       except ValidationError as e:
           raise PoisonMessageError(f"Invalid schema: {e}")  # goes to DLQ immediately
       await process_valid_event(event)
   ```

3. **Monitor your DLQ:** alert when messages land in it, and build tooling to inspect and replay them

4. **RabbitMQ native DLQ support:**
   ```python
   # Declare queue with DLQ policy
   channel.queue_declare(
       queue="orders",
       arguments={
           "x-dead-letter-exchange": "dlx",
           "x-dead-letter-routing-key": "orders.dead",
           "x-message-ttl": 60000,  # retry delay
       }
   )
   ```

---

### Issue #15: N+1 Query Problem in Production

**The Problem:**
A page that lists 50 orders with customer details makes 51 queries: 1 to fetch orders + 50 individual queries to fetch each customer. Response time is 2 seconds instead of 50ms.

**Why It Happens:**
- ORM lazy loading: accessing a relationship triggers a query per object
- Hidden in development with small datasets; devastating in production

**How to Detect:**
```python
# Django: use django-debug-toolbar or django-query-inspector
# SQLAlchemy: enable echo=True or use sqltap
import sqltap

profiler = sqltap.start()
response = client.get("/api/orders")
stats = profiler.stop()
print(f"Total queries: {len(stats)}")  # if this is >> 2, you have N+1
```

**How to Fix:**

1. **Eager loading (JOIN):**
   ```python
   # SQLAlchemy
   orders = session.query(Order).options(joinedload(Order.customer)).all()
   
   # Django
   orders = Order.objects.select_related("customer").all()
   
   # For many-to-many or reverse FK:
   orders = Order.objects.prefetch_related("items").all()
   ```

2. **Batch loading with DataLoader** (GraphQL or custom):
   ```python
   # Instead of 50 individual queries:
   # SELECT * FROM customers WHERE id = 1;
   # SELECT * FROM customers WHERE id = 2; ...
   
   # One batched query:
   # SELECT * FROM customers WHERE id IN (1, 2, 3, ..., 50);
   ```

3. **Lint for N+1 in tests:**
   ```python
   # Assert query count in tests
   from django.test.utils import override_settings
   
   def test_order_list_query_count(self):
       create_orders(50)
       with self.assertNumQueries(2):  # exactly 2 queries allowed
           response = self.client.get("/api/orders/")
   ```

---

### Issue #16: Race Conditions in Business Logic

**The Problem:**
Two users simultaneously try to book the last available seat, buy the last item in stock, or claim a coupon. Both requests read "1 available," both proceed, and you've oversold.

**How to Fix:**

1. **Database-level locking (SELECT FOR UPDATE):**
   ```sql
   BEGIN;
   SELECT quantity FROM inventory WHERE product_id = 42 FOR UPDATE;
   -- row is now locked; other transactions wait
   UPDATE inventory SET quantity = quantity - 1 WHERE product_id = 42 AND quantity > 0;
   COMMIT;
   ```

2. **Atomic conditional updates (no explicit lock needed):**
   ```sql
   -- Only succeeds if quantity > 0; atomic at the database level
   UPDATE inventory
   SET quantity = quantity - 1
   WHERE product_id = 42 AND quantity > 0
   RETURNING quantity;
   -- If 0 rows returned → item is out of stock
   ```

3. **Distributed locking with Redis** (for cross-service coordination):
   ```python
   import redis
   
   def acquire_lock(resource_id: str, ttl: int = 10) -> bool:
       return redis_client.set(
           f"lock:{resource_id}",
           value=str(uuid4()),  # unique owner ID
           nx=True,             # only if not exists
           ex=ttl               # auto-expire to prevent deadlocks
       )
   
   def release_lock(resource_id: str, owner_id: str):
       # Lua script for atomic check-and-delete
       script = """
       if redis.call("get", KEYS[1]) == ARGV[1] then
           return redis.call("del", KEYS[1])
       else
           return 0
       end
       """
       redis_client.eval(script, 1, f"lock:{resource_id}", owner_id)
   ```

4. **Use Redlock** for distributed locking across multiple Redis instances (for higher reliability)

---

### Issue #17: Logging Sensitive Data Accidentally

**The Problem:**
A developer logs the full request body for debugging. The logs now contain passwords, credit card numbers, SSNs, and API keys. This violates PCI-DSS, GDPR, and company policy.

**How to Prevent It:**

1. **Create a sanitization layer for log output:**
   ```python
   SENSITIVE_FIELDS = {"password", "ssn", "credit_card", "token", "secret", "authorization"}
   
   def sanitize_for_logging(data: dict) -> dict:
       sanitized = {}
       for key, value in data.items():
           if any(sensitive in key.lower() for sensitive in SENSITIVE_FIELDS):
               sanitized[key] = "***REDACTED***"
           elif isinstance(value, dict):
               sanitized[key] = sanitize_for_logging(value)
           else:
               sanitized[key] = value
       return sanitized
   
   logger.info("Request received", extra=sanitize_for_logging(request_body))
   ```

2. **Use structured logging** and configure field-level redaction in your logging pipeline (e.g., Fluentd, Logstash filters)

3. **Never log raw request/response bodies in production** — log only metadata (method, path, status, duration, request ID)

4. **Add automated scanning** for sensitive data patterns in log storage (PII detection tools)

5. **Code review checklist:** flag any `log.*` call that includes user input without sanitization

---

### Issue #18: Graceful Shutdown and Zero-Downtime Deployments

**The Problem:**
During a deployment, in-flight requests get killed mid-processing. Users see 502 errors. Background jobs are interrupted and leave data in an inconsistent state.

**How to Handle It:**

1. **Implement graceful shutdown in your application:**
   ```python
   import signal
   import asyncio
   
   shutdown_event = asyncio.Event()
   
   def handle_sigterm(signum, frame):
       logger.info("Received SIGTERM, starting graceful shutdown")
       shutdown_event.set()
   
   signal.signal(signal.SIGTERM, handle_sigterm)
   
   async def main():
       server = await start_server()
       await shutdown_event.wait()
       
       # Step 1: Stop accepting new requests
       server.stop_accepting()
       
       # Step 2: Wait for in-flight requests to complete (with timeout)
       await asyncio.wait_for(server.drain(), timeout=30)
       
       # Step 3: Close database connections, flush buffers
       await db.close()
       await cache.close()
       logger.info("Graceful shutdown complete")
   ```

2. **Kubernetes readiness & liveness probes:**
   ```yaml
   livenessProbe:
     httpGet:
       path: /health/live
       port: 8000
     initialDelaySeconds: 5
   readinessProbe:
     httpGet:
       path: /health/ready   # returns 503 during shutdown
       port: 8000
     periodSeconds: 5
   terminationGracePeriodSeconds: 60  # give app time to drain
   ```

3. **For background job workers:** finish the current job before exiting, don't accept new ones
   ```python
   class GracefulWorker:
       def __init__(self):
           self.should_stop = False
           signal.signal(signal.SIGTERM, lambda *_: setattr(self, 'should_stop', True))
       
       def run(self):
           while not self.should_stop:
               job = queue.get(timeout=5)
               if job:
                   process(job)  # finish this job before checking should_stop
           logger.info("Worker stopped gracefully")
   ```

4. **Rolling deployments:** update pods one at a time, waiting for the new pod to be ready before killing the old one

---

### Issue #19: Flaky Tests Due to Shared State or Timing

**The Problem:**
Tests pass locally but fail randomly in CI. Or they pass individually but fail when run together. The test suite is unreliable, so developers ignore failures and bugs slip through.

**Why It Happens:**
- Tests share database state and don't clean up
- Tests depend on execution order
- Time-dependent tests (checking "created today")
- Race conditions in async test code
- External service dependencies (network calls in tests)

**How to Fix:**

1. **Isolate database state per test:**
   ```python
   # Use transactions that roll back after each test
   @pytest.fixture(autouse=True)
   async def db_transaction(db):
       async with db.transaction() as tx:
           yield
           await tx.rollback()  # undo all changes
   ```

2. **Freeze time in time-dependent tests:**
   ```python
   from freezegun import freeze_time
   
   @freeze_time("2026-04-08 12:00:00")
   def test_daily_report():
       report = generate_daily_report()
       assert report.date == date(2026, 4, 8)
   ```

3. **Mock external services, not your own code:**
   ```python
   # Mock the HTTP client, not your business logic
   @responses.activate
   def test_payment():
       responses.add(responses.POST, "https://api.stripe.com/charges",
                     json={"id": "ch_123", "status": "succeeded"})
       result = payment_service.charge(user_id=1, amount=50.00)
       assert result.success
   ```

4. **Use unique identifiers** instead of fixed values:
   ```python
   def test_create_user():
       email = f"test-{uuid4()}@example.com"  # unique per test run
       user = create_user(email=email)
       assert user.email == email
   ```

5. **Run tests in random order** to catch order dependencies (`pytest-randomly`)

---

### Issue #20: Monitoring and Alerting That Actually Works

**The Problem:**
The team has 500 alerts. Most are noise. Real incidents get lost in the flood. On-call engineers are burned out from alert fatigue and start ignoring pages.

**How to Fix:**

1. **Alert on symptoms, not causes:**
   ```
   BAD alerts:
   - CPU > 80%              (may be normal under load)
   - Memory > 70%           (garbage collector hasn't run yet)
   - Disk I/O is high       (batch job is running, it's fine)
   
   GOOD alerts:
   - Error rate > 1% for 5 minutes     (users are affected)
   - p99 latency > 2s for 10 minutes   (users are affected)
   - Success rate < 99.5%              (SLA is at risk)
   ```

2. **Use the RED method for service alerting:**
   - **R**ate: request throughput (sudden drops = problem)
   - **E**rrors: error rate percentage
   - **D**uration: latency percentiles (p50, p95, p99)

3. **Every alert must have:**
   - A clear description of what's wrong
   - A runbook link with steps to diagnose and fix
   - An owner team
   - A defined severity level

4. **Implement SLOs (Service Level Objectives):**
   ```
   SLO: 99.9% of requests complete successfully within 500ms
   Error budget: 0.1% = ~43 minutes of downtime per month
   
   Alert when: error budget burn rate > 2x normal for 1 hour
   (This means you're on track to blow through your monthly budget)
   ```

5. **Review alerts quarterly:** delete alerts that haven't fired or that fired but required no action

---

### Issue #21: DNS Resolution Failures Causing Outages

**Real-World Example:** GitHub experienced a major outage when an internal DNS change propagated incorrectly, causing services to be unable to find each other.

**The Problem:**
Your microservices communicate via hostnames. DNS resolution fails or returns stale records. Services suddenly can't reach their dependencies, even though everything is running fine.

**Why It Happens:**
- DNS TTL is cached at multiple layers (OS, language runtime, JVM, container)
- DNS provider outage or rate limiting
- Kubernetes service DNS resolution fails under high load
- Java's JVM caches DNS indefinitely by default

**How to Detect:**
```bash
# Check DNS resolution from inside a container
nslookup service-b.default.svc.cluster.local
dig +short service-b.default.svc.cluster.local

# Monitor DNS resolution time
time dig service-b.default.svc.cluster.local
```

**How to Fix:**
1. **Set appropriate DNS TTL caching in your language runtime:**
   ```java
   // Java: default is infinite caching! Override it.
   java.security.Security.setProperty("networkaddress.cache.ttl", "30");
   java.security.Security.setProperty("networkaddress.cache.negative.ttl", "5");
   ```
   ```python
   # Python requests library resolves DNS per connection by default — OK
   # But if using connection pooling, DNS is cached for the pool lifetime
   # Force periodic pool recycling:
   session = requests.Session()
   adapter = HTTPAdapter(pool_maxsize=10, pool_connections=10)
   # Recreate the session periodically (every 5 min)
   ```

2. **Use multiple DNS providers** or a local DNS cache (CoreDNS in Kubernetes, dnsmasq)

3. **Implement DNS failover:** if hostname resolution fails, retry with exponential backoff before failing the request

4. **For Kubernetes:** tune CoreDNS resources and enable `ndots` optimization in pod DNS config:
   ```yaml
   dnsConfig:
     options:
       - name: ndots
         value: "2"  # reduce unnecessary DNS search domain queries
   ```

**Lesson learned:** DNS is a hidden single point of failure. Monitor DNS resolution latency as a key infrastructure metric.

---

### Issue #22: Thundering Herd After a Service Restart

**Real-World Example:** Instagram experienced this when restarting a cache tier — millions of requests flooded the database simultaneously as all instances started with empty caches.

**The Problem:**
After deploying or restarting your service fleet, all instances start with cold caches. Every request results in a cache miss and hits the database. The database gets crushed under the sudden load.

**Why It Happens:**
- In-memory caches (application-level) are wiped on restart
- Redis flush or restart clears the shared cache
- Rolling deploys of many instances in quick succession
- Cache warm-up is not implemented

**How to Fix:**

1. **Cache warming on startup:**
   ```python
   async def warm_cache_on_startup():
       """Pre-load the most frequently accessed data into cache."""
       # Fetch top 1000 most accessed items
       popular_items = await db.fetch("""
           SELECT id, data FROM products
           ORDER BY access_count DESC
           LIMIT 1000
       """)
       pipeline = redis.pipeline()
       for item in popular_items:
           pipeline.setex(f"product:{item['id']}", 3600, json.dumps(item['data']))
       await pipeline.execute()
       logger.info(f"Cache warmed with {len(popular_items)} items")
   ```

2. **Staggered restarts:** don't restart all instances at once
   ```yaml
   # Kubernetes: rolling update with slow rollout
   spec:
     strategy:
       rollingUpdate:
         maxSurge: 1          # add 1 new pod at a time
         maxUnavailable: 0     # never reduce below desired count
   ```

3. **Request coalescing:** when multiple requests ask for the same key during warm-up, only one actually queries the database:
   ```python
   import asyncio
   
   _in_flight: dict[str, asyncio.Future] = {}
   
   async def get_with_coalescing(key: str):
       cached = await redis.get(key)
       if cached:
           return json.loads(cached)
       
       if key in _in_flight:
           return await _in_flight[key]  # wait for the in-flight request
       
       future = asyncio.get_event_loop().create_future()
       _in_flight[key] = future
       try:
           value = await fetch_from_db(key)
           await redis.setex(key, 3600, json.dumps(value))
           future.set_result(value)
           return value
       finally:
           del _in_flight[key]
   ```

4. **Use external cache (Redis) instead of in-process caches** so restarts don't wipe the cache

**Lesson learned:** Always plan for what happens when your cache is empty. If your system can't survive a cold cache, it's more fragile than you think.

---

### Issue #23: Disk Space Exhaustion from Unrotated Logs

**Real-World Example:** GitLab had a production incident where a server ran out of disk space because application logs were not being rotated, filling the disk over weeks.

**The Problem:**
The disk fills up gradually. The database stops accepting writes ("could not extend file: No space left on device"), log writes fail, and the entire application crashes.

**Why It Happens:**
- No log rotation configured
- Database WAL (Write-Ahead Log) segments accumulating (replication slot lag)
- Temp files from failed operations not cleaned up
- Docker container logs growing unbounded
- Large core dumps or heap dumps from crashes

**How to Detect:**
```bash
# Check disk usage
df -h

# Find largest files
du -sh /var/log/* | sort -rh | head -20

# PostgreSQL: check WAL size
SELECT pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(), '0/0')) AS wal_size;

# Docker: check container log sizes
docker system df -v
```

**How to Fix:**

1. **Configure log rotation:**
   ```yaml
   # Docker Compose: limit container log size
   services:
     api:
       logging:
         driver: "json-file"
         options:
           max-size: "50m"
           max-file: "5"
   ```
   ```bash
   # logrotate config for application logs
   /var/log/app/*.log {
       daily
       rotate 14
       compress
       delaycompress
       missingok
       notifempty
       maxsize 100M
   }
   ```

2. **Monitor disk usage and alert at 80%** — not 95% when it's too late

3. **PostgreSQL WAL management:**
   ```sql
   -- Check for stale replication slots (common cause of WAL bloat)
   SELECT slot_name, active, pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn)) AS lag
   FROM pg_replication_slots;
   
   -- Drop stale slots that are causing WAL accumulation
   SELECT pg_drop_replication_slot('stale_slot_name');
   ```

4. **Add a cron job** to clean temp files older than N days:
   ```bash
   find /tmp/app-uploads -mtime +7 -delete
   ```

5. **Ship logs to external storage** (ELK, CloudWatch, Datadog) and keep only recent logs on disk

**Lesson learned:** Disk space is the silent killer. Set up monitoring before it's an emergency. Every production server should have disk usage alerts.

---

### Issue #24: Connection Leaks from Unhandled Exceptions in ORM Code

**Real-World Example:** A fintech startup had their payment service freeze every few hours. The root cause was a database connection leak in an error handling path that only triggered for a specific payment provider error.

**The Problem:**
Your connection pool slowly drains. The app works fine for hours, then suddenly all requests start timing out waiting for a connection. Restarting fixes it temporarily.

**Why It Happens:**
- Exception thrown between acquiring a connection and releasing it
- ORM session not closed in error paths
- Connection borrowed for a background task but never returned
- Connection returned to pool in a broken state (half-finished transaction)

**How to Detect:**
```python
# Add connection pool event listeners to track borrows/returns
from sqlalchemy import event

@event.listens_for(engine, "checkout")
def on_checkout(dbapi_conn, connection_record, connection_proxy):
    logger.debug("Connection checked out", extra={
        "pool_size": engine.pool.size(),
        "checked_out": engine.pool.checkedout(),
        "overflow": engine.pool.overflow()
    })

@event.listens_for(engine, "checkin")
def on_checkin(dbapi_conn, connection_record):
    logger.debug("Connection returned", extra={
        "checked_out": engine.pool.checkedout()
    })
```

**How to Fix:**

1. **Always use context managers or middleware:**
   ```python
   # FastAPI: use dependency injection for sessions
   async def get_db():
       session = SessionLocal()
       try:
           yield session
       finally:
           await session.close()  # ALWAYS closes, even on exception
   
   @app.get("/users/{user_id}")
   async def get_user(user_id: int, db: Session = Depends(get_db)):
       return db.query(User).get(user_id)
   ```

2. **Configure pool recycle to close stale connections:**
   ```python
   engine = create_engine(
       DATABASE_URL,
       pool_size=20,
       max_overflow=5,
       pool_recycle=3600,        # recycle connections after 1 hour
       pool_pre_ping=True,       # test connection before using it
       pool_timeout=10,          # timeout waiting for connection
   )
   ```

3. **Set `pool_pre_ping=True`** (SQLAlchemy) to detect broken connections before use

4. **Monitor `checked_out` connection count** as a metric — it should stay relatively stable, not grow over time

**Lesson learned:** Connection leaks are the "slow puncture" of backend systems. They don't crash your app immediately — they degrade it over hours, making them hard to diagnose. Always use context managers.

---

### Issue #25: Infinite Retry Loops Amplifying Failures

**Real-World Example:** Amazon had a cascading failure where a single overloaded service caused all callers to retry aggressively, which made the overloaded service even worse, creating a feedback loop that took down multiple services.

**The Problem:**
Service A calls Service B. B is overloaded and returns 503. A retries immediately. But A isn't the only caller — hundreds of instances all retry simultaneously. B receives 10× its normal load from retries alone. B never recovers.

**Why It Happens:**
- Retry without backoff
- Retry on all errors (including 400 Bad Request, which will never succeed)
- No jitter in backoff — all clients retry at the same time
- No retry budget — unlimited retries amplify load

**How to Fix:**

1. **Exponential backoff with jitter:**
   ```python
   import random
   
   def retry_with_backoff(func, max_retries=3, base_delay=1.0):
       for attempt in range(max_retries):
           try:
               return func()
           except RetryableError:
               if attempt == max_retries - 1:
                   raise
               # Full jitter: random delay between 0 and exponential cap
               delay = random.uniform(0, base_delay * (2 ** attempt))
               time.sleep(delay)
   ```

2. **Only retry on retryable errors:**
   ```python
   RETRYABLE_STATUS_CODES = {429, 500, 502, 503, 504}
   
   def should_retry(status_code: int) -> bool:
       return status_code in RETRYABLE_STATUS_CODES
       # 400, 401, 403, 404, 422 → DO NOT retry (will always fail)
   ```

3. **Implement retry budgets:**
   ```python
   class RetryBudget:
       """Allow max 20% of requests to be retries."""
       def __init__(self, budget_ratio=0.2, window_seconds=60):
           self.budget_ratio = budget_ratio
           self.total_requests = 0
           self.retry_requests = 0
       
       def can_retry(self) -> bool:
           if self.total_requests == 0:
               return True
           return (self.retry_requests / self.total_requests) < self.budget_ratio
   ```

4. **Respect `Retry-After` headers** from the server:
   ```python
   if response.status_code == 429:
       retry_after = int(response.headers.get("Retry-After", 5))
       time.sleep(retry_after)
   ```

**Lesson learned:** Retries are a form of amplification. Without proper backoff, jitter, and budgets, retries make outages worse, not better. Every retry policy should answer: "What happens if every client retries at the same time?"

---

### Issue #26: Hot Partitions / Uneven Data Distribution

**Real-World Example:** A social media platform sharded their database by user ID. A celebrity with 50M followers caused their shard to receive 1000× more read traffic than other shards, effectively making sharding useless.

**The Problem:**
You've sharded your database or partitioned your queue, but one partition receives disproportionately more traffic than others. That partition becomes a bottleneck while other partitions sit idle.

**Why It Happens:**
- Poor shard key choice (e.g., sharding by country — US shard is 100× bigger than others)
- Power-law distribution in data (celebrity accounts, viral content, popular products)
- Time-based partitioning where all writes go to the "current" partition
- Sequential IDs cause all inserts to hit the same B-tree leaf page

**How to Fix:**

1. **Choose a shard key with high cardinality and even distribution:**
   ```
   BAD shard keys:
   - country (uneven distribution)
   - created_date (all writes hit today's partition)
   - status (most records are "active")
   
   GOOD shard keys:
   - user_id (hash-based, even distribution)
   - order_id (UUID, uniformly distributed)
   - composite key: hash(user_id) for distribution + user_id for locality
   ```

2. **Add a secondary caching layer for hot keys:**
   ```python
   # Detect and cache hot keys separately
   HOT_KEY_THRESHOLD = 1000  # requests per minute
   
   async def get_with_hot_key_detection(key: str):
       # Track access frequency
       count = await redis.incr(f"freq:{key}")
       if count == 1:
           await redis.expire(f"freq:{key}", 60)
       
       if count > HOT_KEY_THRESHOLD:
           # Replicate hot key across multiple cache shards
           shard = random.randint(0, 4)
           cached = await redis.get(f"hot:{key}:{shard}")
           if cached:
               return json.loads(cached)
       
       return await normal_get(key)
   ```

3. **Split hot partitions:** if one shard is hot, sub-shard it further

4. **Use consistent hashing** so that rebalancing only moves a minimal amount of data

5. **For Kafka:** increase partition count for hot topics and use a custom partitioner:
   ```java
   // Custom partitioner that spreads hot keys across partitions
   public int partition(String topic, Object key, ...) {
       if (isHotKey(key)) {
           return random.nextInt(numPartitions);  // spread across all partitions
       }
       return defaultPartition(key, numPartitions);
   }
   ```

**Lesson learned:** Sharding doesn't help if all the traffic goes to one shard. Always analyze your access patterns before choosing a partition strategy.

---

### Issue #27: Database Replication Lag Causing Stale Reads

**Real-World Example:** A user updates their profile, gets redirected to their profile page, and sees the old data because the read was served by a replica that hasn't caught up yet.

**The Problem:**
You have read replicas for scaling. Writes go to the primary, reads go to replicas. But replicas are slightly behind (milliseconds to seconds). Users see stale data immediately after writing.

**Why It Happens:**
- Asynchronous replication: the primary doesn't wait for replicas to confirm
- Heavy write load increases replication lag
- Replica is under-provisioned or processing a long-running query
- Network latency between primary and replica

**How to Fix:**

1. **Read-your-own-writes consistency:**
   ```python
   class DatabaseRouter:
       def get_connection(self, user_id: str, just_wrote: bool = False):
           if just_wrote:
               return self.primary  # read from primary after writes
           return self.replica
   
   # Track recent writes per user (short TTL)
   async def mark_recent_write(user_id: str):
       await redis.setex(f"recent_write:{user_id}", 5, "1")  # 5 second window
   
   async def should_read_primary(user_id: str) -> bool:
       return await redis.exists(f"recent_write:{user_id}")
   ```

2. **Sticky sessions for reads after writes:**
   ```python
   @app.middleware("http")
   async def route_db_reads(request, call_next):
       # If user recently wrote, route reads to primary for N seconds
       if request.cookies.get("_read_primary"):
           request.state.db = primary_db
       else:
           request.state.db = replica_db
       
       response = await call_next(request)
       
       # After a write operation, set a short-lived cookie
       if request.method in ("POST", "PUT", "PATCH", "DELETE"):
           response.set_cookie("_read_primary", "1", max_age=5)
       return response
   ```

3. **Monitor replication lag and alert:**
   ```sql
   -- PostgreSQL: check replication lag
   SELECT client_addr, state,
          pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) AS lag_bytes,
          now() - pg_last_xact_replay_timestamp() AS lag_time
   FROM pg_stat_replication;
   ```

4. **For critical reads** (e.g., payment verification), always read from the primary

**Lesson learned:** Eventually consistent reads are fine for most use cases, but "read after write" is a special case that users notice immediately. Handle it explicitly.

---

### Issue #28: Orphaned Resources and Data Leaks

**Real-World Example:** A SaaS company discovered they were storing 40TB of S3 objects for deleted accounts because the deletion flow only removed database records but not the associated files, costing them $900/month in unnecessary storage.

**The Problem:**
When you delete an entity, related data in other systems (file storage, search index, cache, third-party services) is left behind. Over time, orphaned data accumulates, increasing costs and creating compliance risks.

**Why It Happens:**
- Deletion logic only handles the primary database, not associated resources
- Microservices: deleting in Service A doesn't propagate to Service B
- Failed partial deletions leave things in an inconsistent state
- Soft deletes without a cleanup process for eventual hard delete

**How to Fix:**

1. **Event-driven cleanup:**
   ```python
   # When a user is deleted, publish an event
   async def delete_user(user_id: int):
       async with db.transaction():
           await db.execute("DELETE FROM users WHERE id = $1", user_id)
           await outbox.insert(event="UserDeleted", data={"user_id": user_id})
       
   # Each service listens and cleans up its own data
   # Storage service:
   async def handle_user_deleted(event):
       files = await s3.list_objects(Prefix=f"users/{event['user_id']}/")
       await s3.delete_objects(files)
   
   # Search service:
   async def handle_user_deleted(event):
       await elasticsearch.delete_by_query(index="users", 
           body={"query": {"term": {"user_id": event["user_id"]}}})
   ```

2. **Reconciliation jobs:** periodically scan for orphaned resources
   ```python
   async def find_orphaned_files():
       """Run weekly: find S3 objects with no matching database record."""
       s3_user_ids = set()
       for obj in await s3.list_objects(Prefix="users/"):
           user_id = obj.key.split("/")[1]
           s3_user_ids.add(user_id)
       
       db_user_ids = set(await db.fetch_column("SELECT id FROM users"))
       orphaned = s3_user_ids - db_user_ids
       
       logger.warning(f"Found {len(orphaned)} orphaned user directories in S3")
       for user_id in orphaned:
           await cleanup_queue.enqueue("delete_user_files", user_id)
   ```

3. **Foreign keys with CASCADE** for database-level cleanup:
   ```sql
   CREATE TABLE orders (
       id SERIAL PRIMARY KEY,
       user_id INT REFERENCES users(id) ON DELETE CASCADE
   );
   ```

4. **Track all associated resources** in a resource registry table when creating them

**Lesson learned:** Deletion is harder than creation. For every "create" path, design the corresponding "delete" path that covers all associated resources across all systems.

---

### Issue #29: Configuration Drift Between Environments

**Real-World Example:** A team deployed a feature that worked perfectly in staging. In production, it caused a 500 error because a required environment variable (`FEATURE_X_API_KEY`) existed in staging but had never been added to the production configuration.

**The Problem:**
Staging and production environments slowly diverge. A feature works in staging but fails in production because of missing environment variables, different database settings, or different feature flags.

**Why It Happens:**
- Manual configuration management (someone forgot to add the env var to production)
- Staging uses different infrastructure (smaller database, different Redis version)
- Feature flags set differently across environments
- Secrets added to staging but not requested for production

**How to Fix:**

1. **Validate configuration at startup:**
   ```python
   from pydantic import BaseSettings, validator
   
   class AppConfig(BaseSettings):
       DATABASE_URL: str
       REDIS_URL: str
       JWT_SECRET: str
       STRIPE_API_KEY: str
       FEATURE_X_ENABLED: bool = False
       FEATURE_X_API_KEY: str = ""
       
       @validator("FEATURE_X_API_KEY")
       def validate_feature_x_key(cls, v, values):
           if values.get("FEATURE_X_ENABLED") and not v:
               raise ValueError(
                   "FEATURE_X_API_KEY is required when FEATURE_X_ENABLED is True"
               )
           return v
       
       class Config:
           env_file = ".env"
   
   # App fails FAST on startup if config is invalid
   config = AppConfig()  # raises ValidationError with clear message
   ```

2. **Infrastructure as Code (IaC):** define all environments from the same templates
   ```hcl
   # Terraform: same module, different variables per environment
   module "api_service" {
     source      = "./modules/api"
     environment = var.environment  # "staging" or "production"
     db_instance = var.environment == "production" ? "db.r6g.xlarge" : "db.t3.medium"
     # All required env vars are defined in the module — can't be missed
   }
   ```

3. **Environment parity checks in CI:**
   ```python
   def test_config_parity():
       """Ensure staging and production have the same config keys."""
       staging_keys = load_config_keys("staging")
       production_keys = load_config_keys("production")
       missing = staging_keys - production_keys
       assert not missing, f"Production missing config keys: {missing}"
   ```

4. **Use a centralized config service** (Consul, AWS Parameter Store, Spring Cloud Config) with clear promotion workflows from staging → production

**Lesson learned:** "Works in staging" means nothing if staging doesn't match production. Validate configuration at startup and treat environment parity as a CI check.

---

### Issue #30: Uncontrolled Fan-Out in Microservices

**Real-World Example:** Twitter's early architecture had a fan-out problem where posting a tweet for a user with 30M followers meant writing to 30M timelines synchronously, causing massive delays.

**The Problem:**
A single user action triggers an explosion of downstream operations. One API call fans out to hundreds or thousands of internal calls, overwhelming downstream services.

**Why It Happens:**
- Notification systems: one event → notification to all subscribers
- Timeline/feed generation: write to every follower's feed
- Cascade of service calls: A calls B, B calls C and D, C calls E, F, G...
- Broadcasting updates to many cache keys

**How to Fix:**

1. **Fan-out on read vs fan-out on write:**
   ```
   Fan-out on WRITE (eager): pre-compute and store results for each consumer
   - Tweet posted → write to each follower's timeline
   - Fast reads, slow writes
   - Good for: most users (few followers)
   
   Fan-out on READ (lazy): compute the result at read time
   - User opens timeline → fetch tweets from all followed users, merge, sort
   - Fast writes, slower reads
   - Good for: celebrities (millions of followers)
   
   HYBRID approach (what Twitter does):
   - Regular users: fan-out on write
   - Celebrities (>500K followers): fan-out on read
   ```

2. **Asynchronous fan-out with rate limiting:**
   ```python
   async def notify_followers(user_id: int, event: dict):
       followers = await get_followers(user_id)
       
       if len(followers) > 10000:
           # Large fan-out: batch into queue jobs
           for batch in chunked(followers, 500):
               await queue.enqueue("send_notifications", {
                   "follower_ids": batch,
                   "event": event
               })
       else:
           # Small fan-out: process directly
           await send_notifications(followers, event)
   ```

3. **Depth limits on service call chains:**
   ```python
   # Propagate and enforce a call depth header
   @app.middleware("http")
   async def limit_call_depth(request, call_next):
       depth = int(request.headers.get("X-Call-Depth", 0))
       if depth > MAX_CALL_DEPTH:
           return JSONResponse(
               status_code=508,
               content={"error": "Call depth limit exceeded"}
           )
       request.state.call_depth = depth + 1
       return await call_next(request)
   ```

4. **Use pub/sub or streaming** instead of direct calls for high-fan-out scenarios

**Lesson learned:** Fan-out is where theoretical complexity becomes real-world pain. Count how many downstream operations each user action triggers and set explicit limits.

---

### Issue #31: Zombie Processes and Leaked Background Jobs

**Real-World Example:** A payments company had duplicate charges because crashed worker processes left jobs in an "in progress" state. When new workers started, they picked up new jobs but the old "in progress" jobs were never completed or failed — they just sat there. Periodic restart of the queue accidentally reprocessed them.

**The Problem:**
A background worker crashes mid-job. The job is marked as "processing" but no one is processing it. It stays stuck indefinitely, or worse, gets reprocessed later causing duplicate operations.

**Why It Happens:**
- Worker crash (OOM kill, unhandled exception, node failure)
- Job visibility timeout too long or too short
- No heartbeat mechanism to detect abandoned jobs
- Jobs are not idempotent, so re-processing causes side effects

**How to Fix:**

1. **Visibility timeout + heartbeat:**
   ```python
   class RobustWorker:
       async def process_job(self, job):
           # Job becomes invisible to other workers for 60 seconds
           # Worker must extend the timeout periodically (heartbeat)
           while not job.is_done():
               await job.extend_visibility(60)  # "I'm still working on this"
               await self.do_work_chunk(job)
           await job.ack()
       
       # If worker crashes, the visibility timeout expires,
       # and the job becomes available for another worker to pick up
   ```

2. **Stuck job detector:**
   ```python
   async def detect_stuck_jobs():
       """Run every 5 minutes: find jobs stuck in 'processing' too long."""
       stuck = await db.fetch("""
           SELECT id, started_at, worker_id FROM jobs
           WHERE status = 'processing'
             AND started_at < NOW() - INTERVAL '30 minutes'
       """)
       for job in stuck:
           logger.warning(f"Stuck job detected: {job['id']}, resetting")
           await db.execute("""
               UPDATE jobs SET status = 'pending', retry_count = retry_count + 1
               WHERE id = $1 AND status = 'processing'
           """, job['id'])
   ```

3. **Make all jobs idempotent** so re-processing is safe:
   ```python
   async def process_payment(job):
       # Use idempotency key to prevent double-charging
       existing = await db.fetch_one(
           "SELECT id FROM payments WHERE idempotency_key = $1",
           job.idempotency_key
       )
       if existing:
           logger.info(f"Payment already processed: {job.idempotency_key}")
           return  # skip — already done
       
       await charge_card(job.amount, job.card_token)
       await db.execute(
           "INSERT INTO payments (idempotency_key, amount, status) VALUES ($1, $2, 'completed')",
           job.idempotency_key, job.amount
       )
   ```

4. **Use mature job frameworks** that handle this natively (Celery, BullMQ, Sidekiq) instead of building your own

**Lesson learned:** Jobs in a distributed system will get interrupted. Design for it. Every job must be safe to retry, and every queue must have a mechanism to detect and recover stuck jobs.

---

### Issue #32: Unbounded Query Results Causing OOM

**Real-World Example:** An admin dashboard endpoint returned all users without pagination. With 10K users it was fine. After growing to 2M users, the endpoint loaded all records into memory, caused an OOM kill, and took down the service.

**The Problem:**
An endpoint or query returns an unbounded number of results. It works fine with small data, but as data grows, it consumes all available memory and crashes the process.

**Why It Happens:**
- No LIMIT on SQL queries
- ORM `.all()` or `.find()` loading entire tables into memory
- API endpoints without pagination
- Export/report endpoints that build the entire result in memory
- Log or event tables that grow without bound

**How to Fix:**

1. **Always enforce a maximum limit:**
   ```python
   @app.get("/api/users")
   async def list_users(limit: int = Query(default=20, le=100), cursor: str = None):
       # Max 100 items per request, regardless of what the client asks for
       query = "SELECT * FROM users"
       if cursor:
           query += f" WHERE id > {decode_cursor(cursor)}"
       query += f" ORDER BY id LIMIT {limit + 1}"  # fetch 1 extra to detect "has_more"
       
       rows = await db.fetch(query)
       has_more = len(rows) > limit
       items = rows[:limit]
       
       return {
           "data": items,
           "pagination": {
               "next_cursor": encode_cursor(items[-1]["id"]) if has_more else None,
               "has_more": has_more
           }
       }
   ```

2. **Stream large exports** instead of buffering in memory:
   ```python
   @app.get("/api/users/export")
   async def export_users():
       async def generate():
           yield "id,name,email\n"  # CSV header
           async for batch in db.fetch_in_batches("SELECT * FROM users", batch_size=1000):
               for row in batch:
                   yield f"{row['id']},{row['name']},{row['email']}\n"
       
       return StreamingResponse(generate(), media_type="text/csv")
   ```

3. **Add a safety LIMIT to all internal queries:**
   ```python
   # Even internal helper functions should have limits
   async def get_user_orders(user_id: int, limit: int = 1000):
       return await db.fetch(
           "SELECT * FROM orders WHERE user_id = $1 ORDER BY created_at DESC LIMIT $2",
           user_id, limit
       )
   ```

4. **Database-side protection:**
   ```sql
   -- PostgreSQL: set a statement timeout to kill runaway queries
   SET statement_timeout = '30s';
   
   -- Set a max rows limit for specific roles
   ALTER ROLE api_user SET statement_timeout = '10s';
   ```

**Lesson learned:** Every query that can return more than one row MUST have a LIMIT. No exceptions. This is one of the most common causes of production OOM crashes.

---

### Issue #33: Improper Error Handling Leaking Internal Details

**Real-World Example:** A banking app returned full stack traces including database connection strings, internal IP addresses, and table names in error responses. An attacker used this information to craft targeted SQL injection attacks.

**The Problem:**
Error responses expose internal implementation details: stack traces, database errors, file paths, internal service URLs, or configuration values.

**Why It Happens:**
- Framework default error handlers return full exception details
- Debug mode accidentally left on in production
- Catching and re-raising exceptions without sanitizing the message
- ORM/database errors passed directly to the client

**How to Fix:**

1. **Global error handler that sanitizes responses:**
   ```python
   @app.exception_handler(Exception)
   async def global_exception_handler(request, exc):
       # Generate a unique error ID for correlation
       error_id = str(uuid4())
       
       # Log the FULL error internally (for debugging)
       logger.error(
           "Unhandled exception",
           error_id=error_id,
           exc_info=exc,
           path=request.url.path,
           method=request.method,
       )
       
       # Return a SAFE error to the client (no internals)
       return JSONResponse(
           status_code=500,
           content={
               "error": {
                   "code": "INTERNAL_ERROR",
                   "message": "An unexpected error occurred",
                   "error_id": error_id  # client can report this for support
               }
           }
       )
   
   @app.exception_handler(ValueError)
   async def validation_error_handler(request, exc):
       return JSONResponse(
           status_code=400,
           content={
               "error": {
                   "code": "VALIDATION_ERROR",
                   "message": str(exc)  # safe — this is our own validation message
               }
           }
       )
   ```

2. **Map database errors to safe messages:**
   ```python
   DB_ERROR_MAP = {
       "UniqueViolation": ("DUPLICATE_RESOURCE", "A resource with this identifier already exists"),
       "ForeignKeyViolation": ("INVALID_REFERENCE", "Referenced resource does not exist"),
       "CheckViolation": ("VALIDATION_ERROR", "Data validation failed"),
   }
   
   @app.exception_handler(DatabaseError)
   async def db_error_handler(request, exc):
       error_type = type(exc).__name__
       code, message = DB_ERROR_MAP.get(error_type, ("INTERNAL_ERROR", "An error occurred"))
       # NEVER include exc.detail or the SQL query in the response
       return JSONResponse(status_code=400, content={"error": {"code": code, "message": message}})
   ```

3. **Ensure debug mode is OFF in production:**
   ```python
   # FastAPI
   app = FastAPI(debug=False)  # NEVER True in production
   
   # Django
   DEBUG = os.getenv("ENVIRONMENT") != "production"
   ```

4. **Test that errors don't leak information:**
   ```python
   def test_internal_error_does_not_leak():
       response = client.get("/api/trigger-error")
       assert response.status_code == 500
       body = response.json()
       assert "traceback" not in str(body).lower()
       assert "postgresql" not in str(body).lower()
       assert "password" not in str(body).lower()
       assert "error_id" in body["error"]  # but does include error ID for support
   ```

**Lesson learned:** Every error message visible to a client is also visible to an attacker. Internal details belong in logs, not responses. Always use a global error handler.

---

### Issue #34: Clock Skew in Distributed Systems

**Real-World Example:** Google's Spanner database was designed specifically to solve this problem. Before Spanner, Google engineers dealt with subtle data corruption bugs where two servers disagreed about "which write happened first" because their clocks were slightly off.

**The Problem:**
You rely on timestamps to order events, expire tokens, or determine "latest write wins." But different servers have slightly different clocks (milliseconds to seconds of drift). This causes: expired tokens being accepted, "latest" write actually being the earlier one, distributed cache entries expiring at different times.

**Why It Happens:**
- NTP (Network Time Protocol) synchronization is not instant or perfectly accurate
- VMs and containers can have worse clock drift than bare metal
- Leap seconds cause clocks to jump
- System clock can be adjusted backward by NTP

**How to Fix:**

1. **Use logical clocks / version vectors for ordering:**
   ```python
   # Instead of comparing timestamps from different servers,
   # use a monotonically increasing version number
   
   # Optimistic concurrency with version counter
   UPDATE documents
   SET content = 'new content', version = version + 1
   WHERE id = 42 AND version = 5;
   -- Only succeeds if no one else updated since we read version 5
   ```

2. **Use a centralized timestamp authority for critical ordering:**
   ```python
   # For distributed ID generation with ordering guarantees, use:
   # - Database sequences (single source of truth)
   # - Snowflake IDs (timestamp + worker ID + sequence)
   
   def generate_snowflake_id(worker_id: int, sequence: int) -> int:
       timestamp = int(time.time() * 1000) - EPOCH
       return (timestamp << 22) | (worker_id << 12) | sequence
   ```

3. **Add tolerance windows for time-sensitive operations:**
   ```python
   # JWT verification with clock skew tolerance
   jwt.decode(
       token,
       secret,
       algorithms=["HS256"],
       leeway=timedelta(seconds=30)  # accept tokens up to 30s past expiry
   )
   ```

4. **Use monotonic clocks** for measuring durations (not wall clock):
   ```python
   # BAD — wall clock can jump backward
   start = time.time()
   do_work()
   duration = time.time() - start  # could be negative!
   
   # GOOD — monotonic clock only moves forward
   start = time.monotonic()
   do_work()
   duration = time.monotonic() - start  # always positive
   ```

5. **Monitor clock drift** across your fleet and alert if drift exceeds your tolerance

**Lesson learned:** Never assume clocks are synchronized in a distributed system. Use logical ordering where possible, and add tolerance windows where you must use wall-clock time.

---

### Issue #35: Unexpected Costs from Auto-Scaling Gone Wrong

**Real-World Example:** A startup woke up to a $72,000 AWS bill after a bug caused their service to retry a failing API call in a tight loop. The retries triggered auto-scaling to spin up hundreds of instances, each also retrying in a tight loop.

**The Problem:**
Auto-scaling works correctly — it detects high CPU/memory usage and adds more instances. But the root cause isn't legitimate traffic; it's a bug, an infinite loop, or a retry storm. More instances just means more resources wasting money on the same bug.

**Why It Happens:**
- Auto-scaling based on CPU without considering error rates
- No spending alerts or budget caps
- Retry loops causing artificial load
- Missing or incorrect max instance limits
- A crawling bot or DDoS triggering legitimate-looking traffic

**How to Fix:**

1. **Always set max instance limits:**
   ```yaml
   # Kubernetes HPA
   spec:
     minReplicas: 2
     maxReplicas: 20  # NEVER omit this
   
   # AWS Auto Scaling Group
   MaxSize: 20  # hard cap
   ```

2. **Scale on the right signals (not just CPU):**
   ```yaml
   # Scale based on request queue depth, not CPU
   metrics:
   - type: External
     external:
       metric:
         name: http_requests_per_second
       target:
         type: AverageValue
         averageValue: 100
   # AND add a condition: only scale if error rate < 10%
   # High CPU + high error rate = bug, not traffic
   ```

3. **Set up billing alerts and budget caps:**
   ```
   AWS Budget Alert:
   - Alert at 50% of monthly budget
   - Alert at 80% of monthly budget
   - Auto-action at 100%: prevent new resource creation
   ```

4. **Add circuit breakers on auto-scaling triggers:**
   ```python
   # Custom scaling logic: don't scale up if error rate is high
   async def should_scale_up():
       error_rate = await prometheus.query("rate(http_errors_total[5m])")
       request_rate = await prometheus.query("rate(http_requests_total[5m])")
       
       if error_rate / request_rate > 0.1:
           logger.warning("High error rate detected, suppressing scale-up")
           return False
       return True
   ```

5. **Anomaly detection on spend:** alert if daily spend exceeds 2× the 7-day average

**Lesson learned:** Auto-scaling without spending limits is an uncapped credit card for bugs. Always set max limits and tie scaling decisions to healthy traffic signals, not just resource utilization.

---

### Issue #36: Idempotency Failures Causing Duplicate Operations

**Real-World Example:** Stripe built their entire API around idempotency keys because payment double-charges are one of the most damaging bugs possible. Even with this, they documented cases where incorrect idempotency key usage by clients led to duplicate charges or dropped payments.

**The Problem:**
A network timeout occurs during a payment API call. The client doesn't know if the server received the request. It retries. The server processes it twice. The customer is charged twice.

**Why It Happens:**
- Network timeouts where the request was received but the response was lost
- Client retries on any error (including successful operations that timed out)
- Load balancer retries on 502/503
- Message queue delivers the same message twice (at-least-once delivery)

**How to Fix:**

1. **Implement server-side idempotency keys:**
   ```python
   @app.post("/api/payments")
   async def create_payment(request: PaymentRequest, idempotency_key: str = Header()):
       # Check if we've already processed this key
       existing = await redis.get(f"idempotency:{idempotency_key}")
       if existing:
           return json.loads(existing)  # return the same response as before
       
       # Process the payment
       result = await process_payment(request)
       
       # Store the result keyed by idempotency key (TTL: 24 hours)
       await redis.setex(
           f"idempotency:{idempotency_key}",
           86400,
           json.dumps(result)
       )
       return result
   ```

2. **Database-level idempotency with unique constraints:**
   ```sql
   CREATE TABLE payments (
       id SERIAL PRIMARY KEY,
       idempotency_key TEXT UNIQUE NOT NULL,  -- prevents duplicate inserts
       amount DECIMAL NOT NULL,
       status TEXT NOT NULL,
       created_at TIMESTAMPTZ DEFAULT NOW()
   );
   
   -- INSERT ... ON CONFLICT for atomic upsert
   INSERT INTO payments (idempotency_key, amount, status)
   VALUES ('pay_abc123', 50.00, 'pending')
   ON CONFLICT (idempotency_key) DO NOTHING
   RETURNING *;
   ```

3. **Make all state changes naturally idempotent where possible:**
   ```python
   # BAD — not idempotent (running twice adds $50 twice)
   UPDATE accounts SET balance = balance + 50 WHERE id = 1;
   
   # GOOD — idempotent (running twice sets the same value)
   UPDATE accounts SET balance = 150 WHERE id = 1 AND balance = 100;
   
   # GOOD — idempotent via unique transaction reference
   INSERT INTO ledger (transaction_ref, account_id, amount)
   VALUES ('txn_abc123', 1, 50)
   ON CONFLICT (transaction_ref) DO NOTHING;
   ```

4. **Client-side:** always generate an idempotency key before the first attempt and reuse it for retries

**Lesson learned:** In a world of unreliable networks, every write operation must be safe to execute more than once. This is not optional for financial operations — it's the difference between a minor retry and a customer support nightmare.

---

### Issue #37: Dependency Injection Nightmares in Tests

**Real-World Example:** A team spent 3 months building a microservice with hard-coded dependencies (direct HTTP calls, direct database connections, direct AWS SDK calls). When they tried to write integration tests, they realized they couldn't test anything without spinning up the entire infrastructure. Test suite took 45 minutes to run.

**The Problem:**
Your code directly instantiates its dependencies. You can't replace the real database, the real HTTP client, or the real message queue with test doubles. Testing requires the entire infrastructure to be running.

**Why It Happens:**
- Importing and using clients directly at the module level
- Business logic tightly coupled to infrastructure
- No interfaces or abstractions between layers

**How to Fix:**

1. **Constructor/function injection:**
   ```python
   # BAD — tightly coupled, can't test without real Redis
   import redis
   cache = redis.Redis()
   
   class UserService:
       def get_user(self, user_id):
           cached = cache.get(f"user:{user_id}")  # hard-coded dependency
           ...
   
   # GOOD — dependency is injected, easily swapped in tests
   class UserService:
       def __init__(self, cache, db):
           self.cache = cache
           self.db = db
       
       async def get_user(self, user_id: int):
           cached = await self.cache.get(f"user:{user_id}")
           if cached:
               return json.loads(cached)
           user = await self.db.fetch_one("SELECT * FROM users WHERE id = $1", user_id)
           await self.cache.setex(f"user:{user_id}", 3600, json.dumps(user))
           return user
   ```

2. **Test with fakes, not the real thing:**
   ```python
   class FakeCache:
       def __init__(self):
           self.store = {}
       
       async def get(self, key):
           return self.store.get(key)
       
       async def setex(self, key, ttl, value):
           self.store[key] = value
   
   # Test is fast, isolated, and doesn't need Redis running
   async def test_get_user_caches_result():
       cache = FakeCache()
       db = FakeDB(users=[{"id": 1, "name": "Alice"}])
       service = UserService(cache=cache, db=db)
       
       user = await service.get_user(1)
       assert user["name"] == "Alice"
       assert "user:1" in cache.store  # verify it was cached
   ```

3. **FastAPI dependency injection (built-in):**
   ```python
   # Production
   def get_cache():
       return redis.Redis()
   
   @app.get("/users/{user_id}")
   async def get_user(user_id: int, cache = Depends(get_cache)):
       ...
   
   # Test: override the dependency
   app.dependency_overrides[get_cache] = lambda: FakeCache()
   ```

4. **Separate business logic from I/O:**
   ```python
   # Pure business logic — easy to test, no I/O
   def calculate_order_total(items: list[OrderItem], tax_rate: float) -> Decimal:
       subtotal = sum(item.price * item.quantity for item in items)
       return subtotal * (1 + tax_rate)
   
   # I/O orchestration — thin layer that calls business logic
   async def create_order(request, db, cache):
       items = await db.get_cart_items(request.cart_id)
       tax_rate = await tax_service.get_rate(request.shipping_address)
       total = calculate_order_total(items, tax_rate)  # easy to unit test
       await db.insert_order(request.user_id, items, total)
   ```

**Lesson learned:** If you can't test a function without the internet, a database, and a message queue, you have an architecture problem, not a testing problem. Inject dependencies from day one.

---

### Issue #38: Incorrect Pagination Under Concurrent Writes

**Real-World Example:** A team used offset-based pagination (`OFFSET 20 LIMIT 20`) for a listing page. When a new item was inserted while a user was paginating, they'd see duplicate items on the next page or miss items entirely.

**The Problem:**
With offset-based pagination, if rows are inserted or deleted between page requests, the "window" shifts. Users see duplicates or miss records entirely.

```
Page 1 (OFFSET 0 LIMIT 3):  [A, B, C]
-- New item "X" inserted at position 2 --
Page 2 (OFFSET 3 LIMIT 3):  [C, D, E]  ← "C" appears again! "X" is missed!
```

**Why It Happens:**
- OFFSET counts from the beginning of the result set each time
- Inserts and deletes shift row positions between requests
- More frequent writes = more pagination bugs

**How to Fix:**

1. **Use cursor-based (keyset) pagination:**
   ```python
   @app.get("/api/items")
   async def list_items(after: str = None, limit: int = 20):
       query = "SELECT * FROM items"
       params = []
       
       if after:
           # Use the last seen ID as a cursor — stable regardless of inserts
           cursor_id = decode_cursor(after)
           query += " WHERE id > $1"
           params.append(cursor_id)
       
       query += " ORDER BY id ASC LIMIT $" + str(len(params) + 1)
       params.append(limit + 1)
       
       rows = await db.fetch(query, *params)
       has_more = len(rows) > limit
       items = rows[:limit]
       
       return {
           "data": items,
           "pagination": {
               "next_cursor": encode_cursor(items[-1]["id"]) if has_more else None,
               "has_more": has_more
           }
       }
   ```

2. **For sorted results (not by ID), use composite cursors:**
   ```sql
   -- Cursor-based pagination sorted by created_at (with tie-breaking on id)
   SELECT * FROM items
   WHERE (created_at, id) > ('2026-04-08T10:00:00Z', 42)
   ORDER BY created_at ASC, id ASC
   LIMIT 20;
   ```

3. **When offset pagination is unavoidable** (e.g., "jump to page 50"):
   - Accept that results may shift slightly under concurrent writes
   - Document this behavior for API consumers
   - Use `COUNT(*)` with caution — it can be expensive on large tables

4. **For admin dashboards** where exact paging is critical, snapshot the query:
   ```python
   # Create a materialized snapshot for the pagination session
   snapshot_id = str(uuid4())
   await db.execute("""
       CREATE TEMP TABLE snapshot_{} AS
       SELECT * FROM items WHERE status = 'active' ORDER BY created_at
   """.format(snapshot_id))
   # Paginate against the snapshot (won't shift)
   ```

**Lesson learned:** Offset pagination is broken by design in any system with concurrent writes. Use cursor-based pagination for all user-facing APIs.

---

### Issue #39: Cascading Schema Migration Failures

**Real-World Example:** A team ran a migration that added a NOT NULL column with a default value on a 500M row table. The migration locked the table for 20 minutes, causing a full outage. The rollback also took 20 minutes.

**The Problem:**
Schema migrations that seem simple can lock tables, cause outages, or leave the database in an inconsistent state that's hard to roll back.

**Dangerous migrations:**
```sql
-- DANGEROUS: locks entire table for duration of rewrite
ALTER TABLE users ADD COLUMN status TEXT NOT NULL DEFAULT 'active';

-- DANGEROUS: full table rewrite
ALTER TABLE orders ALTER COLUMN amount TYPE NUMERIC(12,2);

-- DANGEROUS: scans entire table
CREATE UNIQUE INDEX CONCURRENTLY idx_users_email ON users(email);
-- (CONCURRENTLY helps but still takes time and can fail)
```

**How to Fix:**

1. **Break dangerous migrations into safe steps:**
   ```sql
   -- SAFE: Add nullable column (instant, no table lock)
   ALTER TABLE users ADD COLUMN status TEXT;
   
   -- SAFE: Set default for new rows (instant in PostgreSQL 11+)
   ALTER TABLE users ALTER COLUMN status SET DEFAULT 'active';
   
   -- SAFE: Backfill in batches (no table lock)
   -- Run via application code with batching + sleep
   
   -- SAFE: Add NOT NULL constraint (after all rows have values)
   ALTER TABLE users ALTER COLUMN status SET NOT NULL;
   ```

2. **Use database migration linters in CI:**
   ```yaml
   # squawk — PostgreSQL migration linter
   - name: Lint migrations
     run: squawk lint migrations/*.sql
   
   # Catches dangerous patterns:
   # - Adding NOT NULL column without default
   # - Creating index without CONCURRENTLY
   # - Changing column type (causes table rewrite)
   ```

3. **Always test migrations against production-sized data:**
   ```bash
   # Restore a production snapshot to staging
   # Run the migration and measure:
   # - How long it takes
   # - Whether it locks the table
   # - Whether queries still work during migration
   pg_restore --dbname=staging production_backup.dump
   time psql staging -f migration.sql
   ```

4. **Make migrations reversible:**
   ```python
   # Alembic (Python)
   def upgrade():
       op.add_column("users", sa.Column("status", sa.Text()))
   
   def downgrade():
       op.drop_column("users", "status")
   ```

5. **Follow the expand-contract pattern** (covered in Issue #13) for all non-trivial changes

**Lesson learned:** Never run an untested migration on a large production table. Every migration should be tested against realistic data volumes, and CI should lint for dangerous patterns.

---

### Issue #40: Handling Partial Failures in Third-Party API Calls

**Real-World Example:** An e-commerce checkout calls 4 external services: payment gateway, shipping provider, tax calculator, and email service. The payment succeeds, but the shipping API returns a 500. Now you have a charged customer with no shipment created.

**The Problem:**
Your business operation depends on multiple external APIs. Some succeed, some fail. You're left in a partial state that's hard to recover from.

**Why It Happens:**
- External APIs are unreliable (timeouts, rate limits, outages)
- No compensation logic for partial failures
- Treating the entire flow as a single operation when it's really multiple independent operations
- No distinction between critical and non-critical steps

**How to Fix:**

1. **Separate critical from non-critical operations:**
   ```python
   async def checkout(order):
       # CRITICAL — must succeed or abort
       payment = await payment_gateway.charge(order.total)
       if not payment.success:
           raise PaymentError("Payment failed")
       
       try:
           # CRITICAL — must succeed or refund
           shipment = await shipping_api.create(order.items, order.address)
       except ShippingError:
           await payment_gateway.refund(payment.id)  # compensate
           raise
       
       # NON-CRITICAL — failure is OK, retry later
       try:
           await email_service.send_confirmation(order)
       except EmailError:
           await queue.enqueue("retry_email", {"order_id": order.id})
           # Don't fail the checkout over an email
       
       try:
           await analytics.track("checkout_completed", order)
       except Exception:
           pass  # analytics failure should never affect user experience
   ```

2. **Use a state machine to track multi-step processes:**
   ```python
   class OrderState(Enum):
       CREATED = "created"
       PAYMENT_CHARGED = "payment_charged"
       SHIPMENT_CREATED = "shipment_created"
       COMPLETED = "completed"
       PAYMENT_REFUNDED = "payment_refunded"
       FAILED = "failed"
   
   async def advance_order(order_id: int):
       order = await db.get_order(order_id)
       
       if order.state == OrderState.CREATED:
           try:
               await charge_payment(order)
               await update_state(order, OrderState.PAYMENT_CHARGED)
           except PaymentError:
               await update_state(order, OrderState.FAILED)
               return
       
       if order.state == OrderState.PAYMENT_CHARGED:
           try:
               await create_shipment(order)
               await update_state(order, OrderState.SHIPMENT_CREATED)
           except ShippingError:
               await refund_payment(order)
               await update_state(order, OrderState.PAYMENT_REFUNDED)
               return
       
       # ... each state transition is retryable
   ```

3. **Reconciliation worker for stuck orders:**
   ```python
   async def reconcile_stuck_orders():
       """Run every 10 minutes: find and fix orders stuck in intermediate states."""
       stuck = await db.fetch("""
           SELECT * FROM orders
           WHERE state NOT IN ('completed', 'failed', 'payment_refunded')
             AND updated_at < NOW() - INTERVAL '30 minutes'
       """)
       for order in stuck:
           logger.warning(f"Reconciling stuck order: {order['id']}")
           await advance_order(order['id'])  # retry from current state
   ```

4. **Store the state of each external call** so you know exactly where things failed and can resume

**Lesson learned:** Multi-step operations across external services will have partial failures. Design each step to be retryable and reversible. Never assume all external calls will succeed.

---

---

## Appendix B: Common Mistakes by Junior / Inexperienced Engineers

> These are not exotic production incidents — they are **everyday mistakes** made by engineers who lack experience or skipped fundamentals. Learning to recognize and avoid them early will save you months of debugging and rework. Each entry explains the mistake, why juniors make it, what goes wrong, and what the correct approach looks like.

---

### Issue #41: Trusting Client-Side Input Blindly

**The Mistake:**
A junior engineer builds a discount endpoint: the client sends `{ "price": 9.99, "discount": 80 }` and the server applies it without checking. An attacker sends `{ "price": 0.01, "discount": 100 }` and gets everything for free.

**Why Juniors Make This Mistake:**
- They build features from the frontend perspective ("the UI only shows valid options")
- They assume requests only come from their own frontend
- They don't understand that anyone can craft raw HTTP requests with curl or Postman

**What Goes Wrong:**
- Price manipulation, unauthorized access escalation
- Users giving themselves admin roles: `{ "role": "admin" }` in a registration body
- Bypassing business rules by sending impossible values

**The Correct Approach:**

1. **Never trust any data from the client — validate everything server-side:**
   ```python
   from pydantic import BaseModel, validator, Field
   
   class CreateOrderRequest(BaseModel):
       product_id: int
       quantity: int = Field(gt=0, le=100)  # 1-100 only
       # NO price field — server looks up the price
       # NO role field — server determines permissions
       
       @validator("quantity")
       def reasonable_quantity(cls, v):
           if v > 100:
               raise ValueError("Cannot order more than 100 items at once")
           return v
   
   @app.post("/api/orders")
   async def create_order(request: CreateOrderRequest, user: User = Depends(get_current_user)):
       # Server fetches the REAL price — never accept price from client
       product = await db.get_product(request.product_id)
       total = product.price * request.quantity
       
       # Server determines what user can do — never accept role from client
       order = await db.create_order(
           user_id=user.id,  # from auth token, not request body
           product_id=request.product_id,
           quantity=request.quantity,
           total=total  # calculated server-side
       )
       return order
   ```

2. **Allowlist fields** — only accept fields you explicitly expect:
   ```python
   # BAD — accepts any field the client sends, including "is_admin"
   user = User(**request.json())
   
   # GOOD — only extract the fields you expect
   user = User(
       name=request.name,
       email=request.email,
       # role is NOT accepted from the client
   )
   ```

3. **Validate at system boundaries, not just in the UI:**
   - Input validation (types, ranges, formats)
   - Business rule validation (can this user perform this action?)
   - Authorization checks (does this user own this resource?)

**Rule of thumb:** If the client could benefit from lying about a value, don't let the client provide that value.

---

### Issue #42: Storing Passwords in Plain Text or Using Weak Hashing

**The Mistake:**
A junior stores passwords as `MD5(password)` or worse, in plain text. When the database is breached, all user passwords are instantly compromised.

**Why Juniors Make This Mistake:**
- They've heard of hashing but use fast hashes (MD5, SHA-256) not designed for passwords
- They don't understand the difference between hashing and password hashing
- They skip salting because they don't understand rainbow table attacks

**What Goes Wrong:**
- MD5/SHA-256 can be cracked at billions of hashes per second on a GPU
- Without salt, identical passwords produce identical hashes (rainbow tables)
- A single database breach exposes every user's password

**The Correct Approach:**

```python
# Use bcrypt or argon2 — NEVER MD5, SHA-1, or SHA-256 for passwords
import bcrypt

def hash_password(plain_password: str) -> str:
    """Hash a password with bcrypt. Salt is automatically generated and embedded."""
    salt = bcrypt.gensalt(rounds=12)  # ~250ms per hash — intentionally slow
    return bcrypt.hashpw(plain_password.encode(), salt).decode()

def verify_password(plain_password: str, hashed: str) -> bool:
    """Verify a password against its hash."""
    return bcrypt.checkpw(plain_password.encode(), hashed.encode())

# Usage
stored_hash = hash_password("user_password_123")
# stored_hash = "$2b$12$LJ3m4ys..." — includes algorithm, cost, salt, and hash

is_valid = verify_password("user_password_123", stored_hash)  # True
```

**Why bcrypt/argon2 and not SHA-256:**
| Algorithm | Speed (per second on GPU) | Purpose |
|-----------|--------------------------|---------|
| MD5 | ~50 billion | Checksums, NOT passwords |
| SHA-256 | ~5 billion | Data integrity, NOT passwords |
| bcrypt (cost=12) | ~5,000 | Password hashing (intentionally slow) |
| argon2id | ~1,000 | Modern password hashing (memory-hard) |

**Additional rules:**
- Never implement your own crypto
- Never limit password length to fewer than 64 characters
- Never store the salt separately — bcrypt embeds it automatically
- Enforce minimum password length (8+), but don't enforce excessive complexity rules

---

### Issue #43: Not Handling Null / Empty / Missing Fields

**The Mistake:**
Code assumes a field always exists and always has a value. In production, a user submits a form with a missing optional field, or a database column is NULL, and the application crashes with `NoneType has no attribute 'lower'` or similar.

**Why Juniors Make This Mistake:**
- They test with complete, perfect data
- They don't think about edge cases: empty string, null, missing key, 0, false
- They confuse empty string `""`, `None`/`null`, `0`, and `false`

**What Goes Wrong:**
```python
# Crash: user.middle_name is None
full_name = user.first_name + " " + user.middle_name.strip() + " " + user.last_name

# Crash: "phone" key doesn't exist
phone = request_data["phone"]

# Logic bug: 0 is falsy but valid
if not quantity:   # True when quantity is 0, which may be a valid input
    raise Error("quantity is required")
```

**The Correct Approach:**

1. **Use explicit type annotations and optional markers:**
   ```python
   from typing import Optional
   from pydantic import BaseModel
   
   class UserProfile(BaseModel):
       first_name: str                     # required, must be non-empty string
       middle_name: Optional[str] = None   # explicitly optional
       email: str
       phone: Optional[str] = None
       age: Optional[int] = None           # None is different from 0
   ```

2. **Handle None before using a value:**
   ```python
   # SAFE
   full_name = user.first_name
   if user.middle_name:
       full_name += f" {user.middle_name}"
   full_name += f" {user.last_name}"
   
   # SAFE — use .get() with default for dicts
   phone = request_data.get("phone", "")
   
   # SAFE — explicit None check (don't confuse 0 with "missing")
   if quantity is None:
       raise ValueError("quantity is required")
   # Now 0 is accepted as a valid value
   ```

3. **Use database NOT NULL constraints** to prevent None where it shouldn't exist:
   ```sql
   CREATE TABLE users (
       id SERIAL PRIMARY KEY,
       email TEXT NOT NULL,           -- required
       phone TEXT,                    -- optional (NULL allowed)
       first_name TEXT NOT NULL,
       middle_name TEXT,              -- optional
       created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
   );
   ```

4. **Test with edge cases explicitly:**
   ```python
   @pytest.mark.parametrize("middle_name", [None, "", "  ", "Anne"])
   def test_full_name_with_various_middle_names(middle_name):
       user = User(first_name="Jane", middle_name=middle_name, last_name="Doe")
       result = format_full_name(user)
       assert result  # should never crash
   ```

**Rule of thumb:** Always ask "what happens if this value is None, empty, or missing?" for every field you access.

---

### Issue #44: Catching All Exceptions and Swallowing Errors Silently

**The Mistake:**
```python
# THE MOST DANGEROUS PATTERN IN BACKEND CODE
try:
    process_payment(order)
except Exception:
    pass  # "it works now, the error is gone!"
```

The payment fails, but the order is marked as successful. No log, no alert, no trace. The customer is never charged but receives the product. The bug goes undetected for weeks.

**Why Juniors Make This Mistake:**
- They hit an error, want to "handle" it quickly, and use a bare `except`
- They confuse "suppressing an error" with "handling an error"
- They don't know which specific exceptions to catch
- They were told "never let the app crash" and took it too literally

**What Goes Wrong:**
- Silent data corruption
- Business logic bypassed
- Bugs hidden for weeks/months until financial reconciliation catches them
- `except Exception` catches everything including `KeyboardInterrupt`, `SystemExit`, `MemoryError`

**The Correct Approach:**

1. **Catch specific exceptions:**
   ```python
   # BAD — catches everything, hides bugs
   try:
       user = db.get_user(user_id)
   except Exception:
       user = None
   
   # GOOD — catch only what you expect and can handle
   try:
       user = db.get_user(user_id)
   except UserNotFoundError:
       return {"error": "User not found"}, 404
   except DatabaseConnectionError:
       logger.error("Database connection failed", exc_info=True)
       return {"error": "Service temporarily unavailable"}, 503
   # Any other exception propagates up — as it should
   ```

2. **Always log when catching exceptions:**
   ```python
   try:
       send_notification(user_id, message)
   except NotificationServiceError as e:
       # Log the error with full context
       logger.error(
           "Failed to send notification",
           user_id=user_id,
           error=str(e),
           exc_info=True  # includes stack trace
       )
       # Decide: is this critical or can we continue?
       # Non-critical: continue but track the failure
       metrics.increment("notification_failures")
   ```

3. **Distinguish between recoverable and unrecoverable errors:**
   ```python
   # Recoverable: retry or fallback
   try:
       response = external_api.call(data)
   except TimeoutError:
       response = cache.get_fallback(data)  # graceful degradation
   
   # Unrecoverable: fail loudly, don't pretend it's OK
   try:
       await db.save_order(order)
   except IntegrityError:
       raise  # let it propagate — caller needs to know the order wasn't saved
   ```

4. **Never use bare `except:` (without specifying the exception type):**
   ```python
   # TERRIBLE — catches SystemExit, KeyboardInterrupt, MemoryError
   except:
       pass
   
   # STILL BAD — too broad
   except Exception:
       pass
   
   # OK — broad catch but at least logs it and re-raises
   except Exception as e:
       logger.error(f"Unexpected error: {e}", exc_info=True)
       raise
   ```

**Rule of thumb:** If you can't describe what specific error you're handling and why, you shouldn't be catching it.

---

### Issue #45: Hardcoding Configuration and Secrets

**The Mistake:**
```python
# Found in actual production code
DATABASE_URL = "postgresql://admin:P@ssw0rd123@prod-db.company.com:5432/mydb"
STRIPE_SECRET_KEY = "sk_test_1234567890abcdef"
API_BASE_URL = "http://localhost:8000"
```

The developer pushes this to GitHub. The credentials are now public. Bots scan for exposed API keys within minutes.

**Why Juniors Make This Mistake:**
- It's the fastest way to get things working
- They don't know about environment variables or secrets managers
- They think a private repo is secure enough
- They copy-paste from tutorials that use hardcoded values

**What Goes Wrong:**
- Credentials leaked to version control history (even if you delete the file, it's in git history forever)
- Different environments (dev, staging, prod) can't use different values
- Rotating a secret requires a code change and redeployment
- Automated scanners find exposed credentials within minutes on public repos

**The Correct Approach:**

1. **Use environment variables:**
   ```python
   import os
   
   DATABASE_URL = os.environ["DATABASE_URL"]        # required — crashes if missing (good!)
   STRIPE_KEY = os.environ["STRIPE_SECRET_KEY"]
   DEBUG = os.getenv("DEBUG", "false") == "true"     # optional with default
   ```

2. **Use a configuration library with validation:**
   ```python
   from pydantic import BaseSettings
   
   class Settings(BaseSettings):
       database_url: str
       redis_url: str
       stripe_secret_key: str
       debug: bool = False
       log_level: str = "INFO"
       
       class Config:
           env_file = ".env"  # loads from .env file in development
   
   settings = Settings()  # fails FAST if required vars are missing
   ```

3. **Always `.gitignore` your secrets files:**
   ```gitignore
   # .gitignore
   .env
   .env.local
   .env.production
   *.pem
   *.key
   credentials.json
   ```

4. **If you accidentally committed a secret:**
   ```bash
   # Rotate the secret IMMEDIATELY — assume it's compromised
   # Then remove from history (but still assume it was seen):
   git filter-branch --force --index-filter \
     "git rm --cached --ignore-unmatch .env" \
     --prune-empty -- --all
   ```

5. **For production, use a secrets manager:** AWS Secrets Manager, HashiCorp Vault, or GCP Secret Manager

**Rule of thumb:** If you see a password, API key, or connection string with real credentials in source code, it's already a security incident.

---

### Issue #46: Writing Business Logic Inside Route Handlers (Fat Controllers)

**The Mistake:**
```python
@app.post("/api/orders")
async def create_order(request: Request):
    data = await request.json()
    user = await db.fetch_one("SELECT * FROM users WHERE id = $1", data["user_id"])
    if not user:
        raise HTTPException(404, "User not found")
    
    items = []
    total = 0
    for item in data["items"]:
        product = await db.fetch_one("SELECT * FROM products WHERE id = $1", item["product_id"])
        if product["stock"] < item["quantity"]:
            raise HTTPException(400, f"Not enough stock for {product['name']}")
        items.append({"product": product, "quantity": item["quantity"]})
        total += product["price"] * item["quantity"]
    
    if data.get("coupon_code"):
        coupon = await db.fetch_one("SELECT * FROM coupons WHERE code = $1", data["coupon_code"])
        if coupon and coupon["expires_at"] > datetime.now():
            total = total * (1 - coupon["discount_percent"] / 100)
    
    tax = total * 0.1
    total_with_tax = total + tax
    
    order = await db.fetch_one(
        "INSERT INTO orders (user_id, total, tax) VALUES ($1, $2, $3) RETURNING *",
        user["id"], total_with_tax, tax
    )
    
    for item in items:
        await db.execute(
            "INSERT INTO order_items (order_id, product_id, quantity, price) VALUES ($1, $2, $3, $4)",
            order["id"], item["product"]["id"], item["quantity"], item["product"]["price"]
        )
        await db.execute(
            "UPDATE products SET stock = stock - $1 WHERE id = $2",
            item["quantity"], item["product"]["id"]
        )
    
    await send_email(user["email"], "Order Confirmation", f"Order #{order['id']} placed!")
    return order
```

This 40-line route handler is untestable, unreusable, and impossible to maintain.

**Why Juniors Make This Mistake:**
- It works, and getting it working feels like the goal
- They don't know about layered architecture
- Tutorials often show everything in one handler for simplicity
- They haven't worked in a codebase that grew beyond a few endpoints

**The Correct Approach — Layered Architecture:**

```python
# --- Layer 1: Route handler (thin — just HTTP concerns) ---
@app.post("/api/orders", status_code=201)
async def create_order(
    request: CreateOrderRequest,
    user: User = Depends(get_current_user),
    order_service: OrderService = Depends(get_order_service),
):
    order = await order_service.create_order(user.id, request.items, request.coupon_code)
    return order

# --- Layer 2: Service (business logic — no HTTP, no SQL) ---
class OrderService:
    def __init__(self, db: OrderRepository, inventory: InventoryService, email: EmailService):
        self.db = db
        self.inventory = inventory
        self.email = email
    
    async def create_order(self, user_id: int, items: list[OrderItem], coupon_code: str = None):
        # Validate stock
        await self.inventory.validate_stock(items)
        
        # Calculate pricing
        subtotal = sum(item.price * item.quantity for item in items)
        discount = await self._apply_coupon(coupon_code, subtotal)
        tax = (subtotal - discount) * Decimal("0.10")
        total = subtotal - discount + tax
        
        # Create order (single transaction)
        order = await self.db.create_order_with_items(user_id, items, total, tax)
        
        # Deduct stock
        await self.inventory.deduct_stock(items)
        
        # Non-critical: send email (don't fail the order if email fails)
        try:
            await self.email.send_order_confirmation(user_id, order)
        except EmailError:
            logger.warning(f"Failed to send confirmation for order {order.id}")
        
        return order

# --- Layer 3: Repository (data access — only SQL, no business logic) ---
class OrderRepository:
    async def create_order_with_items(self, user_id, items, total, tax):
        async with self.db.transaction():
            order = await self.db.fetch_one(
                "INSERT INTO orders (user_id, total, tax) VALUES ($1, $2, $3) RETURNING *",
                user_id, total, tax
            )
            for item in items:
                await self.db.execute(
                    "INSERT INTO order_items (...) VALUES (...)", ...
                )
            return order
```

**Benefits:**
- `OrderService` is testable without HTTP or a real database
- Business rules are in one place, not scattered across routes
- Swapping databases or email providers requires changing only one layer
- Multiple routes can reuse the same service logic

---

### Issue #47: Not Using Database Transactions When They're Needed

**The Mistake:**
```python
async def transfer_money(from_id: int, to_id: int, amount: float):
    # Step 1: Deduct from sender
    await db.execute("UPDATE accounts SET balance = balance - $1 WHERE id = $2", amount, from_id)
    
    # Step 2: Add to receiver
    await db.execute("UPDATE accounts SET balance = balance + $1 WHERE id = $2", amount, to_id)
    
    # Step 3: Record the transfer
    await db.execute("INSERT INTO transfers (...) VALUES (...)", from_id, to_id, amount)
```

If Step 2 fails (database error, network timeout), the sender loses money but the receiver doesn't get it. The money vanishes.

**Why Juniors Make This Mistake:**
- They don't understand what transactions are
- They think each SQL statement is independent and that's OK
- They test the happy path only, where all statements succeed
- They've never experienced a crash between two database calls

**What Goes Wrong:**
- Partial updates leave data inconsistent
- Money disappears or duplicates
- Orders are created without order items
- Users are charged but their subscription isn't activated

**The Correct Approach:**

```python
async def transfer_money(from_id: int, to_id: int, amount: Decimal):
    async with db.transaction():  # all-or-nothing
        # Check sufficient balance (with row lock to prevent race conditions)
        sender = await db.fetch_one(
            "SELECT balance FROM accounts WHERE id = $1 FOR UPDATE",
            from_id
        )
        if sender["balance"] < amount:
            raise InsufficientFundsError(f"Balance {sender['balance']} < {amount}")
        
        await db.execute(
            "UPDATE accounts SET balance = balance - $1 WHERE id = $2",
            amount, from_id
        )
        await db.execute(
            "UPDATE accounts SET balance = balance + $1 WHERE id = $2",
            amount, to_id
        )
        await db.execute(
            "INSERT INTO transfers (from_id, to_id, amount) VALUES ($1, $2, $3)",
            from_id, to_id, amount
        )
    # If ANY statement fails, ALL changes are rolled back automatically
```

**When you need a transaction:**
- Any operation that involves 2+ write statements that must succeed or fail together
- Read-then-write operations (check balance, then deduct)
- Creating a parent record and its children (order + order_items)
- Any operation where partial completion would leave data in a broken state

---

### Issue #48: Using `SELECT *` Everywhere

**The Mistake:**
```python
# Every query fetches ALL columns
users = await db.fetch("SELECT * FROM users")
products = await db.fetch("SELECT * FROM products")
orders = await db.fetch("SELECT * FROM orders JOIN order_items ON ...")
```

**Why Juniors Make This Mistake:**
- It's convenient and seems to always work
- They don't know which columns they actually need
- They don't think about performance until the app is slow

**What Goes Wrong:**
- Fetches unnecessary data (BLOBs, large text fields, JSON columns) — wastes memory and network bandwidth
- Makes queries slower — database reads more data from disk
- Breaks code when columns are added/removed/renamed
- Prevents covering indexes from being used (index must include all selected columns)
- Leaks sensitive columns (password hashes, internal notes) to API responses

**The Correct Approach:**

```python
# GOOD — only fetch what you need
users = await db.fetch("SELECT id, name, email FROM users")

# GOOD — API response doesn't accidentally include password_hash
@app.get("/api/users/{user_id}")
async def get_user(user_id: int):
    user = await db.fetch_one(
        "SELECT id, name, email, created_at FROM users WHERE id = $1",
        user_id
    )
    return user  # no password_hash, no internal fields

# GOOD — covering index can be used (all columns are in the index)
# Index: CREATE INDEX idx_users_email ON users(email) INCLUDE (name)
user = await db.fetch_one(
    "SELECT name, email FROM users WHERE email = $1",
    email
)
```

**When `SELECT *` is OK:**
- Quick debugging in a SQL terminal
- Inside a subquery where you immediately filter: `SELECT id FROM (SELECT * FROM ...)`
- Never in application code

---

### Issue #49: Not Understanding How Indexes Work

**The Mistake:**
A junior adds an index on every column, or no indexes at all, or adds an index but writes queries that can't use it.

```sql
-- Created this index:
CREATE INDEX idx_users_name ON users(name);

-- But writes this query (index is useless here):
SELECT * FROM users WHERE LOWER(name) = 'alice';  -- function on column prevents index use

-- Or this (wrong column order in composite index):
CREATE INDEX idx_orders ON orders(status, created_at);
SELECT * FROM orders WHERE created_at > '2026-01-01';  -- can't use index (status is the leftmost column)
```

**Why Juniors Make This Mistake:**
- They know indexes "make things faster" but don't understand how
- They don't check query execution plans with EXPLAIN
- They don't understand the leftmost prefix rule for composite indexes

**The Correct Approach:**

1. **Understand what an index is:** a sorted data structure (B-tree) that lets the database jump directly to matching rows instead of scanning every row.

2. **Know when indexes are used and when they're NOT:**
   ```sql
   -- INDEX IS USED:
   SELECT * FROM users WHERE email = 'alice@example.com';  -- equality on indexed column
   SELECT * FROM users WHERE created_at > '2026-01-01' ORDER BY created_at;  -- range + sort
   
   -- INDEX IS NOT USED:
   SELECT * FROM users WHERE LOWER(email) = 'alice@example.com';  -- function wrapping column
   -- Fix: CREATE INDEX idx_users_email_lower ON users(LOWER(email));  -- expression index
   
   SELECT * FROM users WHERE name LIKE '%alice%';  -- leading wildcard
   -- Fix: use full-text search or trigram index
   
   SELECT * FROM users WHERE age + 1 = 26;  -- arithmetic on column
   -- Fix: SELECT * FROM users WHERE age = 25;  -- move arithmetic to the value side
   ```

3. **Composite index leftmost prefix rule:**
   ```sql
   -- Index on (country, city, street)
   -- CAN use index:   WHERE country = 'US'
   -- CAN use index:   WHERE country = 'US' AND city = 'NYC'
   -- CAN use index:   WHERE country = 'US' AND city = 'NYC' AND street = 'Broadway'
   -- CANNOT use index: WHERE city = 'NYC'  (skipped leftmost column)
   -- CANNOT use index: WHERE street = 'Broadway'
   ```

4. **Always verify with EXPLAIN ANALYZE:**
   ```sql
   EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'alice@example.com';
   -- Look for: "Index Scan" (good) vs "Seq Scan" (bad on large tables)
   ```

---

### Issue #50: Ignoring Database Migrations — Editing Schema Manually

**The Mistake:**
A junior SSHes into the production database server and runs `ALTER TABLE` directly. Or they modify the schema in development and tell teammates "just run this SQL." There's no record of what changed, when, or why.

**Why Juniors Make This Mistake:**
- Migrations feel like unnecessary overhead for a "quick change"
- They don't know migration tools exist
- They've never worked on a team where multiple people modify the schema

**What Goes Wrong:**
- No record of schema changes — can't recreate the database from scratch
- Teammates' databases diverge — "works on my machine" but not theirs
- Can't roll back a bad schema change
- Staging and production schemas drift apart
- New developers can't set up the project without asking "which SQL scripts do I need to run?"

**The Correct Approach:**

```python
# Alembic (Python/SQLAlchemy) — generate a migration
# $ alembic revision --autogenerate -m "add phone column to users"

"""add phone column to users"""
revision = 'a1b2c3d4'
down_revision = 'e5f6g7h8'

def upgrade():
    op.add_column('users', sa.Column('phone', sa.String(20), nullable=True))

def downgrade():
    op.drop_column('users', 'phone')
```

```javascript
// Knex.js (Node.js) migration
exports.up = function(knex) {
    return knex.schema.table('users', (table) => {
        table.string('phone', 20).nullable();
    });
};

exports.down = function(knex) {
    return knex.schema.table('users', (table) => {
        table.dropColumn('phone');
    });
};
```

**Migration rules:**
- Every schema change goes through a migration file (checked into git)
- Migrations run in order and are tracked in a version table
- Every migration has an `upgrade` and `downgrade` function
- Never edit a migration that has already been applied to staging/production — create a new one
- New developer setup: `alembic upgrade head` — done

---

### Issue #51: Using Float for Money

**The Mistake:**
```python
price = 19.99
tax = price * 0.1  # Expected: 1.999 → 2.00
total = price + tax  # Expected: 21.99
print(total)         # Actual: 21.988999999999997
```

Over thousands of transactions, rounding errors accumulate. Financial reports don't balance. Customers see charges of $21.99 but the database stores $21.988999999999997.

**Why Juniors Make This Mistake:**
- Float is the default number type and "looks like it works"
- They learned floating-point in school but not its limitations
- The error is tiny and doesn't show up in basic testing

**What Goes Wrong:**
- Financial calculations are off by fractions of cents
- `0.1 + 0.2 = 0.30000000000000004` (IEEE 754 floating-point)
- Rounding errors compound over millions of transactions
- Audit failures, accounting discrepancies, legal issues

**The Correct Approach:**

1. **Use Decimal types everywhere for money:**
   ```python
   from decimal import Decimal, ROUND_HALF_UP
   
   price = Decimal("19.99")          # from string, NOT float
   tax_rate = Decimal("0.10")
   tax = (price * tax_rate).quantize(Decimal("0.01"), rounding=ROUND_HALF_UP)
   total = price + tax
   print(total)  # Decimal('21.99') — exact!
   ```

2. **Use NUMERIC/DECIMAL in the database:**
   ```sql
   CREATE TABLE products (
       id SERIAL PRIMARY KEY,
       name TEXT NOT NULL,
       price NUMERIC(10, 2) NOT NULL  -- up to 99,999,999.99, exact
   );
   
   -- NEVER: price FLOAT or price DOUBLE PRECISION for money
   ```

3. **Alternative: store amounts in cents as integers:**
   ```python
   # Store $19.99 as 1999 cents (integer — no floating point at all)
   price_cents = 1999
   tax_cents = round(price_cents * 0.1)  # 200
   total_cents = price_cents + tax_cents   # 2199
   
   # Convert to dollars only for display
   display_total = f"${total_cents / 100:.2f}"  # "$21.99"
   ```

4. **API contracts should use strings for money (not numbers):**
   ```json
   {
     "amount": "19.99",
     "currency": "USD"
   }
   ```
   JSON numbers are floats — use strings to preserve precision.

---

### Issue #52: Not Closing Resources (Files, Connections, Cursors)

**The Mistake:**
```python
def read_config():
    f = open("/etc/app/config.json")
    data = json.load(f)
    # f is never closed — file descriptor leak
    return data

def get_users():
    conn = psycopg2.connect(DATABASE_URL)
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users")
    results = cursor.fetchall()
    # cursor and conn are never closed — connection leak
    return results
```

**Why Juniors Make This Mistake:**
- In small scripts, it doesn't matter (Python closes them on exit)
- They don't know about file descriptor limits or connection pools
- Garbage collection "usually" cleans up, so the bug is intermittent

**What Goes Wrong:**
- File descriptor exhaustion: `OSError: [Errno 24] Too many open files`
- Database connection pool exhaustion (see Issue #2)
- Memory leaks from unclosed HTTP sessions
- Locked files that other processes can't access

**The Correct Approach:**

```python
# ALWAYS use context managers (with statement)

# Files
with open("/etc/app/config.json") as f:
    data = json.load(f)
# f is automatically closed, even if json.load raises an exception

# Database connections
async with db.acquire() as conn:
    result = await conn.fetch("SELECT * FROM users")
# connection returned to pool, even on error

# HTTP sessions
async with httpx.AsyncClient() as client:
    response = await client.get("https://api.example.com/data")
# session and underlying TCP connections are closed

# Temporary files
import tempfile
with tempfile.NamedTemporaryFile(suffix=".csv") as tmp:
    write_report(tmp)
    tmp.seek(0)
    upload_to_s3(tmp)
# file is deleted automatically
```

**If you can't use `with`, use `try/finally`:**
```python
conn = await db.acquire()
try:
    await conn.execute("UPDATE ...")
finally:
    await conn.close()  # always runs, even on exception
```

**Rule of thumb:** If you call `open()`, `connect()`, `acquire()`, or `create_session()`, you must have a matching close — use `with` to make it automatic.

---

### Issue #53: Building Features Without Considering the Unhappy Path

**The Mistake:**
A junior builds an order checkout flow. It works perfectly when the user has a valid address, a valid payment method, sufficient inventory, and no coupon edge cases. In production:
- What if the address validation API is down?
- What if the card is declined?
- What if inventory drops to zero between adding to cart and checkout?
- What if the user applies a coupon that expired 5 seconds ago?
- What if the user double-clicks the "Pay" button?

None of these are handled. The app returns a generic 500 error.

**Why Juniors Make This Mistake:**
- They focus on making the happy path work first (which is correct) but then stop
- They underestimate how many things can go wrong
- They don't have experience with real users doing unexpected things
- They test by manually clicking through the UI, which always follows the happy path

**The Correct Approach:**

1. **For every feature, enumerate failure modes before writing code:**
   ```
   Feature: Checkout
   
   Happy path: user pays, order is created, email sent
   
   Failure modes:
   □ Payment declined → show clear error, don't create order
   □ Payment API timeout → retry once, then show error
   □ Insufficient inventory → show which items, suggest alternatives
   □ Invalid/expired coupon → show specific message, proceed without coupon
   □ Address validation fails → allow manual override with warning
   □ Double-submit → idempotency key prevents duplicate charge
   □ User's session expires mid-checkout → redirect to login, preserve cart
   □ Email service down → order succeeds, queue email for retry
   ```

2. **Return specific error messages, not generic 500s:**
   ```python
   @app.post("/api/checkout")
   async def checkout(request: CheckoutRequest, user: User = Depends(get_current_user)):
       # Validate inventory BEFORE charging
       stock_issues = await inventory.check_availability(request.items)
       if stock_issues:
           return JSONResponse(status_code=409, content={
               "error": "INSUFFICIENT_STOCK",
               "details": [
                   {"product_id": s.product_id, "available": s.available, "requested": s.requested}
                   for s in stock_issues
               ]
           })
       
       # Validate coupon
       if request.coupon_code:
           try:
               discount = await coupons.validate_and_apply(request.coupon_code, user.id)
           except CouponExpiredError:
               return JSONResponse(status_code=400, content={
                   "error": "COUPON_EXPIRED",
                   "message": "This coupon has expired"
               })
           except CouponAlreadyUsedError:
               return JSONResponse(status_code=400, content={
                   "error": "COUPON_ALREADY_USED",
                   "message": "You have already used this coupon"
               })
       
       # Attempt payment
       try:
           payment = await payment_gateway.charge(
               user.payment_method_id, total,
               idempotency_key=request.idempotency_key  # prevent double-charge
           )
       except CardDeclinedError as e:
           return JSONResponse(status_code=402, content={
               "error": "PAYMENT_DECLINED",
               "message": e.user_message  # "Your card was declined. Please try another card."
           })
   ```

3. **Write tests for every failure mode, not just the happy path:**
   ```python
   def test_checkout_happy_path(): ...
   def test_checkout_payment_declined(): ...
   def test_checkout_insufficient_inventory(): ...
   def test_checkout_expired_coupon(): ...
   def test_checkout_double_submit(): ...
   def test_checkout_payment_timeout(): ...
   ```

**Rule of thumb:** For every feature, spend equal time thinking about what happens when things go wrong as you do making things go right.

---

### Issue #54: Not Understanding HTTP Status Codes

**The Mistake:**
```python
# Everything returns 200 OK, even errors
@app.post("/api/users")
async def create_user(data: dict):
    try:
        user = await db.create_user(data)
        return {"success": True, "data": user}
    except DuplicateEmailError:
        return {"success": False, "error": "Email already exists"}  # still 200 OK!
    except Exception as e:
        return {"success": False, "error": str(e)}  # still 200 OK!
```

Or the opposite — returning 500 for user input errors, 200 for "not found," or 403 when they mean 401.

**Why Juniors Make This Mistake:**
- They see HTTP status codes as decoration, not semantics
- They've worked with APIs that always return 200 and put the "real" status in the body
- They don't know the differences between 400, 401, 403, 404, 409, 422

**What Goes Wrong:**
- Monitoring tools count 5xx as server errors — if you return 200 for everything, you have no error visibility
- HTTP caches cache 200 responses — your error response gets cached
- API clients use status codes for control flow — `if response.status_code == 200` fails to detect errors
- Load balancers use 5xx rates to determine service health

**The Correct Approach:**

```python
# Common status codes and when to use them:

# 200 OK — request succeeded, here's the data
# 201 Created — resource was created (return the created resource + Location header)
# 204 No Content — success, no body (DELETE, some PUT)

# 400 Bad Request — malformed request (invalid JSON, missing required fields)
# 401 Unauthorized — not authenticated (no token, expired token)
# 403 Forbidden — authenticated but not authorized (not your resource, wrong role)
# 404 Not Found — resource doesn't exist
# 409 Conflict — state conflict (duplicate email, trying to delete a non-empty folder)
# 422 Unprocessable Entity — valid syntax but invalid semantics (age = -5)
# 429 Too Many Requests — rate limited

# 500 Internal Server Error — unexpected server bug (should trigger alerts)
# 502 Bad Gateway — upstream service returned bad response
# 503 Service Unavailable — overloaded or in maintenance

@app.post("/api/users", status_code=201)  # 201 for creation
async def create_user(data: CreateUserRequest):
    try:
        user = await user_service.create(data)
        return user
    except DuplicateEmailError:
        raise HTTPException(status_code=409, detail="Email already exists")  # 409 Conflict
    except InvalidDataError as e:
        raise HTTPException(status_code=422, detail=str(e))  # 422 Validation
    # Unhandled exceptions → automatic 500 (which triggers monitoring alerts)
```

**Quick decision tree:**
```
Is the request wrong?
  → Is it malformed? → 400
  → Is the user not logged in? → 401
  → Is the user logged in but not allowed? → 403
  → Does the resource not exist? → 404
  → Is there a conflict with current state? → 409
  → Is the data invalid (business rules)? → 422

Is the server wrong?
  → Bug in our code? → 500
  → Upstream service failed? → 502
  → We're overloaded? → 503
```

---

### Issue #55: Mixing Synchronous and Asynchronous Code Incorrectly

**The Mistake:**
```python
# FastAPI/async endpoint calling synchronous blocking code
@app.get("/api/users")
async def get_users():
    # This BLOCKS the entire event loop for 2 seconds!
    # No other request can be processed during this time
    users = requests.get("https://api.external.com/users")  # sync HTTP library
    time.sleep(2)  # blocks event loop
    data = open("large_file.csv").read()  # blocking file I/O
    return users.json()
```

**Why Juniors Make This Mistake:**
- They don't understand the difference between sync and async
- They mix `requests` (sync) with `httpx`/`aiohttp` (async)
- They use `time.sleep()` instead of `asyncio.sleep()` in async code
- It "works" in testing with one request at a time

**What Goes Wrong:**
- One slow request blocks ALL other requests (event loop is frozen)
- Under load, the entire server becomes unresponsive
- Latency spikes that are hard to diagnose
- 100 concurrent users, but only 1 request processed at a time

**The Correct Approach:**

```python
import httpx
import asyncio
import aiofiles

@app.get("/api/users")
async def get_users():
    # Use async HTTP client (non-blocking)
    async with httpx.AsyncClient() as client:
        response = await client.get("https://api.external.com/users")
    
    # Use asyncio.sleep (non-blocking)
    await asyncio.sleep(2)
    
    # Use async file I/O (non-blocking)
    async with aiofiles.open("large_file.csv") as f:
        data = await f.read()
    
    return response.json()
```

**If you MUST call synchronous code from async context:**
```python
import asyncio
from concurrent.futures import ThreadPoolExecutor

executor = ThreadPoolExecutor(max_workers=4)

@app.get("/api/report")
async def generate_report():
    # Run sync/blocking code in a thread pool (doesn't block event loop)
    loop = asyncio.get_event_loop()
    result = await loop.run_in_executor(executor, heavy_sync_computation)
    return result
```

**Rule of thumb:**
- In an `async def` function: ONLY use `await`-based I/O
- Never call `requests.get()`, `time.sleep()`, or blocking file I/O in async code
- If in doubt, run it in a thread pool with `run_in_executor`

---

### Issue #56: Deploying Without Health Checks

**The Mistake:**
The app deploys and the load balancer starts sending traffic to it immediately. But the app hasn't finished connecting to the database, loading config, or warming caches. Users get 500 errors for the first 10-30 seconds after every deployment.

**Why Juniors Make This Mistake:**
- They don't know health checks exist
- They test locally where startup is instant
- They don't understand how load balancers and Kubernetes decide where to route traffic

**What Goes Wrong:**
- Errors during deployment window
- Rolling deployments don't help (new instances aren't ready when they receive traffic)
- Auto-scaling adds instances that immediately start failing
- Dead instances keep receiving traffic because nothing checks if they're alive

**The Correct Approach:**

```python
# Implement THREE types of health checks:

# 1. LIVENESS — "Is the process alive?" (should it be restarted?)
@app.get("/health/live")
async def liveness():
    return {"status": "alive"}

# 2. READINESS — "Is the app ready to receive traffic?" (should load balancer send traffic?)
@app.get("/health/ready")
async def readiness():
    checks = {}
    
    # Check database
    try:
        await db.execute("SELECT 1")
        checks["database"] = "ok"
    except Exception:
        checks["database"] = "failing"
        return JSONResponse(status_code=503, content={"status": "not ready", "checks": checks})
    
    # Check Redis
    try:
        await redis.ping()
        checks["redis"] = "ok"
    except Exception:
        checks["redis"] = "failing"
        return JSONResponse(status_code=503, content={"status": "not ready", "checks": checks})
    
    return {"status": "ready", "checks": checks}

# 3. STARTUP — "Has the app finished initializing?" (Kubernetes only, prevents premature liveness checks)
startup_complete = False

@app.on_event("startup")
async def startup():
    global startup_complete
    await db.connect()
    await redis.connect()
    await warm_cache()
    startup_complete = True

@app.get("/health/startup")
async def startup_check():
    if startup_complete:
        return {"status": "started"}
    return JSONResponse(status_code=503, content={"status": "starting"})
```

```yaml
# Kubernetes deployment with proper health checks
containers:
- name: api
  livenessProbe:
    httpGet:
      path: /health/live
      port: 8000
    periodSeconds: 10
    failureThreshold: 3     # restart after 3 consecutive failures
  readinessProbe:
    httpGet:
      path: /health/ready
      port: 8000
    periodSeconds: 5
    failureThreshold: 2     # stop sending traffic after 2 failures
  startupProbe:
    httpGet:
      path: /health/startup
      port: 8000
    periodSeconds: 5
    failureThreshold: 30    # allow up to 150s for startup
```

---

### Issue #57: Logging Everything at DEBUG Level in Production

**The Mistake:**
```python
# Every function logs multiple lines
logger.debug(f"Entering get_user with id={user_id}")
logger.debug(f"Query: SELECT * FROM users WHERE id = {user_id}")
logger.debug(f"Query result: {result}")
logger.debug(f"Returning user: {result}")
```

In development, this is fine. In production with 1000 requests/second, this generates millions of log lines per hour. Log storage costs explode, log search becomes slow, and the I/O from logging actually degrades application performance.

**Why Juniors Make This Mistake:**
- They add logging to debug a problem and never remove it
- They don't understand log levels
- They set `LOG_LEVEL=DEBUG` in production "just in case"
- They log full request/response bodies "for debugging"

**The Correct Approach:**

```python
import structlog

logger = structlog.get_logger()

# Use the RIGHT log level:

# DEBUG — detailed diagnostic info, only for development
logger.debug("Cache lookup", key=cache_key)

# INFO — normal operational events, one per business operation
logger.info("Order created", order_id=order.id, total=order.total, user_id=user.id)

# WARNING — unexpected but handled situations
logger.warning("Retry needed for payment API", attempt=2, order_id=order.id)

# ERROR — failures that need investigation
logger.error("Payment failed", order_id=order.id, error=str(e), exc_info=True)

# CRITICAL — system is down
logger.critical("Database connection pool exhausted")
```

**Production log level guidelines:**
| Environment | Log Level | Why |
|-------------|-----------|-----|
| Development | DEBUG | See everything for debugging |
| Staging | DEBUG or INFO | Catch issues before production |
| Production | INFO | Operational visibility without noise |
| Production (incident) | DEBUG temporarily | Enable via config change, disable after |

**Make log level configurable without redeployment:**
```python
LOG_LEVEL = os.getenv("LOG_LEVEL", "INFO")
logging.basicConfig(level=getattr(logging, LOG_LEVEL))

# During an incident: change env var to "DEBUG", restart
# After investigation: change back to "INFO"
```

**What to log at each level in production:**
- **INFO:** request started, request completed (with duration), order created, payment processed, user registered, job started/completed
- **WARNING:** retry triggered, deprecated API called, rate limit approaching, high latency detected
- **ERROR:** unhandled exception, external service failure, database error, authentication failure
- **Never in production:** full request bodies, SQL queries with parameters, response bodies, personally identifiable information

---

### Issue #58: Not Using Indexes on Foreign Keys

**The Mistake:**
```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),  -- foreign key, but no index!
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- This query is now a full table scan on orders:
SELECT * FROM orders WHERE user_id = 42;

-- This JOIN is catastrophically slow on large tables:
SELECT u.name, COUNT(o.id)
FROM users u
JOIN orders o ON o.user_id = u.id
GROUP BY u.name;
```

**Why Juniors Make This Mistake:**
- They assume foreign keys automatically create indexes (PostgreSQL doesn't, MySQL InnoDB does)
- They focus on defining relationships, not query performance
- The query works fine with 100 rows; the problem only appears at 1M+ rows

**What Goes Wrong:**
- JOINs on foreign key columns do full table scans
- DELETE or UPDATE on the parent table checks all child rows (without index: full scan per delete)
- Queries that filter by foreign key are unexpectedly slow
- Database CPU spikes as data grows

**The Correct Approach:**

```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INT NOT NULL REFERENCES users(id),
    status TEXT NOT NULL DEFAULT 'pending',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- ALWAYS create an index on foreign key columns
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Also index columns you frequently filter or sort by
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created_at ON orders(created_at);

-- Composite index for common query patterns
-- "Get recent orders for a user"
CREATE INDEX idx_orders_user_created ON orders(user_id, created_at DESC);
```

**Checklist for new tables:**
1. Every foreign key column gets an index
2. Every column in a WHERE clause used frequently gets an index
3. Every column used in ORDER BY with LIMIT gets an index
4. Verify with `EXPLAIN ANALYZE` after creating the table

---

### Issue #59: Not Understanding the Difference Between Authentication and Authorization

**The Mistake:**
```python
# Junior checks: "is the user logged in?" and calls it done
@app.delete("/api/posts/{post_id}")
async def delete_post(post_id: int, user: User = Depends(get_current_user)):
    # User is authenticated (logged in) — but are they allowed to delete THIS post?
    await db.execute("DELETE FROM posts WHERE id = $1", post_id)
    # BUG: ANY logged-in user can delete ANY post!
    return {"message": "Deleted"}
```

This is an IDOR (Insecure Direct Object Reference) vulnerability — one of the most common web security flaws.

**Why Juniors Make This Mistake:**
- They conflate "who are you" (authentication) with "what can you do" (authorization)
- They check login status but not resource ownership
- They test with their own account and their own resources, so the bug never shows up

**What Goes Wrong:**
- User A can delete User B's posts, read User B's private messages, modify User B's settings
- Attackers enumerate IDs (`/api/posts/1`, `/api/posts/2`, ...) and access everything
- Data breaches, unauthorized modifications, privacy violations

**The Correct Approach:**

```python
@app.delete("/api/posts/{post_id}")
async def delete_post(post_id: int, user: User = Depends(get_current_user)):
    post = await db.fetch_one("SELECT * FROM posts WHERE id = $1", post_id)
    
    if not post:
        raise HTTPException(status_code=404, detail="Post not found")
    
    # AUTHORIZATION: check ownership or admin role
    if post["author_id"] != user.id and "admin" not in user.roles:
        raise HTTPException(status_code=403, detail="You don't have permission to delete this post")
    
    await db.execute("DELETE FROM posts WHERE id = $1", post_id)
    return {"message": "Deleted"}
```

**Even better — make it impossible to access unauthorized resources in the query:**
```python
@app.delete("/api/posts/{post_id}")
async def delete_post(post_id: int, user: User = Depends(get_current_user)):
    # Include user_id in the WHERE clause — can only delete own posts
    result = await db.execute(
        "DELETE FROM posts WHERE id = $1 AND author_id = $2",
        post_id, user.id
    )
    if result.rowcount == 0:
        raise HTTPException(status_code=404, detail="Post not found")
    return {"message": "Deleted"}
```

**Authorization checklist for every endpoint:**
- Does this endpoint check that the user OWNS the resource?
- Does this endpoint check the user's ROLE for admin operations?
- Can a user access another user's data by changing an ID in the URL?
- Can a user escalate privileges by modifying the request body?

---

### Issue #60: Writing Code That Only Works at Small Scale

**The Mistake:**
```python
# "Get all users who haven't logged in for 30 days and send them an email"
async def send_reengagement_emails():
    # Loads ALL inactive users into memory at once
    users = await db.fetch(
        "SELECT * FROM users WHERE last_login < NOW() - INTERVAL '30 days'"
    )  # 500,000 users loaded into memory
    
    for user in users:  # processes one at a time
        email_body = render_template("reengagement.html", user=user)  # renders 500K templates
        await send_email(user["email"], "We miss you!", email_body)  # 500K individual API calls
```

This works with 100 users. With 500,000 users, it runs out of memory, takes 12 hours, and sends thousands of duplicate emails if it crashes and restarts.

**Why Juniors Make This Mistake:**
- They build and test with small datasets (10-100 rows)
- They don't think about what happens when data grows 1000×
- They process records one at a time in a loop
- They don't consider failure/restart scenarios for long-running processes

**The Correct Approach:**

1. **Process in batches, not all at once:**
   ```python
   async def send_reengagement_emails():
       last_id = 0
       batch_size = 500
       
       while True:
           # Fetch a bounded batch using cursor pagination
           users = await db.fetch("""
               SELECT id, email, name FROM users
               WHERE last_login < NOW() - INTERVAL '30 days'
                 AND id > $1
               ORDER BY id
               LIMIT $2
           """, last_id, batch_size)
           
           if not users:
               break
           
           last_id = users[-1]["id"]
           
           # Process batch
           for user in users:
               await queue.enqueue("send_email", {
                   "email": user["email"],
                   "template": "reengagement",
                   "user_id": user["id"]
               })
           
           logger.info(f"Queued {len(users)} emails, last_id={last_id}")
   ```

2. **Use background job queues** for individual items (emails, notifications):
   ```python
   # Worker processes emails from the queue — can scale horizontally
   # If worker crashes, only the current email is lost (not the whole batch)
   # Queue provides retry logic, DLQ, and concurrency control
   ```

3. **Track progress** so you can resume after failure:
   ```python
   # Save checkpoint after each batch
   await redis.set("reengagement:last_processed_id", last_id)
   
   # On restart: resume from checkpoint
   last_id = int(await redis.get("reengagement:last_processed_id") or 0)
   ```

4. **Deduplicate** to prevent sending the same email twice:
   ```python
   async def send_email_worker(job):
       already_sent = await redis.sismember("reengagement:sent", job["user_id"])
       if already_sent:
           return  # skip — already sent in this campaign
       
       await email_service.send(job["email"], ...)
       await redis.sadd("reengagement:sent", job["user_id"])
   ```

**Scale thinking checklist:**
- What happens if this table has 1M rows? 10M?
- Am I loading everything into memory at once?
- What happens if this process crashes halfway through?
- Can I resume from where I left off?
- Am I making N individual API calls when I could batch them?

---

### Quick Reference: Issue-to-Solution Matrix

| Issue | First Response | Architectural Fix |
|-------|---------------|-------------------|
| Connection exhaustion | Restart + increase pool | PgBouncer + pool monitoring |
| Slow queries | Add missing index | pg_stat_statements + query monitoring |
| Cache stampede | Increase TTL | Single-flight + stale-while-revalidate |
| Memory leak | Restart + set memory limits | Profiling + bounded caches |
| Cascading failure | Restart failing service | Circuit breakers + timeouts + bulkheads |
| Distributed inconsistency | Manual reconciliation | Saga pattern + outbox |
| Timezone bugs | Fix the immediate query | UTC everywhere + presentation-layer conversion |
| Large file uploads | Increase timeout | Pre-signed URLs + async processing |
| Breaking API changes | Hotfix rollback | API versioning + contract tests |
| Deadlocks | Retry the transaction | Consistent lock ordering + optimistic locking |
| Backpressure collapse | Scale consumers | Bounded queues + auto-scaling + load shedding |
| Secret rotation | Rotate + restart | Dual-read pattern + secrets manager |
| Large table migration | Off-hours maintenance window | Expand-contract + batched backfill |
| Poison messages | Manual removal from queue | DLQ + retry limits + schema validation |
| N+1 queries | Eager load the relation | Query count assertions in tests |
| Race conditions (oversell) | Manual correction | Atomic DB updates + distributed locks |
| Logging sensitive data | Scrub the logs | Sanitization middleware + structured logging |
| Ungraceful shutdown | Increase termination grace period | SIGTERM handling + readiness probes |
| Flaky tests | Rerun and hope | Isolation + time freezing + random order |
| Alert fatigue | Mute noisy alerts | SLO-based alerting + quarterly review |
| DNS resolution failures | Restart + flush DNS cache | Multiple DNS providers + TTL tuning + monitoring |
| Cold cache thundering herd | Scale database temporarily | Cache warming + request coalescing + staggered restarts |
| Disk space exhaustion | Delete old logs + expand disk | Log rotation + disk monitoring at 80% + external log shipping |
| ORM connection leaks | Restart app | Context managers + pool monitoring + `pool_pre_ping` |
| Infinite retry loops | Kill the retrying service | Exponential backoff + jitter + retry budgets |
| Hot partitions | Rebalance manually | Better shard keys + hot key caching + consistent hashing |
| Replication lag stale reads | Force reads to primary | Read-your-own-writes routing + lag monitoring |
| Orphaned resources | Manual cleanup | Event-driven deletion + reconciliation jobs |
| Configuration drift | Add missing config manually | Startup validation + IaC + environment parity CI checks |
| Uncontrolled fan-out | Rate limit the source | Fan-out on read for hot entities + async batched fan-out |
| Zombie/stuck jobs | Manually reset jobs | Visibility timeouts + heartbeats + stuck job detector |
| Unbounded query results OOM | Restart + add LIMIT | Mandatory pagination + streaming exports + statement timeout |
| Error responses leaking internals | Patch error handler | Global error handler + error ID correlation + security tests |
| Clock skew ordering bugs | Manual data reconciliation | Logical clocks + version vectors + monotonic timers |
| Auto-scaling cost explosion | Kill instances + set max | Max instance limits + error-rate-aware scaling + billing alerts |
| Idempotency failures (double charge) | Manual refund | Idempotency keys + unique constraints + `ON CONFLICT DO NOTHING` |
| Untestable tightly-coupled code | Spin up full infra for tests | Dependency injection + fakes + separate business logic from I/O |
| Pagination bugs under concurrent writes | Accept duplicates | Cursor-based (keyset) pagination |
| Dangerous schema migrations | Rollback + outage | Migration linters + expand-contract + test against prod-sized data |
| Partial failures in third-party calls | Manual reconciliation | State machines + critical/non-critical separation + reconciliation workers |
| **Junior Mistakes** | | |
| Trusting client input (#41) | Patch the endpoint | Server-side validation + never accept price/role from client |
| Plain text / weak password hashing (#42) | Rotate all passwords | bcrypt/argon2 + security audit |
| Not handling null/empty fields (#43) | Fix the crash | Pydantic models + Optional types + edge case tests |
| Swallowing exceptions silently (#44) | Find the silent `except: pass` | Catch specific exceptions + always log |
| Hardcoded secrets (#45) | Rotate leaked credentials | Environment variables + secrets manager + `.gitignore` |
| Fat controller / no layers (#46) | Refactor gradually | Service layer + repository layer + dependency injection |
| Missing database transactions (#47) | Fix the corrupted data | `async with db.transaction()` for multi-step writes |
| `SELECT *` everywhere (#48) | Rewrite queries | Select only needed columns + prevent sensitive data leaks |
| Not understanding indexes (#49) | Add missing index | EXPLAIN ANALYZE + leftmost prefix rule + expression indexes |
| Manual schema changes (#50) | Document what was changed | Migration tool (Alembic, Knex) + version-controlled migrations |
| Using float for money (#51) | Recalculate affected records | Decimal/NUMERIC types + store as cents |
| Not closing resources (#52) | Restart the service | Context managers (`with` statement) for all resources |
| Ignoring unhappy paths (#53) | Hotfix each failure case | Enumerate failure modes before coding + test each one |
| Wrong HTTP status codes (#54) | Fix the response codes | Use semantic status codes (4xx client, 5xx server) |
| Blocking async event loop (#55) | Restart the frozen service | Use async libraries + `run_in_executor` for sync code |
| No health checks (#56) | Fix after users report the outage | Liveness + readiness + startup probes |
| DEBUG logging in production (#57) | Change log level + clean up logs | Configurable log levels + structured logging + level guidelines |
| No index on foreign keys (#58) | Add index (may lock table briefly) | Index every FK column + EXPLAIN ANALYZE after table creation |
| Auth without authorization / IDOR (#59) | Emergency patch | Check resource ownership on every endpoint + include user_id in queries |
| Code that only works at small scale (#60) | Rewrite under pressure | Batch processing + cursors + job queues + checkpointing |

---

*Course designed as a reference companion to [backend_concepts.md](backend_concepts.md)*
