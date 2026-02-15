# 🌐 04 - API Resource Transformation: HATEOAS & JSON

### 🧐 Explanation
Never return `Model::all()` in an API response.
1.  **Security**: You accidentally expose `password_reset_token` or `is_admin`.
2.  **Flexibility**: Changing a database column name (`first_name` -> `fname`) breaks all frontend clients.
3.  **Data Structure**: Frontend often needs nested data (`user.role.name`).

**API Resources** act as a transformation layer between your Database and the JSON output.

---

### 💻 Code Example

**Resource:** `app/Http/Resources/UserResource.php`

```php
public function toArray(Request $request): array
{
    return [
        'id' => $this->id,
        'full_name' => $this->first_name . ' ' . $this->last_name,
        'email' => $this->email,
        'role' => $this->whenLoaded('role', function () {
            return $this->role->name; // Only included if Eager Loaded
        }),
        'created_at_human' => $this->created_at->diffForHumans(),
        'links' => [
            'self' => route('api.users.show', $this->id),
        ],
    ];
}
```

**Controller:** `app/Http/Controllers/Api/UserController.php`

```php
public function index()
{
    // 1. Efficient Query with Eager Loading
    $users = User::with('role')->paginate(15);
    
    // 2. Wrap in Resource Collection
    return UserResource::collection($users);
}

public function show(User $user)
{
    return new UserResource($user);
}
```

---

### ✅ When to Use
1.  **Public APIs**: Mandatory. Hide implementation details.
2.  **Versioning**: API v1 returns `fullname`, v2 returns nested `name: { first, last }`. Resources allow this easily.
3.  **Date Formatting**: Keep frontend dumb. Send already formatted dates.

### ❌ When NOT to Use
1.  **Internal Admin Panel**: If you control both backend and frontend (e.g., Inertia.js), sometimes returning the model directly is fine for velocity *if* strict typing is used.

---

### 🎙 Interview Insight
**Q: "What is the N+1 problem in API Resources?"**

**A:**
"If you access a relationship inside a Resource (`$this->posts->count()`) but didn't eager load it in the controller (`User::with('posts')`), Laravel executes a query for *every singe user* in the list. 
To fix this, always use `whenLoaded('posts')` inside the Resource. This conditionally includes the data only if it was already eager loaded, preventing N+1 queries."
