# 🍼 Intermediate: Traits vs Interfaces

## ❓ The Question
**"When should I use a Trait vs an Interface in PHP/Laravel?"**

---

### 💡 The Answer (Intermediate)
1.  **Interface (`implements`)**: Defines a **contract** ("This class MUST have a `log()` method"). It enforces consistency. It doesn't provide implementation logic.
    *   **Use when**: You want interchangeability (Polymorphism). `Storage::disk('s3')` and `Storage::disk('local')` both implement `FilesystemInterface`.
    
2.  **Trait (`use`)**: Provides **implementation** ("Here is the code for the `log()` method"). It's horizontal code reuse (Mixins).
    *   **Use when**: Multiple unrelated classes need the same *behavior* (e.g., `SoftDeletes`, `HasApiTokens`).

**Analogy**:
*   **Interface**: "You must have an engine." (Rule)
*   **Trait**: "Here is a V8 Engine." (Part)

---

### 💻 Code Example

**❌ Bad (Inheritance hell):**
`class Admin extends User extends Person extends Mammal`... (Doesn't work for shared logic).

**✅ Good (Trait):**
```php
trait Loggable {
    public function log($message) {
        // Implementation logic
    }
}

class User {
    use Loggable;
}

class Order {
    use Loggable;
}
```

---

### 🔄 Follow-Up Question
**Q: "Can Traits implement Interfaces?"**
**A:** "Technically, no. Traits cannot implement interfaces. But a class uses a Trait *to satisfiy* an Interface requirement. This is a common pattern in Laravel (e.g., `Authenticatable` interface is satisfied by `Authenticatable` trait)."
