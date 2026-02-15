# 🔐 04 - SQL Injection Prevention (The Classic)

### 🧐 Explanation
"DROP TABLE users;"
If you write RAW SQL manually, you are likely vulnerable.
Laravel uses **PDO Parameter Binding**, which separates SQL structure from user data.

---

### 💻 Code Example

**❌ Bad (Vulnerable):**

```php
$email = $request->input('email');
// DANGEROUS: If email is "'; DROP TABLE users; --"
$user = DB::select("SELECT * FROM users WHERE email = '$email'");
```

**✅ Good (Binding):**

```php
$user = DB::select('SELECT * FROM users WHERE email = ?', [$email]);
```

**✅ Better (Eloquent):**

```php
$user = User::where('email', $email)->first(); // Eloquent handles binding automatically
```

---

### ⚠️ Dangerous Methods in Laravel
Even Eloquent has **Raw** methods that are vulnerable if misused.

**❌ `whereRaw()` Mistake:**

```php
// If $col is untrusted input (e.g., sort_by querystring)
User::whereRaw("$col > 100")->get();
```

**✅ Correct Usage:**

```php
// Validate column name against whitelist FIRST
if (!in_array($col, ['age', 'score'])) {
    abort(400);
}

User::whereRaw("$col > ?", [100])->get();
```

---

### ✅ When to Use
1.  **Always**: Use Eloquent or `DB::table()`.
2.  **Raw Queries**: Only for complex analytics (`GROUP BY`, `HAVING`) that Eloquent can't express cleanly. **Always bind parameters**.

### ❌ When NOT to Use
1.  **Never**: There is zero reason to ever concatenate user input into a SQL string.

---

### 🎙 Interview Insight
**Q: "Can `orderBy()` be vulnerable to SQL Injection?"**
**A:** "Yes! `orderBy($request->sort)` is risky if unchecked.
Attackers can inject time-based blind SQLi via ordering clause: `orderBy('id, (SELECT SLEEP(10))')`.
Always validate the sort column against a whitelist array before passing it to `orderBy`."
