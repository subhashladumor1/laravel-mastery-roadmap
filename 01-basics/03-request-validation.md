# 🍼 03 - Request Validation: Keep Your Code Safe

### 🧐 Explanation
Before touching your business logic, **Validate input**.
**Form Requests** allow you to separate validation rules from Controller logic.

---

### 💻 Code Example

**❌ Bad (Inline Validation):**

```php
// In a Controller
public function store(Request $request) {
    if (!$request->has('email')) {
        abort(400, 'Email missing');
    }
    
    // Logic mixup
    $user = User::create($request->all());
}
```

**✅ Good (Form Request):**

**File:** `app/Http/Requests/StorePostRequest.php`

```php
use Illuminate\Validation\Rule;

public function rules(): array
{
    return [
        'title' => ['required', 'string', 'max:255'],
        'content' => ['required', 'string'],
        'status' => ['required', Rule::in(['draft', 'published'])],
        'slug' => ['required', Rule::unique('posts', 'slug')->ignore($this->post)],
    ];
}

public function messages(): array
{
    return [
        'slug.unique' => 'URL slug already taken. Try another title.',
    ];
}
```

---

### ✅ When to Use
1.  **Complex Rules**: `unique:users,email,NULL,id,role,admin`.
2.  **Authorization**: `authorize()` method inside Form Request.

### ❌ When NOT to Use
1.  **Simple Checks**: If you just need `required|string`, you can do `$request->validate(['email' => 'required'])` inside method. But consistently using Form Requests is safer.

---

### 🎙 Interview Insight
**Q: "Tell me about Route Model Binding inside Form Requests."**
**A:** "In a Form Request, I can access the route parameter (like `$this->user` or `$this->route('post')`).
This is critical for validation rules like 'Check if email is unique, but ignore the current user ID when updating.'
Example: `Rule::unique('users')->ignore($this->user->id)`."
