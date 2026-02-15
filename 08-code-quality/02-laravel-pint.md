# 🧪 02 - Laravel Pint: Automatic Style Fixing

### 🧐 Explanation
Before 2022, we used `php-cs-fixer`.
Now, **Laravel Pint** is standard. It's a wrapper around `php-cs-fixer` with built-in Laravel rules (PSR-12 + Laravel style).

**Goal**: Stop arguing about braces `{` in PRs.

---

### 💻 Usage

```bash
# Run on whole project
./vendor/bin/pint

# Run on specific file/folder
./vendor/bin/pint app/Models

# Test (Dry Run)
./vendor/bin/pint --test

# Fix & Commit
./vendor/bin/pint --dirty
```

---

### 💻 Configuration (`pint.json`)

```json
{
    "preset": "laravel",
    "rules": {
        "array_indentation": true,
        "clean_namespace": true,
        "concat_space": {
            "spacing": "one"
        },
        "function_typehint_space": true
    }
}
```

---

### ✅ When to Use
1.  **Every Commit**: Add a pre-commit hook (Husky) to auto-format staged files.
2.  **CI/CD**: Reject PRs that don't pass styling.

### ❌ When NOT to Use
1.  **Huge Legacy Codebase**: Running strict formatting on 10,000 files creates a massive diff. Do it gradually or on changed files only.

---

### 🎙 Interview Insight
**Q: "Why follow PSR-12 standard?"**
**A:** "Because consistent code style reduces cognitive load. If every file looks the same, I focus on *what* the code does, not *how* it's formatted. It also prevents merge conflicts caused by whitespace changes."
