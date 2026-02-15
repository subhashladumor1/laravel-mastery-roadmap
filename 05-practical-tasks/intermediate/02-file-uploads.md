# 🍼 Intermediate Task: File Uploads & Resizing

### 📌 Problem Description
**"User uploads an Avatar. We need to resize it to 200x200."**
Storing 5MB images for avatars is wasteful.

**Requirements**:
1.  **Validate**: `mimes:jpg,png`, `max:2048`.
2.  **Store**: `storage/app/public/avatars`.
3.  **Resize**: Use Intervention Image (`intervention/image`).
4.  **Save**: Update User model with new path.
5.  **Delete**: Remove OLD avatar if new one is uploaded.

---

### 💻 Solution (Controller)

```php
use Intervention\Image\Facades\Image;
use Illuminate\Support\Facades\Storage;

public function update(Request $request)
{
    $request->validate([
        'avatar' => 'image|mimes:jpeg,png,jpg,gif,svg|max:2048',
    ]);

    if ($request->hasFile('avatar')) {
        $user = auth()->user();
        
        // 1. Delete Old Avatar
        if ($user->avatar) {
            Storage::delete('public/avatars/' . $user->avatar);
        }

        // 2. Resize and Save New
        $avatarName = time().'.'.$request->avatar->getClientOriginalExtension();
        $path = $request->file('avatar')->storeAs('public/avatars', $avatarName);
        
        // Imagine: $img = Image::make($path)->resize(200, 200)->save($path);
        
        $user->avatar = $avatarName;
        $user->save();
        
        return back()->with('success','You did it!');
    }
}
```

---

### ⚠️ Common Issue
**Storage Link**: Why is my image 404?
Remember: `php artisan storage:link` creates a symlink from `public/storage` -> `storage/app/public`.
Without this, files are hidden from the browser.
