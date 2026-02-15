# ⚡ 03 - Optimization Audit (Find 95% of Bottlenecks)

### 🧐 Explanation
"My App is slow."
Don't guess. **Measure.**
Use **Laravel Telescope** (Dev) and **Laravel Debugbar** (Dev) to visualize N+1 queries.
Use **Datadog** (Prod) for live metrics.

---

### 💻 Checklist

1.  **N+1 Queries**: Look for `Model::all()` loops without `with()`.
    *   **Solution**: `Model::with('relation')->get()`.

2.  **Missing Indexes**: Look for `select * from users where status = 'active'` taking 500ms.
    *   **Solution**: Add `->index()` migration.

3.  **Config Not Cached**: Look for "config loading" taking 40ms.
    *   **Solution**: `php artisan config:cache`.

4.  **Route Not Cached**: Look for "Router dispatch" taking 50ms.
    *   **Solution**: `php artisan route:cache`.

5.  **View Compilation**: Blade compiling every render.
    *   **Solution**: `php artisan view:cache`.

---

### 💻 Code Example (Debugbar Profiling)

**❌ Bad (Invisible Slowness):**

```php
// Looks innocent?
foreach ($posts as $post) {
    echo $post->user->name; // Runs: SELECT * FROM users WHERE id = X (100 times!)
}
```

**✅ Good (Debugbar Shows 101 Queries):**

Install Debugbar: `composer require barryvdh/laravel-debugbar --dev`.
Open `/posts`. Click "Queries" tab.
See **Duplicate Queries** (100x user lookup).

**✅ Fix:**

```php
$posts = Post::with('user')->get(); // 2 Queries Total (1 for posts, 1 for users IN (...))
```

---

### ✅ When to Use
1.  **Development**: Debugbar enabled locally.
2.  **Production**: Telescope (Pruned heavily) or Sentry/NewRelic.

### ❌ When NOT to Use
1.  **Production Debugbar**: NEVER enable Debugbar in production. It leaks every DB query, Session ID, and Environment variable. Massive security risk.

---

### 🎙 Interview Insight
**Q: "What makes Laravel slower than raw PHP?"**
**A:** "Bootstrapping. Loading 500 classes, parsing configs, registering providers takes 30-50ms (Framework Overhead).
To minimize this, use **Octane**. It keeps the application booted in RAM (Swoole/RoadRunner), reducing overhead to ~2ms per request."
