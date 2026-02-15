# 🏗️ 01 - The Repository Pattern: When to Use & When NOT to Use

### 🧐 Explanation
The Repository Pattern mediates between the domain and data mapping layers.
In simpler terms: It decouples your business logic from Eloquent/ActiveRecord.

**Goal:** Swap MySQL for MongoDB or an API later? (Ideally, yes. Practically, 99% never do).

---

### 💻 Code Example (The "Clean" Way)

**Interface:** `app/Repositories/Contracts/UserRepositoryInterface.php`

```php
interface UserRepositoryInterface {
    public function findByEmail(string $email): ?User;
    public function create(array $data): User;
}
```

**Implementation:** `app/Repositories/Eloquent/EloquentUserRepository.php`

```php
class EloquentUserRepository implements UserRepositoryInterface {
    public function findByEmail(string $email): ?User {
        return User::where('email', $email)->first();
    }
    // ...
}
```

**Usage (Service/Controller):**

```php
// Type-hint the Interface, NOT the implementation
public function __construct(UserRepositoryInterface $users) {
    $this->users = $users;
}
```

---

### ✅ When to Use
1.  **Testing (Mocking)**: You want to unit test your Service without touching the database. Mocking an Interface is cleaner than mocking Eloquent static calls.
2.  **Multiple Data Sources**: You retrieve users from MySQL for app usage, but from an external API for legacy systems. A repository hides this complexity.
3.  **Complex Queries**: If you have 50-line raw SQL queries joining 5 tables, hide that mess in a Repository method like `getActivePremiumUsers()`.

### ❌ When NOT to Use (The Trap)
1.  **Simple CRUD**: Wrapping `User::find($id)` in `repo->find($id)` is **useless boilerplate**. It adds files, complexity, and zero value.
2.  **Losing Eloquent Power**: If you return arrays instead of Models, you lose Model Accessors, Scopes, and Relationships. You end up re-inventing Eloquent.
3.  **YAGNI (You Ain't Gonna Need It)**: "What if we switch to MongoDB?" You won't. And if you do, rewriting the queries is a 2-week job, not impossible. Don't over-engineer for a 1% chance.

---

### 🎙 Interview Insight
**Q: "Why do some developers argue against Repositories in Laravel?"**

**A:**
"Because Eloquent is an implementation of the **Active Record** pattern, which merges data and behavior. The Repository pattern is from **Data Mapper** (like Doctrine/Java/Hibernate).
Forcing a Data Mapper pattern onto an Active Record ORM often leads to an 'Anemic Domain Model' where models are just data bags. Use Repositories for *complex data retrieval*, but don't be afraid to use Eloquent directly for simple logic."
