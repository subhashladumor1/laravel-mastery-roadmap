# 🧠 04 - Custom Drivers: The Clean Extensibility

### 🧐 Explanation
Laravel allows you to swap out core components based on config.
*   **Cache**: Redis, File, Database.
*   **Session**: Cookie, Redis.
*   **Auth**: Eloquent, Database.

Sometimes, you need something custom:
*   Connect `Session` to **MongoDB**.
*   Connect `Auth` to **Active Directory** (via LDAP).

---

### 💻 Code Example (Custom Auth Guard)

**File:** `app/Providers/AuthServiceProvider.php`

```php
use App\Services\Auth\LdapUserProvider;
use Illuminate\Support\Facades\Auth;

public function boot(): void
{
    Auth::provider('ldap', function ($app, array $config) {
        return new LdapUserProvider($config['host']);
    });
}
```

**File:** `config/auth.php`

```php
'providers' => [
    'users' => [
        'driver' => 'ldap', // Use our custom driver!
        'model' => App\Models\User::class,
    ],
],
```

**File:** `app/Services/Auth/LdapUserProvider.php`

```php
use Illuminate\Contracts\Auth\UserProvider;
use Illuminate\Contracts\Auth\Authenticatable;

class LdapUserProvider implements UserProvider
{
    public function retrieveByCredentials(array $credentials)
    {
        // 1. Connect to LDAP server
        // 2. Validate user/pass
        // 3. Return generic User object
    }
    
    // Implement retrieveById, updateRememberToken, etc.
}
```

---

### ✅ When to Use
1.  **Legacy Integration**: User table is in Oracle DB, not MySQL.
2.  **SaaS Multi-tenancy**: Auth logic depends on Subdomain, not just email.
3.  **No-Code Auth**: Auth via Magic Link via Email (Passwordless).

### ❌ When NOT to Use
1.  **Direct Modification**: Never edit `vendor/laravel/framework`. Always extend via driver.
2.  **Simple Tweaks**: If you just need to add `where active=1` to login, use a global scope on the User model instead of a custom Auth driver.

---

### 🎙 Interview Insight
**Q: "How do you extend Laravel's Queue system to use RabbitMQ?"**
**A:** "I would use `Queue::extend('rabbitmq', function () { ... })` in a Service Provider.
Inside the closure, I return a class implementing `Illuminate\Contracts\Queue\Queue`. This allows me to use `Queue::connection('rabbitmq')->push(...)` without changing any application code."
