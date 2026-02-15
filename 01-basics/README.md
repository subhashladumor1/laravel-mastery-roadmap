# 🍼 01 - Laravel Basics: Solid Foundations

> **"A skyscraper built on sand will fall."**
> These basics aren't just "how to write a route". They are about **Modern PHP 8.4+** features, strict typing, and clean code principles.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-modern-php.md](./01-modern-php.md)** | Constructor Property Promotion, Enums, Match expressions. | 🟢 Basic |
| **[02-clean-controllers.md](./02-clean-controllers.md)** | Keeping controllers skinny. Single Action Controllers. | 🟢 Basic |
| **[03-request-validation.md](./03-request-validation.md)** | Form Requests vs Inline Validation. | 🟢 Basic |
| **[04-dependency-injection.md](./04-dependency-injection.md)** | Why `new Class()` is evil in Laravel. | 🟠 Medium |

---

## 💡 Pro Tip
Stop using `compact('users', 'posts')`.
Use structured arrays or DTOs.
```php
// ❌ Bad
return view('posts.index', compact('posts'));

// ✅ Good
return view('posts.index', [
    'posts' => $posts,
]);
```
It's easier to grep and refactor.
