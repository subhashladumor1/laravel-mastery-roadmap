# 🏗️ 02 - The Service Layer: Where Business Logic Lives

### 🧐 Explanation
Controllers handle HTTP. Models handle Database.
Where do you put the logic for **"Register User + Send Email + Add to Newsletter + Charge Credit Card"**?
The **Service Layer**.

**Goal**: Thin Controllers, Fat Services.

---

### 💻 Code Example

**❌ Bad (Fat Controller):**
```php
public function store(Request $request) {
    // 1. Validation
    $data = $request->validate([...]);
    
    // 2. Logic (Messy)
    $user = User::create($data);
    
    Mail::to($user)->send(new WelcomeEmail());
    Newsletter::subscribe($user->email);
    Stripe::charge($request->token, 1000);
    
    return redirect('/');
}
```

**✅ Good (Service Pattern):**

**File:** `app/Services/UserOnboardingService.php`

```php
class UserOnboardingService {
    public function execute(array $data, string $token): User {
        return DB::transaction(function() use ($data, $token) {
            $user = User::create($data);
            
            // Handle failures gracefully
            try {
                Stripe::charge($token, 1000);
            } catch (StripeException $e) {
                throw new PaymentFailedException($e->getMessage());
            }

            event(new UserRegistered($user)); // Decouples Email/Newsletter via Listeners!
            
            return $user;
        });
    }
}
```

**Controller:**
```php
public function store(StoreUserRequest $request, UserOnboardingService $service) {
    try {
        $user = $service->execute($request->validated(), $request->token);
        return redirect()->route('dashboard');
    } catch (PaymentFailedException $e) {
        return back()->withErrors(['payment' => $e->getMessage()]);
    }
}
```

---

### ✅ When to Use
1.  **Complex Actions**: More than 1 line of creation logic.
2.  **Reusability**: You need to register a user from the API *and* the Admin Panel *and* a Console Command. The logic lives in *one place*.
3.  **Testing**: Easy to unit test `UserOnboardingService` without mocking HTTP requests.

### ❌ When NOT to Use
1.  **Simple CRUD**: If your controller just does `Post::create($request->all())`, keep it simple. Don't add a service for a on-liner.

---

### 🎙 Interview Insight
**Q: "What is the difference between a Service and a Helper function?"**

**A:**
"Helpers are for generic utilities (formatting dates, strings). They are stateless.
Services encapsulate **domain logic** and often have dependencies (Repositories, Mailers, Stripe Clients) injected via the Constructor. Services represent a specific business action or domain context."
