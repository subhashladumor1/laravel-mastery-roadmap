# 🔐 03 - Secure Authentication (Wait... there's more than Login?)

### 🧐 Explanation
"I have Auth enabled, I'm safe." -> WRONG.
1.  **Session Fixation**: Hacker sets the session ID *before* you login.
2.  **Brute Force**: Trying `password123` 1000 times/second.
3.  **No 2FA**: Password leak = Total Compromise.

---

### 💻 Defense 1: Session Rotation (Fixation)

**Laravel does this automatically** on login (`Auth::login()`).
It regenerates the session ID.
But if you manually log someone in (e.g., Impersonation), call:

```php
Session::regenerate();
Auth::login($admin);
```

---

### 💻 Defense 2: Rate Limiting (Throttle)

**Route Middleware:** `app/Providers/RouteServiceProvider.php`

```php
RateLimiter::for('login', function (Request $request) {
    return Limit::perMinute(5)->by($request->email.$request->ip());
});
// 5 attempts per EMAIL + IP combo.
// Stops botnet from trying 'admin@site.com' from 100 different IPs.
```

---

### 💻 Defense 3: Passkeys / 2FA

Use **Laravel Fortify** or create a middleware.

**Middleware:** `app/Http/Middleware/EnsureTwoFactorEnabled.php`

```php
public function handle($request, Closure $next)
{
    $user = $request->user();

    if ($user && $user->two_factor_secret && !$request->session()->has('2fa_verified')) {
         return redirect()->route('2fa.index'); // Gate access
    }

    return $next($request);
}
```

---

### ✅ When to Use
1.  **Always**: Rate Limiting.
2.  **Admins**: Mandate 2FA for anyone with `can('view-dashboard')`.

### ❌ When NOT to Use
1.  **Public API**: If external devs need access, use API Tokens (Sanctum/Passport), not Session Auth.

---

### 🎙 Interview Insight
**Q: "Why do we throttle by Email AND IP?"**
**A:** "If we throttle only by IP, a botnet can attack one account from 1000 IPs (Distributed Attack).
If we throttle only by Email, an attacker can annoy a legitimate user by locking their account (Denial of Service) by intentionally failing 5 times."
