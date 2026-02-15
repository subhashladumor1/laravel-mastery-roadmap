# ⚡ Advanced Mega-List (100 Questions)

> **"Professional Grade."**
> These 100 questions cover Architecture, Design Patterns, Internals, and Scalability.

---

### 🔴 01-10: Architecture & Patterns
1.  **Service Provider `register()` vs `boot()`?**
    *   `register()` binds to container. `boot()` uses registered services.
2.  **Repository Pattern benefit?**
    *   Decouples logic from Eloquent/ORM.
3.  **Strategy Pattern usage?**
    *   Switching payment gateways (`Stripe`, `PayPal`) via an interface.
4.  **Observer vs Event?**
    *   Observer tracks model lifecycle. Event is generic application hook.
5.  **Decorator Pattern in Laravel?**
    *   Middleware decorators on Requests.
6.  **Facade implementation detail?**
    *   `getFacadeAccessor()` returns service binding string.
7.  **Singleton vs Bind?**
    *   Singleton returns *same instance* every time. Bind returns *new instance*.
8.  **Contextual Binding?**
    *   Inject `S3Client` into `VideoController` but `LocalClient` into `ImageController`.
9.  **Pipeline Pattern?**
    *   `Pipeline::send($request)->through($pipes)->then(...)` (Middleware logic).
10. **Macroable Trait?**
    *   Allows adding methods to classes at runtime (`Collection::macro`).

### 🔴 11-20: Database Internals
11. **N+1 problem in depth?**
    *   Looping `belongsTo` without eager loading.
12. **Prevent N+1 strictly?**
    *   `Model::preventLazyLoading(!app()->isProduction())`.
13. **Polymorphic relation table structure?**
    *   `id`, `imageable_id`, `imageable_type` (String class name).
14. **Database Transaction isolation?**
    *   Laravel uses default DB isolation (usually READ COMMITTED).
15. **Pessimistic Locking?**
    *   `lockForUpdate()` (SELECT ... FOR UPDATE).
16. **Optimistic Locking?**
    *   Using version column to check modifications before update.
17. **DB Index types?**
    *   B-Tree (default), Hash, Fulltext, Spatial.
18. **Composite Index order?**
    *   Leftmost prefix rule matters (`(a, b)` index helps `a` query, not `b` query).
19. **Cursor Pagination vs Offset?**
    *   Cursor uses `where id > last_id` (O(1)). Offset uses `LIMIT 100000, 10` (O(N) slow).
20. **When to use raw SQL?**
    *   Complex aggregations/Window functions not supported by builder.

### 🔴 21-30: Caching & Redis
21. **Cache Tags?**
    *   `Cache::tags(['users'])->put(...)`.
22. **Tag limitation?**
    *   Not supported by `file` or `database` drivers (Redis/Memcached only).
23. **Atomic Lock?**
    *   `Cache::lock('invoice-1')->get()`.
24. **Cache Stampede?**
    *   Many processes miss cache simultaneously and hit DB.
25. **Prevent Stampede?**
    *   Locking or "Probabilistic early expiration".
26. **Redis Persistence?**
    *   RDB (Snapshot) vs AOF (Log).
27. **Redis Eviction Policy?**
    *   `allkeys-lru`, `volatile-lru`.
28. **Redis Pipeline?**
    *   Sending multiple commands in one network round-trip.
29. **Queued Job uniqueness?**
    *   `ShouldBeUnique` interface (Uses lock based on job content).
30. **Redis Cluster vs Sentinel?**
    *   Cluster shards data. Sentinel handles failover/HA.

### 🔴 31-40: Queues & Horizontal Scaling
31. **Queue Driver `sync`?**
    *   Runs immediately in same process (No queue).
32. **Supervisor Configuration?**
    *   `numprocs=8` defines concurrent workers.
33. **Job Batching?**
    *   `Bus::batch([])->then(...)`.
34. **Job Chaining?**
    *   Sequential execution. Fails entire chain if one fails.
35. **Worker Memory Leak?**
    *   PHP doesn't free memory well in loops. Use `queue:restart` or max jobs.
36. **Job Retry After?**
    *   Time to wait before releasing job back to queue (Must be > timeout).
37. **Horizon functionality?**
    *   Auto-scaling workers based on wait times.
38. **Dead Letter Queue (DLQ)?**
    *   Table where failed jobs go after max retries (`failed_jobs`).
39. **Handling large payloads?**
    *   Don't put full Objects in Job. Put ID. Pass Model.
40. **Job Serialization?**
    *   `SerializesModels` trait stores Model ID and re-fetches from DB.

### 🔴 41-50: Security & Auth
41. **CSRF Token mismatch?**
    *   Session expired or cookie missing.
42. **XSS Protection details?**
    *   Blade utilizes `htmlspecialchars()`.
43. **SQL Injection details?**
    *   PDO Prepared Statements separate query structure from data.
44. **Rate Limiting strategy?**
    *   Token Bucket / Leaky Bucket algorithm (Redis).
45. **Signed Routes?**
    *   URL containing HMAC hash signature to prevent tampering.
46. **Encryption Algorithm?**
    *   AES-256-CBC (via OpenSSL).
47. **Hashing Algorithm?**
    *   Bcrypt (default) or Argon2id via Sodium.
48. **CORS Headers?**
    *   Access-Control-Allow-Origin logic in middleware.
49. **JWT vs Sanctum (Cookies)?**
    *   Cookies are HttpOnly (XSS safe). JWT in localStorage is XSS unsafe.
