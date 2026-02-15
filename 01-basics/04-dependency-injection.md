# 🍼 04 - Dependency Injection (Stop Using `new Class`)

### 🧐 Explanation
In vanilla PHP, you do:
`$mailer = new Mailer('smtp.mailgun.org', 'API_KEY');`

In Laravel, you do:
`public function __construct(Mailer $mailer)`

**Why?**
1.  **Testing**: You can swap `Mailer` for `FakeMailer` in tests.
2.  **Configuration**: Credentials are loaded once in `AppServiceProvider`, not everywhere.
3.  **Refactoring**: Change Mailgun to Postmark in one place.

---

### 💻 Code Example

**❌ Bad (Hardcoded Dependency):**

```php
class UserController extends Controller
{
    public function store()
    {
        // Tightly coupled. Impossible to test without sending real email.
        $mailer = new \App\Services\Mailer('secret-api-key');
        $mailer->send('Welcome!');
    }
}
```

**✅ Good (Dependency Injection):**

```php
use App\Services\Mailer;

class UserController extends Controller
{
    // Laravel automatically "injects" the Mailer instance here
    public function __construct(protected Mailer $mailer) {}

    public function store()
    {
        $this->mailer->send('Welcome!');
    }
}
```

---

### ✅ When to Use
1.  **Always**: Use DI for Services, Repositories, and Helpers.
2.  **Route Model Binding**: `show(User $user)` is DI for Models.

### ❌ When NOT to Use
1.  **DTOs / Value Objects**: `new Money(100, 'USD')` is fine. These are data, not services.

---

### 🎙 Interview Insight
**Q: "What is the Service Container?"**
**A:** "It's the heart of Laravel. It manages class dependencies. 
When I type-hint `Mailer` in a controller constructor, the Container looks up how to build that class (reflection), resolves its dependencies recursively, and gives me the instance. It's an **Inversion of Control (IoC)** container."
