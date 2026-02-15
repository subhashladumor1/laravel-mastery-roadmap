# 🧠 03 - Service Providers: The Application Bootstrapper

### 🧐 Explanation
Every request in Laravel **boots** the framework.
This means **Service Providers** register everything:
1.  **Routes**
2.  **Views**
3.  **Configs**
4.  **Events**
5.  **Bindings** (Interfaces -> Classes)

**Key Concept**: "Deferred Providers".
If your service (like `SMSClient`) is heavy (connects to Twilio APIs on construct), **don't load it on every request**!
Load it **lazily** only when requested.

---

### 💻 Code Example (Performance Optimization)

**Provider:** `app/Providers/SmsServiceProvider.php`

```php
use Illuminate\Support\ServiceProvider;
use Illuminate\Contracts\Support\DeferrableProvider; // Implement this!

class SmsServiceProvider extends ServiceProvider implements DeferrableProvider
{
    /**
     * Register services.
     */
    public function register(): void
    {
        $this->app->singleton(SmsClient::class, function ($app) {
            // Expensive operation: connects to external API
            return new SmsClient(config('services.twilio.key'));
        });
    }

    /**
     * Bootstrap services.
     */
    public function boot(): void
    {
        // Don't do heavy work here!
    }

    /**
     * Get the services provided by the provider.
     * Only load this provider when someone calls app(SmsClient::class)!
     */
    public function provides(): array
    {
        return [SmsClient::class];
    }
}
```

---

### ✅ When to Use
1.  **Package Development**: Always register your package in a SP.
2.  **Heavy Services**: Always defer heavy/rarely used services.
3.  **View Composers**: Bind data to views globally in `boot()`.

### ❌ When NOT to Use
1.  **Global Middleware**: Register those in `bootstrap/app.php` (Laravel 11+).
2.  **Simple Config**: Don't make a provider just to merge one config file.

---

### 🎙 Interview Insight
**Q: "What is the difference between `register()` and `boot()` methods?"**
**A:** "In `register()`, we only bind things into the Service Container. We assume *other* services might not exist yet.
In `boot()`, all providers have been registered, so we can access any other service (e.g., Database, Validation, Events).
Example: Bind interface in `register()`, Add event listener in `boot()`."
