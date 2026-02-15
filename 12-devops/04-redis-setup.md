# 🐳 04 - Redis Setup (Cache, Session, Queues)

### 🧐 Explanation
Redis (Remote Dictionary Server) is the Swiss-Army knife of Laravel.
1.  **Cache**: Fast lookups.
2.  **Session**: User login state.
3.  **Queue**: Jobs for Horace.
4.  **Pub/Sub**: Livewire/Echo events.
5.  **Metrics**: Telescope and Horizon stats.

**Don't use Database or File for Queues.** It's slow and risky.

---

### 💻 `config/database.php` (Redis)

Use `phpredis` extension (C-based, faster) not `predis` (PHP-based, slower).

```php
'redis' => [

    'client' => env('REDIS_CLIENT', 'phpredis'),

    'default' => [
        'url' => env('REDIS_URL'),
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD'),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_DB', '0'),
    ],

    'cache' => [
        'url' => env('REDIS_URL'),
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD'),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_CACHE_DB', '1'), // Different DB!
    ],

],
```

---

### 💻 `config/queue.php` & `config/session.php`

**Session:**
```php
'driver' => env('SESSION_DRIVER', 'redis'),
'connection' => 'cache',
```

**Queue:**
```php
'redis' => [
    'driver' => 'redis',
    'connection' => 'default',
    'queue' => env('REDIS_QUEUE', 'default'),
    'retry_after' => 90,
    'block_for' => null,
],
```

---

### ✅ When to Use
1.  **Always**: Use Redis for Cache and Sessions in prod.
2.  **Multiple Servers**: If you have 2 load balanced web servers, **File Session** breaks (User logs in on Server A, next request hits Server B -> Login Lost). Redis centralizes sessions.

### ❌ When NOT to Use
1.  **Tiny Static Site**: Use `file` driver if you have 10 visitors a day.
2.  **Strict Durability**: Don't store "User Wallet Balance" in Redis. Store it in MySQL. Redis is ephemeral (RAM). If server restarts, you might lose data unless configured (AOF/RDB).

---

### 🎙 Interview Insight
**Q: "Why separate `REDIS_DB` for Cache and Default?"**
**A:** "Because `php artisan cache:clear` runs `FLUSHDB` on the cache connection.
If Cache and Queues share `DB 0`, flushing the cache **deletes all queued jobs**!
Isolate them (`DB 0` for Queues/App, `DB 1` for Cache) to prevent accidental data loss."
