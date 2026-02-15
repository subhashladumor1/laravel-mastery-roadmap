# 🗃️ Eloquent Basics: Models & Migrations

> **"Your blueprint for data."**
> Understanding how to interact with the database using PHP instead of SQL.

---

## 🟢 1. Migrations (Schema Definition)
Never edit database tables manually.

### Best Practices

```php
// ❌ Don't be vague
$table->string('name');

// ✅ Be Explicit
$table->string('email', 191)->unique();
$table->decimal('amount', 8, 2); // Avoid floats for money
$table->foreignIdFor(User::class)->constrained()->cascadeOnDelete(); // Handles FK
```

### Soft Deletes
Keep records in the database, but consider them deleted.
```php
// Migration
$table->softDeletes();

// Model (User.php)
use Illuminate\Database\Eloquent\SoftDeletes;

class User extends Model {
    use SoftDeletes;
}

// Restore
User::withTrashed()->find(1)->restore();
```

---

## 🟠 2. Factories & Seeders (Fake Data)
Populate your local database quickly.

### Defining a Factory
```php
// UserFactory.php
public function definition() {
    return [
        'name' => fake()->name(),
        'email' => fake()->unique()->safeEmail(),
        'role' => 'user',
    ];
}
```

### Using States (Avoid Logic)
```php
public function admin() {
    return $this->state(fn (array $attributes) => [
        'role' => 'admin',
    ]);
}

// Seeder
User::factory(10)->create();
User::factory()->admin()->create();
```

---

## 🔴 3. Mass Assignment Protection ($fillable)
Prevent users from updating restricted fields (like `is_admin`).

### Using `$guarded` (Recommended)
Allow everything *except* critical fields.

```php
class User extends Model {
    protected $guarded = ['id', 'is_admin']; 
}
```

### Using `$fillable` (Legacy / Explicit)
Only allow specific fields.

```php
protected $fillable = ['name', 'email', 'password'];
```
