# ⚡ 50+ Advanced Laravel Interview Questions (Rapid Fire)

> **Instructions**: These differentiate Senior Developers from Mid-Levels. Covers Architecture, Optimization, and Security.

---

### 🔴 Architecture & Design Patterns
1.  **What is the Service Container?**
    *   A Dependency Injection Container (IoC) that manages class dependencies and performs global binding.
2.  **What is a Service Provider?**
    *   The central place where you strap application logic (register bindings, listeners, routes).
3.  **What is the Repository Pattern?**
    *   An abstraction layer that separates business logic from data access logic (e.g., Eloquent queries).
4.  **What is a Facade explicitly?**
    *   A class that provides a static interface to classes available in the service container.
5.  **What is Dependency Injection (DI)?**
    *   Injecting class dependencies via constructor/method rather than hard-coding `new Class()`.
6.  **What is Route Model Binding?**
    *   Automatically injecting model instances into routes based on ID/Slug.
7.  **What is an Observer?**
    *   A class that groups event listeners for a model (creating, updating, deleting).
8.  **What is a Job Queue?**
    *   Asynchronous execution of tasks (e.g., sending emails) to unblock the main thread.
9.  **What is Laravel Horizon?**
    *   A dashboard and configuration system for Redis queues.
10. **What is middleware priority?**
    *   The order in which middleware executes (defined in `Kernel.php`).

---

### 🔴 Optimization & Performance
11. **How do you cache configuration?**
    *   `php artisan config:cache`
12. **How do you cache routes?**
    *   `php artisan route:cache`
13. **How do you optimize Composer autoload?**
    *   `composer dump-autoload -o`
14. **What is N+1 query problem?**
    *   Executing 1 query for parent + N queries for children in a loop (Lazy Loading).
15. **How do you fix N+1?**
    *   Eager Loading: `User::with('posts')->get()`.
16. **What is Database Indexing?**
    *   Creating a data structure (B-Tree) on columns used in WHERE/ORDER BY for faster lookups.
17. **What is Redis?**
    *   In-memory data structure store used as a database, cache, and message broker.
18. **How do you use Redis for caching?**
    *   `Cache::store('redis')->put('key', 'value', 60);`
19. **What is OPcache?**
    *   PHP extension that caches precompiled script bytecode in shared memory (eliminating parsing overhead).
20. **What is Laravel Octane?**
    *   Supercharges Laravel application performance by serving requests with Swoole/RoadRunner (High concurrency/Keep-alive application state).

---

### 🔴 Security & Best Practices
21. **What is CSRF protection?**
    *   Anti-Cross-Site Request Forgery. Ensures form submissions come from your own site.
22. **What is XSS protection?**
    *   Cross-Site Scripting. Blade `{{ }}` escapes HTML entities by default.
23. **What is SQL Injection prevention?**
    *   Using PDO parameter binding (prepared statements) automatically by Eloquent.
24. **How do you validate file uploads?**
    *   `validate(['avatar' => 'file|max:2048|mimes:jpeg,png'])`
25. **How do you implement API authentication?**
    *   Laravel Sanctum (Tokens/Cookies) or Passport (OAuth2).
26. **What is Rate Limiting?**
    *   Limiting requests from an IP. `Route::middleware('throttle:60,1')`.
27. **What is Mass Assignment vulnerability?**
    *   User passing unexpected fields (is_admin) in request. Prevent with `$fillable` / `$guarded`.
28. **How do you secure sensitive data?**
    *   Environment variables (`.env`) and Encryption (`Crypt::encrypt()`).
29. **What is CSP (Content Security Policy)?**
    *   HTTP header that restricts sources of executable scripts (prevents XSS).
30. **How do you force HTTPS?**
    *   `$this->app['request']->server->set('HTTPS', 'on');` (in Provider) or middleware.

---

### 🔴 Testing & Deployment
31. **What is PHPUnit?**
    *   The standard testing framework for PHP.
32. **What is Pest PHP?**
    *   A testing framework with a focus on simplicity and elegance (wrapper around PHPUnit).
33. **What is Mocking?**
    *   Simulating external dependencies (APIs, Classes) in tests.
34. **What is a Factory State?**
    *   Pre-defined model modifications (e.g., `User::factory()->admin()->create()`).
35. **What is DatabaseTransactions trait?**
    *   Wraps each test in a transaction and rolls it back after execution (clean state).
36. **What is CI/CD?**
    *   Continuous Integration/Deployment (Automated testing and deployment pipeline).
37. **What is Laravel Forge?**
    *   Server management and deployment service for PHP.
38. **What is Laravel Vapor?**
    *   Serverless deployment platform for Laravel (AWS Lambda).
39. **What is Docker?**
    *   Container platform to package application with dependencies (Nginx, PHP, MySQL).
40. **What is Supervisor?**
    *   Process control system used to keep queue workers running (`queue:work`).

---

### 🔴 Advanced Concepts
41. **What is Event Sourcing?**
    *   Storing all changes to application state as a sequence of events.
42. **What is CQRS?**
    *   Command Query Responsibility Segregation (Separate Read and Write models).
43. **What is Domain Driven Design (DDD)?**
    *   Software design approach focusing on complex domain logic (Entities, Value Objects, Aggregates).
44. **What is a Macro in Laravel?**
    *   Extending existing classes with custom methods dynamically (`Collection::macro(...)`).
45. **What is a Pipeline?**
    *   Passing an object through a series of "pipes" (tasks) (Middleware pattern).
46. **How do you broadcast events?**
    *   Implementing `ShouldBroadcast` interface (WebSockets/Pusher).
47. **What is LazyCollection?**
    *   Using PHP Generators to process manageable chunks of data (low memory usage).
48. **What is a Custom Artisan Command?**
    *   Creating CLI tools (`php artisan make:command`).
49. **How do you handle multi-tenancy?**
    *   Single DB (tenant_id) or Multi-DB (connection switching) strategies.
50. **What is the Service Locator pattern (Anti-pattern)?**
    *   Retrieving dependencies directly from the container inside classes, hiding dependencies.
