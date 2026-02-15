# 🪪 02 - Route Model Binding: Implicit vs Explicit

### 🧐 Explanation
Before:
`$user = User::findOrFail($id);`

**Implicit Binding** (Laravel Magic):
`public function show(User $user)`
Laravel looks at the URL `/users/1` and automatically fetches User ID 1.

**Scoped Binding** (Implicit):
`Route::get('/users/{user}/posts/{post}', [PostController::class, 'show'])`
Laravel ensures post belongs to user!

---

### 💻 Code Example

**❌ Implicit (Easy):**

```php
// Route
Route::get('/users/{user:slug}', function (User $user) {
    return $user;
});

// URL: /users/taylor-otwell (Finds by slug column)
```

**✅ Explicit (Complex):**

If you need `deleted_at` users or cross-database logic.

**Provider:** `app/Providers/RouteServiceProvider.php`

```php
public function boot()
{
    Route::bind('user_token', function ($value) {
        return User::where('api_token', $value)->firstOrFail();
    });
}
```

**Controller:** `public function show($user_token)` (Injects the Model)

---

### ✅ When to Use
1.  **Always**: Use Implicit Binding for standard CRUD.
2.  **Scopes**: `/teams/{team}/projects/{project}` -> Ensures project belongs to team.

### ❌ When NOT to Use
1.  **Complex Permissions**: "Find user only if current user is admin".
    Middleware is better. Or a query inside the controller.

---

### 🎙 Interview Insight
**Q: "How does Scoped Binding handle missing relationships?"**
**A:** "If I visit `/users/1/posts/99` and Post 99 belongs to User 2, Scoped Binding throws a **404 Not Found**.
It adds a `where('user_id', 1)` to the query automatically.
Without Scoping (`withoutScopedBindings()`), Laravel would return Post 99 separately, allowing potential IDOR vulnerabilities."
