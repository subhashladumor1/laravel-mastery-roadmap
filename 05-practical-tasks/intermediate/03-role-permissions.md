# 🍼 Intermediate Task: Role-Based Access (Simple V1)

### 📌 Problem Description
**"Build a basic Role system without packages."**
Before Spatie, how did we do it?
Learn `Policies`.

**Requirements**:
1.  **User**: `role` column ('admin', 'editor', 'user').
2.  **Post**: `user_id` (Author).
3.  **Policy**:
    *   **Admin**: Can Edit ANY Post.
    *   **Editor**: Can Edit ANY Post.
    *   **User**: Can Edit **ONLY THEIR OWN** Post.

---

### 💻 Solution (Policy)

**Generate**: `php artisan make:policy PostPolicy --model=Post`

**app/Policies/PostPolicy.php**

```php
public function update(User $user, Post $post)
{
    // Admin Override
    if ($user->role === 'admin' || $user->role === 'editor') {
        return true;
    }

    // Ownership Check
    return $user->id === $post->user_id;
}
```

**Controller**:

```php
public function update(Request $request, Post $post)
{
    // Authorize Action
    $this->authorize('update', $post);

    // If unauthorized, throws 403 Forbidden automatically.
    
    $post->update($request->all());
    return redirect('/posts');
}
```

---

### 🧪 Test Cases
1.  **Admin -> Update Someone Else**: PASS.
2.  **User A -> Update User A**: PASS.
3.  **User A -> Update User B**: FAIL (403).
