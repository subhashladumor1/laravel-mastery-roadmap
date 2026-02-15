# 🧠 03 - Memory Leaks in Laravel Queue Workers (PHP GC)

### ❓ The Interview Question
**"Queue workers run as long-lived processes in memory (daemon). How does this cause memory leaks? How do you prevent and debug them in a large Laravel application?"**

---

### 💡 High-Level Answer (Senior Architect)
"PHP was originally designed to 'die' after every request (Share-Nothing Architecture). However, Laravel Queue Workers (`php artisan queue:work`) run indefinitely in a loop.

A memory leak happens when objects are created inside the loop but never destroyed because a reference still exists (e.g., in a static array or a singleton). Over hours, this consumes RAM until the worker crashes with `Allowed memory size exhausted`.

To prevent this, I:
1.  **Monitor**: Use Laravel Horizon metrics or external tools (New Relic/Sentry).
2.  **Restart**: Use the `--max-jobs` or `--max-time` flags to restart the worker process periodically.
3.  **Clean Up**: Avoid large static arrays and manually unset heavy variables in `__destruct` if necessary.
4.  **Database**: Reconnect DB connections if lost (Laravel handles this automatically but custom connections might need care)."

---

### 💻 Code Example: The Leak
Here is a classic mistake.

**Bad Code (Leaking Memory):**
```php
class UserReportJob implements ShouldQueue
{
    // ❌ Static property persists across job executions!
    protected static array $processedUserIds = [];

    public function handle()
    {
        $users = User::all(); // Usage: 50MB RAM
        
        foreach ($users as $user) {
            // This array grows indefinitely with every job execution
            self::$processedUserIds[] = $user->id;
        }

        // The job finishes, but static properties remain in memory!
    }
}
```

**Good Code (Fixing the Leak):**
1.  **Avoid Static State**: Don't use static properties unless absolutely necessary.
2.  **Chunking**: Process large datasets in chunks to keep memory low.
3.  **Worker Config**: Force restart.

```bash
# Supervisor Config (laravel-worker.conf)
command=php /var/www/html/artisan queue:work redis --tries=3 --max-time=3600 --max-jobs=1000
```
This tells the worker to **die** and restart after 1 hour or 1000 jobs, clearing all memory leaks guaranteed.

---

### 🌍 Real World Scenario
**Scenario:**
You have a PDF Generation Service. Each job loads a 10MB PDF template into memory.
If your worker processes 100 jobs without restarting, it might hold onto references of the PDF library's internal cache.
Result: **Crash after 500 jobs**.

**Solution:**
Use `queue:listen` for heavy tasks (starts a fresh process per job - slower but safer) OR use `queue:work --max-jobs=50` to recycle the worker frequently.

---

### 🔄 Follow-Up Question
**Q: "Why choose `queue:work` over `queue:listen` if `listen` solves memory leaks automatically?"**

**A:**
"`queue:listen` boots the entire Laravel framework (Service Container, Config, Routes) for **every single job**. This is extremely slow and CPU intensive.
`queue:work` boots once and stays in memory. It is incredibly fast but requires memory management discipline. In production, we always use `queue:work` with Supervisor to manage restarts."
