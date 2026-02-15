# 🍼 04-interview-questions/intermediate/01-n-plus-1.md

## ❓ The Question
**"What is the N+1 Query Problem in Eloquent? Why is it dangerous?"**

---

### 💡 The Answer (Intermediate)
"The **N+1** problem means executing **1 query** to fetch the parent records (N), and then **1 additional query** for *each* parent record to fetch its children.

If I fetch 100 Users (1 query) and then access `$user->posts` inside a loop, Laravel runs **100 extra queries**.
Total: 101 queries for a simple page.

**Solution**: Eager Loading (`with('posts')`).
This runs only **2 queries** total:
1. `SELECT * FROM users`
2. `SELECT * FROM posts WHERE user_id IN (1, 2, ..., N)`"

---

### 💻 Code Example

**❌ Bad (Lazy Loading - N+1):**
```php
$users = User::all(); // 1 Query
foreach ($users as $user) {
    echo $user->posts->count(); // 1 Query *per User*
}
```

**✅ Good (Eager Loading):**
```php
$users = User::with('posts')->get(); // 2 Queries Total
foreach ($users as $user) {
    echo $user->posts->count(); // 0 Queries (From Memory)
}
```

---

### 🔄 Follow-Up Question
**Q: "Can I Eager Load *after* fetching the model?"**
**A:** "Yes, using `$user->load('posts')`. This is called Lazy Eager Loading. Useful when you only need the relation conditionally inside an `if` block."
