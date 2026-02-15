# 🎓 Intermediate Laravel Magic

## ⚠️ Warning: Beyond Hello World
We assume you know `Route::get('/', function())`.
Here we discuss creating **Middleware**, **Event Listeners**, and **Custom Service Providers**.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-middleware-lifecycle.md](./01-middleware-lifecycle.md)** | Before vs After Middleware. Terminable Middleware. | 🟠 Medium |
| **[02-route-binding.md](./02-route-binding.md)** | Customizing `Route::model()`, Slugs, and Scoping. | 🟢 Basic |
| **[03-eloquent-relationships.md](./03-eloquent-relationships.md)** | Polymorphic Many-to-Many? `morphMap`? We cover it. | 🟠 Medium |
| **[04-api-resources.md](./04-api-resources.md)** | Transforming Models to JSON properly (hateoas). | 🟢 Basic |

---

## 💡 Practical Tip
Don't use `request()->input('foo')` in your Controller.
Use **Form Requests** (`php artisan make:request StorePostRequest`). it keeps your controller clean and handles validation automatically.
