# 🌐 04 - Socialite: OAuth Made Painless (Google/GitHub/Twitter)

### 🧐 Explanation
"Login with Google".
**Old Way**:
1.  Read Google Docs (300 pages).
2.  Implement OAuth2 endpoints.
3.  Handle refresh token logic.
4.  Get banned for incorrect Scopes.

**New Way (Socialite)**:
`Socialite::driver('google')->redirect();`
`Socialite::driver('google')->user();`

---

### 💻 Setup

```bash
composer require laravel/socialite
```

**Config**: `config/services.php`

```php
'google' => [
    'client_id' => env('GOOGLE_CLIENT_ID'),
    'client_secret' => env('GOOGLE_CLIENT_SECRET'),
    'redirect' => env('GOOGLE_REDIRECT_URI', 'http://localhost/auth/google/callback'),
],
```

---

### 💻 Code Example

**Routes**: `routes/web.php`

```php
Route::get('/auth/google', fn() => Socialite::driver('google')->redirect());

Route::get('/auth/google/callback', function () {
    $googleUser = Socialite::driver('google')->user();

    // Find or Create User
    $user = User::updateOrCreate([
        'email' => $googleUser->email,
    ], [
        'name' => $googleUser->name,
        'google_id' => $googleUser->id,
        'avatar' => $googleUser->avatar,
        'password' => Str::random(24), // Dummy password
    ]);

    Auth::login($user);

    return redirect('/dashboard');
});
```

---

### ✅ When to Use
1.  **Always**: Use OAuth login. Users hate remembering passwords.
2.  **Stateless API**: `Socialite::driver('google')->stateless()->user()` (If backend is separate from frontend).

### ❌ When NOT to Use
1.  **Multiple Accounts**: If user signs up with 'Google + Github' (same email), manage merging logic carefully! Don't create two accounts or overwrite carelessly.

---

### 🎙 Interview Insight
**Q: "How do you handle scopes?"**
**A:** "By default, Socialite only asks for `email` and `profile`.
If I need access to a user's Google Drive, I must explicitly call `->scopes(['https://www.googleapis.com/auth/drive'])`.
Also, handle the 'User Denied Access' error gracefully in the callback!"
