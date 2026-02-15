# 🧪 01 - PHPStan & Larastan: Static Analysis

### 🧐 Explanation
PHP is dynamically typed. This means `$x` can be a string, array, or object.
**PHPStan** checks your code **without running it** and finds bugs.
**Larastan** is a wrapper that teaches PHPStan about Laravel Magic (Models, Facades).

**Goal**: Catch `Call to undefined method User::is_admin()` before deploying.

---

### 💻 Setup

1.  **Install:** `composer require --dev nunomaduro/larastan`
2.  **Config:** Create `phpstan.neon`

```yaml
includes:
    - vendor/nunomaduro/larastan/extension.neon

parameters:
    paths:
        - app/

    # Level 9 is the highest level (strictest)
    level: 5 

#    ignoreErrors:
#        - '#PHPDoc tag @var#'
#
#    excludePaths:
#        - ./*/*/FileToBeExcluded.php
#
#    checkMissingIterableValueType: false
```

---

### 💻 Code Example

**❌ Bad (Fails Level 5):**

```php
public function getUser($id) {
    // PHPStan error: Parameter $id has no typehint.
    // PHPStan error: Call to undefined method is_admin().
    return User::find($id)->is_admin(); 
}
```

**✅ Good (Level 9):**

```php
/**
 * @param int $id
 * @return bool
 */
public function isAdmin(int $id): bool 
{
    $user = User::find($id);

    if (!$user) {
        return false;
    }

    return (bool) $user->is_admin;
}
```

---

### ✅ When to Use
1.  **CI/CD**: Always run `vendor/bin/phpstan analyse` in GitHub Actions.
2.  **Refactoring**: Ensures you didn't break obscure methods.

### ❌ When NOT to Use
1.  **Never**: Static analysis is mandatory for modern PHP. Start at Level 1 if Level 5 is too hard.

---

### 🎙 Interview Insight
**Q: "What is Larastan meant for?"**
**A:** "PHPStan doesn't understand Laravel's `User::where(...)` magic methods because they don't exist on the class (they are routed via `__callStatic`).
Larastan parses the Eloquent Mixins and docblocks to tell PHPStan: 'Yes, `User::where()` exists and returns a Builder instance'. Without it, PHPStan would report errors on almost every Laravel line."
