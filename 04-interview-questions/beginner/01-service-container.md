# 🍼 04-interview-questions/beginner/01-service-container.md

## ❓ The Question
**"What is the Service Container and Dependency Injection?"**

---

### 💡 The Answer (Beginner)
1.  **Dependency Injection (DI)**: Instead of creating a `new Mailer()` inside your controller (Hardcod), you ask for it in the constructor (`__construct(Mailer $mail)`).
2.  **Service Container**: The "Robot" that looks at your constructor, sees you need a `Mailer`, and automatically creates it for you. It holds all the bindings (Rules on how to create classes).

**Analogy**:
*   **Hardcoding**: You build a car engine inside the car yourself.
*   **DI**: You buy an engine and put it in the car.
*   **Container**: The factory that builds the engine for you and delivers it.

---

### 💻 Code Example

**❌ Bad (No DI):**
```php
class UserController {
    public function store() {
        $mailer = new Mailer('api-key'); // Hardcoded dependency
        $mailer->send();
    }
}
```

**✅ Good (DI):**
```php
class UserController {
    public function __construct(protected Mailer $mailer) {}

    public function store() {
        $this->mailer->send();
    }
}
```

---

### 🔄 Follow-Up Question
**Q: "Where do we register bindings?"**
**A:** "In Service Providers, typically `AppServiceProvider` inside the `register()` method."
