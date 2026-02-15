# 🗃️ Eloquent Relationships: The Complete Guide

> **"Data is relational. Your code should be too."**
> Mastering One-to-One, One-to-Many, Many-to-Many, Polymorphic, and their inverses.

---

## 🟢 1. Basic Relationships

### One to One (`hasOne` / `belongsTo`)
*User has one Phone.*
```php
// User.php
public function phone() {
    return $this->hasOne(Phone::class);
}

// Phone.php
public function user() {
    return $this->belongsTo(User::class);
}
```

### One to Many (`hasMany` / `belongsTo`)
*Post has many Comments.*
```php
// Post.php
public function comments() {
    return $this->hasMany(Comment::class);
}
```

### Many to Many (`belongsToMany`)
*User has many Roles, Role has many Users.*
Requires a pivot table: `role_user` (user_id, role_id).

```php
// User.php
public function roles() {
    return $this->belongsToMany(Role::class)
        ->withTimestamps()
        ->withPivot('expires_at'); // Extra pivot data
}
```

---

## 🟠 2. Advanced Relationships

### Has One Through (`hasOneThrough`)
*Mechanic -> Car -> Owner.*
Get the Owner of the Car that the Mechanic fixes, without direct link.
```php
// Mechanic.php
public function carOwner() {
    return $this->hasOneThrough(Owner::class, Car::class);
}
```

### Has Many Through (`hasManyThrough`)
*Country -> Users -> Posts.*
Get all Posts for a Country.
```php
// Country.php
public function posts() {
    return $this->hasManyThrough(Post::class, User::class);
}
```

---

## 🔴 3. Polymorphic Relationships (The "Morph")
Allow a model to belong to more than one other type of model.

### Morph One / Many (`morphMany`)
*Posts have Comments. Videos have Comments.*
Table `comments`: `body`, `commentable_id`, `commentable_type`.

```php
// Comment.php
public function commentable() {
    return $this->morphTo();
}

// Post.php
public function comments() {
    return $this->morphMany(Comment::class, 'commentable');
}

// Video.php
public function comments() {
    return $this->morphMany(Comment::class, 'commentable');
}
```

### Morph Many to Many (`morphToMany`)
*Posts have Tags. Videos have Tags.*
Table `taggables`: `tag_id`, `taggable_id`, `taggable_type`.

```php
// Post.php
public function tags() {
    return $this->morphToMany(Tag::class, 'taggable');
}
```

---

## ⚡ 4. Deep Optimization

### Eager Loading (`with`)
Prevent N+1 queries.
```php
$posts = Post::with(['comments.user', 'tags'])->get();
```

### Constraining Eager Loads
Load only *approved* comments.
```php
$posts = Post::with(['comments' => function ($query) {
    $query->where('is_approved', true);
}])->get();
```

### Dynamic Relationships (`ofMany`)
Get the *latest* order.
```php
// User.php
public function latestOrder() {
    return $this->hasOne(Order::class)->latestOfMany();
}
```
