# 🍼 04-interview-questions/beginner/04-middleware-basics.md

## ❓ The Question
**"What is Middleware in Laravel and when does it run?"**

---

### 💡 The Answer (Beginner)
**Middleware** are layers that HTTP requests must pass through before hitting your Controller.
They act as filters.

**Analogy:**
Security Check at an Airport.
1.  **Request**: You (trying to get to the plane).
2.  **Middleware**: Security guard (Checks ID/Ticket).
3.  **Controller**: The plane logic.
4.  **Response**: Flight (You traveling).

**Examples:**
*   `auth`: "Are you logged in?"
*   `throttle`: "Are you requesting too fast?"
*   `verified`: "Is your email confirmed?"

---

### 💻 Code Example

**Create**: `php artisan make:middleware CheckAge`

**Logic**:
```php
public function handle(Request $request, Closure $next)
{
    if ($request->age < 200) {
        return redirect('home');
    }

    return $next($request); // Pass to next layer
}
```

**Register**: in `app/Http/Kernel.php` (Laravel 11: `bootstrap/app.php`).
