# 🍼 01 - Modern PHP in Laravel: Embrace 8.4+

### 🧐 Explanation
PHP is old. But **Modern PHP 8.4** is amazing.
*   **Enums**: Define states strictly.
*   **Match**: Better `switch`.
*   **Constructor Property Promotion**: Less boilerplate.
*   **Attributes**: Replace DOC comments.

---

### 💻 Code Example

**❌ Bad (Legacy PHP):**

```php
class User {
    private $email;
    private $role = 'guest';

    public function __construct($email, $role) {
        $this->email = $email;
        $this->role = $role;
    }
}
```

**✅ Good (Modern PHP):**

**File:** `app/Enums/UserRole.php`

```php
enum UserRole: string {
    case GUEST = 'guest';
    case ADMIN = 'admin';
    case CLIENT = 'client';
}
```

**Class:**

```php
class User {
    // Constructor Property Promotion
    public function __construct(
        private string $email,
        private UserRole $role = UserRole::GUEST
    ) {}
    
    public function getPermissions(): array {
        // Match Expression
        return match($this->role) {
            UserRole::ADMIN => ['create', 'read', 'update', 'delete'],
            UserRole::CLIENT => ['read'],
            default => [],
        };
    }
}
```

---

### ✅ When to Use
1.  **Enums**: Instead of magic strings like `'active'`, `'inactive'`.
2.  **Match**: Instead of long `if` chains.
3.  **Attributes**: For routing `#[Route('/home')]`.

### ❌ When NOT to Use
1.  **Backward Compatibility**: If you maintain a legacy package that supports PHP 7.4.

---

### 🎙 Interview Insight
**Q: "What are Typed Properties in PHP 7.4+?"**
**A:** "We can enforce types directly on class properties. `public string $name;`.
Before this, we used doc blocks `/** @var string */`. Now it's native. Faster execution and throws `TypeError` immediately if wrong data is passed."
