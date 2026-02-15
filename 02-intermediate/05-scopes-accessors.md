# 🗃️ Scope & Accessor Magic

> **"Keep your Controllers Clean."**
> Using Local Scopes, Global Scopes, Accessors, and Mutators to encapsulate logic.

---

## 🟢 1. Local Scopes (`scope...`)
Filters you reuse often.

```php
// User.php
public function scopeActive($query) {
    return $query->where('is_active', true);
}

// Controller
$users = User::active()->get();
```

### Dynamic Scopes
```php
public function scopeOfType($query, string $type) {
    return $query->where('type', $type);
}

// Usage
User::ofType('admin')->get();
```

---

## 🟠 2. Global Scopes
Applies to EVERY query automatically.

### Anonymous Global Scope (Boot Method)
```php
// Post.php
protected static function booted() {
    static::addGlobalScope('published', function ($builder) {
        $builder->whereNotNull('published_at');
    });
}
```

### Removing Global Scopes
Sometimes you need the inactive users (e.g., Admin Panel).
```php
$allPosts = Post::withoutGlobalScope('published')->get();
```

---

## 🔴 3. Accessors & Mutators (New Syntax)
Modify data when getting (`get`) or setting (`set`).

### The Attribute Class (Modern Way)
```php
use Illuminate\Database\Eloquent\Casts\Attribute;

protected function name(): Attribute {
    return Attribute::make(
        get: fn ($value) => ucfirst($value),
        set: fn ($value) => strtolower($value),
    );
}
```

### Computed Attributes (Virtual Columns)
No database column needed.
```php
protected function fullName(): Attribute {
    return Attribute::make(
        get: fn ($value, $attributes) => $attributes['first_name'] . ' ' . $attributes['last_name'],
    );
}

// Usage (Enable appending to JSON)
protected $appends = ['full_name'];
```
