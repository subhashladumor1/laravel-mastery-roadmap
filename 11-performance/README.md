# ⚡ 11 - Performance: Speed is a Feature

> **"A 100ms delay costs 1% in sales."**
> Laravel is fast enough for 99% of apps. But if you hit scale, you need to optimize.
> We cover Caching, Database Indexing, Queues, and Horizon.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-caching-redis.md](./01-caching-redis.md)** | Cache Tags, Atomic Locks, and `remember()`. | 🟢 Basic |
| **[02-database-indexing.md](./02-database-indexing.md)** | Why your query is slow. `EXPLAIN ANALYZE`. | 🟠 Medium |
| **[03-optimization-audit.md](./03-optimization-audit.md)** | How to profile with Telescope/Debugbar. | 🟢 Basic |
| **[04-queue-workers-horizon.md](./04-queue-workers-horizon.md)** | Keep the main thread free. | 🟠 Medium |

---

## 💡 Quick Tips

1.  **Don't use `n+1` queries**: Use `with('posts')`.
2.  **Cache Config**: `php artisan config:cache` (40% boot speed boost).
3.  **Route Cache**: `php artisan route:cache` (Essential for API).
