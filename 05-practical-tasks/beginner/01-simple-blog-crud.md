# 🍼 Beginner Task: Blog CRUD

### 📌 Problem Description
**"Create a simple CRUD for Blog Posts."**
This is the "Hello World" of web development.

**Requirements**:
1.  **Index**: List all posts.
2.  **Show**: Show one post.
3.  **Create**: Form to add post (Title, Body).
4.  **Edit**: Form to update post.
5.  **Validation**: Title is required. Body > 10 chars.

---

### 💻 Solution (Controller)

```php
public function index()
{
    // Eager Load User relationship if needed
    $posts = Post::latest()->paginate(10);
    return view('posts.index', compact('posts'));
}

public function store(Request $request)
{
    // Simple Inline Validation
    $data = $request->validate([
        'title' => 'required|max:255',
        'body' => 'required|min:10',
    ]);

    Post::create($data);

    return redirect()->route('posts.index')->with('success', 'Post created!');
}
```

---

### 🧪 Test It (Manual)
1.  Visit `/posts/create`.
2.  Submit empty form -> Should see error "The title field is required."
3.  Submit valid form -> Should see "Post created!" on index page.
