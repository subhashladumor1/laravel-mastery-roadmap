# 🍼 Beginner Task: API Pagination

### 📌 Problem Description
**"Create a paginated JSON API for users."**
Frontend needs `page=1`, `page=2`.
Don't return 1000 users at once!

**Requirements**:
1.  **GET /api/users**: Standard JSON structure (Wrapper).
2.  **Filter**: `?active=1`.
3.  **Sort**: `?sort_by=name`.
4.  **Paginate**: 15 items per page.

---

### 💻 Solution (Resource + Controller)

**API Resource: `app/Http/Resources/UserResource.php`**

```php
public function toArray($request)
{
    // Transform Data (Hide Password!)
    return [
        'id' => $this->id,
        'full_name' => $this->name,
        'email' => $this->email,
        'joined' => $this->created_at->diffForHumans(),
    ];
}
```

**Controller:**

```php
public function index(Request $request)
{
    // Filter & Sort
    $query = User::query();

    if ($request->has('active')) {
        $query->where('is_active', $request->active);
    }

    if ($request->filled('sort_by')) {
        $query->orderBy($request->sort_by);
    }

    // Paginate automatically adds 'meta' (current_page, last_page) & 'links' (prev/next)
    return UserResource::collection($query->paginate(15));
}
```

---

### 🧪 API Response
```json
{
    "data": [
        { "id": 1, "full_name": "Alice", ... }
    ],
    "links": {
        "first": "http://api/users?page=1",
        "last": "http://api/users?page=10",
        "prev": null,
        "next": "http://api/users?page=2"
    },
    "meta": {
        "current_page": 1,
        "from": 1,
        "last_page": 10
    }
}
```
