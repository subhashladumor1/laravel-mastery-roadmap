# 🗃️ Advanced Eloquent: Performance Tuning

> **"Don't kill your database."**
> Eager Loading vs Lazy Loading, Chunking, Cursors, and when to use raw SQL.

---

## 🟢 1. The N+1 Problem
The #1 performance killer.

### Detection (Strict Mode)
Laravel 9+ can prevent N+1 automatically.

```php
// AppServiceProvider.php
public function boot() {
    Model::preventLazyLoading(! app()->isProduction());
}
```

### The Solution (Eager Loading)
```php
// Loading nested relations
$users = User::with('posts.comments.author')->get();
```

---

## 🟠 2. Handling Massive Data

### `chunk()` vs `cursor()`

**`chunk(100)`:** Loads 100 records into memory, processes them, then loads the next 100.
*Good for: Updating records inside a transaction.*

```php
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        $user->update(['status' => 'active']);
    }
});
```

**`cursor()`:** Uses a generator to load *one record at a time*. Extremely low memory usage.
*Good for: Exporting CSVs, iterating millions of rows.*

```php
foreach (User::cursor() as $user) {
    // Only 1 user in memory at a time
    echo $user->name;
}
```

---

## 🔴 3. Advanced Query Techniques

### Subqueries (Select)
Add a column from a related table without a join.

```php
$users = User::addSelect(['last_login_at' => Login::select('created_at')
    ->whereColumn('user_id', 'users.id')
    ->latest()
    ->limit(1)
])->get();
```

### Where Has (Filtering by Relation)
Only get users who have commented.

```php
$users = User::whereHas('comments', function ($query) {
    $query->where('content', 'like', '%awesome%');
})->get();
```

### Raw Expressions (`DB::raw`)
Sometimes you need the power of SQL.

```php
$orders = Order::select(DB::raw('count(*) as total, status'))
    ->groupBy('status')
    ->get();
```
