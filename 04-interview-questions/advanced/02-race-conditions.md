# 🧠 02 - Race Conditions: The Silent Killer

### ❓ The Interview Question
**"You have a wallet system. Two requests come in at the exact same millisecond to withdraw $100. The user has only $100. How do you prevent the user from withdrawing $200 (double spending)?"**

---

### 💡 High-Level Answer (Senior Architect)
"This is a classic **Race Condition** caused by the 'Read-Modify-Write' cycle.
1.  Request A reads balance: $100.
2.  Request B reads balance: $100.
3.  Request A subtracts $100 -> writes $0.
4.  Request B subtracts $100 -> writes $0.

To fix this, we need **Atomic Operations** or **Database Locking**.
In Laravel, I use:
1.  **Pessimistic Locking**: `lockForUpdate()`.
2.  **Atomic Updates**: `decrement()`.
3.  **Optimistic Locking**: Version column."

---

### 💻 Code Example

**❌ Bad (Vulnerable):**

```php
$balance = $user->balance;
if ($balance >= 100) {
    $user->balance = $balance - 100;
    $user->save();
}
```

**✅ Good (Atomic Update):**

```php
// The database handles the math atomically.
// Returns 1 if a row was updated (success), 0 if condition failed.
$affected = DB::table('users')
    ->where('id', $user->id)
    ->where('balance', '>=', 100)
    ->decrement('balance', 100);

if ($affected) {
    // Grant standardized access
}
```

**✅ Good (Pessimistic Lock):**

```php
DB::transaction(function () use ($user) {
    // LE SELECT ... FOR UPDATE
    $user = User::where('id', $user->id)->lockForUpdate()->first();

    if ($user->balance >= 100) {
        $user->balance -= 100;
        $user->save();
    }
});
```

---

### 🌍 Real World Scenario
**Scenario:**
Ticket Sales. 100 tickets available.
1000 users click "Buy" at 10:00:00 AM.
Without locking, you might sell 150 tickets.
**Solution**: Redis Atomic Locks or Database Row Locking on the `concerts` table.

---

### 🔄 Follow-Up Question
**Q: "What is the downside of `lockForUpdate()`?"**
**A:** "Reduced concurrency. It locks the row (and sometimes the gap/page) until the transaction commits.
If the transaction invokes a slow external API (e.g., Stripe), other users trying to buy tickets will wait and timeout (Database Deadlock).
Always keep transactions **short and fast**."