50. **OIDC vs OAuth2?**
    *   OIDC is AuthN (Identity). OAuth2 is AuthZ (Access).

### 🔴 51-60: Testing Strategies
51. **Mockery vs InteractsWithContainer?**
    *   `mock(Class)` acts on container resolution.
52. **Contract Testing?**
    *   Ensuring Interface implementation matches expectations.
53. **Mutation Testing?**
    *   Modifying code (mutants) to see if tests fail (Infection PHP).
54. **Dusk Headless?**
    *   Running Chrome without GUI in CI pipeline.
55. **Test Parallelization?**
    *   `php artisan test --parallel` (Uses Paratest).
56. **Database Transactions in Tests?**
    *   `RefreshDatabase` trait (Begin/Rollback).
57. **Factory States?**
    *   `User::factory()->admin()->create()`.
58. **Snapshot Testing?**
    *   Comparing output HTML/JSON against saved "golden" file.
59. **Faking Services?**
    *   `Event::fake()`, `Mail::fake()`, `Queue::fake()`.
60. **Coverage Report?**
    *   `--coverage-html report` (Requires Xdebug/PCOV).

### 🔴 61-70: PHP Internals
61. **PSR standards?**
    *   PSR-4 (Autoloading), PSR-12 (Style), PSR-7 (HTTP).
62. **Composer Autoload optimization?**
    *   `dump-autoload -o` (Classmap generation).
63. **OPcache preloading?**
    *   Loading core framework files into RAM at startup (PHP 7.4+).
64. **JIT Compiler?**
    *   Just-In-Time compilation of Opcodes (PHP 8.0+).
65. **WeakMap?**
    *   Map that holds weak references to objects (Garbage Collection friendly).
66. **Generators (`yield`)?**
    *   Iterating large datasets without loading array into memory.
67. **Attributes vs Annotations?**
    *   Attributes (`#[Route]`) are native PHP syntax. Annotations are DocBlock comments.
68. **Fiber (Green Threads)?**
    *   Mechanism for interruptible functions (Async PHP base).
69. **Covariance/Contravariance?**
    *   Type compatibility rules for inheritance overrides.
70. **Strict Types?**
    *   `declare(strict_types=1);` disables aggressive type coercion.

### 🔴 71-80: Advanced Eloquent
71. **Custom Casts?**
    *   `CastsAttributes` interface to transform data on set/get.
72. **Model Pruning?**
    *   `Prunable` trait to auto-delete old records (`php artisan model:prune`).
73. **Query Scopes parameter?**
    *   `scopeActive($query, $bool)`.
74. **Subqueries?**
    *   `addSelect(['last_login' => Login::select(...)])`.
75. **Upsert?**
    *   `User::upsert($values, ['email'], ['updated_at'])`.
76. **Pivot Events?**
    *   `using(PivotModel::class)` to hook into pivot lifecycle.
77. **Touching timestamps?**
    *   `$touches = ['post']` on Comment model updates parent timestamp.
78. **Replicating models?**
    *   `$user->replicate()->save()` (Cloning).
79. **Without Global Scopes?**
    *   `User::withoutGlobalScopes()->get()`.
80. **Inspect Query Log?**
    *   `DB::enableQueryLog(); ... DB::getQueryLog();`

### 🔴 81-90: Server & Deployment
81. **Nginx vs Apache?**
    *   Nginx is event-driven (C10k problem). Apache is process-based.
82. **PHP-FPM?**
    *   FastCGI Process Manager. Handles PHP execution pool.
83. **Load Balancer (Health Check)?**
    *   Ping `/up` endpoint to verify service status.
84. **Floating IP?**
    *   Static IP that can move between droplets/servers.
85. **Zero Downtime Deployment?**
    *   Symlinking current release folder. `php artisan down` if needed.
86. **Database Migration Strategy?**
    *   Backward compatible changes. 1. Add column (nullable). 2. Deploy code. 3. Backfill. 4. Remove old.
87. **Blue/Green Deployment?**
    *   Spin up new environment (Green). Switch traffic. Kill old (Blue).
88. **Canary Deployment?**
    *   Send 10% traffic to new version. Monitor errors. Roll out 100%.
89. **Environment Management?**
    *   Secrets Manager (AWS) or Vault instead of `.env` file on disk.
90. **Static Asset Caching?**
    *   Versioning (hashing) filenames (`app.js?id=hash`) via Mix/Vite.

### 🔴 91-100: Microservices & Distributed Design
91. **Monolith vs Microservices?**
    *   Monolith: Single codebase. Microservices: Distributed domains via HTTP/GRPC.
92. **Database per Service?**
    *   Microservice rule: Private database. No sharing tables.
93. **Eventual Consistency?**
    *   Data might be stale for milliseconds. Syncs via events.
94. **Saga Pattern?**
    *   Distributed transaction management (Sequence of local transactions).
95. **API Gateway?**
    *   Single entry point routing requests to services (Auth, Rate Limit).
96. **gRPC vs REST?**
    *   gRPC: Binary (Protobuf), faster, typed. REST: JSON, loose.
97. **Idempotency Key?**
    *   Header preventing duplicate processing of same request (Network retries).
98. **Distributed Tracing?**
    *   Passing `Trace-ID` header to log request flow across services (OpenTelemetry).
99. **Circuit Breaker?**
    *   Stop calling failing service to prevent cascading failure.
100. **CAP Theorem?**
    *   Consistency, Availability, Partition Tolerance. Choose 2.
