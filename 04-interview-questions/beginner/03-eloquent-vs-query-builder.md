# 🍼 04-interview-questions/beginner/03-eloquent-vs-query-builder.md

## ❓ The Question
**"What is the difference between Eloquent ORM and Query Builder?"**

---

### 💡 The Answer (Beginner)
1.  **Eloquent (`User::all()`)**:
    *   **Object-Oriented**: Returns instances of `User` model.
    *   **Features**: accessors, mutators, relationships, events (`saving`, `deleted`).
    *   **Performance**: Slightly slower (overhead of creating objects).

2.  **Query Builder (`DB::table('users')->get()`)**:
    *   **Direct SQL Wrapper**: Returns `stdClass` objects (arrays).
    *   **Features**: Just raw data. No events, no relationships logic.
    *   **Performance**: Faster. Good for reports or bulk updates.

**When to use which?**
"Use Eloquent for 95% of the logic (CRUD, logic). Use Query Builder for high-performance complex reports or bulk updates where model overhead is too high."

---

### 💻 Code Example

**Eloquent:**
```php
$users = User::where('active', 1)->get();
// Each $item is a User model.
```

**Query Builder:**
```php
$users = DB::table('users')->where('active', 1)->get();
// Each $item is a stdClass object.
```
