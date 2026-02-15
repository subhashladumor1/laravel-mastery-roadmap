# 🍼 Intermediate Task: Blog with Comments (Polymorphic)

### 📌 Problem Description
**"Add comments to Post AND Video."**
Using a single `comments` table.

**Requirements**:
1.  **Table**: `comments`: `body`, `commentable_id`, `commentable_type`.
2.  **Model**: `Comment`, `Post`, `Video`.
3.  **Relation**: `morphMany`.
4.  **UI**: Display form on both pages.
5.  **Bonus**: Nested comments (Recursion).

---

### 💻 Solution (Migration)

```php
$table->id();
$table->text('body');
$table->morphs('commentable'); // Creates id & type
$table->timestamps();
```

---

### 💻 Solution (Controller)

```php
// Route::post('/video/{video}/comment', [VideoController::class, 'comment']);

public function comment(Request $request, Video $video)
{
    $video->comments()->create([
        'body' => $request->body,
        'user_id' => auth()->id(),
    ]);
    
    return back();
}
```
