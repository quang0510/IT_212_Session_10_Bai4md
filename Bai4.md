1. Nội dung Prompt
   Act as a Principal Software Architect and Lead System Analyst specializing in High-Availability E-commerce Architectures.
   Generate a rigorous, quantifiable, and production-ready Non-Functional Requirements (NFR) specification document for the `Guai-api` system to handle massive high-concurrency traffic events.

   The system backend is built on Spring Boot, using MySQL as the primary relational database and JWT/RBAC for authentication. The infrastructure must be explicitly benchmarked to avoid performance bottlenecks, database deadlocks, and authentication latency during flash sales or heavy traffic spikes.

   You must analyze and provide precise, non-vague technical indicators for the following three areas:
   1. Performance (Response Time): Define maximum response time limits (p95 and p99 latencies) for product catalog search and retrieval endpoints under high load.
   2. Database Efficiency (MySQL): Propose concrete indexing, query execution optimization, and connection pooling strategies to prevent slow queries and handle concurrent read/write stress.
   3. Authentication Overhead (JWT Latency & Security): Define strict latency caps for the JWT verification/allocation filter stream, balancing cryptographic security (e.g., BCrypt rounds) with microsecond processing speeds.

   Generate the response strictly as a scannable, professional Markdown Table containing four columns:
   - Metric ID
   - Category
   - Technical Requirement Description & Target Objective
   - Quantitative KPI / Implementation Verification Target

2. Bảng tài liệu NFR do AI sinh ra
   | Metric ID | Category | Technical Requirement Description & Target Objective | Quantitative KPI / Implementation Verification Target |
   | --- | --- | --- | --- |
   | **NFR-PERF-001** | Performance & Latency | **Product Catalog Query Efficiency:** All read-only product browsing and search endpoints (e.g., `GET /api/v1/products`) must utilize a distributed cache layer (Redis Cluster) to minimize direct MySQL disk read operations during traffic spikes. | - Baseline: Average Response Time $\le 50\text{ ms}$.<br>

   <br>- High Load (Concurrent Users): $p95 \le 120\text{ ms}$, $p99 \le 250\text{ ms}$ at $10,000\text{ req/sec}$.<br>

   <br>- Maximum Error Rate: $\le 0.01\%$ under peak flash-sale conditions. |
   | **NFR-DB-001** | Database Efficiency | **MySQL Indexing & Execution Plans:** Prevent full table scans on production transactional and catalog tables. Every search query must register an optimized execution plan (`type` field in `EXPLAIN` must be `ref`, `eq_ref`, or `range`). | - Index Match Rate: $100\%$ of active search, filter, and sorting queries must match a B-Tree or Composite Index.<br>

   <br>- Slow Query Threshold: Zero queries exceeding $50\text{ ms}$ runtime.<br>

   <br>- Zero table locks during concurrent writes. |
   | **NFR-DB-002** | Database Performance | **Connection Pooling Strategy:** Configure the database connection pool (HikariCP) inside the Spring Boot container to optimize pool size, avoid thread starvation, and efficiently recycle stagnant connections. | - Pool Parameters: `maximum-pool-size: 64`, `minimum-idle: 10`.<br>

   <br>- Connection Acquisition Timeout: $p99 \le 25\text{ ms}$ under maximum database write pressure.<br>

   <br>- Idle Timeout: `600000 ms` (10 minutes). |
   | **NFR-AUTH-001** | Authentication Overhead | **Stateless Security Filter Pipeline:** The custom `JwtSecurityFilter` interceptor chain must parse and cryptographically verify asymmetric/symmetric JWT signatures completely in-memory without invoking network or I/O blockages. | - Filter Execution Latency: Average $\le 2\text{ ms}$ per request; $p99 \le 5\text{ ms}$.<br>

   <br>- CPU Utilization Ceiling: Security filtering tasks must consume $\le 15\%$ of total application container CPU cycles under peak load. |
   | **NFR-AUTH-002** | Cryptographic Security | **Password Hashing Cost Factor:** Ensure robust protection against brute-force attacks at the entry endpoint (`POST /api/v1/auth/login`) by tuning hashing rounds to balance security with computational speed. | - Work Factor: BCrypt algorithm cost factor set to exactly `10`.<br>

   <br>- Target Verification Speed: A single password verification execution must take between $60\text{ ms}$ and $100\text{ ms}$ on target hardware, preventing CPU starvation loops. |
   | **NFR-AUTH-003** | Token Lifecycles | **Token Verification & Blacklisting Latency:** Validating active access tokens against short-lived blacklists (e.g., logged-out or invalidated tokens) must happen inside an in-memory keystore rather than disk lookups. | - Redis Blacklist Query Latency: $p95 \le 1\text{ ms}$, $p99 \le 3\text{ ms}$.<br>

   <br>- Token Validation Memory Footprint: $\le 128\text{ bytes}$ per active token reference tracking entry. |
