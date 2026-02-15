# 🍼 Beginner Task: Custom Login Logic

### 📌 Problem Description
**"Create a manual login form (Not using Breeze/Jetstream/Breeze)."**
This teaches you how `Auth::attempt()` works.

**Requirements**:
1.  **Form**: Email/Password.
2.  **Submit**: POST to `/login`.
3.  **Validate**: `required|email`, `required|min:6`.
4.  **Attempt**: `Auth::attempt($credentials)`.
5.  **Redirect**: `/dashboard` on success, `/login` with error on failure.

---

### 💻 Solution (Controller)

```php
public function attempt(Request $request)
{
    $credentials = $request->validate([
        'email' => ['required', 'email'],
        'password' => ['required'],
    ]);

    if (Auth::attempt($credentials)) {
        // Important: Regenerate session token! (Anti-Fixation)
        $request->session()->regenerate();

        return redirect()->intended('/dashboard');
    }

    return back()->withErrors([
        'email' => 'The provided credentials do not match our records.',
    ])->onlyInput('email');
}
```

---

### 🧪 Test It (Manual)
1.  Enter wrong password -> See error "The provided credentials...".
2.  Enter correct password -> You are redirected.
3.  Check Database -> `sessions` table (if driver=db) or `storage/framework/sessions`.
4.  Logout -> `Auth::logout()` -> Invalidates session.
