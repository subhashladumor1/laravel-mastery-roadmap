# 🐳 03 - Supervisor & Horizon (Keep Queues Alive)

### 🧐 Explanation
`php artisan queue:work` runs in **Foreground**.
If you close SSH, the queue stops.
If it crashes, it stops.
**Supervisor** is a process monitor that auto-restarts the queue workers if they die.

---

### 💻 Supervisor Config (`/etc/supervisor/conf.d/laravel-worker.conf`)

This ensures **8 concurrent workers** run permanently.

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d

# Point to artisan
command=php /var/www/html/artisan queue:work redis --sleep=3 --tries=3 --max-time=3600

# Important: Run as non-root user (security!)
user=www-data

autostart=true
autorestart=true

numprocs=8
redirect_stderr=true
stdout_logfile=/var/www/html/storage/logs/worker.log

# Stop Signal (Use TERM for graceful shutdown)
stopwaitsecs=3600
```

---

### 💻 Horizon (Advanced)

If you use **Laravel Horizon**, Supervisor config is simpler.
It just runs **ONE** master process (`horizon`), which spawns child workers.

```ini
[program:horizon]
command=php /var/www/html/artisan horizon
user=www-data
autostart=true
autorestart=true
stopwaitsecs=3600
```

---

### ✅ When to Use
1.  **Production**: Always. Never run `queue:work` manually.
2.  **Graceful Shutdown**: During deployment (`php artisan queue:restart`), Supervisor waits for current jobs to finish (SIGTERM) before restarting workers.

### ❌ When NOT to Use
1.  **Local**: Just `queue:work` is fine.
2.  **Serverless**: AWS Lambda (Bref) handles queues differently (SQS triggers Lambda directly). No Supervisor needed.

---

### 🎙 Interview Insight
**Q: "Why do we use `--max-time=3600` (1 hour)?"**
**A:** "To prevent **Memory Leaks** in PHP.
PHP is designed to die after a request. Long running daemon scripts accumulate memory (Circular References, Static Arrays). By forcing a restart every hour, we clean the slate and keep RAM usage stable."
