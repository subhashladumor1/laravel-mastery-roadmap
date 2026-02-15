# 🏗️ 05 - CQRS (Command Query Responsibility Segregation)

### 🧐 Explanation
Most devs do **CRUD** (Create, Read, Update, Delete) on 1 model.
**CQRS** splits this into 2 models:
1.  **Command (Write)**: `CreateUserCommand` (Side effects, validation).
2.  **Query (Read)**: `GetUserQuery` (Fast, optimized, DTOs).

**Why?**
*   **Write**: Complex logic, transactions, locking.
*   **Read**: Simple `SELECT *`, caching, possibly a *read replica*.

---

### 💻 Code Example (Simple CQRS)

**Action (Command):** `app/Actions/User/CreateUserAction.php`

```php
class CreateUserAction
{
    public function execute(UserData $data): User
    {
        return DB::transaction(function () use ($data) {
            $user = User::create($data->toArray());
            event(new UserCreated($user));
            return $user;
        });
    }
}
```

**Query (Read Model):** `app/Queries/User/UserDashboardQuery.php`

```php
class UserDashboardQuery
{
    public function getStats(int $userId): array
    {
        // Don't use Eloquent relationships! Use fast DB query.
        return DB::table('orders')
            ->where('user_id', $userId)
            ->selectRaw('COUNT(*) as total_orders, SUM(amount) as total_spent')
            ->first();
    }
}
```

---

### ✅ When to Use
1.  **Strict Performance**: Dashboard needs 50ms response. Use raw SQL queries.
2.  **Scalability**: Separate Write DB (Primary) from Read DB (Replica).
3.  **Complex Domains**: E-commerce Checkout vs browsing products.

### ❌ When NOT to Use
1.  **Simple CRUD**: If you just need `User::find($id)`, CQRS is 10x code for 0x gain.
2.  **Small Teams**: Hard to maintain 2 models for everything.

---

### 🎙 Interview Insight
**Q: "Do you use Event Sourcing with CQRS?"**
**A:** "CQRS *often* pairs with Event Sourcing (storing 'UserCreated' event instead of user row), but they are distinct.
I can use CQRS with a standard MySQL table. I just separate the *classes* that read/write to it to clarify intent and optimize reads."
