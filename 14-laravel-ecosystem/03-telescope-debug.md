# 🌐 03 - Telescope: Debugging on Steroids

### 🧐 Explanation
Before: `dd($request->all())`.
Now: **Telescope**.
A local dashboard `/telescope` that records:
1.  **Requests**: Headers, body, duration.
2.  **Commands**: Artisan output.
3.  **Schedule**: Background tasks.
4.  **Exceptions**: Full stack trace.
5.  **Log**: `Log::info()`.
6.  **Queries**: Shows slow SQL.
7.  **Models**: Created/Updated events.
8.  **Mail**: Preview emails without sending.
9.  **Notifications**: Preview Slack payloads.
10. **Cache**: Keys set/missed.

---

### 💻 Setup

```bash
composer require laravel/telescope --dev
php artisan telescope:install
php artisan migrate
```

**Config (Critical)**: `app/Providers/TelescopeServiceProvider.php`

```php
protected function gate()
{
    Gate::define('viewTelescope', function ($user) {
        return in_array($user->email, [
            'admin@example.com',
        ]);
    });
}
```

---

### ✅ When to Use
1.  **Local**: Always.
2.  **Staging**: Useful to debug QA issues.

### ❌ When NOT to Use
1.  **Production**: It records EVERY request into the database. Your DB will grow by 1GB/day on high traffic.
    If you MUST use it in Prod, filter heavily:

```php
// Only record Exceptions & Failed Jobs
Telescope::filter(function (IncomingEntry $entry) {
    return $entry->isReportableException() ||
           $entry->isFailedJob() ||
           $entry->isScheduledTask() ||
           $entry->hasMonitoredTag();
});
```

And run pruning daily:
`$schedule->command('telescope:prune')->daily();`

---

### 🎙 Interview Insight
**Q: "How does Telescope hurt performance?"**
**A:** "It writes to the database on *every* request (synchronously by default).
This adds latency (inserting logs). On high traffic, it consumes connections and disk I/O.
Disable the watcher for things you don't need (e.g., `EventWatcher` if you have 1000 events/sec)."
