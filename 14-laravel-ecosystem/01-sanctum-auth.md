# 🌐 01 - Sanctum: SPA & Mobile Auth (RIP Passport)

### 🧐 Explanation
Before: **Passport** (OAuth2 Server). Hard to setup. Overkill.
Now: **Sanctum** (Cookie/Token Auth). Simple!
1.  **SPA**: Cookie-based Session (Next.js/Nuxt). No token storage problems (XSS).
2.  **Mobile**: Token-based (Flutter/React Native). `Authorization: Bearer <token>`.

---

### 💻 Setup

```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

**Kernel**: Add middleware to `api` group.

```php
'api' => [
    \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
    'throttle:api',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
],
```

---

### 💻 Code Example (Issue Token)

**Login Controller**:

```php
public function login(Request $request)
{
    $user = User::where('email', $request->email)->first();
    
    if (!$user || !Hash::check($request->password, $user->password)) {
        return response('Bad credentials', 401);
    }
    
    // Create Token (Store in 'personal_access_tokens' table)
    $token = $user->createToken('iphone_app')->plainTextToken;
    
    return response(['token' => $token]);
}
```

**Accessing API**:

```bash
curl -H "Authorization: Bearer 1|abcdef123456" http://localhost/api/user
```

---

### ✅ When to Use
1.  **React/Vue SPA**: Sanctum allows you to use `axios` with `withCredentials: true`. Laravel verifies the session cookie perfectly. No manual token storage needed!
2.  **Simple API**: Just create API tokens manually for 3rd party integrations.

### ❌ When NOT to Use
1.  **OAuth Server**: If you want *other* developers to create apps ("Login with MySite"), you need Passport (OAuth2 scopes). Sanctum does NOT support this flow.

---

### 🎙 Interview Insight
**Q: "Why is Sanctum safer than JWT for SPAs?"**
**A:** "JWTs (storing tokens in `localStorage`) are vulnerable to XSS. If hacker injects JS, they steal the token.
Sanctum uses `HttpOnly` cookies for SPAs. Javascript cannot read the cookie. XSS cannot steal the session. CSRF protection is handled by Laravel automatically."
