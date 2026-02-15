# 🍼 Intermediate Interview Questions: Digging Deeper

> **"Can you explain why N+1 happens?"**
> These questions show you understand performance and database design.

---

## 📚 Topics

| Question | Core Concept | Difficulty |
| :--- | :--- | :--- |
| **[01-n-plus-1.md](./01-n-plus-1.md)** | Eager Loading vs Lazy Loading. | 🟠 Medium |
| **[02-traits-vs-interfaces.md](./02-traits-vs-interfaces.md)** | Code Reusability (`use SoftDeletes`) vs Code Contract (`implements Repository`). | 🟠 Medium |
| **[03-database-transactions.md](./03-database-transactions.md)** | `DB::transaction()` and ACID. | 🟠 Medium |
| **[04-request-lifecycle.md](./04-request-lifecycle.md)** | `public/index.php` -> `App` -> `Kernel` -> `Route` -> `Middleware` -> `Controller`. | 🟠 Medium |
| **[05-rapid-fire-questions.md](./05-rapid-fire-questions.md)** | **50+ One-Liner Questions** (Routes, Pivot, Polymorphic, API). | 🟠 Revision |

---

## 💡 How to Answer
**Don't say:**
> "Always use `with()` to prevent slow queries."

**Say:**
> "N+1 happens because relationships are lazy-loaded by default. If we loop through 100 users and access `$user->posts`, we run 101 queries. I use `User::with('posts')->get()` (Eager Loading) to fetch all related posts in just **2 SQL queries**, regardless of how many users exist."
