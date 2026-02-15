# 🧪 04 - Mocking in Laravel (Fake it 'til you make it)

### 🧐 Explanation
Mocking is: **Replacing Real Classes with Fake Classes**.
**Why?**
1.  **Speed**: Tests shouldn't wait 2 seconds for Stripe API.
2.  **Money**: Don't charge your credit card every time you run tests.
3.  **Consistency**: Stripe might be down. Your tests should still pass.

**Mockery** is built-in.

---

### 💻 Code Example

**❌ Bad (Real Stripe Call):**

```php
public function test_it_charges_user()
{
    // Hits real API! Takes 3 seconds! Charges $10!
    $response = $this->post('/checkout', ['token' => 'tok_visa']);
    
    $response->assertOk();
}
```

**✅ Good (Mocked Stripe):**

**Service:** `app/Services/StripeService.php`

```php
interface PaymentGateway {
    public function charge($token, $amount);
}
```

**Test:**

```php
public function test_it_charges_user_mocked()
{
    // 1. Create a Mock Object
    $mockProxy = Mockery::mock(PaymentGateway::class);
    
    // 2. Expect specific method calls
    $mockProxy->shouldReceive('charge')
        ->once() // Must be called exactly 1 time
        ->with('tok_visa', 1000) // Must pass these exact args
        ->andReturn('ch_12345'); // Return fake success ID
        
    // 3. Swap the Real Class with the Mock in the Service Container
    $this->instance(PaymentGateway::class, $mockProxy);
    
    // 4. Run the code
    $this->post('/checkout', ['token' => 'tok_visa'])->assertOk();
}
```

---

### ✅ When to Use
1.  **All External APIs**: Stripe, Twilio, S3, OpenAI.
2.  **Slow Processes**: PDF Generaton, Image resizing.
3.  **Hard to Reproduce States**: "What if Stripe returns a 503 error?" -> Simulating edge cases.

### ❌ When NOT to Use
1.  **Database Calls**: Don't mock Eloquent `User::find()`. Use an in-memory SQLite database or `RefreshDatabase` trait. Mocking Eloquent hides bugs.

---

### 🎙 Interview Insight
**Q: "What is `Http::fake()` vs Mockery?"**
**A:** "Laravel provides a lightweight wrapper for Guzzle called `Http::fake()`.
For simple REST calls: `Http::fake(['stripe.com/*' => Http::response(['id' => 'ch_1'], 200)])`.
It's easier than Mockery because you don't need Interfaces or manual binding. But for complex SDKs (like Stripe PHP SDK which doesn't use Laravel's Http client), you must mock the Class itself using Mockery."
