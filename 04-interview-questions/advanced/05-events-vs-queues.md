# 🧠 05 - Scale: Events vs Queues

### ❓ The Interview Question
**"We use Events for almost everything. Is this scalable? When should we switch to Queues, and what is the difference for the mental model?"**

---

### 💡 High-Level Answer (Senior Architect)
"Events are just **Hooks**. 'When `OrderPlaced` happens...'.
Queues are **Processes**. 'Do this work later...'.

You attach Queued Listeners to Events. An event can trigger:
1.  **Sync**: Log to file (Instant).
2.  **Async**: Send Email (Queue).
3.  **Sync**: Validate Stock (Instant).

Using events is *good architecture* because it decouples logic. The key is implementing `ShouldQueue` on heavy listeners."

---

### 💻 Code Example

**❌ Bad (Everything Sync):**

```php
public function store()
{
    // 500ms
    $order = Order::create();
    
    // 2000ms (Slow API) - User waits 2.5s!
    Mail::send(new OrderEmail($order)); 
    
    // 500ms
    SMS::send($order->phone);
}
```

**✅ Good (Event -> Queue):**

**Event:** `OrderPlaced` (Just passes data).

**Listener:** `SendOrderEmail` implements `ShouldQueue`.

```php
class SendOrderEmail implements ShouldQueue
{
    public $queue = 'high-priority'; // Specific queue name
    public $tries = 3; // Retry on failure

    public function handle(OrderPlaced $event)
    {
        Mail::send(....);
    }
}
```

**Result:** User waits 500ms. Code returns immediately. Email sends 1s later in background worker.

---

### 🌍 Real World Scenario
**Scenario:**
Black Friday. 1000 orders/sec.
Without queues, PHP-FPM processes will hang waiting for SMTP servers.
The server will crash (504 Timeout) because all workers are busy sending emails.
With queues, orders are saved instantly. Workers process emails at their own pace (or scale up workers).

---

### 🔄 Follow-Up Question
**Q: "Can events be lost?"**
**A:** "If you use `ShouldQueue`, the job is stored in Redis/Database.
If Redis crashes before persistence (AOF/RDB), yes, you lose the job.
For critical data (Financial Ledgers), use a durable queue like **Amazon SQS** or **RabbitMQ** with explicit acknowledgments, not Redis."
