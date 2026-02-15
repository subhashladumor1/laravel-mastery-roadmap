# 🏗️ 03 - Event Listeners (Decoupling Your Code)

### 🧐 Explanation
Events are the **Glue** of modern apps.
1.  User Registers -> Fire `UserRegistered`.
2.  Listener 1: `SendWelcomeEmail`.
3.  Listener 2: `SyncToCrm`.
4.  Listener 3: `NotifyAdmins`.

**Benefit**: If email fails, user still registers.
**Benefit**: You can add/remove listeners without changing the Controller.

---

### 💻 Code Example

**Event:** `app/Events/OrderPlaced.php`

```php
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class OrderPlaced
{
    use Dispatchable, SerializesModels;

    public function __construct(public Order $order) {}
}
```

**Listener:** `app/Listeners/SendOrderConfirmation.php`

```php
use Illuminate\Contracts\Queue\ShouldQueue; // Job Queue

class SendOrderConfirmation implements ShouldQueue
{
    public function handle(OrderPlaced $event): void
    {
        // 1. Send Email (Async!)
        Mail::to($event->order->user)->send(new OrderConfirmation($event->order));
        
        // 2. Log Access
        Log::info("Order Email Sent: {$event->order->id}");
    }
}
```

**Wiring It Up:** `app/Providers/EventServiceProvider.php` (Auto-discovered often)

```php
protected $listen = [
    OrderPlaced::class => [
        SendOrderConfirmation::class,
        UpdateStockLevels::class,
    ],
];
```

---

### ✅ When to Use
1.  **Side Effects**: Anything that happens *after* the main action (logging, emails, notifications).
2.  **Async Work**: Sending data to Salesforce (slow API) via Queue.
3.  **Metrics**: Increment 'New Users Today' in Redis.

### ❌ When NOT to Use
1.  **Dependency**: If step 2 *must* happen for step 1 to be valid (e.g., deducting money before shipping), don't put it in a queued listener. Do it in a Transaction in the Service.

---

### 🎙 Interview Insight
**Q: "What is `ShouldQueue` on a Listener?"**
**A:** "It tells Laravel to push this listener to the Queue (Redis/SQS) instead of executing it synchronously. This speeds up the HTTP response significantly. Without `ShouldQueue`, the user waits for the email to send before seeing the success page."
