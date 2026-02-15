# 🍼 Beginner Task: URL Shortener

### 📌 Problem Description
**"Build a basic `bit.ly` clone."**
Focus on routing and database lookups.

**Requirements**:
1.  **Form**: Input long URL.
2.  **Logic**: Generate short code (6 chars - `Str::random(6)`).
3.  **Validate**: URL format.
4.  **Route**: `/{code}` redirects to Long URL.
5.  **Bonus**: Track click count.

---

### 💻 Solution (Logic)

**Model**: `app/Models/ShortLink.php`
*   `code` (string, unique, index)
*   `link` (text)
*   `clicks` (integer, default 0)

**Controller**:

```php
public function store(Request $request)
{
    $request->validate(['link' => 'required|url']);

    $code = Str::random(6);
    
    // Check collision manually (Beginner)
    while(ShortLink::where('code', $code)->exists()) {
        $code = Str::random(6);
    }
    
    ShortLink::create([
        'link' => $request->link, 'code' => $code
    ]);
    
    return back()->with('success', "Short link: " . url($code));
}

public function redirect($code)
{
    $link = ShortLink::where('code', $code)->firstOrFail();
    $link->increment('clicks');
    
    return redirect($link->link);
}
```
