# 🪪 01 - Middleware Lifecycle: Before & After

### 🧐 Explanation
Requests -> **Middleware 1 (Before)** -> **Middleware 2 (Before)** -> **Controller** -> **Middleware 2 (After)** -> **Middleware 1 (After)** -> Response.

**Before**: Check Auth, Rate Limits, Clean Input.
**After**: Log Response Time, Add Headers, Encrypt Cookies.

---

### 💻 Code Example

**Middleware:** `app/Http/Middleware/CheckAge.php`

```php
public function handle(Request $request, Closure $next)
{
    // BEFORE (Validation)
    if ($request->age < 18) {
        return redirect('home');
    }

    // PASS TO NEXT MIDDLEWARE / CONTROLLER
    $response = $next($request);

    // AFTER (Modify Response)
    // Add custom header to every response
    $response->header('X-Age-Check', 'Passed');

    return $response;
}
```

---

### 💻 Terminable Middleware (Post-Response)
Sometimes you need to do work **after the response is sent to the browser** (e.g., Logging).
Laravel calls `terminate()` if you implement `TerminableMiddleware`.
Using `App::terminating(function() { ... })` often works too.

```php
public function terminate($request, $response)
{
    // Request is finished! Browser is happy.
    // Now log slow queries to DB without slowing down user.
    Log::info("Request took " . (microtime(true) - RAM_START) . "s");
}
```

---

### ✅ When to Use
1.  **Global Logic**: Convert Empty Strings to Null (`ConvertEmptyStringsToNull`).
2.  **Auth**: `auth:sanctum`.
3.  **Roles**: `role:admin`.
4.  **Security**: Setting CSP headers.

### ❌ When NOT to Use
1.  **Business Logic**: "If user is premium, apply discount". This belongs in a **Service**, not Middleware. Middleware should gate access, not modify data heavy-handedly.

---

### 🎙 Interview Insight
**Q: "What happens if I return a response in 'Before' middleware?"**
**A:** "The request cycle stops immediately. The controller is **never executed**.
Middleware acts as a filter. If `return redirect()` happens, the chain breaks. This is how Authentication works—it stops unauthenticated users dead in their tracks."
