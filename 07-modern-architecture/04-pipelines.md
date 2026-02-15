# 🏗️ 04 - Pipelines (Architectural Pattern)

### 🧐 Explanation
This is about how **Data Flows** through layers of your application.
1.  **Request Pipeline**: Middleware -> Route -> Controller.
2.  **Domain Pipeline**: Step 1 -> Step 2 -> Step 3 -> Success.

**Goal**: Keep code **declarative**.
"I want to Validate, then Normalize, then Save, then Notify."
Instead of nested `try-catch`, use pipes.

---

### 💻 Code Example

**Service:** `app/Services/OrderPipeline.php`

```php
use Illuminate\Pipeline\Pipeline;

public function process(Order $order)
{
    app(Pipeline::class)
        ->send($order)
        ->through([
            CheckInventory::class,
            ChargeCreditCard::class,
            DeductStock::class,
            SendReceipt::class,
        ])
        ->then(fn ($order) => $order->complete());
}
```

**Step:** `app/Pipeline/CheckInventory.php`

```php
class CheckInventory
{
    public function handle(Order $order, Closure $next)
    {
        if ($order->items->unavailable()) {
            throw new InventoryException("Out of stock!");
        }

        return $next($order);
    }
}
```

---

### ✅ When to Use
1.  **Strict Order**: Steps must execute sequentially.
2.  **Reusability**: Use `CheckInventory` in API *and* Web *and* Admin panel.
3.  **Testing**: Easy to mock individual pipes.

### ❌ When NOT to Use
1.  **Simple Logic**: Just call `update()` on the model.
2.  **Async**: If step 2 requires waiting 5 mins, use a Queue Job chain (`Bus::chain()`) instead of a Pipeline.

---

### 🎙 Interview Insight
**Q: "How do Pipelines differ from Queue Chains?"**
**A:** "Pipelines run **synchronously** in memory within one request. If one step fails (Exception), the whole request fails immediately.
Queue Chains run **asynchronously** in the background. If step 1 succeeds, step 2 runs. If step 2 fails, it retries later.
Use Pipelines for *atomic* operations (Database transactions). Use Queues for *long-running* tasks."
