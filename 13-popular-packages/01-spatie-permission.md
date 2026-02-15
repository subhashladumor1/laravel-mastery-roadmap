# 📦 01 - Spatie Permission: RBAC Made Easy

### 🧐 Explanation
"User has multiple roles. Role has multiple permissions."
**RBAC** (Role-Based Access Control).
1.  **Direct**: `$user->can('edit-post')`.
2.  **Via Role**: `$user->assignRole('editor')` -> Gives `edit-post`, `create-post`.

**Package**: `spatie/laravel-permission` (The standard).

---

### 💻 Setup

```bash
composer require spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```

**Model**: `app/Models/User.php`

```php
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasRoles;
}
```

---

### 💻 Code Example (Usage)

**Seeder**: `database/seeders/RoleSeeder.php`

```php
$role = Role::create(['name' => 'writer']);
$permission = Permission::create(['name' => 'edit articles']);

$role->givePermissionTo($permission);
$user->assignRole('writer');
```

**Auth Gates**: `app/Providers/AuthServiceProvider.php`
(This package automatically registers Gates, so standard Laravel methods work!)

```php
// Controller
$this->authorize('edit articles');

// Blade
@can('edit articles')
    <button>Edit</button>
@endcan
```

---

### 💻 Super Admin Bypass

**Provider**: `app/Providers/AuthServiceProvider.php`

```php
// Typically you want Super Admin to bypass ALL checks
Gate::before(function ($user, $ability) {
    return $user->hasRole('Super Admin') ? true : null;
});
```

---

### ✅ When to Use
1.  **Always**: Don't roll your own `roles` table unless it's stupidly simple.
2.  **Complex Logic**: "User can edit post ONLY if they belong to the same Team". Combine Spatie with **Laravel Policies**.

### ❌ When NOT to Use
1.  **Simple Boolean**: `is_admin` column on User table is enough for a blog with 1 author.

---

### 🎙 Interview Insight
**Q: "If I add a permission to a Role, does the user get it immediately?"**
**A:** "Yes and No.
The package caches permissions to avoid 50 DB queries per request.
However, `Cache::tags(['spatie.permission.cache'])->flush()` runs automatically on model updates.
If you update permissions manually in DB (SQL), the cache is stale. You must clear it manually."
