# 🧪 09 - The Testing Pyramid (Pest vs PHPUnit)

> **"Code without tests is legacy code."** (Michael Feathers)
> Laravel shines because it makes testing *fun*.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-pest-vs-phpunit.md](./01-pest-vs-phpunit.md)** | Why Pest is winning the Laravel world. | 🟢 Basic |
| **[02-factories-seeders.md](./02-factories-seeders.md)** | Creating thousands of dummy users in milliseconds. | 🟢 Basic |
| **[03-browser-testing-dusk.md](./03-browser-testing-dusk.md)** | Clicking buttons with Selenium/Dusk. | 🔴 Hard |
| **[04-mocking.md](./04-mocking.md)** | Faking Stripe API calls correctly. | 🟠 Medium |

---

## 💡 Quick Start (Pest)

```bash
composer require pestphp/pest-plugin-laravel --dev
php artisan pest:install
php artisan test
```

**Example Test:**
```php
it('creates a user', function () {
    $user = User::factory()->create();
    
    expect($user)->toBeInstanceOf(User::class);
});
```
This is readable even by non-coders.
