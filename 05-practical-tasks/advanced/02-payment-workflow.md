# 💰 Advanced Task: Payment Workflow (Zero Failure)

### 📌 Problem Description
**"Build a payment system that handles duplicate webhooks, bank delays, and race conditions without double-charging or losing an order."**

**Core Requirements:**
1.  **Idempotency**: Processing the same webhook twice must not create two orders.
2.  **Atomic Transactions**: DB updates happen *only* if payment succeeds.
3.  **Audit Logs**: Every step (Started, Pending, Succeeded, Failed) is logged.
4.  **Race Condition**: User clicks "Pay" twice -> Only 1 charge happens.

---

### 📐 Architecture Diagram (ASCII)

```
[ User ] --> [ Frontend (React) ] --> [ API: /checkout (Create Intent) ]
                                              |
      [ Stripe Processing ] ------------------+
              | (Async Webhook)
              v
      [ Laravel Webhook Handler ]
              |
      [ Redis Lock: order_123 ] 
              |
      [ DB Transaction: Update Order -> Send Email ]
```

---

### 💻 Implementation Guide

**1. Idempotency Key (Database):**
Add `stripe_payment_intent_id` and `status` to `orders` table.

**Migration:**
```php
Schema::table('orders', function (Blueprint $table) {
    $table->string('stripe_payment_intent_id')->unique()->nullable();
    $table->string('status')->default('pending'); // pending, paid, failed
});
```

**2. Webhook Handler (Safe Logic):**
**File:** `app/Http/Controllers/StripeWebhookController.php`

```php
public function handle(Request $request)
{
    $payload = $request->input();
    $event = $payload['type'];
    $intentId = $payload['data']['object']['id'];

    // 1. Lock (Prevent Race Condition)
    // If webhook fires twice instantly, second one waits 5s then fails.
    return Cache::lock('stripe_webhook_'.$intentId, 5)->get(function () use ($event, $intentId) {
        
        $order = Order::where('stripe_payment_intent_id', $intentId)->firstOrFail();

        // 2. Idempotency Check
        if ($order->status === 'paid') {
            Log::info("Duplicate webhook for Order {$order->id}");
            return response('Handled', 200);
        }

        // 3. Process
        if ($event === 'payment_intent.succeeded') {
            DB::transaction(function () use ($order) {
                $order->update(['status' => 'paid']);
                // Send email, grant access, etc.
            });
        }

        return response('Success', 200);
    });
}
```

---

### 🧪 Test Cases (Pest PHP)

**Test 1: Race Condition Simulation**
```php
it('handles concurrent webhooks safely', function () {
    $order = Order::factory()->create(['status' => 'pending']);
    $payload = stripe_event('payment_intent.succeeded', $order->stripe_payment_intent_id);

    // Simulate 2 parallel requests
    $response1 = postJson('/stripe/webhook', $payload);
    $response2 = postJson('/stripe/webhook', $payload);

    // Both succeed (200 OK), but ONLY ONE updates the DB
    $this->assertDatabaseHas('orders', ['id' => $order->id, 'status' => 'paid']);
    // Check logs for "Duplicate webhook"
});
```

---

### 🚀 Deployment Steps

1.  **Redis**: Ensure Redis is running for Locking (`Cache::lock`). File cache is flaky.
2.  **Stripe CLI**: Test locally: `stripe listen --forward-to localhost:8000/stripe/webhook`.
3.  **Monitor**: Sentry/Datadog to track failed webhooks.
