# 🧠 01 - Service Container: Internals (Reflection & Auto-Wiring)

### 🧐 Explanation
How does Laravel know to give you a `Mailer` when you type `__construct(Mailer $mail)`?
It uses **PHP Reflection API**.
1.  Check constructor args.
2.  See `Mailer` class hint.
3.  Check if `Mailer` is bound in container.
4.  If yes, return it.
5.  If no, use `new Mailer()`.

**Recursive Resolution**:
If `Mailer` depends on `SmtpTransport`, it resolves that too!

---

### 💻 Code Example (Manual Resolution)

**What happens inside `app()` helper:**

```php
$reflector = new ReflectionClass(UserController::class);
$constructor = $reflector->getConstructor();
$dependencies = $constructor->getParameters(); // [Mailer $mailer]

foreach ($dependencies as $dep) {
    if ($dep->getType() && !$dep->getType()->isBuiltin()) {
        $className = $dep->getType()->getName(); // "App\Services\Mailer"
        $instances[] = new $className(); // Recursion happens here
    }
}

return $reflector->newInstanceArgs($instances);
```

---

### ✅ When to Use
1.  **Always via Injection**: Never use `new Class` for services.
2.  **Contextual Binding**: `when(PhotoController::class)->needs(Filesystem::class)->give(S3::class)`.
    `when(VideoController::class)->needs(Filesystem::class)->give(Local::class)`.

### ❌ When NOT to Use
1.  **Over-engineering**: Binding string primitives (`'api_key'`) into everything. Just use config().

---

### 🎙 Interview Insight
**Q: "Can the Service Container resolve circular dependencies?"**
**A:** "No. If A needs B and B needs A, PHP will loop infinitely until Stack Overflow.
Solution: Use **Setter Injection** or **Defer** one dependency using a Closure or `App::make()` inside a method instead of `__construct()`."
