# ⚡ 04 - Queue Workers & Horizon: Async Power

### 🧐 Explanation
Nobody likes waiting 2s for an email.
**Queues** allow you to "Do it Later".
**Horizon** is a Dashboard to monitor Redis Queues.
It shows "Jobs Fails", "Wait Time", "Throughput".

---

### 💻 Code Example

**❌ Bad (Sync Email - Slow):**

```php
// User waits 2s.
Mail::to($user)->send(new WelcomeEmail());
```

**✅ Good (Async Job - Fast):**

```php
// User waits 0.1s. Job is pushed to Redis.
dispatch(new SendWelcomeEmail($user));
```

**✅ Better (Supervisor):**

You need a worker running in the background.

```bash
# In production, use Supervisor to keep this alive:
php artisan horizon
```

---

### 💻 Horizon Config (`config/horizon.php`)

```php
'environments' => [
    'production' => [
        'supervisor-1' => [
            'connection' => 'redis',
            'queue' => ['default', 'emails'],
            'balance' => 'auto', // Auto-scale workers based on load!
            'minProcesses' => 1,
            'maxProcesses' => 10,
            'tries' => 3,
        ],
    ],
],
```

---

### ✅ When to Use
1.  **Always**: Emails, SMS, PDF Generation, S3 Uploads (video encoding).
2.  **Webhooks**: Receiving Stripe webhooks? Process them `async`. Reply 200 OK immediately.
3.  **Metrics**: Updating user stats.

### ❌ When NOT to Use
1.  **Synchronous Logic**: If you need the result immediately to redirect the user (`$charge->status`), do it synchronously.

---

### 🎙 Interview Insight
**Q: "What is `queue:retry` vs `queue:failed`?"**
**A:** "When a job fails (Exception), it goes to the `failed_jobs` table.
`queue:retry [id]` moves it back to the queue (Retries execution).
`queue:flush` deletes all failed jobs.
Horizon automates retries with 'Retry After' logic (e.g., waiting 60s before retrying fetching a URL)."
