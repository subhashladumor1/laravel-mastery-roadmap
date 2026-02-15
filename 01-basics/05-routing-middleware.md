# 🚏 Routing & Middleware: The Traffic Control

> **"Don't put logic in `web.php`."**
> Understand Route Model Binding, Named Routes, Groups, and Scoping.

---

## 🟢 1. Route Model Binding (Implicit)
Laravel automatically resolves Eloquent models defined in route/controller method.

```php
// web.php
Route::get('/users/{user}', [UserController::class, 'show']);

// Controller
public function show(User $user) {
    // $user is automatically fetched by ID: User::findOrFail($id)
    return view('users.show', ['user' => $user]);
}
```

### Custom Keys (Slugs)
Find user by `username` instead of `id`.

```php
// web.php
Route::get('/posts/{post:slug}', [PostController::class, 'show']);
```

---

## 🟠 2. Route Groups & Prefixes
Keep your `web.php` clean. Group by functionality.

```php
Route::middleware(['auth', 'verified'])->prefix('admin')->name('admin.')->group(function () {
    
    // URL: /admin/dashboard
    // Name: admin.dashboard
    Route::get('/dashboard', [AdminController::class, 'index'])->name('dashboard');
    
    // URL: /admin/users
    Route::resource('users', AdminUserController::class);
});
```

---

## 🔴 3. Route Caching (Production Tip)
Routes are compiled to a single file for speed.

**Only use Controller-based routes.** Routes with Closures **cannot be cached**.

```php
// ❌ This breaks `php artisan route:cache`
Route::get('/about', function () {
    return view('about');
});

// ✅ This works
Route::view('/about', 'about');
```

---

## 🟣 4. Reusable Functionality (Fallback)
Handle 404s gracefully via a catch-all route.

```php
Route::fallback(function () {
    return response()->view('errors.404', [], 404);
});
```
