# 🍼 04-interview-questions/beginner/02-facades-vs-helpers.md

## ❓ The Question
**"What's the difference between `Auth::user()` (Facade) and `auth()->user()` (Helper)?"**

---

### 💡 The Answer (Beginner)
1.  **Facades (`Auth::user()`)**:
    *   Syntactic sugar for accessing the Service Container.
    *   Looks like a static method, but actually uses `__callStatic()` to resolve a specific binding (e.g., `auth` service).
    *   Harder to mock in tests *if misused*.

2.  **Helpers (`auth()->user()`)**:
    *   Functions that return the same underlying service.
    *   Syntactic sugar for `app('auth')->user()`.
    *   Easier to use in views: `{{ auth()->user()->name }}`.

**Which one is better?**
"Neither is strictly better. They both do the EXACT same thing under the hood. However, Facades are more consistent with Laravel's style, while Helpers are cleaner in Blade templates."

---

### 💻 Code Example

**Facade:**
```php
use Illuminate\Support\Facades\Log;

Log::info('Login success'); // Resolves 'log' service
```

**Helper:**
```php
info('Login success'); // Resolves 'log' service
```

---

### 🔄 Follow-Up Question
**Q: "Can Facades be tested easily?"**
**A:** "Yes! Before, they were considered hard to test. Now, `Log::shouldReceive('info')->once();` mocks the facade easily. So the main criticism is outdated."
