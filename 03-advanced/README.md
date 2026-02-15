# 🧠 03 - Advanced Laravel: The Core

> **Warning: Here Be Dragons 🐉**
> These concepts require deep framework knowledge. They are used for building packages or complex SaaS systems.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-macros.md](./01-macros.md)** | Extending Laravel's core classes dynamically. | 🟠 Medium |
| **[02-pipelines.md](./02-pipelines.md)** | Chain-of-responsibility pattern for complex workflows. | 🔴 Hard |
| **[03-service-providers.md](./03-service-providers.md)** | Bootstrapping services correctly. Deferred vs Eager. | 🔴 Hard |
| **[04-custom-drivers.md](./04-custom-drivers.md)** | Building custom Cache, Session, or Auth drivers. | 🔴 Hard |
| **[05-eloquent-performance.md](./05-eloquent-performance.md)** | **Optimization**: Chunk, Cursor, Subqueries, Raw SQL. | 🔴 Hard |
| **[06-container-facades.md](./06-container-facades.md)** | **Internal Deep Dive**: How DI and Facades *actually* work. | 🔴 Hard |

---

## 🎙 Interview Insight
**Q: "What is a Macro in Laravel?"**

**A:**
"A Macro is a way to dynamically add methods to a class at runtime using the `Macroable` trait. It's heavily used in Laravel's Collections, Response, and Request classes.
Instead of extending `Illuminate\Http\Response` just to add a helper method, I can register a macro in `AppServiceProvider`:
```php
Response::macro('apiSuccess', function ($data) {
    return Response::json(['data' => $data, 'status' => 'success']);
});
```
This keeps the code clean and avoids inheritance hell."
